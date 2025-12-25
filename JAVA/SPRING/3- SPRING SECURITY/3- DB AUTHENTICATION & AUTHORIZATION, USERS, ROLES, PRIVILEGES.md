## 1. Using Database Users for Authentication & Authorization

### Why Databases Are Used in Real Projects

In real systems:

- Users must persist across restarts
    
- Roles & privileges change dynamically
    
- Security is managed by admins
    
- Auditing is required
    

👉 **In-memory users are never used in production**.

---

## 2. Role vs Privilege vs Authority (CRITICAL CONCEPT)

### Definitions

|Term|Meaning|
|---|---|
|**Privilege**|A single permission (fine-grained)|
|**Role**|A group of privileges|
|**Authority**|What Spring Security actually checks|

---

### Real-World Analogy (Best Way to Remember)

**Bank System**

- Privileges: `READ_ACCOUNT`, `TRANSFER_MONEY`, `CLOSE_ACCOUNT`
    
- Roles:
    
    - CUSTOMER → READ_ACCOUNT
        
    - MANAGER → READ_ACCOUNT + TRANSFER_MONEY
        
    - ADMIN → ALL
        
- Authorities = roles + privileges converted to strings
    

---

### How Spring Security Sees It

Spring Security **does NOT know roles or privileges**.

It only knows:

```text
GrantedAuthority (String)
```

Examples:

- `ROLE_ADMIN`
    
- `READ_USER`
    
- `DELETE_ORDER`
    

---

## 3. Recommended Real-World Data Model

### User Entity (Email-Based Login)

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue
    private Long id;

    @Column(unique = true, nullable = false)
    private String email;

    private String password;
    private boolean enabled;

    @ManyToMany(fetch = FetchType.EAGER)
    @JoinTable(
        name = "users_roles",
        joinColumns = @JoinColumn(name = "user_id"),
        inverseJoinColumns = @JoinColumn(name = "role_id")
    )
    private Set<Role> roles;
}
```

---

### Role Entity

```java
@Entity
@Table(name = "roles")
public class Role {

    @Id
    @GeneratedValue
    private Long id;

    private String name; // ADMIN, USER

    @ManyToMany(fetch = FetchType.EAGER)
    @JoinTable(
        name = "roles_privileges",
        joinColumns = @JoinColumn(name = "role_id"),
        inverseJoinColumns = @JoinColumn(name = "privilege_id")
    )
    private Set<Privilege> privileges;
}
```

---

### Privilege Entity

```java
@Entity
@Table(name = "privileges")
public class Privilege {

    @Id
    @GeneratedValue
    private Long id;

    private String name; // READ_USER, DELETE_USER
}
```

---

## 4. Sample Database Records (Very Realistic)

### Privileges

|id|name|
|---|---|
|1|READ_USER|
|2|CREATE_USER|
|3|DELETE_USER|

---

### Roles

|id|name|
|---|---|
|1|USER|
|2|ADMIN|

---

### Role → Privilege Mapping

|role|privilege|
|---|---|
|USER|READ_USER|
|ADMIN|READ_USER|
|ADMIN|CREATE_USER|
|ADMIN|DELETE_USER|

---

### Users

|email|roles|
|---|---|
|[john@mail.com](mailto:john@mail.com)|USER|
|[admin@mail.com](mailto:admin@mail.com)|ADMIN|

---

## 5. Role vs Privilege vs Authority (How to Map)

### Rule to Memorize

> Roles and privileges must be converted into **GrantedAuthority strings**.

### Common Convention

- Roles → `ROLE_ADMIN`
    
- Privileges → `READ_USER`
    

---

## 6. SetupDataLoader (REAL-WORLD MUST-HAVE)

### Why It’s Needed

In production systems:

- Roles & privileges must exist **before users**
    
- Manual DB inserts are error-prone
    
- Deployments must be repeatable
    
- Multiple environments must stay consistent
    

---

### `SetupDataLoader` (Production-Style)

```java
@Component
@Transactional
public class SetupDataLoader
        implements ApplicationListener<ContextRefreshedEvent> {

    private boolean alreadySetup = false;

    private final PrivilegeRepository privilegeRepository;
    private final RoleRepository roleRepository;

    public SetupDataLoader(
            PrivilegeRepository privilegeRepository,
            RoleRepository roleRepository) {
        this.privilegeRepository = privilegeRepository;
        this.roleRepository = roleRepository;
    }

    @Override
    public void onApplicationEvent(ContextRefreshedEvent event) {

        if (alreadySetup) return;

        Privilege readUser = createPrivilegeIfNotFound("READ_USER");
        Privilege createUser = createPrivilegeIfNotFound("CREATE_USER");
        Privilege deleteUser = createPrivilegeIfNotFound("DELETE_USER");

        createRoleIfNotFound("USER", Set.of(readUser));
        createRoleIfNotFound("ADMIN", Set.of(readUser, createUser, deleteUser));

        alreadySetup = true;
    }

    private Privilege createPrivilegeIfNotFound(String name) {
        return privilegeRepository.findByName(name)
            .orElseGet(() -> privilegeRepository.save(new Privilege(name)));
    }

    private void createRoleIfNotFound(String name, Set<Privilege> privileges) {
        roleRepository.findByName(name)
            .orElseGet(() -> {
                Role role = new Role();
                role.setName(name);
                role.setPrivileges(privileges);
                return roleRepository.save(role);
            });
    }
}
```

---

## 7. Custom `UserDetailsService` (Email-Based)

### Repository

```java
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
}
```

---

### Custom `UserDetailsService` (Production-Style)

```java
@Service
public class CustomUserDetailsService implements UserDetailsService {

