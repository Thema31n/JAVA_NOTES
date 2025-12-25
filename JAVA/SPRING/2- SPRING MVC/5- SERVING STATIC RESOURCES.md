# 1. What Are Static Resources?

## Definition

> **Static resources are files that do not change dynamically and are served directly to the client.**

### Examples

- CSS (`.css`)
    
- JavaScript (`.js`)
    
- Images (`.png`, `.jpg`)
    
- Fonts
    
- PDFs
    

---

## Real-World Use Case

👉 A web application needs:

- `/css/style.css`
    
- `/js/app.js`
    
- `/images/logo.png`
    

Spring MVC must serve these **without going through controllers**.

---

# 2. Why Static Resources Need Special Configuration

## Problem

Spring MVC routes **all requests** through `DispatcherServlet`.

Without configuration:  
❌ Static files treated as controller requests  
❌ 404 errors

---

## Solution

> **Configure Spring MVC to bypass controllers for static resources.**

---

# 3. How Spring MVC Serves Static Resources (INTERNALS)

## Core Component

> **`ResourceHttpRequestHandler`**

---

## What It Does

- Locates static resource
    
- Applies resolvers & transformers
    
- Writes resource to HTTP response
    

---

## Execution Flow

1. Request for `/css/style.css`
    
2. DispatcherServlet delegates to `ResourceHttpRequestHandler`
    
3. Resource resolved
    
4. File streamed to browser
    

---

# 4. XML-Based Configuration for Static Resources (LEGACY)

## Example: Serve Resources from `/resources/`

```xml
<mvc:resources mapping="/resources/**"
               location="/resources/" />
```

---

## Folder Structure

```
src/main/webapp/
 └── resources/
     ├── css/
     ├── js/
     └── images/
```

---

## URL Access

```text
/resources/css/style.css
```

---

## How It Works

- `mapping` → URL pattern
    
- `location` → physical location inside WAR
    

---

## Enable MVC Namespace

```xml
<mvc:annotation-driven/>
```

---

# 5. Java-Based Configuration for Static Resources (MODERN)

## Using `WebMvcConfigurer`

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(
            ResourceHandlerRegistry registry) {

        registry.addResourceHandler("/resources/**")
                .addResourceLocations("/resources/");
    }
}
```

---

## Interview Line

> **ResourceHandlerRegistry maps URL paths to resource locations.**

---

# 6. Serving Static Resources from the Same WAR

## Common WAR Locations

- `/resources/`
    
- `/static/`
    
- `/public/`
    
- `/WEB-INF/resources/`
    

---

## Example

```java
registry.addResourceHandler("/assets/**")
        .addResourceLocations("/assets/");
```

---

## Folder

```
src/main/webapp/assets/
```

---

# 7. Serving Static Resources from the File System

## Real-World Scenario

👉 Uploads stored outside application:

```text
/opt/app/uploads/
```

---

## Java Config Example

```java
registry.addResourceHandler("/uploads/**")
        .addResourceLocations("file:/opt/app/uploads/");
```

---

## URL Access

```text
/uploads/profile.png
```

---

## Important Notes

✔ Use `file:` prefix  
✔ Ensure OS permissions

---

## Interview Tip

> **File-system resources are common for user-uploaded content.**

---

# 8. Serving Static Resources from Multiple Locations

## Real-World Scenario

👉 Serve:

- Common resources from WAR
    
- User uploads from file system
    

---

## Example

```java
registry.addResourceHandler("/assets/**")
        .addResourceLocations(
            "/assets/",
            "file:/opt/app/assets/"
        );
```

---

## Resolution Order

Spring checks locations **in order** and serves first match.

---

# 9. ResourceHandlerRegistry (KEY CLASS)

## What Is It?

> **Registers mappings between URL patterns and resource locations.**

---

## Methods You Must Remember

```java
addResourceHandler()
addResourceLocations()
setCachePeriod()
resourceChain()
```

---

# 10. Resource Resolvers (ADVANCED, INTERVIEW-IMPORTANT)

## Why Resource Resolvers?

✔ Security  
✔ Performance  
✔ Versioning  
✔ Compression

---

## Resolver Chain

> Resources are resolved through a **chain of resolvers**.

---

# 11. PathResourceResolver

## Purpose

> **Resolves resource paths safely and prevents path traversal attacks.**

---

## Example Configuration

```java
registry.addResourceHandler("/resources/**")
        .addResourceLocations("/resources/")
        .resourceChain(true)
        .addResolver(new PathResourceResolver());
```

---

## Security Benefit

❌ Blocks `../` access  
✔ Prevents unauthorized file access

---

## Interview Line

> **PathResourceResolver is the default and most important resolver.**

---

# 12. EncodedResourceResolver

## Purpose

> **Serves pre-compressed resources (gzip / brotli).**

---

## Real-World Scenario

👉 Browser requests:

```text
Accept-Encoding: gzip
```

Spring serves:

```text
style.css.gz
```

---

## Configuration Example

```java
registry.addResourceHandler("/resources/**")
        .addResourceLocations("/resources/")
        .resourceChain(true)
        .addResolver(new EncodedResourceResolver())
        .addResolver(new PathResourceResolver());
```

---

## Benefits

✔ Faster loading  
✔ Reduced bandwidth

---

# 13. Chaining of Resource Resolvers

## Rule

> **Resolvers are executed in the order they are added.**

---

## Example Chain

```java
.resourceChain(true)
.addResolver(new EncodedResourceResolver())
.addResolver(new PathResourceResolver());
```

---

## Execution Flow

1. Try encoded resource
    
2. Fallback to normal resource
    
3. Validate path
    
4. Serve file
    

---

# 14. Caching Static Resources (BEST PRACTICE)

```java
registry.addResourceHandler("/resources/**")
        .addResourceLocations("/resources/")
        .setCachePeriod(3600);
```

---

## Why Cache?

✔ Faster response  
✔ Reduced server load

---

# 15. Spring Security & Static Resources

## Problem

Spring Security may block static resources.

---

## Solution: Ignore Static Resources

### Java Config

```java
@Override
public void configure(WebSecurity web) {
    web.ignoring().antMatchers(
        "/resources/**",
        "/css/**",
        "/js/**",
        "/images/**"
    );
}
```

---

## OR Permit Access

```java
http.authorizeRequests()
    .antMatchers("/resources/**").permitAll()
    .anyRequest().authenticated();
```

---

## Interview Tip

> **Static resources should usually bypass security filters.**

---

# 16. Common Production Setup (REAL-WORLD)

|Resource Type|Location|
|---|---|
|CSS / JS|WAR|
|Images|WAR|
|Uploads|File system|
|Fonts|WAR|

---

# 17. Advantages of Spring MVC Static Resource Handling

### ✅ Secure

### ✅ Flexible

### ✅ High performance

### ✅ Multiple locations support

---

# 18. Common Mistakes (INTERVIEW FAVORITES)

❌ Forgetting `file:` prefix  
❌ No caching  
❌ Blocking static resources with security  
❌ Not using resolver chain

---

# 19. FINAL MEMORIZATION CHEAT-SHEET

- **Static resources** → CSS, JS, images
    
- **ResourceHttpRequestHandler** → serves resources
    
- **XML config** → `<mvc:resources>`
    
- **Java config** → `ResourceHandlerRegistry`
    
- **Same WAR** → `/resources/`
    
- **File system** → `file:/path/`
    
- **Multiple locations** → ordered lookup
    
- **PathResourceResolver** → security
    
- **EncodedResourceResolver** → compression
    
- **Resolver chain** → ordered execution
    
- **Spring Security** → ignore static paths
    
