# FILTERS & INTERCEPTORS IN SPRING MVC

## COMPLETE REAL-WORLD GUIDE

---

# 1. What is a Web Filter?

## Definition

> **A Web Filter is a Servlet-level component that intercepts HTTP requests and responses before they reach Spring MVC.**

Filters are part of the **Servlet specification**, not Spring-specific.

---

## Where a Filter Works

```
Client → Filter → DispatcherServlet → Controller
Client ← Filter ← DispatcherServlet ← Controller
```

---

## Real-World Use Cases

- Authentication / authorization
    
- Logging request & response
    
- Request modification
    
- CORS handling
    
- Compression
    

---

## Key Characteristics

- Runs **before Spring MVC**
    
- Applies to **all requests** (even static resources)
    
- Cannot access controller or model
    

---

# 2. What is `HandlerInterceptor` in Spring MVC?

## Definition

> **A HandlerInterceptor is a Spring MVC component that intercepts requests before and after controller execution.**

---

## Where Interceptor Works

```
Client → Filter → DispatcherServlet
        → Interceptor → Controller
        → Interceptor → View
```

---

## Real-World Use Cases

- Login checks
    
- Role validation
    
- Request timing
    
- Audit logging
    
- Locale setting
    

---

## Key Characteristics

- Spring-specific
    
- Works **inside Spring MVC**
    
- Can access:
    
    - Handler (controller)
        
    - Model
        
    - View
        

---

# 3. Goals of `HandlerInterceptor`

## Why Interceptors Exist

✔ Pre-process requests  
✔ Post-process responses  
✔ Avoid code duplication  
✔ Centralize cross-cutting logic

---

## Interceptor Lifecycle Methods (MEMORIZE)

```java
preHandle()
postHandle()
afterCompletion()
```

---

### `preHandle`

- Runs **before controller**
    
- Can block request
    

### `postHandle`

- Runs **after controller**
    
- Before view rendering
    

### `afterCompletion`

- Runs **after response**
    
- Cleanup logic
    

---

# 4. Filter vs HandlerInterceptor (VERY IMPORTANT)

|Feature|Filter|HandlerInterceptor|
|---|---|---|
|Level|Servlet|Spring MVC|
|Framework|Java EE|Spring|
|Executes|Before DispatcherServlet|Before Controller|
|Access Controller|❌|✅|
|Access Model/View|❌|✅|
|Static Resources|✅|❌ (by default)|
|Typical Use|Security, CORS|Auth, logging|

---

## Interview One-Liner

> **Filters work at the servlet level, interceptors work at the Spring MVC level.**

---

# 5. Web Filter Demo (REAL-WORLD)

## Scenario

👉 Log every incoming request URL.

---

## Step 1: Create Filter

```java
public class LoggingFilter implements Filter {

    @Override
    public void doFilter(
        ServletRequest request,
        ServletResponse response,
        FilterChain chain)
        throws IOException, ServletException {

        HttpServletRequest req =
            (HttpServletRequest) request;

        System.out.println("Request URL: " + req.getRequestURI());

        chain.doFilter(request, response);
    }
}
```

---

## Step 2: Register Filter (Java Config)

```java
@Component
public class LoggingFilter implements Filter {
}
```

OR using `FilterRegistrationBean`:

```java
@Bean
public FilterRegistrationBean<LoggingFilter> filter() {
    FilterRegistrationBean<LoggingFilter> bean =
            new FilterRegistrationBean<>();
    bean.setFilter(new LoggingFilter());
    bean.addUrlPatterns("/*");
    return bean;
}
```

---

## How It Works

1. Request arrives
    
2. Filter executes
    
3. Request forwarded to Spring MVC
    

---

## When Filter is Triggered

✔ Controllers  
✔ Static resources  
✔ Error pages

---

# 6. HandlerInterceptor Demo (REAL-WORLD)

## Scenario

👉 Allow access only if user is logged in.

---

## Step 1: Create Interceptor

```java
public class AuthInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(
        HttpServletRequest request,
        HttpServletResponse response,
        Object handler) throws Exception {

        HttpSession session = request.getSession(false);

        if (session == null || session.getAttribute("user") == null) {
            response.sendRedirect("/login");
            return false; // stop request
        }
        return true;
    }
}
```

---

## Step 2: Register Interceptor

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(
            InterceptorRegistry registry) {

        registry.addInterceptor(new AuthInterceptor())
                .addPathPatterns("/orders/**")
                .excludePathPatterns("/login");
    }
}
```

---

## How It Works

1. DispatcherServlet receives request
    
2. Interceptor `preHandle()` runs
    
3. Controller executes (if allowed)
    
4. `postHandle()` runs
    
5. View rendered
    
6. `afterCompletion()` runs
    

---

# 7. When to Use Filter vs Interceptor (REAL DECISION GUIDE)

---

## Use Filter When

✔ Need to intercept **all requests**  
✔ Need to work **outside Spring**  
✔ Working with security / CORS / encoding  
✔ Want to modify request/response

### Examples

- JWT token parsing
    
- CORS headers
    
- Request compression
    
- Logging raw requests
    

---

## Use Interceptor When

✔ Need access to controller or model  
✔ Want Spring-aware logic  
✔ Want URL-specific interception  
✔ Want pre/post controller logic

### Examples

- Login check
    
- Role-based access
    
- Audit logging
    
- Locale setup
    

---

## Interview Rule

> **Filters are framework-agnostic; interceptors are Spring-aware.**

---

# 8. Execution Order (IMPORTANT)

```
Client
 ↓
Filter (pre)
 ↓
Interceptor preHandle
 ↓
Controller
 ↓
Interceptor postHandle
 ↓
View
 ↓
Interceptor afterCompletion
 ↓
Filter (post)
 ↓
Client
```

---

# 9. Common Real-World Architecture

|Concern|Solution|
|---|---|
|CORS|Filter|
|JWT validation|Filter|
|Login check|Interceptor|
|Logging|Both (depends)|
|Metrics|Interceptor|

---

# 10. Common Interview Mistakes

❌ Using interceptor for CORS  
❌ Using filter for controller logic  
❌ Forgetting `chain.doFilter()`  
❌ Returning false incorrectly

---

# 11. FINAL MEMORIZATION CHEAT-SHEET

- **Filter** → Servlet-level, all requests
    
- **HandlerInterceptor** → Spring MVC-level
    
- **Filter runs before DispatcherServlet**
    
- **Interceptor runs before Controller**
    
- **Filter** → security, CORS, logging
    
- **Interceptor** → auth, validation, audit
    
- **Use filter for infrastructure**
    
- **Use interceptor for business flow**
    
