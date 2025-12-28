# Spring Boot Filters

In **Spring Boot**, **filters** are a core part of the web request lifecycle. They let you **intercept HTTP requests and responses before they reach controllers and after they leave controllers**. Filters are commonly used for **cross-cutting concerns** like security, logging, auditing, and request modification.

Below is a **deep, practical explanation with real-world examples**, progressing from basics → advanced usage.

---

## 1. What is a Filter in Spring Boot?

A **Filter** is based on the **Servlet API** (`jakarta.servlet.Filter`).

Filters sit **before Spring MVC**, meaning they run **before controllers, interceptors, and AOP logic**.

### Position in the request flow

```
Client
  ↓
Servlet Filter(s)
  ↓
DispatcherServlet
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↑
Response back through Filters
```

### Key takeaway

Filters operate at the **lowest web layer**, making them ideal for technical and infrastructural concerns.

---

## 2. When to Use Filters (Real-World Use Cases)

Use filters when you need to:

- Work with **raw HTTP requests and responses**
    
- Apply logic **globally** across all endpoints
    
- Perform **pre-processing** or **post-processing**
    
- Reject requests **early** before reaching controllers
    

### Common real-world scenarios

|Use Case|Why Filter|
|---|---|
|Authentication|Validate JWT or API keys|
|Request logging|Log request metadata globally|
|Rate limiting|Block excessive traffic early|
|CORS handling|Add or modify headers|
|Request/response modification|Wrap or transform payloads|
|Auditing|Track system access|

---

## 3. Basic Filter Example – Request Logging

### Real-world scenario

You want to log **every incoming request**, including execution time.

### Implementation

```java
@Component
public class LoggingFilter implements Filter {

    @Override
    public void doFilter(
            ServletRequest request,
            ServletResponse response,
            FilterChain chain)
            throws IOException, ServletException {

        HttpServletRequest httpRequest = (HttpServletRequest) request;

        long startTime = System.currentTimeMillis();

        chain.doFilter(request, response);

        long duration = System.currentTimeMillis() - startTime;

        System.out.println(
            "Request " + httpRequest.getMethod() +
            " " + httpRequest.getRequestURI() +
            " took " + duration + " ms"
        );
    }
}
```

### What happens internally

- Code before `chain.doFilter()` runs **before controller**
    
- Code after it runs **after response generation**
    

---

## 4. Authentication Filter (JWT Validation)

### Real-world scenario

Your APIs are protected using **JWT tokens**.

### What the filter does

1. Extracts the `Authorization` header
    
2. Validates the JWT
    
3. Rejects unauthorized requests
    

### Example

```java
@Component
public class JwtAuthenticationFilter implements Filter {

    @Override
    public void doFilter(
            ServletRequest request,
            ServletResponse response,
            FilterChain chain)
            throws IOException, ServletException {

        HttpServletRequest httpRequest = (HttpServletRequest) request;
        HttpServletResponse httpResponse = (HttpServletResponse) response;

        String authHeader = httpRequest.getHeader("Authorization");

        if (authHeader == null || !authHeader.startsWith("Bearer ")) {
            httpResponse.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
            httpResponse.getWriter().write("Missing or invalid token");
            return;
        }

        String token = authHeader.substring(7);

        if (!isValidToken(token)) {
            httpResponse.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
            httpResponse.getWriter().write("Invalid token");
            return;
        }

        chain.doFilter(request, response);
    }

    private boolean isValidToken(String token) {
        return true; // validate JWT signature, expiration, claims
    }
}
```

### Why use a filter here?

Authentication is a **cross-cutting concern** and should happen **before controller logic**.

---

## 5. Filter Registration and URL Patterns

By default, filters annotated with `@Component` apply to **all URLs**.

To restrict them to specific paths:

```java
@Configuration
public class FilterConfig {

    @Bean
    public FilterRegistrationBean<LoggingFilter> loggingFilter() {
        FilterRegistrationBean<LoggingFilter> registrationBean =
                new FilterRegistrationBean<>();

        registrationBean.setFilter(new LoggingFilter());
        registrationBean.addUrlPatterns("/api/*");
        registrationBean.setOrder(1);

        return registrationBean;
    }
}
```

### Real-world usage

- `/api/*` → secured endpoints
    
- `/health` → excluded
    

---

## 6. Filter Ordering

Multiple filters execute in a **defined order**.

- Lower order value = higher priority
    
