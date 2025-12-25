# 1. Web Filters — Overview (Recap)

A **Web Filter** is a Java component that:

- Intercepts requests **before** they reach a servlet/JSP
    
- Intercepts responses **after** processing
    

```
Client → Filter(s) → Servlet/JSP → Filter(s) → Client
```

Filters are used for **cross-cutting concerns** like:

- Authentication
    
- Logging
    
- Encoding
    
- Validation
    
- CORS
    
- Performance monitoring
    

---

# 2. Filter Lifecycle (Recap)

Managed by the container.

1. `init(FilterConfig)` → once
    
2. `doFilter()` → every request
    
3. `destroy()` → once
    

```java
init() → doFilter() → destroy()
```

---

# 3. Filter API (Recap)

Important interfaces:

- `Filter`
    
- `FilterChain`
    
- `FilterConfig`
    

```java
chain.doFilter(request, response); // continue flow
```

⚠ If you **don’t call** `chain.doFilter()` → request stops.

---

# 4. Filter Configuration (Recap)

## 4.1 Annotation-based

```java
@WebFilter("/admin/*")
public class AuthFilter implements Filter {}
```

## 4.2 XML-based

```xml
<filter>
    <filter-name>AuthFilter</filter-name>
    <filter-class>com.app.AuthFilter</filter-class>
</filter>

<filter-mapping>
    <filter-name>AuthFilter</filter-name>
    <url-pattern>/admin/*</url-pattern>
</filter-mapping>
```

---

# 5. Filter Chain (Recap)

Multiple filters execute in order.

```
Browser
 ↓
EncodingFilter
 ↓
LoggingFilter
 ↓
AuthFilter
 ↓
Servlet
```

- `web.xml` → order is **declaration order**
    
- Annotations → container decides
    

---

# 6. Dispatcher Types — IN DEPTH (CORE TOPIC)

## What is a DispatcherType?

**DispatcherType defines _when_ a filter should execute.**

A request can reach a servlet in **different ways**, not just directly from the browser.

---

## Available Dispatcher Types

```java
DispatcherType.REQUEST
DispatcherType.FORWARD
DispatcherType.INCLUDE
DispatcherType.ERROR
DispatcherType.ASYNC
```

---

# 7. DispatcherType.REQUEST

### Meaning

Triggered when:

- A request comes **directly from the client (browser)**
    

---

### Example Flow

```
Browser → /login → LoginServlet
```

---

### Example Filter

```java
@WebFilter(
    urlPatterns = "/*",
    dispatcherTypes = DispatcherType.REQUEST
)
public class LoggingFilter implements Filter {

    public void doFilter(ServletRequest req, ServletResponse res,
                         FilterChain chain)
            throws IOException, ServletException {

        HttpServletRequest request = (HttpServletRequest) req;
        System.out.println("REQUEST: " + request.getRequestURI());

        chain.doFilter(req, res);
    }
}
```

---

### When to Use `REQUEST`

✅ Logging  
✅ Authentication  
✅ Validation  
✅ Encoding

📌 **Most common dispatcher type**

---

# 8. DispatcherType.FORWARD

### Meaning

Triggered when:

- Request is forwarded using `RequestDispatcher.forward()`
    

---

### Example Flow

```
Browser → /login
         → LoginServlet
         → forward("/dashboard")
         → DashboardServlet
```

---

### Example Filter

```java
@WebFilter(
    urlPatterns = "/dashboard",
    dispatcherTypes = DispatcherType.FORWARD
)
public class ForwardFilter implements Filter {

    public void doFilter(ServletRequest req, ServletResponse res,
                         FilterChain chain)
            throws IOException, ServletException {

        System.out.println("FORWARD request detected");
        chain.doFilter(req, res);
    }
}
```

---

### When to Use `FORWARD`

✅ MVC internal navigation  
✅ Role-based authorization after login  
✅ Internal security checks

📌 Use this when **forwarded requests must also be filtered**

---

# 9. DispatcherType.INCLUDE

### Meaning

Triggered when:

- Request is included using `RequestDispatcher.include()`
    

---

### Example Flow

```
PageServlet
 → include("/header.jsp")
 → include("/footer.jsp")
```

---

### Example Filter

```java
@WebFilter(
    urlPatterns = "*.jsp",
    dispatcherTypes = DispatcherType.INCLUDE
)
public class IncludeFilter implements Filter {

    public void doFilter(ServletRequest req, ServletResponse res,
                         FilterChain chain)
            throws IOException, ServletException {

        System.out.println("INCLUDE request detected");
        chain.doFilter(req, res);
    }
}
```

---

### When to Use `INCLUDE`

✅ Header/footer logging  
✅ UI analytics  
✅ Partial page rendering logic

📌 Rare but useful in **layout-based JSP apps**

---

# 10. DispatcherType.ERROR

### Meaning

Triggered when:

- An error occurs
    
- Error page is invoked via `sendError()` or `<error-page>`
    

---

### Example Flow

```
Servlet → response.sendError(404)
        → error.jsp
```

---

### Example Filter

```java
@WebFilter(
    urlPatterns = "/error.jsp",
    dispatcherTypes = DispatcherType.ERROR
)
public class ErrorFilter implements Filter {

    public void doFilter(ServletRequest req, ServletResponse res,
                         FilterChain chain)
            throws IOException, ServletException {

        System.out.println("ERROR page accessed");
        chain.doFilter(req, res);
    }
}
```

---

### When to Use `ERROR`

✅ Error logging  
✅ Security auditing  
✅ Custom error tracking

📌 Very useful for **centralized error monitoring**

---

# 11. DispatcherType.ASYNC

### Meaning

Triggered when:

- Request is processed **asynchronously**
    

---

### Example Use Case

- Long-running tasks
    
- File uploads
    
- Background processing
    

---

### Example Filter

```java
@WebFilter(
    urlPatterns = "/async",
    dispatcherTypes = DispatcherType.ASYNC
)
public class AsyncFilter implements Filter {

    public void doFilter(ServletRequest req, ServletResponse res,
                         FilterChain chain)
            throws IOException, ServletException {

        System.out.println("ASYNC request detected");
        chain.doFilter(req, res);
    }
}
```

---

### When to Use `ASYNC`

✅ Async servlets  
✅ Streaming responses  
✅ Non-blocking operations

📌 Advanced usage (not beginner-heavy)

---

# 12. Using Multiple Dispatcher Types (REAL WORLD)

### Example: Auth Filter for ALL cases

```java
@WebFilter(
    urlPatterns = "/secure/*",
    dispatcherTypes = {
        DispatcherType.REQUEST,
        DispatcherType.FORWARD,
        DispatcherType.ERROR
    }
)
public class SecurityFilter implements Filter {

    public void doFilter(ServletRequest req, ServletResponse res,
                         FilterChain chain)
            throws IOException, ServletException {

        System.out.println("Security check executed");
        chain.doFilter(req, res);
    }
}
```

---

# 13. Dispatcher Types Summary Table ⭐

|Dispatcher|Triggered When|Typical Use|
|---|---|---|
|REQUEST|Browser request|Auth, logging|
|FORWARD|`forward()`|MVC flow|
|INCLUDE|`include()`|Headers, layout|
|ERROR|Error page|Error logging|
|ASYNC|Async processing|Background tasks|

---

# 14. Interview Gold Points ⭐⭐⭐

- Filters can trigger **multiple times for same request**
    
- Dispatcher types control **when**
    
- `REQUEST` is default
    
- `FORWARD` & `INCLUDE` are server-side
    
- `ERROR` filters error handling
    
- Multiple dispatcher types can be combined
    
