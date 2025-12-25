## PART A — REMEMBER-ME AUTHENTICATION

---

## 1. What Is “Remember-Me” in Spring Security?

**Remember-Me** allows users to stay logged in **after the session expires** or the browser is closed.

Typical behavior:

- User checks **“Remember me”**
    
- Login persists for days or weeks
    
- User is automatically authenticated later
    

---

## 2. Real-World Use Cases

- “Keep me logged in” checkbox
    
- Public websites
    
- Admin panels (with caution)
    

❌ **Not recommended** for:

- Banking systems
    
- Highly sensitive apps
    

---

## 3. How Remember-Me Works (High-Level)

1. User logs in with checkbox enabled
    
2. Spring Security creates a **remember-me cookie**
    
3. Cookie stored in browser
    
4. Session expires
    
5. Cookie sent with next request
    
6. User auto-authenticated
    

---

## 4. `rememberMe()` Configuration Overview

### Basic Configuration

```java
.rememberMe(remember -> remember
    .key("secureKey")
    .tokenValiditySeconds(86400) // 1 day
)
```

### What Each Option Means

|Option|Purpose|
|---|---|
|`key`|Used to sign remember-me tokens|
|`tokenValiditySeconds`|Cookie lifespan|
|`rememberMeParameter`|Name of checkbox field|
|`userDetailsService`|Reload user|
|`useSecureCookie`|HTTPS only|

---

## 5. Types of Remember-Me Implementations

### 5.1 Hash-Based (Default)

- Cookie contains:
    
    - username
        
    - expiry
        
    - hash
        
- Stateless
    
- Easy to implement
    
- Less secure
    

```java
.rememberMe()
```

---

### 5.2 Persistent Token (Recommended)

- Tokens stored in DB
    
- Can revoke tokens
    
- More secure
    

```java
.rememberMe(remember -> remember
    .tokenRepository(persistentTokenRepository())
)
```

---

## 6. Coding Exercise — Remember-Me (Persistent Token)

### Database Table

```sql
CREATE TABLE persistent_logins (
    username VARCHAR(64) NOT NULL,
    series VARCHAR(64) PRIMARY KEY,
    token VARCHAR(64) NOT NULL,
    last_used TIMESTAMP NOT NULL
);
```

---

### Token Repository

```java
@Bean
public PersistentTokenRepository persistentTokenRepository(DataSource dataSource) {
    JdbcTokenRepositoryImpl repo = new JdbcTokenRepositoryImpl();
    repo.setDataSource(dataSource);
    return repo;
}
```

---

### Security Configuration

```java
.rememberMe(remember -> remember
    .tokenRepository(persistentTokenRepository(dataSource))
    .tokenValiditySeconds(7 * 24 * 60 * 60)
    .key("rememberMeKey")
)
```

---

### Login Form

```html
<input type="checkbox" name="remember-me"> Remember me
```

> Default parameter name is **`remember-me`**

---

## 7. Security Risks & Best Practices

✔ Use HTTPS  
✔ Rotate keys  
✔ Set short validity  
✔ Delete tokens on logout

```java
.logout(logout -> logout
    .deleteCookies("remember-me")
)
```

---

## 8. Interview Tips (Remember-Me)

- Remember-me ≠ session
    
- Cookies are used
    
- Persistent tokens are safer
    
- Always delete on logout
    

---

## PART B — METHOD-LEVEL SECURITY

---

## 9. Why Method-Level Security Exists

URL security is not enough:

- Services can be reused
    
- Methods can be invoked internally
    
- Business rules need protection
    

👉 **Method security protects business logic**.

---

## 10. Enabling Method-Level Security

### Modern Way (Spring Security 6+)

```java
@EnableMethodSecurity
@Configuration
public class MethodSecurityConfig {
}
```

---

### Legacy Way (Still Seen)

```java
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true)
```

---

## 11. Spring Expression Language (SpEL)

SpEL allows **dynamic security rules**.

Examples:

```java
hasRole('ADMIN')
hasAuthority('DELETE_USER')
#id == authentication.principal.id
```

---

## 12. `@PreAuthorize` (Most Important)

### What It Does

- Checks authorization **before method execution**
    

### Example

```java
@PreAuthorize("hasAuthority('DELETE_USER')")
public void deleteUser(Long id) {
}
```

---

### Real-World Example (Ownership)

```java
@PreAuthorize("#userId == authentication.principal.id or hasRole('ADMIN')")
public User getProfile(Long userId) {
}
```

---

## 13. `@PostAuthorize`

### What It Does

- Checks authorization **after method execution**
    
- Can inspect returned object
    

### Example

```java
@PostAuthorize("returnObject.owner == authentication.name")
public Document getDocument(Long id) {
    return document;
}
```

---

## 14. `@Secured` (Simple & Legacy)

### Example

```java
@Secured("ROLE_ADMIN")
public void adminOnlyAction() {
}
```

### Limitations

- No SpEL
    
- Role-only checks
    

---

## 15. `@PreAuthorize` vs `@Secured`

|Feature|@PreAuthorize|@Secured|
|---|---|---|
|SpEL|✔|❌|
|Privileges|✔|❌|
|Object checks|✔|❌|
|Recommended|✔|❌|

---

## 16. Real-World Service Example (Complete)

```java
@Service
public class UserService {

    @PreAuthorize("hasAuthority('READ_USER')")
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    @PreAuthorize("hasAuthority('DELETE_USER')")
    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }

    @PostAuthorize("returnObject.email == authentication.name")
    public User getMyProfile(Long id) {
        return userRepository.findById(id).orElseThrow();
    }

    @Secured("ROLE_ADMIN")
    public void adminAction() {
    }
}
```

---

## 17. Common Mistakes (Seen in Production)

- Forgetting `@EnableMethodSecurity`
    
- Using roles everywhere
    
- Putting logic only at URL level
    
- Forgetting to secure service layer
    
- Overusing `@PostAuthorize`
    

---

## 18. Interview Questions & Answers (🔥)

### Q: Why method security over URL security?

**A:** It protects business logic regardless of entry point.

---

### Q: What’s the most powerful annotation?

**A:** `@PreAuthorize`

---

### Q: Can you check object ownership?

**A:** Yes, using SpEL.

---

### Q: Where should method security live?

**A:** Service layer.

---

## 19. Things to Memorize (High Value)

- Remember-me uses cookies
    
- Persistent tokens are safer
    
- Delete remember-me on logout
    
- Method security > URL security
    
- `@PreAuthorize` is preferred
    
- SpEL enables dynamic rules
    

---

## 20. One-Line Senior-Level Explanation (Gold)

> Remember-me extends authentication beyond sessions using cookies, while method-level security protects business logic using expressive, fine-grained rules.
