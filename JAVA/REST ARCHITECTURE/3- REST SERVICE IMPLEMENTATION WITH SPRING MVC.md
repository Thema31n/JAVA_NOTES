# 1️⃣ Configure Required Dependencies (Spring MVC)

### Goal

Enable:

- Spring MVC
    
- REST controllers
    
- JSON serialization/deserialization
    

---

### Required Maven dependencies

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webmvc</artifactId>
  <version>5.3.x</version>
</dependency>

<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
  <version>2.15.x</version>
</dependency>
```

---

### Why Jackson is required

Spring MVC **does not include JSON support by default**.  
Jackson is needed to:

- Convert Java objects → JSON
    
- Convert JSON → Java objects
    

---

### Interview tip

Say:

> “In pure Spring MVC, Jackson must be added explicitly for JSON serialization.”

---

### Things to memorize

- No auto-configuration
    
- Jackson is mandatory
    
- Embedded server is NOT provided
    

---

# 2️⃣ Creating the REST Web Service (Spring MVC)

### Java-based configuration

```java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.example.api")
public class WebConfig {
}
```

---

### web.xml configuration

```xml
<servlet>
  <servlet-name>dispatcher</servlet-name>
  <servlet-class>
    org.springframework.web.servlet.DispatcherServlet
  </servlet-class>
  <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
  <servlet-name>dispatcher</servlet-name>
  <url-pattern>/</url-pattern>
</servlet-mapping>
```

---

### DispatcherServlet role

- Front controller
    
- Routes HTTP requests to controllers
    
- Core of Spring MVC
    

---

### Interview tip

Say:

> “DispatcherServlet is the front controller that handles all incoming HTTP requests.”

---

### Things to memorize

- Spring MVC uses Front Controller pattern
    
- DispatcherServlet is mandatory
    

---

# 3️⃣ @RestController vs @Controller (Spring MVC)

### @Controller

```java
@Controller
public class UserController {

    @GetMapping("/home")
    public String home() {
        return "home";
    }
}
```

- Used for **MVC views**
    
- Returns view names
    

---

### @RestController

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping
    public List<User> users() {
        return userService.findAll();
    }
}
```

---

### Key difference

```java
@RestController = @Controller + @ResponseBody
```

---

### Interview tip (VERY common)

Say:

> “@RestController eliminates the need to add @ResponseBody on every method.”

---

### Things to memorize

- REST → `@RestController`
    
- Views → `@Controller`
    

---

# 4️⃣ @ResponseBody

### Purpose

Tells Spring:

> “Serialize return value and write it directly to HTTP response body.”

---

### Example

```java
@Controller
@RequestMapping("/users")
public class UserController {

    @GetMapping
    @ResponseBody
    public List<User> users() {
        return userService.findAll();
    }
}
```

---

### What happens internally

- Spring uses `HttpMessageConverter`
    
- Jackson converts object → JSON
    

---

### Interview tip

Say:

> “@ResponseBody bypasses view resolution.”

---

### Things to memorize

- Without it → Spring looks for a view
    
- Uses message converters
    

---

# 5️⃣ @RequestBody

### Purpose

Maps JSON request body → Java object.

---

### Example

```http
POST /users
Content-Type: application/json

{
  "name": "Ahmed",
  "email": "ahmed@mail.com"
}
```

```java
@PostMapping
public User create(@RequestBody User user) {
    return userService.save(user);
}
```

---

### Requirements

- Jackson on classpath
    
- Valid JSON
    
- Correct Content-Type header
    

---

### Interview tip

Say:

> “@RequestBody reads HTTP request body and converts it to a Java object.”

---

### Things to memorize

- Used with POST / PUT / PATCH
    
- JSON → Java mapping
    

---

# 6️⃣ @PathVariable

### Purpose

Extract values from URI path.

---

### Example

```http
GET /users/42
```

```java
@GetMapping("/{id}")
public User get(@PathVariable("id") Long userId) {
    return userService.findById(userId);
}
```

---

### Multiple path variables

```java
@GetMapping("/users/{userId}/orders/{orderId}")
public Order getOrder(
    @PathVariable Long userId,
    @PathVariable Long orderId
) {}
```

---

### Interview tip

Say:

> “@PathVariable identifies a specific resource.”

---

### Things to memorize

- Used for identity
    
- Mandatory by default
    

---

# 7️⃣ @RequestParam

### Purpose

Read query parameters.

---

### Example

```http
GET /users?page=1&size=10
```

```java
@GetMapping
public List<User> list(
    @RequestParam int page,
    @RequestParam int size
) {}
```

---

### Optional parameter

```java
@RequestParam(required = false) String status
```

---

### Default value

```java
@RequestParam(defaultValue = "0") int page
```

---

### Interview tip

Say:

> “@RequestParam is used for filtering, sorting, and pagination.”

---

### Things to memorize

- Query params are optional by nature
    
- Not resource identifiers
    

---

# 8️⃣ @ResponseStatus

### Purpose

Set HTTP status code statically.

---

### Example

```java
@PostMapping
@ResponseStatus(HttpStatus.CREATED)
public User create(@RequestBody User user) {
    return userService.save(user);
}
```

---

### Limitation

- Status code is fixed
    
- Cannot change dynamically
    

---

### Interview tip

Say:

> “Use @ResponseStatus for simple, fixed responses.”

---

### Things to memorize

- Static behavior
    
- Limited flexibility
    

---

# 9️⃣ ResponseEntity

### Purpose

Full HTTP control.

---

### Example

```java
@GetMapping("/{id}")
public ResponseEntity<User> get(@PathVariable Long id) {

    User user = userService.findById(id);

    if (user == null) {
        return ResponseEntity.notFound().build();
    }

    return ResponseEntity.ok(user);
}
```

---

### Add headers

```java
return ResponseEntity
    .status(HttpStatus.CREATED)
    .header("Location", "/users/42")
    .body(user);
```

---

### Interview tip (IMPORTANT)

Say:

> “ResponseEntity is preferred in real-world APIs for flexibility.”

---

### Things to memorize

- Dynamic status codes
    
- Headers + body control
    

---

# 🔟 Reading HTTP Headers in REST Controller

### Why read headers?

- Authentication
    
- Tracing
    
- Localization
    

---

### Example

```java
@GetMapping("/profile")
public User profile(
    @RequestHeader("Authorization") String authHeader
) {
    return authService.getUser(authHeader);
}
```

---

### Optional header

```java
@RequestHeader(value = "X-Request-Id", required = false) String requestId
```

---

### Read all headers

```java
@GetMapping
public void headers(@RequestHeader Map<String, String> headers) {
    headers.forEach((k, v) -> System.out.println(k + ":" + v));
}
```

---

### Interview tip

Say:

> “Headers carry metadata, not business data.”

---

# 1️⃣1️⃣ @RequestHeader (Summary)

### What it does

Maps HTTP headers → method parameters.

---

### Common headers

- Authorization
    
- Accept
    
- Content-Type
    
- X-Correlation-Id
    

---

### Interview tip

Say:

> “Headers are commonly used for security and observability.”

---

### Things to memorize

- Headers ≠ request body
    
- Metadata only
    

---

## ✅ Final Interview Memorization Summary

- Spring MVC ≠ Spring Boot
    
- DispatcherServlet is mandatory
    
- Jackson must be added manually
    
- `@RestController = @Controller + @ResponseBody`
    
- `@RequestBody` → JSON payload
    
- `@PathVariable` → identity
    
- `@RequestParam` → filtering
    
- `ResponseEntity` is preferred
    
- Headers are metadata
    