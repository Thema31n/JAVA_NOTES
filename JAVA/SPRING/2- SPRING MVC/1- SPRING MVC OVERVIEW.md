# 1. What is Spring MVC?

## Definition

> **Spring MVC is a web framework built on the Model–View–Controller design pattern for building web applications and RESTful services.**

---

## What Spring MVC Does in Real Life

- Handles HTTP requests
    
- Calls business logic
    
- Returns HTML views or JSON responses
    

Used in:

- Web applications
    
- REST APIs
    
- Microservices (controller layer)
    

---

# 2. MVC Design Pattern

## MVC = Model + View + Controller

---

## 2.1 Model

> **Holds application data and business logic**

Example:

```java
public class Order {
    private Long id;
    private String status;
}
```

---

## 2.2 View

> **Displays data to the user**

Examples:

- JSP
    
- Thymeleaf
    
- HTML
    

---

## 2.3 Controller

> **Handles user requests and coordinates Model and View**

Example:

```java
@Controller
public class OrderController {
}
```

---

## Real-World Analogy

- **Model** → Database / business data
    
- **Controller** → Manager
    
- **View** → UI screen
    

---

# 3. Front Controller Design Pattern

## Definition

> **All requests are handled by a single controller which dispatches them to appropriate handlers.**

---

## In Spring MVC

> **DispatcherServlet is the Front Controller**

---

## Why Front Controller?

✔ Centralized request handling  
✔ Common logic (security, logging)  
✔ Clean architecture

---

# 4. DispatcherServlet (HEART OF SPRING MVC)

## Definition

> **DispatcherServlet is the central servlet that receives all incoming HTTP requests and coordinates request processing.**

---

## What DispatcherServlet Does

- Receives request
    
- Finds controller
    
- Calls handler method
    
- Resolves view
    
- Returns response
    

---

## Configuration (XML – Legacy)

```xml
<servlet>
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>
        org.springframework.web.servlet.DispatcherServlet
    </servlet-class>
</servlet>
```

---

# 5. Flow of Execution in Spring MVC (VERY IMPORTANT)

## Step-by-Step Flow

1. Client sends HTTP request
    
2. DispatcherServlet receives request
    
3. DispatcherServlet asks **HandlerMapping**
    
4. Controller method found
    
5. Controller executes business logic
    
6. Returns Model + View name
    
7. ViewResolver resolves view
    
8. View rendered
    
9. Response sent to client
    

---

## Interview One-Liner

> **DispatcherServlet coordinates the entire request-response lifecycle in Spring MVC.**

---

# 6. Hello World Spring MVC Application

---

## 6.1 Web Application Initializer (No web.xml)

```java
public class WebAppInitializer
        implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext sc) {

        AnnotationConfigWebApplicationContext ctx =
                new AnnotationConfigWebApplicationContext();
        ctx.register(WebConfig.class);

        DispatcherServlet ds =
                new DispatcherServlet(ctx);

        ServletRegistration.Dynamic servlet =
                sc.addServlet("dispatcher", ds);

        servlet.setLoadOnStartup(1);
        servlet.addMapping("/");
    }
}
```

---

## 6.2 Spring MVC Configuration

```java
@Configuration
@EnableWebMvc
@ComponentScan("com.app")
public class WebConfig {
}
```

---

## 6.3 Controller

```java
@Controller
public class HelloController {

    @RequestMapping("/hello")
    public String hello(Model model) {
        model.addAttribute("msg", "Hello Spring MVC");
        return "hello";
    }
}
```

---

## 6.4 View (JSP Example)

```jsp
<h1>${msg}</h1>
```

---

## URL

```text
http://localhost:8080/hello
```

---

# 7. `@RequestMapping`

## Definition

> **Maps HTTP requests to controller methods.**

---

## Class-Level Mapping

```java
@RequestMapping("/orders")
@Controller
public class OrderController {
}
```

---

## Method-Level Mapping

```java
@RequestMapping("/list")
public String listOrders() {
    return "orders";
}
```

---

## HTTP-Specific Shortcuts

```java
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
```

---

## Real-World Example

```java
@GetMapping("/{id}")
public Order getOrder(@PathVariable Long id) {
    return service.getOrder(id);
}
```

---

# 8. `@EnableWebMvc`

## Purpose

> **Enables Spring MVC configuration and registers default MVC components.**

---

## What It Enables

- HandlerMapping
    
- HandlerAdapter
    
- ViewResolver
    
- Message converters
    

---

## Example

```java
@EnableWebMvc
@Configuration
public class WebConfig {
}
```

---

## Interview Tip

> Without `@EnableWebMvc`, Spring MVC won’t work properly.

---

# 9. ViewResolver

## Definition

> **Maps logical view names to actual view files.**

---

## Example (JSP ViewResolver)

```java
@Bean
public InternalResourceViewResolver viewResolver() {
    InternalResourceViewResolver vr =
            new InternalResourceViewResolver();
    vr.setPrefix("/WEB-INF/views/");
    vr.setSuffix(".jsp");
    return vr;
}
```

---

## How It Works

Controller returns:

```java
return "hello";
```

Resolved to:

```text
/WEB-INF/views/hello.jsp
```

---

# 10. WebApplicationInitializer Interface

## Purpose

> **Replaces `web.xml` for configuring Spring MVC programmatically.**

---

## Why It Exists

✔ Java-based config  
✔ No XML  
✔ Modern approach

---

## Used With

- DispatcherServlet
    
- ApplicationContext
    

---

# 11. AnnotationConfigWebApplicationContext

## Purpose

> **ApplicationContext implementation that reads Java-based configuration.**

---

## Example

```java
AnnotationConfigWebApplicationContext ctx =
        new AnnotationConfigWebApplicationContext();
ctx.register(WebConfig.class);
```

---

## Used For

- Registering Spring MVC configuration
    
- Component scanning
    

---

# 12. ContextLoaderListener

## Purpose

> **Loads the root application context (business & data layers).**

---

## Separation of Contexts

|Context|Contains|
|---|---|
|Root Context|Services, Repositories|
|Servlet Context|Controllers, Views|

---

## XML Example

```xml
<listener>
    <listener-class>
        org.springframework.web.context.ContextLoaderListener
    </listener-class>
</listener>
```

---

## Real-World Meaning

> Keeps business logic independent from web layer.

---

# 13. Advantages of Spring MVC

### ✅ Clean separation of concerns

### ✅ Flexible view technologies

### ✅ REST & web support

### ✅ Easy testing

### ✅ Powerful annotation support

---

# 14. Disadvantages of Spring MVC

### ❌ Learning curve

### ❌ Configuration complexity (without Boot)

### ❌ Overkill for very small apps

---

# 15. FINAL INTERVIEW REVISION CHEAT-SHEET

- **Spring MVC** → Web framework
    
- **MVC Pattern** → Model, View, Controller
    
- **Front Controller** → DispatcherServlet
    
- **Flow** → Request → Dispatcher → Controller → View
    
- **@RequestMapping** → URL mapping
    
- **@EnableWebMvc** → enable MVC
    
- **ViewResolver** → resolve views
    
- **WebApplicationInitializer** → replaces web.xml
    
- **AnnotationConfigWebApplicationContext** → Java config
    
- **ContextLoaderListener** → root context
    
- **Spring MVC Pros** → flexible, clean
    
- **Spring MVC Cons** → complexity
    
