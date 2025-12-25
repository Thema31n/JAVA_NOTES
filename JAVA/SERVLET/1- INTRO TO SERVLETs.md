# 1. Why Do We Need to Learn Servlet Technology?

Before Servlets, web applications were mostly:

- Static HTML pages
    
- CGI programs (slow, not scalable)
    

### Problems with old approaches

- ❌ Each request created a new process (very slow)
    
- ❌ Poor performance
    
- ❌ Difficult session handling
    
- ❌ Not portable
    

### Why Servlets?

Servlets solve these problems by:

- ✅ Running inside a **JVM** (fast)
    
- ✅ Handling multiple requests using **threads**
    
- ✅ Being **platform independent**
    
- ✅ Supporting **session tracking**
    
- ✅ Integrating easily with databases, JSP, frameworks (Spring, Hibernate)
    

👉 **Servlets are the foundation of Java web development**  
(Spring MVC, Spring Boot, JSF all depend on servlet concepts)

---

# 2. What is a Servlet?

### Definition

A **Servlet** is a **Java class** that:

- Runs on a **web server**
    
- Handles **client (browser) requests**
    
- Generates **dynamic responses**
    

### Simple definition

> Servlet = Java program that runs on a server and responds to HTTP requests

### Example

Browser → Request → **Servlet** → Response (HTML/JSON)

---

# 3. What is Java EE?

### Java EE (Java Enterprise Edition)

Java EE is a **set of specifications** for building **enterprise-level applications**.

It includes APIs like:

- Servlets
    
- JSP
    
- JDBC
    
- EJB
    
- JPA
    
- JMS
    

### Important point

- Java EE is **not software**
    
- It is a **specification**
    
- Implemented by servers like:
    
    - Apache Tomcat
        
    - GlassFish
        
    - JBoss / WildFly
        

---

# 4. What is Jakarta EE?

### Why Jakarta EE came?

- Oracle transferred Java EE to the **Eclipse Foundation**
    
- After Java EE 8, the name **Java EE** could not be used
    

### Jakarta EE

- New name for Java EE
    
- Package name changed from:
    

```java
javax.servlet.*
```

➡️

```java
jakarta.servlet.*
```

### Key difference

|Java EE|Jakarta EE|
|---|---|
|javax.*|jakarta.*|
|Oracle|Eclipse Foundation|
|Older|Current & Future|

👉 **Modern projects use Jakarta EE**

---

# 5. Servlet Lifecycle

Servlet lifecycle is **managed by the web container** (Tomcat).

### Lifecycle steps

1. Loading
    
2. Instantiation
    
3. Initialization
    
4. Request handling
    
5. Destruction
    

---

# 6. Servlet Lifecycle Methods

## 6.1 `init()`

- Called **once**
    
- Used for:
    
    - Resource initialization
        
    - DB connections
        
    - Configuration loading
        

```java
public void init() throws ServletException {
    System.out.println("Servlet initialized");
}
```

---

## 6.2 `service()`

- Called **for every request**
    
- Decides which method to call:
    
    - `doGet()`
        
    - `doPost()`
        

```java
public void service(HttpServletRequest req, HttpServletResponse res)
        throws ServletException, IOException {
    System.out.println("Request received");
}
```

---

## 6.3 `destroy()`

- Called **once**
    
- Used for:
    
    - Closing resources
        
    - Cleanup
        

```java
public void destroy() {
    System.out.println("Servlet destroyed");
}
```

---

### Lifecycle Flow

```
init() → service() → destroy()
```

---

# 7. Servlet API

### What is Servlet API?

A **set of interfaces and classes** provided by Java/Jakarta to create servlets.

### Important packages

```java
jakarta.servlet.*
jakarta.servlet.http.*
```

### Important interfaces/classes

- Servlet
    
- GenericServlet
    
- HttpServlet
    
- ServletRequest
    
- ServletResponse
    
- HttpServletRequest
    
- HttpServletResponse
    

---

# 8. URL Mapping Rules

URL mapping connects:

```
Browser URL → Servlet class
```

---

## 8.1 Using web.xml (Deployment Descriptor)

```xml
<servlet>
    <servlet-name>HelloServlet</servlet-name>
    <servlet-class>com.example.HelloServlet</servlet-class>
</servlet>

<servlet-mapping>
    <servlet-name>HelloServlet</servlet-name>
    <url-pattern>/hello</url-pattern>
</servlet-mapping>
```

Access URL:

```
http://localhost:8080/app/hello
```

---

## 8.2 Using Annotation (Modern way)

```java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
}
```

---

### URL Mapping Rules

|Pattern|Example|
|---|---|
|Exact|`/login`|
|Path|`/user/*`|
|Extension|`*.do`|
|Default|`/`|

---

# 9. Creating a Hello World Servlet

### Step 1: Create Servlet Class

```java
package com.example;

import jakarta.servlet.*;
import jakarta.servlet.http.*;
import jakarta.servlet.annotation.*;
import java.io.*;

@WebServlet("/hello")
public class HelloServlet extends HttpServlet {

    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        out.println("<h1>Hello World from Servlet</h1>");
    }
}
```

---

### Step 2: Deploy on Server

- Use **Apache Tomcat**
    
- Start server
    
- Open browser
    

---

### Step 3: Access URL

```
http://localhost:8080/YourAppName/hello
```

---

# 10. IntelliJ IDEA Configuration for Servlets

## 10.1 Add Tomcat Server

1. File → Settings
    
2. Build, Execution, Deployment → Application Servers
    
3. Add → Tomcat Home Directory
    

---

## 10.2 Create Web Project

- New Project → Java → Jakarta EE
    
- Choose:
    
    - Servlet
        
    - Web Application
        
- Select Tomcat server
    

---

## 10.3 Run Configuration

- Add **Tomcat Server (Local)**
    
- Deploy artifact (WAR exploded)
    

---

# 11. Using External vs Internal Browser in IntelliJ

### Internal Browser

- Opens inside IntelliJ
    
- Quick testing
    
- Limited dev tools
    

### External Browser (Recommended)

- Chrome / Firefox
    
- Full DevTools
    
- Better debugging
    

### How to choose

1. Run → Edit Configurations
    
2. Server tab
    
3. Choose:
    
    - Default browser
        
    - Specific browser (Chrome)
        

---

# 12. Key Interview Points (Very Important)

- Servlet is **server-side Java technology**
    
- Servlet lifecycle is managed by **container**
    
- `init()` → once
    
- `service()` → every request
    
- `destroy()` → once
    
- Java EE → old name
    
- Jakarta EE → new name
    
- Annotation-based config is **preferred**
    

    