- Execution happens **top-down on request**, **bottom-up on response**
    

```java
registrationBean.setOrder(1);
```

### Example execution order

```
Order 1 → Security Filter
Order 2 → Logging Filter
Order 3 → Metrics Filter
```

---

## 7. Modifying the Response

### Real-world scenario

You want to add a **custom HTTP response header**.

```java
@Component
public class HeaderFilter implements Filter {

    @Override
    public void doFilter(
            ServletRequest request,
            ServletResponse response,
            FilterChain chain)
            throws IOException, ServletException {

        HttpServletResponse httpResponse = (HttpServletResponse) response;

        chain.doFilter(request, response);

        httpResponse.addHeader("X-App-Version", "1.0.0");
    }
}
```

---

## 8. OncePerRequestFilter (Very Important)

### What is OncePerRequestFilter?

`OncePerRequestFilter` is a **Spring abstraction** over `Filter` that guarantees a filter executes **only once per HTTP request**.

```java
org.springframework.web.filter.OncePerRequestFilter
```

### Why it exists

A normal `Filter` can run multiple times due to:

- Request forwarding
    
- Error dispatch
    
- Async processing
    

This can cause:

- Duplicate logs
    
- Repeated authentication
    
- Request body read failures
    

`OncePerRequestFilter` **prevents these issues automatically**.

---

### Example – Logging with OncePerRequestFilter

```java
@Component
public class RequestLoggingFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain)
            throws ServletException, IOException {

        long startTime = System.currentTimeMillis();

        filterChain.doFilter(request, response);

        long duration = System.currentTimeMillis() - startTime;

        System.out.println(
            "Request " + request.getMethod() +
            " " + request.getRequestURI() +
            " took " + duration + " ms"
        );
    }
}
```

---

## 9. Wrapping Request and Response Bodies

### Real-world scenario

You want to log **request and response bodies** for debugging.

```java
@Component
public class BodyLoggingFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain)
            throws ServletException, IOException {

        ContentCachingRequestWrapper wrappedRequest =
                new ContentCachingRequestWrapper(request);

        ContentCachingResponseWrapper wrappedResponse =
                new ContentCachingResponseWrapper(response);

        filterChain.doFilter(wrappedRequest, wrappedResponse);

        String requestBody =
                new String(wrappedRequest.getContentAsByteArray());
        String responseBody =
                new String(wrappedResponse.getContentAsByteArray());

        System.out.println("Request Body: " + requestBody);
        System.out.println("Response Body: " + responseBody);

        wrappedResponse.copyBodyToResponse();
    }
}
```

---

## 10. Filter vs OncePerRequestFilter

|Feature|Filter|OncePerRequestFilter|
|---|---|---|
|API|Servlet|Spring|
|Execution|May run multiple times|Runs once per request|
|Async safe|❌|✅|
|Error dispatch safe|❌|✅|
|Boilerplate|More|Less|
|Recommended in Spring Boot|⚠️|✅|

### Rule of thumb

> **In Spring Boot, always prefer `OncePerRequestFilter`.**

---

## 11. Filter vs Interceptor vs AOP

|Feature|Filter|Interceptor|AOP|
|---|---|---|---|
|Level|Servlet|Spring MVC|Method|
|Runs before Controller|✅|✅|❌|
|Access HTTP request|✅|Limited|❌|
|Business logic|❌|⚠️|✅|
|Best for|Security, logging|Auth, locale|Transactions|

---

## 12. Best Practices

✔ Use filters for:

- Authentication
    
- Logging
    
- Rate limiting
    
- Auditing
    

❌ Avoid:

- Business logic
    
- Database calls
    
- Heavy computation
    

✔ Prefer:

- `OncePerRequestFilter`
    
- Explicit ordering
    
- Early failure handling
    

---

## 13. Production-Grade Filter Stack

```
1. CORS Filter
2. JWT Security Filter
3. Rate Limiting Filter
4. Request Logging Filter
5. Metrics / Tracing Filter
```

---

## ✅ Final Summary

- Filters intercept requests **before Spring MVC**
    
- Best for **cross-cutting technical concerns**
    
- `Filter` may execute **multiple times per request**
    
- `OncePerRequestFilter` guarantees **single execution**
    
- Spring Security relies heavily on `OncePerRequestFilter`
    
- Filters must be **fast, stateless, and lightweight**
    

### Golden Rule

> **Spring Boot → use `OncePerRequestFilter`**
