# Spring Stereotype Annotations

## Big Picture (1-Line Rule)

> **All these annotations create Spring beans, but each represents a different application layer.**

They are **specializations of `@Component`**.

---

## 1. `@Component` – Generic Bean

### Purpose

> **Marks a class as a Spring-managed bean when it does not clearly belong to any specific layer.**

---

### Real-World Use Case

- Utility classes
    
- Helpers
    
- Validators
    
- Mappers
    

---

### Example

```java
@Component
public class DateUtils {

    public LocalDate getToday() {
        return LocalDate.now();
    }
}
```

---

### When to Use

✔ No business logic  
✔ No DB access  
✔ No web responsibility

---

### Memorization Tip

> **@Component = “I am a bean, but not a service/controller/repo”**

---

## 2. `@Service` – Business Layer

### Purpose

> **Marks a class that contains business logic and rules.**

---

### Real-World Use Case

- Order processing
    
- Payment handling
    
- Validation logic
    
- Workflow orchestration
    

---

### Example

```java
@Service
public class OrderService {

    public void placeOrder() {
        // business rules
        // validation
        // calculations
    }
}
```

---

### Why Use `@Service` Instead of `@Component`?

- Improves readability
    
- Supports business-related AOP (transactions, logging)
    

---

### Memorization Tip

> **@Service = “Business brain of the application”**

---

## 3. `@Repository` – Data Access Layer

### Purpose

> **Marks a class that interacts with the database and enables exception translation.**

---

### Real-World Use Case

- CRUD operations
    
- SQL / JPA / Hibernate access
    
- DAO classes
    

---

### Example

```java
@Repository
public class OrderRepository {

    public void saveOrder() {
        // database interaction
    }
}
```

---

### Special Feature (VERY IMPORTANT)

Spring converts database exceptions into:

```text
DataAccessException
```

✔ Makes DB errors consistent  
✔ Database-independent

---

### Memorization Tip

> **@Repository = “Database gateway + exception handling”**

---

## 4. `@Controller` – MVC Controller (Web Layer)

### Purpose

> **Handles web requests and returns views (HTML, JSP, Thymeleaf).**

---

### Real-World Use Case

- Web applications
    
- Server-side rendered pages
    

---

### Example

```java
@Controller
public class LoginController {

    @GetMapping("/login")
    public String loginPage() {
        return "login"; // view name
    }
}
```

---

### Key Point

- Returns **view names**
    
- Uses `Model` to pass data
    

---

### Memorization Tip

> **@Controller = “Web page controller”**

---

## 5. `@RestController` – REST APIs

### Purpose

> **Handles HTTP requests and returns data (JSON/XML) directly.**

---

### What It Is

```java
@RestController = @Controller + @ResponseBody
```

---

### Real-World Use Case

- REST APIs
    
- Microservices
    
- Mobile / frontend backends
    

---

### Example

```java
@RestController
@RequestMapping("/api/orders")
public class OrderRestController {

    @GetMapping("/{id}")
    public Order getOrder(@PathVariable Long id) {
        return new Order(id, "CREATED");
    }
}
```

---

### Key Point

- Returns **JSON**
    
- No views
    
- Used in modern applications
    

---

### Memorization Tip

> **@RestController = “API controller”**

---

# One-Look Memorization Table

|Annotation|Layer|Returns|Example Use|
|---|---|---|---|
|`@Component`|Generic|Object|Utility|
|`@Service`|Business|Object|Business logic|
|`@Repository`|DAO|Object|DB access|
|`@Controller`|Web|View|HTML pages|
|`@RestController`|API|JSON|REST APIs|

---

# Interview Gold Summary (Say This)

> “All these annotations are stereotypes of `@Component`.  
> `@Service` represents business logic, `@Repository` handles database access and exception translation, `@Controller` serves web pages, and `@RestController` exposes REST APIs.”
