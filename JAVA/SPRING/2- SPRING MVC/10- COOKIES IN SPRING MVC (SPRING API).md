# PART 1: COOKIES IN SPRING MVC

---

# 1. What is a Cookie?

## Definition

> **A cookie is a small key–value pair stored in the user’s browser and sent with every HTTP request to the same domain.**

---

## Real-World Use Cases

- Remember logged-in user
    
- Store language preference
    
- Track user behavior
    
- Session identifiers
    

---

## Key Characteristics

- Stored on client side
    
- Sent automatically with requests
    
- Size limit (~4 KB)
    
- Can have expiry
    

---

# 2. How to Set Cookies in Spring MVC

---

## 2.1 Using `HttpServletResponse` (LOW-LEVEL, MOST COMMON)

### Real-World Scenario

👉 Remember user’s selected language.

---

### Controller Example

```java
@GetMapping("/set-language")
public String setLanguage(HttpServletResponse response) {

    Cookie cookie = new Cookie("lang", "en");
    cookie.setMaxAge(60 * 60 * 24); // 1 day
    cookie.setPath("/");

    response.addCookie(cookie);

    return "home";
}
```

---

## How It Works

1. Cookie created on server
    
2. Added to response header
    
3. Browser stores cookie
    
4. Cookie sent in future requests
    

---

## Interview Line

> **Cookies are added to the response and stored on the client.**

---

# 3. How to Read Cookies in Spring MVC

---

## 3.1 Using `@CookieValue` (RECOMMENDED)

### Real-World Scenario

👉 Read user’s preferred language.

---

### Example

```java
@GetMapping("/home")
public String home(
    @CookieValue(name = "lang", defaultValue = "en") String lang) {

    System.out.println("Language: " + lang);
    return "home";
}
```

---

## How It Works

- Spring reads cookies from request
    
- Injects value into parameter
    

---

## Optional Cookie

```java
@CookieValue(name = "lang", required = false) String lang
```

---

## Interview Line

> **@CookieValue binds cookie values to controller method parameters.**

---

# 4. Reading Cookies Using `HttpServletRequest` (LOW-LEVEL)

```java
@GetMapping("/cookies")
public String cookies(HttpServletRequest request) {

    if (request.getCookies() != null) {
        for (Cookie c : request.getCookies()) {
            System.out.println(c.getName() + " = " + c.getValue());
        }
    }
    return "home";
}
```

---

## When to Use

- Need all cookies
    
- Need metadata (path, age)
    

---

# 5. Cookie Attributes (IMPORTANT)

```java
cookie.setMaxAge(3600);
cookie.setPath("/");
cookie.setSecure(true);
cookie.setHttpOnly(true);
```

---

## Security Best Practice

✔ `HttpOnly` → blocks JS access  
✔ `Secure` → HTTPS only

---

# PART 2: WORKING WITH PROPERTIES IN SPRING

---

# 6. What Are Properties in Spring?

## Definition

> **Properties externalize configuration values from the code.**

---

## Real-World Examples

- DB URLs
    
- API keys
    
- Feature flags
    
- Environment settings
    

---

# 7. Properties File Example

### `application.properties`

```properties
app.name=Order Management System
app.version=1.0
server.port=8080
```

---

# 8. `@PropertySource` Annotation

## Purpose

> **Loads external properties files into Spring Environment.**

---

## Example

```java
@Configuration
@PropertySource("classpath:app.properties")
public class AppConfig {
}
```

---

## Property File

```properties
app.admin.email=admin@example.com
```

---

## Interview Line

> **@PropertySource makes properties available to the Spring context.**

---

# 9. `@Value` Annotation (INJECTING PROPERTIES)

---

## Inject Simple Property

```java
@Value("${app.name}")
private String appName;
```

---

## Inject with Default Value

```java
@Value("${app.timeout:30}")
private int timeout;
```

---

## Inject Literal Value

```java
@Value("ADMIN")
private String role;
```

---

# 10. Real-World Example Using `@Value`

```java
@Service
public class EmailService {

    @Value("${app.admin.email}")
    private String adminEmail;

    public void sendAlert() {
        System.out.println("Sending email to " + adminEmail);
    }
}
```

---

# 11. How `@Value` Works Internally

1. Spring loads properties into Environment
    
2. `@Value` expressions resolved
    
3. Values injected during bean creation
    

---

# 12. Multiple Properties Files

```java
@PropertySource({
    "classpath:app.properties",
    "classpath:db.properties"
})
```

---

# 13. Common Mistakes (INTERVIEW FAVORITES)

❌ Hard-coding values  
❌ Forgetting `@PropertySource`  
❌ Using `@Value` for complex objects  
❌ Storing secrets in code

---

# 14. Cookies vs Properties (REAL-WORLD COMPARISON)

|Feature|Cookies|Properties|
|---|---|---|
|Stored|Client|Server|
|Purpose|User data|App config|
|Scope|Per user|Application|
|Security|Low|High|

---

# 15. FINAL MEMORIZATION CHEAT-SHEET

- **Cookies** → client-side storage
    
- **Set cookie** → `HttpServletResponse`
    
- **Read cookie** → `@CookieValue`
    
- **Properties** → external config
    
- **@PropertySource** → load props
    
- **@Value** → inject values
    
- **Default value** → `${key:default}`
    
- **Security** → HttpOnly + Secure cookies
    

---

## ONE-LINE INTERVIEW ANSWER 🧠

> **Cookies store user-specific data on the client, while properties store application configuration on the server and are injected using @Value.**