    private final UserRepository userRepository;

    public CustomUserDetailsService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @Override
    public UserDetails loadUserByUsername(String email)
            throws UsernameNotFoundException {

        User user = userRepository.findByEmail(email)
            .orElseThrow(() ->
                new UsernameNotFoundException("User not found"));

        return new org.springframework.security.core.userdetails.User(
            user.getEmail(),
            user.getPassword(),
            user.isEnabled(),
            true,
            true,
            true,
            getAuthorities(user)
        );
    }

    private Collection<? extends GrantedAuthority> getAuthorities(User user) {

        Set<GrantedAuthority> authorities = new HashSet<>();

        for (Role role : user.getRoles()) {
            authorities.add(new SimpleGrantedAuthority(
                "ROLE_" + role.getName()));

            for (Privilege privilege : role.getPrivileges()) {
                authorities.add(new SimpleGrantedAuthority(
                    privilege.getName()));
            }
        }

        return authorities;
    }
}
```

---

## 8. Using Privileges in Authorization (Real-World)

### URL-Based Authorization

```java
.authorizeHttpRequests(auth -> auth
    .requestMatchers("/users/**").hasAuthority("READ_USER")
    .requestMatchers("/admin/**").hasRole("ADMIN")
    .anyRequest().authenticated()
)
```

---

### Method-Level Authorization (Recommended)

```java
@PreAuthorize("hasAuthority('DELETE_USER')")
public void deleteUser(Long id) {
}
```

---

## 9. When to Use Roles vs Privileges

|Use Case|Use|
|---|---|
|High-level access|Role|
|Fine-grained actions|Privilege|
|REST endpoints|Privilege|
|UI navigation|Role|

---

## 10. Why Privileges Scale Better Than Roles

Roles:

- Hard to combine
    
- Grow exponentially
    

Privileges:

- Reusable
    
- Fine-grained
    
- Easy to audit
    

👉 **Real systems always use privileges internally**.

---

## 11. Common Mistakes (Seen in Real Projects)

- Using roles only
    
- Hardcoding role names
    
- Not prefixing roles with `ROLE_`
    
- Loading roles lazily
    
- Not handling disabled users
    

---

## 12. Interview Questions & Answers (🔥)

### Q: Difference between role and authority?

**A:** A role is a logical grouping, authority is what Spring Security evaluates.

### Q: Can Spring Security authenticate by email?

**A:** Yes, `UserDetailsService` controls how users are loaded.

### Q: Why use `SetupDataLoader`?

**A:** To ensure roles and privileges are consistently created at startup.

---

## 13. Things to Memorize (High Value)

- Spring Security checks **authorities only**
    
- Roles are prefixed with `ROLE_`
    
- Privileges are flat strings
    
- `UserDetailsService` bridges DB → Security
    
- `SetupDataLoader` bootstraps RBAC
    
- Email-based login is industry standard
    

---

## 14. One-Line Real-World Explanation (Gold)

> In real systems, users are authenticated by email, roles group privileges, roles and privileges are bootstrapped at startup, and everything is evaluated as authorities by Spring Security.

---

## 15. How This Looks in a Real Project

|Layer|Responsibility|
|---|---|
|DB|Users, roles, privileges|
|Bootstrap|SetupDataLoader|
|Service|Business logic|
|Security|Authentication & authorization|
|Controllers|Access endpoints|
