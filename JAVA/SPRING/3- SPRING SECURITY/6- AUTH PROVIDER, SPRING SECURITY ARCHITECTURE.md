## 1. Spring Security Architecture (Big Picture)

Spring Security is **filter-based**.

Every request goes through a **chain of security filters** _before_ it reaches controllers.

### High-Level Flow (Mental Model)

```
HTTP Request
   ↓
SecurityFilterChain
   ↓
Authentication Filter
   ↓
AuthenticationManager
   ↓
AuthenticationProvider
   ↓
UserDetailsService + PasswordEncoder
   ↓
SecurityContext
```

This flow is **critical to memorize**.

---

## 2. Authentication Filter

### What It Is

An **authentication filter**:

- Intercepts a request
    
- Extracts credentials
    
- Starts authentication
    

Examples:

- `UsernamePasswordAuthenticationFilter`
    
- `BearerTokenAuthenticationFilter`
    
- `BasicAuthenticationFilter`
    

---

### Real-World Example (Form Login)

```java
POST /login
username=admin
password=secret
```

`UsernamePasswordAuthenticationFilter`:

1. Reads username/password
    
2. Creates an **unauthenticated Authentication**
    
3. Passes it to `AuthenticationManager`
    

---

### Key Rule (Interview)

> Filters **do not authenticate** — they only _start_ authentication.

---

## 3. Spring Security Context

### What Is `SecurityContext`?

`SecurityContext` stores the **authenticated user** for the current request.

It contains:

- Principal
    
- Authorities
    
- Authentication state
    

Stored in:

- `SecurityContextHolder`
    

---

### Real-World Use

```java
Authentication auth =
    SecurityContextHolder.getContext().getAuthentication();
```

Used in:

- Controllers
    
- Services
    
- SpEL expressions
    

---

### Lifecycle

- Created after successful authentication
    
- Cleared on logout
    
- Stored in session or thread-local
    

---

## 4. AuthenticationManager

### What It Is

`AuthenticationManager`:

- Coordinates authentication
    
- Delegates to providers
    
- Returns authenticated user
    

---

### Key Rule (VERY IMPORTANT)

> `AuthenticationManager` **never authenticates directly**.

It calls one or more **AuthenticationProviders**.

---

### Code Example

```java
Authentication result =
    authenticationManager.authenticate(authentication);
```

---

## 5. AuthenticationProvider

### What It Is

An `AuthenticationProvider`:

- Performs **actual authentication**
    
- Validates credentials
    
- Loads user details
    

Examples:

- `DaoAuthenticationProvider`
    
- `LdapAuthenticationProvider`
    
- **CustomAuthenticationProvider**
    

---

### How Providers Are Chosen

Each provider implements:

```java
boolean supports(Class<?> authentication);
```

Spring Security:

- Finds a provider that supports the token
    
- Delegates authentication to it
    

---

## 6. UserDetailsService

### What It Is

Loads user information from a source:

- Database
    
- LDAP
    
- API
    

---

### Real-World Responsibility

- Find user by email/username
    
- Load roles & privileges
    
- Return `UserDetails`
    

```java
UserDetails loadUserByUsername(String username);
```

---

## 7. PasswordEncoder

### What It Does

- Hashes passwords
    
- Verifies raw vs hashed password
    

---

### Why It’s Mandatory

> Spring Security **never compares raw passwords**.

```java
passwordEncoder.matches(raw, encoded)
```

---

## 8. Default Authentication Flow (DAO Provider)

### End-to-End Flow

1. Filter extracts credentials
    
2. Creates `UsernamePasswordAuthenticationToken`
    
3. Manager delegates to `DaoAuthenticationProvider`
    
4. Provider calls `UserDetailsService`
    
5. PasswordEncoder verifies password
    
6. Authentication succeeds
    
7. SecurityContext updated
    

---

## 9. Why Implement a Custom AuthenticationProvider?

### Real-World Business Needs

You implement a custom provider when:

|Scenario|Why|
|---|---|
|OTP / SMS login|No password|
|API key authentication|No user form|
|External auth service|Remote validation|
|Multi-factor auth|Custom logic|
|Legacy password hashing|Custom comparison|

