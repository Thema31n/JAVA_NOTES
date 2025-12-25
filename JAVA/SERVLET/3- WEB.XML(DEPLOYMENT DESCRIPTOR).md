# 1. What is `web.xml`?

### Definition

`web.xml` is the **Deployment Descriptor** of a Java web application.

📍 Location:

```
WEB-INF/web.xml
```

### What it does

It tells the **web container (Tomcat)**:

- Which servlets exist
    
- Which URLs map to which servlets
    
- App-level configuration
    
- Security rules
    
- Session behavior
    
- Welcome pages
    

👉 Think of `web.xml` as the **configuration brain** of a web app.

---

# 2. XML-based Config vs Annotation-based Config

## 2.1 XML-Based Configuration (Old / Centralized)

Example:

```xml
<servlet>
    <servlet-name>LoginServlet</servlet-name>
    <servlet-class>com.app.LoginServlet</servlet-class>
</servlet>
```

### Pros

✅ Centralized configuration  
✅ Easy to see whole app structure  
✅ Preferred in large enterprise apps

### Cons

❌ Verbose  
❌ Harder to maintain  
❌ Slower development

---

## 2.2 Annotation-Based Configuration (Modern / Distributed)

```java
@WebServlet("/login")
public class LoginServlet extends HttpServlet {}
```

### Pros

✅ Less code  
✅ Faster development  
✅ Easy to read

### Cons

❌ Config scattered across classes  
❌ Harder to manage large security rules

---

## 2.3 Which One Wins?

|Use Case|Recommended|
|---|---|
|Small / Medium app|Annotations|
|Enterprise security|XML|
|Learning / Exams|Both|
|Spring Boot|Annotations|

👉 **Annotations override `web.xml` if both exist**

---

# 3. Welcome File List

### What is a Welcome File?

A page that loads **automatically** when user hits the root URL.

Example:

```
http://localhost:8080/myapp/
```

---

### Configuration

```xml
<welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.jsp</welcome-file>
</welcome-file-list>
```

### Real World

- Home page
    
- Landing page
    
- Login page
    

---

# 4. Servlet Configuration in `web.xml`

## 4.1 Defining a Servlet

```xml
<servlet>
    <servlet-name>LoginServlet</servlet-name>
    <servlet-class>com.app.LoginServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>
```

### Explanation

- `servlet-name` → logical name
    
- `servlet-class` → Java class
    
- `load-on-startup` → load at server startup
    

---

## 4.2 Servlet Mapping

```xml
<servlet-mapping>
    <servlet-name>LoginServlet</servlet-name>
    <url-pattern>/login</url-pattern>
</servlet-mapping>
```

### URL Types

```xml
/login        <!-- exact -->
/user/*       <!-- path -->
*.do          <!-- extension -->
/             <!-- default -->
```

---

# 5. Context Parameters (Global App Config)

### What are Context Params?

- Application-wide parameters
    
- Shared across **all servlets**
    

---

### Configuration

```xml
<context-param>
    <param-name>appName</param-name>
    <param-value>E-Commerce App</param-value>
</context-param>
```

---

### Access in Servlet

```java
ServletContext ctx = getServletContext();
String appName = ctx.getInitParameter("appName");
```

---

### Real-World Use

- App name
    
- DB URL
    
- Environment flags
    

---

# 6. Security Constraints (VERY IMPORTANT)

### What is a Security Constraint?

Defines:

- Which URLs are protected
    
- Who can access them
    
- Using which HTTP methods
    

---

## 6.1 Protecting URLs

```xml
<security-constraint>
    <web-resource-collection>
        <web-resource-name>Admin Area</web-resource-name>
        <url-pattern>/admin/*</url-pattern>
        <http-method>GET</http-method>
        <http-method>POST</http-method>
    </web-resource-collection>
```

---

## 6.2 Authorization Constraint

```xml
    <auth-constraint>
        <role-name>ADMIN</role-name>
    </auth-constraint>
</security-constraint>
```

👉 Only users with `ADMIN` role can access `/admin/*`

---

## 6.3 Authentication Method

```xml
<login-config>
    <auth-method>FORM</auth-method>
    <realm-name>MyRealm</realm-name>

    <form-login-config>
        <form-login-page>/login.jsp</form-login-page>
        <form-error-page>/error.jsp</form-error-page>
    </form-login-config>
</login-config>
```

---

# 7. Security Roles Configuration

### Define Roles

```xml
<security-role>
    <role-name>ADMIN</role-name>
</security-role>

<security-role>
    <role-name>USER</role-name>
</security-role>
```

---

### Real-World Example

|Role|Access|
|---|---|
|ADMIN|Admin dashboard|
|USER|Profile, orders|
|GUEST|Public pages|

---

# 8. Session Configuration

### What is Session Config?

Controls:

- Session timeout
    
- Cookie behavior
    

---

### Example

```xml
<session-config>
    <session-timeout>30</session-timeout>
</session-config>
```

⏱ Timeout is in **minutes**

---

### Real-World Use

- Auto logout
    
- Security compliance
    
- Banking apps (short timeout)
    

---

# 9. Full Sample `web.xml` (REALISTIC)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee
         https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">

    <!-- App-wide config -->
    <context-param>
        <param-name>appName</param-name>
        <param-value>User Management System</param-value>
    </context-param>

    <!-- Welcome page -->
    <welcome-file-list>
        <welcome-file>login.html</welcome-file>
    </welcome-file-list>

    <!-- Servlet -->
    <servlet>
        <servlet-name>LoginServlet</servlet-name>
        <servlet-class>com.app.LoginServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!-- Mapping -->
    <servlet-mapping>
        <servlet-name>LoginServlet</servlet-name>
        <url-pattern>/login</url-pattern>
    </servlet-mapping>

    <!-- Security -->
    <security-constraint>
        <web-resource-collection>
            <web-resource-name>Admin Area</web-resource-name>
            <url-pattern>/admin/*</url-pattern>
        </web-resource-collection>

        <auth-constraint>
            <role-name>ADMIN</role-name>
        </auth-constraint>
    </security-constraint>

    <!-- Login config -->
    <login-config>
        <auth-method>FORM</auth-method>
        <form-login-config>
            <form-login-page>/login.jsp</form-login-page>
            <form-error-page>/error.jsp</form-error-page>
        </form-login-config>
    </login-config>

    <!-- Roles -->
    <security-role>
        <role-name>ADMIN</role-name>
    </security-role>

    <!-- Session -->
    <session-config>
        <session-timeout>20</session-timeout>
    </session-config>

</web-app>
```

---

# 10. Interview-Level Summary ⭐

- `web.xml` = deployment descriptor
    
- Centralized configuration
    
- Annotations are modern, XML still powerful
    
- Welcome file auto-loads home page
    
- Security constraints protect URLs
    
- Roles define authorization
    
- Session config controls logout timing
    
