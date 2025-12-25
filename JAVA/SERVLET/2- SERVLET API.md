# PART 1 — QUICK BUT SOLID RECAP

---

## 1. `@WebServlet` Annotation

Used to **register a servlet** without `web.xml`.

```java
@WebServlet("/login")
```

It tells the container:

> Requests to `/login` → this servlet

---

### Important `@WebServlet` Attributes

```java
@WebServlet(
    name = "LoginServlet",
    urlPatterns = {"/login", "/signin"},
    loadOnStartup = 1,
    initParams = {
        @WebInitParam(name = "adminEmail", value = "admin@test.com")
    }
)
```

|Attribute|Use|
|---|---|
|`name`|Logical servlet name|
|`urlPatterns`|URL mapping|
|`loadOnStartup`|Load at server startup|
|`initParams`|Servlet config|
|`asyncSupported`|Async requests|

---

## 2. URL Pattern Types (Real Meaning)

|Type|Example|Use Case|
|---|---|---|
|Exact|`/login`|Login, Logout|
|Path|`/user/*`|REST-style URLs|
|Extension|`*.do`|Legacy MVC|
|Default|`/`|Front controller|

---

## 3. `GenericServlet` vs `HttpServlet`

### `GenericServlet`

- Protocol independent
    
- Only `service()` method
    
- Rarely used
    

### `HttpServlet`

- HTTP specific
    
- `doGet`, `doPost`, etc.
    
- **Industry standard**
    

---

## 4. `HttpServletRequest`

Used to **read client data**:

- Parameters
    
- Headers
    
- Session
    
- IP, method, URL
    

```java
request.getParameter("username");
request.getHeader("User-Agent");
```

---

## 5. `HttpServletResponse`

Used to **send data to client**:

- HTML / JSON
    
- Redirect
    
- Headers
    
- Errors
    

```java
response.sendRedirect("login.html");
response.sendError(401);
```

---

## 6. `ServletConfig` vs `ServletContext`

|Feature|ServletConfig|ServletContext|
|---|---|---|
|Scope|One servlet|Entire app|
|Params|Init params|Global params|
|Lifecycle|Per servlet|Per app|

---

## 7. `RequestDispatcher`

Used for **internal navigation**.

```java
request.getRequestDispatcher("/dashboard")
       .forward(request, response);
```

---

## 8. Forward vs Redirect

|Forward|Redirect|
|---|---|
|Server side|Client side|
|Same request|New request|
|Faster|Slower|
|URL unchanged|URL changed|

---

## 9. `sendError()`

```java
response.sendError(403, "Access Denied");
```

Used for:

- Auth failure
    
- Missing resource
    
- Invalid input
    

---

## 10. HTTP Headers

### Set Header

```java
response.setHeader("X-App-Version", "1.0");
```

### Read Header

```java
request.getHeader("User-Agent");
```

---

# PART 2 — FULL REAL-WORLD CODE EXAMPLE

### (Uses **ALL** the Concepts Together)

---

## Scenario: Simple Login Application

### Features Used

✔ `@WebServlet`  
✔ URL patterns  
✔ `loadOnStartup`  
✔ Init params  
✔ `HttpServletRequest`  
✔ `HttpServletResponse`  
✔ Headers  
✔ `ServletConfig`  
✔ `ServletContext`  
✔ Forward vs Redirect  
✔ `sendError()`  
✔ `RequestDispatcher`

---

## 1. Startup Servlet (Uses `loadOnStartup`, `ServletContext`)

```java
package com.app;

import jakarta.servlet.*;
import jakarta.servlet.annotation.*;
import jakarta.servlet.http.*;

@WebServlet(
    name = "StartupServlet",
    urlPatterns = "/startup",
    loadOnStartup = 1
)
public class StartupServlet extends HttpServlet {

    @Override
    public void init() {
        // Application-wide data
        ServletContext context = getServletContext();
        context.setAttribute("appName", "User Management System");

        System.out.println("Application started successfully");
    }
}
```

---

## 2. Login Servlet (MAIN SERVLET)

```java
package com.app;

import jakarta.servlet.*;
import jakarta.servlet.annotation.*;
import jakarta.servlet.http.*;
import java.io.*;

@WebServlet(
    name = "LoginServlet",
    urlPatterns = "/login",
    initParams = {
        @WebInitParam(name = "adminUser", value = "admin"),
        @WebInitParam(name = "adminPass", value = "1234")
    }
)
public class LoginServlet extends HttpServlet {

    private String adminUser;
    private String adminPass;

    @Override
    public void init(ServletConfig config) throws ServletException {
        super.init(config);

        // Read servlet init parameters
        adminUser = config.getInitParameter("adminUser");
        adminPass = config.getInitParameter("adminPass");
    }

    @Override
    protected void doPost(HttpServletRequest request,
                          HttpServletResponse response)
            throws ServletException, IOException {

        // Read request parameters
        String username = request.getParameter("username");
        String password = request.getParameter("password");

        // Read request header
        String browser = request.getHeader("User-Agent");

        // Set response headers
        response.setHeader("X-App", "LoginModule");

        // Validation
        if (adminUser.equals(username) && adminPass.equals(password)) {

            // Store data in request scope
            request.setAttribute("user", username);

            // Forward to dashboard
            RequestDispatcher rd =
                request.getRequestDispatcher("/dashboard");
            rd.forward(request, response);

        } else {
            // Authentication failed
            response.sendError(401, "Invalid username or password");
        }
    }
}
```

---

## 3. Dashboard Servlet (Forward Target)

```java
package com.app;

import jakarta.servlet.*;
import jakarta.servlet.annotation.*;
import jakarta.servlet.http.*;
import java.io.*;

@WebServlet("/dashboard")
public class DashboardServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html");

        PrintWriter out = response.getWriter();

        // Get data from request scope
        String user = (String) request.getAttribute("user");

        // Get app-wide data
        ServletContext context = getServletContext();
        String appName = (String) context.getAttribute("appName");

        out.println("<h1>Welcome " + user + "</h1>");
        out.println("<h3>Application: " + appName + "</h3>");
    }
}
```

---

## 4. Logout Servlet (Redirect Example)

```java
@WebServlet("/logout")
public class LogoutServlet extends HttpServlet {

    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response)
            throws IOException {

        // Redirect to login page
        response.sendRedirect("login.html");
    }
}
```

---

# PART 3 — REQUEST FLOW (IMPORTANT)

```
Browser → /login (POST)
        → LoginServlet
        → Forward → /dashboard
        → DashboardServlet
```

---

# PART 4 — INTERVIEW GOLD POINTS ⭐

- `@WebServlet` replaces `web.xml`
    
- `loadOnStartup` initializes early
    
- `ServletConfig` = servlet-level config
    
- `ServletContext` = application-level data
    
- Forward ≠ Redirect
    
- `sendError()` sends HTTP status codes
    
- Headers help with security & metadata
    