👉 **DAO provider is not enough**.

---

## 10. Business Case Example (OTP Login)

### Scenario

- User logs in using **email + OTP**
    
- No password involved
    
- OTP validated via external service
    

---

## 11. Custom Authentication Token

```java
public class OtpAuthenticationToken
        extends AbstractAuthenticationToken {

    private final String email;
    private final String otp;

    public OtpAuthenticationToken(String email, String otp) {
        super(null);
        this.email = email;
        this.otp = otp;
        setAuthenticated(false);
    }

    public OtpAuthenticationToken(
            String email,
            Collection<? extends GrantedAuthority> authorities) {
        super(authorities);
        this.email = email;
        this.otp = null;
        setAuthenticated(true);
    }

    @Override
    public Object getCredentials() {
        return otp;
    }

    @Override
    public Object getPrincipal() {
        return email;
    }
}
```

---

## 12. Custom AuthenticationProvider (CORE PART)

```java
@Component
public class OtpAuthenticationProvider
        implements AuthenticationProvider {

    private final UserDetailsService userDetailsService;
    private final OtpService otpService;

    public OtpAuthenticationProvider(
            UserDetailsService userDetailsService,
            OtpService otpService) {
        this.userDetailsService = userDetailsService;
        this.otpService = otpService;
    }

    @Override
    public Authentication authenticate(Authentication authentication) {

        String email = authentication.getPrincipal().toString();
        String otp = authentication.getCredentials().toString();

        if (!otpService.verify(email, otp)) {
            throw new BadCredentialsException("Invalid OTP");
        }

        UserDetails user =
            userDetailsService.loadUserByUsername(email);

        return new OtpAuthenticationToken(
            email,
            user.getAuthorities()
        );
    }

    @Override
    public boolean supports(Class<?> authentication) {
        return OtpAuthenticationToken.class.isAssignableFrom(authentication);
    }
}
```

---

## 13. Registering Custom Provider

```java
@Bean
public AuthenticationManager authenticationManager(
        HttpSecurity http,
        OtpAuthenticationProvider otpProvider) throws Exception {

    return http.getSharedObject(AuthenticationManagerBuilder.class)
        .authenticationProvider(otpProvider)
        .build();
}
```

---

## 14. Custom Authentication Filter (OTP)

```java
public class OtpAuthenticationFilter
        extends AbstractAuthenticationProcessingFilter {

    public OtpAuthenticationFilter() {
        super("/login/otp");
    }

    @Override
    public Authentication attemptAuthentication(
            HttpServletRequest request,
            HttpServletResponse response) {

        String email = request.getParameter("email");
        String otp = request.getParameter("otp");

        OtpAuthenticationToken token =
            new OtpAuthenticationToken(email, otp);

        return getAuthenticationManager().authenticate(token);
    }
}
```

---

## 15. Where Everything Connects (IMPORTANT)

|Component|Responsibility|
|---|---|
|Filter|Extracts credentials|
|Token|Carries auth data|
|Manager|Delegates|
|Provider|Validates|
|UserDetailsService|Loads user|
|PasswordEncoder|Verifies secret|
|SecurityContext|Stores result|

---

## 16. Common Mistakes (Production)

❌ Authenticating in filters  
❌ Putting business logic in controllers  
❌ Skipping SecurityContext  
❌ Not implementing `supports()`  
❌ Mixing auth & authorization

---

## 17. Interview Questions (🔥)

**Q: Who does real authentication?**  
A: `AuthenticationProvider`

**Q: Why not authenticate in filter?**  
A: Filters should be stateless and delegate logic.

**Q: When do you write custom provider?**  
A: When authentication logic doesn’t fit username/password.

---

## 18. Things to Memorize (HIGH VALUE)

- Spring Security is filter-based
    
- Filters extract credentials
    
- Providers authenticate
    
- Managers delegate
    
- SecurityContext stores identity
    
- Custom providers solve business needs
    

---

## 19. One-Line Senior-Level Explanation (Gold)

> Spring Security authentication is a filter-driven process where filters extract credentials, providers perform validation, and successful authentication is stored in the security context.
