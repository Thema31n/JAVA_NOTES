# 1. Model in Spring MVC

## What is `Model`?

### Definition

> **`Model` is used to pass data from Controller to View.**

It is basically a **map of key–value pairs**.

---

## Real-World Scenario

👉 Display order details on a web page.

---

## Example

```java
@Controller
public class OrderController {

    @GetMapping("/order")
    public String orderPage(Model model) {
        model.addAttribute("orderId", 101);
        model.addAttribute("status", "CREATED");
        return "order";
    }
}
```

---

## View (JSP / Thymeleaf)

```jsp
Order ID: ${orderId}
Status: ${status}
```

---

## How It Works

1. Controller adds attributes to Model
    
2. DispatcherServlet passes Model to View
    
3. View renders data
    

---

## Interview Line

> **Model is used to transfer data from controller to view.**

---

# 2. Injecting `HttpServletRequest`

## Purpose

> **Gives direct access to the raw HTTP request.**

---

## Real-World Use Cases

- Read headers
    
- Read client IP
    
- Access request attributes
    

---

## Example

```java
@GetMapping("/info")
public String requestInfo(HttpServletRequest request) {
    String browser = request.getHeader("User-Agent");
    System.out.println(browser);
    return "info";
}
```

---

## How It Works

Spring automatically injects the request object from the servlet container.

---

## Interview Tip

> Use `HttpServletRequest` only when low-level access is required.

---

# 3. Injecting `HttpSession`

## Purpose

> **Stores user data across multiple requests.**

---

## Real-World Scenario

👉 Store logged-in user info.

---

## Example

```java
@GetMapping("/login")
public String login(HttpSession session) {
    session.setAttribute("user", "Ahmed");
    return "home";
}
```

---

## Retrieve Session Data

```java
String user = (String) session.getAttribute("user");
```

---

## How It Works

- Session created by container
    
- Spring injects it into controller
    

---

## Interview Line

> **HttpSession is used to maintain user state across requests.**

---

# 4. `@RequestParam`

## Purpose

> **Extracts query parameters or form parameters from the request.**

---

## URL Example

```text
/search?keyword=spring&page=1
```

---

## Controller Example

```java
@GetMapping("/search")
public String search(
    @RequestParam String keyword,
    @RequestParam int page) {

    return "results";
}
```

---

## Optional Parameter

```java
@RequestParam(required = false) String filter
```

---

## Default Value

```java
@RequestParam(defaultValue = "1") int page
```

---

## Interview Line

> **@RequestParam binds request parameters to method arguments.**

---

# 5. Map All Request Parameters into a Map

## Use Case

👉 Dynamic forms where parameter names are unknown.

---

## Example

```java
@PostMapping("/submit")
public String submit(@RequestParam Map<String, String> params) {

    params.forEach((k, v) ->
        System.out.println(k + " = " + v)
    );
    return "success";
}
```

---

## How It Works

- Spring reads all request parameters
    
- Stores them as key–value pairs
    

---

## Limitation

❌ Only single value per parameter

---

# 6. Map Multi-Value Parameters

## Real-World Scenario

👉 User selects multiple interests.

---

## Request

```text
/interests?interest=java&interest=spring&interest=docker
```

---

## Option 1: `List`

```java
@GetMapping("/interests")
public String interests(
    @RequestParam List<String> interest) {

    interest.forEach(System.out::println);
    return "done";
}
```

---

## Option 2: `Map<String, String[]>`

```java
@GetMapping("/interests")
public String interests(
    @RequestParam Map<String, String[]> params) {

    params.get("interest");
    return "done";
}
```

---

## Interview Tip

> Use `List` for clean code, `Map<String, String[]>` for flexibility.

---

# 7. `@PathVariable`

## Purpose

> **Extracts values directly from the URL path.**

---

## Real-World Scenario

👉 Fetch order by ID.

---

## URL

```text
/orders/101
```

---

## Controller

```java
@GetMapping("/orders/{id}")
public String getOrder(@PathVariable Long id) {
    System.out.println(id);
    return "order";
}
```

---

## Multiple Path Variables

```java
@GetMapping("/users/{userId}/orders/{orderId}")
public String getOrder(
    @PathVariable Long userId,
    @PathVariable Long orderId) {
    return "details";
}
```

---

## Interview Line

> **@PathVariable is used for REST-style URLs.**

---

# 8. HTTP Method Mapping Annotations

## Why They Exist

> Replace `@RequestMapping(method=...)` with cleaner annotations.

---

# 8.1 `@GetMapping`

### Use Case

👉 Fetch data

```java
@GetMapping("/orders")
public List<Order> getOrders() {
    return service.getOrders();
}
```

---

# 8.2 `@PostMapping`

### Use Case

👉 Create data

```java
@PostMapping("/orders")
public String createOrder(@RequestParam String name) {
    return "created";
}
```

---

# 8.3 `@PutMapping`

### Use Case

👉 Update entire resource

```java
@PutMapping("/orders/{id}")
public String updateOrder(
    @PathVariable Long id,
    @RequestParam String status) {
    return "updated";
}
```

---

# 8.4 `@DeleteMapping`

### Use Case

👉 Delete resource

```java
@DeleteMapping("/orders/{id}")
public String deleteOrder(@PathVariable Long id) {
    return "deleted";
}
```

---

# 8.5 `@PatchMapping`

### Use Case

👉 Partial update

```java
@PatchMapping("/orders/{id}")
public String patchOrder(
    @PathVariable Long id,
    @RequestParam String status) {
    return "patched";
}
```

---

## Interview Memorization Rule

|HTTP|Annotation|Purpose|
|---|---|---|
|GET|`@GetMapping`|Read|
|POST|`@PostMapping`|Create|
|PUT|`@PutMapping`|Replace|
|DELETE|`@DeleteMapping`|Delete|
|PATCH|`@PatchMapping`|Partial update|

---

# 9. FINAL REAL-WORLD REVISION CHEAT SHEET

- **Model** → Controller → View data
    
- **HttpServletRequest** → low-level request access
    
- **HttpSession** → user session data
    
- **@RequestParam** → query/form parameters
    
- **Map params** → dynamic forms
    
- **Multi-value params** → List / array
    
- **@PathVariable** → URL data
    
- **@GetMapping** → fetch
    
- **@PostMapping** → create
    
- **@PutMapping** → update
    
- **@DeleteMapping** → delete
    
- **@PatchMapping** → partial update
    
