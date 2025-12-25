## 1. What Is Spring Expression Language (SpEL) in Security?

**SpEL** is a powerful expression language used by Spring Security to:

- Make **dynamic authorization decisions**
    
- Access **authentication details**
    
- Compare **method arguments**
    
- Check **roles, privileges, ownership**
    
- Distinguish **authentication types** (full login vs remember-me)
    

It is mainly used in:

- `@PreAuthorize`
    
- `@PostAuthorize`
    
- `@PreFilter`
    
- `@PostFilter`
    

---

## 2. Core SpEL Security Objects (VERY IMPORTANT)

### `authentication`

Represents the **current `Authentication` object**

What you can access:

- `authentication.name` → username/email
    
- `authentication.principal` → logged-in user object
    
- `authentication.authorities` → roles & privileges
    
- `authentication.details` → request-specific info
    

---

### `principal`

Represents the **logged-in user**

Usually:

- Username (String)
    
- Or a **custom `UserDetails` object**
    

```java
principal.username
principal.id
principal.email
```

👉 Used heavily for **ownership checks**

---

### `#parameterName`

Represents **method parameters**

```java
#userId
#orderId
#email
```

Used to compare method input with logged-in user.

---

## 3. Common SpEL Security Expressions (CHEAT SHEET)

---

### `hasRole('ADMIN')`

**What it does**

- Checks for authority `ROLE_ADMIN`
    

**Used for**

- High-level access (admin screens, dashboards)
    

```java
@PreAuthorize("hasRole('ADMIN')")
```

---

### `hasAuthority('READ_USER')`

**What it does**

- Checks for an exact authority string
    

**Used for**

- Fine-grained permissions (API actions)
    

```java
@PreAuthorize("hasAuthority('READ_USER')")
```

---

### `hasAnyRole('ADMIN','MANAGER')`

```java
@PreAuthorize("hasAnyRole('ADMIN','MANAGER')")
```

Used when multiple roles are allowed.

---

### `hasAnyAuthority('READ_USER','UPDATE_USER')`

```java
@PreAuthorize("hasAnyAuthority('READ_USER','UPDATE_USER')")
```

Used when multiple permissions grant access.

---

### `isAuthenticated()`

**What it does**

- Returns `true` if the user is authenticated
    
- Includes **remember-me authentication**
    

```java
@PreAuthorize("isAuthenticated()")
```

✅ Session login  
✅ Remember-me login

---

### `isAnonymous()`

**What it does**

- Returns `true` if the user is **not logged in**
    

```java
@PreAuthorize("isAnonymous()")
```

Used for:

- Login
    
- Register
    
- Forgot-password endpoints
    

---

### `isRememberMe()` ✅ (IMPORTANT)

**What it does**

- Returns `true` if authentication was done via **remember-me**
    
- User is authenticated, but **not fully trusted**
    

```java
@PreAuthorize("isRememberMe()")
```

#### Real-World Use Case

```java
@PreAuthorize("isRememberMe()")
public void promptReAuthentication() { }
```

👉 Used to **force re-login** before sensitive actions.

---

### `isFullyAuthenticated()` ✅ (VERY IMPORTANT)

**What it does**

- Returns `true` only if:
    
    - User logged in with **username/password**
        
    - ❌ Not remember-me
        
    - ❌ Not anonymous
        

```java
@PreAuthorize("isFullyAuthenticated()")
```

#### Real-World Use Case (VERY COMMON)

```java
@PreAuthorize("isFullyAuthenticated()")
public void changePassword() { }
```

🔥 Interview favorite question.

---

### `permitAll()`

Allows everyone (mostly URL config, rarely method-level).

```java
@PreAuthorize("permitAll()")
```

---

### `denyAll()`

Blocks everyone.

```java
@PreAuthorize("denyAll()")
```

---

## 4. Ownership & Identity Checks (REAL-WORLD)

---

### Compare Method Parameter with Principal

```java
@PreAuthorize("#userId == principal.id")
public User getUser(Long userId) { }
```

✔ User can access **only their own data**

---

### Allow Owner OR Admin (CLASSIC INTERVIEW EXAMPLE)

```java
@PreAuthorize("#userId == principal.id or hasRole('ADMIN')")
public User getUser(Long userId) { }
```

🔥 Very common real-world pattern.

---

### Using `authentication.name` (Email Login)

```java
@PreAuthorize("#email == authentication.name")
public User getProfile(String email) { }
```

Used when users log in by email.

---

## 5. `@PostAuthorize` with SpEL

Runs **after method execution**  
Can inspect return value.

```java
@PostAuthorize("returnObject.ownerEmail == authentication.name")
public Document getDocument(Long id) { }
```

✔ Useful when ownership is known **after fetching from DB**

---

## 6. Filtering Collections (Advanced)

### `@PostFilter`

```java
@PostFilter("filterObject.owner == authentication.name")
public List<Document> getDocuments() { }
```

Only allowed items are returned.

---

## 7. When to Use What (Quick Guide)

|Scenario|Expression|
|---|---|
|Admin access|`hasRole`|
|API permission|`hasAuthority`|
|Ownership|`principal` / `authentication`|
|Logged-in check|`isAuthenticated`|
|Remember-me check|`isRememberMe`|
|Sensitive action|`isFullyAuthenticated`|
|Public access|`permitAll`|

---

## 8. Common Mistakes with SpEL

❌ Using `hasRole('ROLE_ADMIN')`  
❌ Forgetting `@EnableMethodSecurity`  
❌ Using SpEL in controllers instead of services  
❌ Treating remember-me as fully authenticated  
❌ Using roles instead of privileges everywhere

---

## 9. Things to Memorize (SpEL – HIGH VALUE)

- Spring Security checks **authorities only**
    
- Roles are prefixed with `ROLE_`
    
- `principal` = logged-in user
    
- `authentication.name` = username/email
    
- `isAuthenticated()` includes remember-me
    
- `isFullyAuthenticated()` excludes remember-me
    
- SpEL works only when method security is enabled
    
- `@PreAuthorize` is the most powerful annotation
    

---

## 🔑 One-Line Interview Answer (Gold)

> Spring Security SpEL allows fine-grained authorization by evaluating roles, privileges, user identity, authentication type, and method parameters at runtime.
