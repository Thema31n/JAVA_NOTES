# 1. Why Exception Handling in Spring MVC?

## Real-World Problem

- User requests a resource that doesn’t exist
    
- Invalid input causes failure
    
- Business rule violation (e.g., insufficient balance)
    

Without proper handling:  
❌ Stack traces shown  
❌ Bad user experience  
❌ No consistent error responses

---

## Goal of Exception Handling

✔ Graceful error messages  
✔ Centralized handling  
✔ Clean controller code  
✔ Proper HTTP status codes

---

# 2. Exception Handling Approaches in Spring MVC

## There are **4 main approaches**

1. **Controller-based (`@ExceptionHandler`)**
    
2. **Global (`@ControllerAdvice`)**
    
3. **HandlerExceptionResolver**
    
4. **Custom exceptions + `@ResponseStatus`**
    

---

# 3. Controller-Based Exception Handling

## What It Is

> **Handle exceptions inside the same controller where they occur.**

---

## Real-World Scenario

👉 Fetching order by ID that doesn’t exist.

---

## Step 1: Custom Exception

```java
public class OrderNotFoundException extends RuntimeException {
    public OrderNotFoundException(String msg) {
        super(msg);
    }
}
```

---

## Step 2: Controller

```java
@Controller
public class OrderController {

    @GetMapping("/orders/{id}")
    public String getOrder(@PathVariable Long id) {
        if (id == 100) {
            throw new OrderNotFoundException("Order not found");
        }
        return "order";
    }

    @ExceptionHandler(OrderNotFoundException.class)
    public String handleOrderNotFound(
            OrderNotFoundException ex, Model model) {

        model.addAttribute("error", ex.getMessage());
        return "error";
    }
}
```

---

## How It Works

1. Exception thrown
    
2. Spring looks for `@ExceptionHandler` in controller
    
3. Method handles exception
    
4. Returns error view
    

---

## Pros & Cons

### ✅ Pros

- Simple
    
- Close to source
    

### ❌ Cons

- Duplicate code
    
- Not reusable
    

---

## Interview Line

> **Controller-based exception handling is limited to a single controller.**

---

# 4. Global Exception Handling with `@ControllerAdvice`

## What is `@ControllerAdvice`?

> **A centralized place to handle exceptions across all controllers.**

---

## Real-World Scenario

👉 Same error handling for all controllers.

---

## Step 1: Global Exception Handler

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(OrderNotFoundException.class)
    public String handleOrderNotFound(
            OrderNotFoundException ex, Model model) {

        model.addAttribute("error", ex.getMessage());
        return "error";
    }
}
```

---

## How It Works

1. Exception thrown in any controller
    
2. Spring scans `@ControllerAdvice`
    
3. Matching `@ExceptionHandler` invoked
    
4. Response returned
    

---

## Advantages

✔ Centralized handling  
✔ Reusable  
✔ Clean controllers

---

## Interview Line

> **@ControllerAdvice enables global exception handling across the application.**

---

# 5. `HandlerExceptionResolver`

## What is It?

> **Low-level SPI that allows resolving exceptions programmatically.**

---

## When to Use

- Legacy systems
    
- Framework-level control
    
- Custom response formats
    

---

## Example

```java
public class CustomExceptionResolver
        implements HandlerExceptionResolver {

    @Override
    public ModelAndView resolveException(
            HttpServletRequest request,
            HttpServletResponse response,
            Object handler,
            Exception ex) {

        ModelAndView mv = new ModelAndView("error");
        mv.addObject("message", ex.getMessage());
        return mv;
    }
}
```

---

## Register Resolver

```java
@Bean
public HandlerExceptionResolver resolver() {
    return new CustomExceptionResolver();
}
```

---

## Interview Note

> **HandlerExceptionResolver is powerful but rarely used in modern Spring MVC.**

---

# 6. Defining Custom Exceptions in Spring MVC

## Why Custom Exceptions?

✔ Clear business meaning  
✔ Better readability  
✔ Cleaner code

---

## Example

```java
public class InsufficientBalanceException
        extends RuntimeException {

    public InsufficientBalanceException(String msg) {
        super(msg);
    }
}
```

---

## Usage in Service Layer

```java
if (balance < amount) {
    throw new InsufficientBalanceException("Low balance");
}
```

---

# 7. `@ResponseStatus`

## Purpose

> **Maps an exception directly to an HTTP status code.**

---

## Example

```java
@ResponseStatus(HttpStatus.NOT_FOUND)
public class OrderNotFoundException
        extends RuntimeException {

    public OrderNotFoundException(String msg) {
        super(msg);
    }
}
```

---

## What Happens

- Exception thrown
    
- Spring returns HTTP 404
    
- No handler required
    

---

## Interview Line

> **@ResponseStatus binds an exception to an HTTP status code.**

---

# 8. Handling 404 (Resource Not Found)

---

## 8.1 Controller-Level 404 Handling

```java
@GetMapping("/orders/{id}")
public String getOrder(@PathVariable Long id) {
    if (id > 1000) {
        throw new OrderNotFoundException("Order not found");
    }
    return "order";
}
```

---

## 8.2 Global 404 Handling

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(NoHandlerFoundException.class)
    public String handle404(Model model) {
        model.addAttribute("error", "Page not found");
        return "404";
    }
}
```

---

## Enable 404 Exceptions (IMPORTANT)

```properties
spring.mvc.throw-exception-if-no-handler-found=true
spring.web.resources.add-mappings=false
```

---

## Interview Tip

> Without enabling this property, Spring handles 404 internally.

---

# 9. Exception Handling in REST APIs (JSON Response)

## Example

```java
@RestControllerAdvice
public class ApiExceptionHandler {

    @ExceptionHandler(OrderNotFoundException.class)
    public ResponseEntity<String> handleNotFound(
            OrderNotFoundException ex) {

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());
    }
}
```

---

# 10. Complete Exception Flow (MEMORIZE)

1. Exception thrown
    
2. Controller handler checked
    
3. `@ControllerAdvice` checked
    
4. `HandlerExceptionResolver` invoked
    
5. Default error handling
    

---

# 11. Advantages of Spring MVC Exception Handling

### ✅ Centralized

### ✅ Clean controllers

### ✅ Consistent responses

### ✅ HTTP-aware

---

# 12. Common Interview Mistakes

❌ Using try-catch in controllers  
❌ No global handling  
❌ Wrong HTTP status codes  
❌ Mixing UI & REST handling

---

# 13. FINAL MEMORIZATION CHEAT-SHEET

- **Controller-based** → `@ExceptionHandler`
    
- **Global** → `@ControllerAdvice`
    
- **Low-level** → `HandlerExceptionResolver`
    
- **Custom exceptions** → business clarity
    
- **@ResponseStatus** → HTTP mapping
    
- **404 handling** → `NoHandlerFoundException`
    
- **REST errors** → `@RestControllerAdvice`
    
