# 1. Why Web Apps React to Events (Quick Recap)

Web apps are **event-driven systems**.

Events happen when:

- App starts / stops
    
- User session is created / destroyed
    
- Request arrives / finishes
    
- Data is added / removed / replaced
    

👉 **Listeners observe these events and react automatically**  
You never call them yourself.

---

# 2. Big Picture: All Servlet Event Categories

|Category|What It Tracks|
|---|---|
|Application (Context)|App lifecycle|
|Session|User session lifecycle|
|Session Attributes|Session data changes|
|Request|Request lifecycle|
|Request Attributes|Request data changes|
|Context Attributes|Application data changes|

---

# 3. APPLICATION (SERVLET CONTEXT) EVENTS

## 3.1 Application Lifecycle Event

### 🔹 Event

`ServletContextEvent`

### 🔹 Listener Interface

`ServletContextListener`

### 🔹 Methods

```java
void contextInitialized(ServletContextEvent sce)
void contextDestroyed(ServletContextEvent sce)
```

### 🔹 Triggered When

- App starts
    
- App stops / redeploys
    

### 🔹 Real-World Use

- Initialize DB pools
    
- Load cache
    
- Close resources
    

### 🔹 Example

```java
@WebListener
public class AppLifecycleListener
        implements ServletContextListener {

    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("Application started");
    }

    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("Application stopped");
    }
}
```

---

## 3.2 Application Attribute Events

### 🔹 Event

`ServletContextAttributeEvent`

### 🔹 Listener Interface

`ServletContextAttributeListener`

### 🔹 Methods

```java
void attributeAdded(ServletContextAttributeEvent e)
void attributeRemoved(ServletContextAttributeEvent e)
void attributeReplaced(ServletContextAttributeEvent e)
```

### 🔹 Triggered When

- `context.setAttribute()`
    
- `removeAttribute()`
    
- `replaceAttribute()`
    

### 🔹 Real-World Use

- Track shared cache changes
    
- Debug global state
    

### 🔹 Example

```java
@WebListener
public class ContextAttributeListener
        implements ServletContextAttributeListener {

    public void attributeAdded(ServletContextAttributeEvent e) {
        System.out.println("Context attr added: " + e.getName());
    }
}
```

---

# 4. SESSION EVENTS (ALL OF THEM)

## 4.1 Session Lifecycle Events

### 🔹 Event

`HttpSessionEvent`

### 🔹 Listener Interface

`HttpSessionListener`

### 🔹 Methods

```java
void sessionCreated(HttpSessionEvent se)
void sessionDestroyed(HttpSessionEvent se)
```

### 🔹 Triggered When

- Session created (`getSession()`)
    
- Session invalidated / timed out
    

### 🔹 Real-World Use

- Count active users
    
- Audit logins
    
- Detect timeouts
    

### 🔹 Example

```java
@WebListener
public class SessionLifecycleListener
        implements HttpSessionListener {

    public void sessionCreated(HttpSessionEvent se) {
        System.out.println("Session created: " + se.getSession().getId());
    }

    public void sessionDestroyed(HttpSessionEvent se) {
        System.out.println("Session destroyed");
    }
}
```

---

## 4.2 Session Attribute Events

### 🔹 Event

`HttpSessionBindingEvent`

### 🔹 Listener Interface

`HttpSessionAttributeListener`

### 🔹 Methods

```java
void attributeAdded(HttpSessionBindingEvent e)
void attributeRemoved(HttpSessionBindingEvent e)
void attributeReplaced(HttpSessionBindingEvent e)
```

### 🔹 Triggered When

- `session.setAttribute()`
    
- `removeAttribute()`
    
- Attribute overwrite
    

### 🔹 Real-World Use

- Track login/logout
    
- Detect privilege changes
    

### 🔹 Example

```java
@WebListener
public class SessionAttributeListener
        implements HttpSessionAttributeListener {

    public void attributeAdded(HttpSessionBindingEvent e) {
        System.out.println("Session attr added: " + e.getName());
    }
}
```

---

## 4.3 Session Binding Events (SPECIAL)

### 🔹 Event

`HttpSessionBindingEvent`

### 🔹 Listener Interface

`HttpSessionBindingListener`

### 🔹 Methods

```java
void valueBound(HttpSessionBindingEvent e)
void valueUnbound(HttpSessionBindingEvent e)
```

### 🔹 Triggered When

- Object added to session
    
- Object removed / session destroyed
    

### 🔹 Real-World Use

- Resource cleanup
    
- User logout hooks
    

### 🔹 Example

```java
public class User
        implements HttpSessionBindingListener {

    public void valueBound(HttpSessionBindingEvent e) {
        System.out.println("User logged in");
    }

    public void valueUnbound(HttpSessionBindingEvent e) {
        System.out.println("User logged out");
    }
}
```

---

## 4.4 Session Activation Events (CLUSTERING)

### 🔹 Event

`HttpSessionEvent`

### 🔹 Listener Interface

`HttpSessionActivationListener`

### 🔹 Methods

```java
void sessionWillPassivate(HttpSessionEvent se)
void sessionDidActivate(HttpSessionEvent se)
```

### 🔹 Triggered When

- Session moved to disk
    
- Session restored (clustering)
    

### 🔹 Real-World Use

- Distributed apps
    
- Session replication
    

### 🔹 Example

```java
public class Cart
        implements HttpSessionActivationListener {

    public void sessionWillPassivate(HttpSessionEvent se) {}
    public void sessionDidActivate(HttpSessionEvent se) {}
}
```

---

# 5. SERVLET REQUEST EVENTS (ALL OF THEM)

## 5.1 Request Lifecycle Events

### 🔹 Event

`ServletRequestEvent`

### 🔹 Listener Interface

`ServletRequestListener`

### 🔹 Methods

```java
void requestInitialized(ServletRequestEvent sre)
void requestDestroyed(ServletRequestEvent sre)
```

### 🔹 Triggered When

- Request enters container
    
- Request completes
    

### 🔹 Real-World Use

- Performance tracking
    
- Logging IPs
    
- Tracing
    

### 🔹 Example

```java
@WebListener
public class RequestListener
        implements ServletRequestListener {

    public void requestInitialized(ServletRequestEvent e) {
        System.out.println("Request started");
    }

    public void requestDestroyed(ServletRequestEvent e) {
        System.out.println("Request finished");
    }
}
```

---

## 5.2 Request Attribute Events

### 🔹 Event

`ServletRequestAttributeEvent`

### 🔹 Listener Interface

`ServletRequestAttributeListener`

### 🔹 Methods

```java
void attributeAdded(ServletRequestAttributeEvent e)
void attributeRemoved(ServletRequestAttributeEvent e)
void attributeReplaced(ServletRequestAttributeEvent e)
```

### 🔹 Triggered When

- `request.setAttribute()`
    
- `removeAttribute()`
    
- Replace attribute
    

### 🔹 Real-World Use

- Debug MVC flow
    
- Track data passing between layers
    

### 🔹 Example

```java
@WebListener
public class RequestAttributeListener
        implements ServletRequestAttributeListener {

    public void attributeAdded(ServletRequestAttributeEvent e) {
        System.out.println("Request attr added: " + e.getName());
    }
}
```

---

# 6. COMPLETE SUMMARY TABLE ⭐⭐⭐

|Event Type|Listener Interface|Purpose|
|---|---|---|
|App lifecycle|ServletContextListener|App start/stop|
|App attributes|ServletContextAttributeListener|Global state|
|Session lifecycle|HttpSessionListener|Login/logout|
|Session attributes|HttpSessionAttributeListener|Session data|
|Session binding|HttpSessionBindingListener|Object hooks|
|Session activation|HttpSessionActivationListener|Clustering|
|Request lifecycle|ServletRequestListener|Request timing|
|Request attributes|ServletRequestAttributeListener|MVC tracking|

---

# 7. How Listeners Are Registered

### Annotation (Modern)

```java
@WebListener
public class MyListener {}
```

### `web.xml` (Declarative)

```xml
<listener>
    <listener-class>com.app.MyListener</listener-class>
</listener>
```

---

# 8. DECLARATIVE EXCEPTION & ERROR HANDLING (FULL ADDITION)

---

## 8.1 What Is Declarative Error Handling?

Declarative error handling means:

> Handling errors and exceptions using **`web.xml` configuration**, not Java `try-catch`.

The **container automatically redirects** to error pages.

---

## 8.2 Why It’s Better Than Try–Catch

|Try–Catch|Declarative (`web.xml`)|
|---|---|
|Repeated code|Centralized|
|Mixed with business logic|Clean separation|
|Hard to maintain|Easy maintenance|
|Inconsistent UX|Consistent UX|
|Error-prone|Container-managed|

👉 **Enterprise standard approach**

---

## 8.3 Core XML Element: `<error-page>`

### Structure

```xml
<error-page>
    <error-code>...</error-code>
    <exception-type>...</exception-type>
    <location>...</location>
</error-page>
```

---

## 8.4 XML Elements (Required vs Optional)

|Element|Required|Description|
|---|---|---|
|`<error-page>`|✅|Parent element|
|`<location>`|✅|Error page path|
|`<error-code>`|❌|HTTP error|
|`<exception-type>`|❌|Java exception|

📌 Rule:  
Each `<error-page>` must have **ONE of**:

- `<error-code>`
    
- `<exception-type>`
    

---

## 8.5 HTTP Error Handling Example

```xml
<error-page>
    <error-code>404</error-code>
    <location>/errors/404.jsp</location>
</error-page>

<error-page>
    <error-code>500</error-code>
    <location>/errors/500.jsp</location>
</error-page>
```

---

## 8.6 Java Exception Handling Example

```xml
<error-page>
    <exception-type>java.lang.Exception</exception-type>
    <location>/errors/error.jsp</location>
</error-page>
```

---

## 8.7 Error Page Request Attributes

Automatically available:

- `jakarta.servlet.error.status_code`
    
- `jakarta.servlet.error.exception`
    
- `jakarta.servlet.error.message`
    
- `jakarta.servlet.error.request_uri`
    

---

## 8.8 Real-World Scenario

**Banking App**

- 403 → Session expired page
    
- 404 → Invalid URL page
    
- 500 → System unavailable page
    
- Exception → Generic safe error page
    

Handled **centrally**, securely, consistently.

---

# 9. Listener vs Filter vs Servlet (Interview Favorite)

|Component|Role|
|---|---|
|Servlet|Handles request|
|Filter|Intercepts request|
|Listener|Observes events|

---

# 10. Final Interview Gold Points ⭐⭐⭐⭐

- Listeners are **passive observers**
    
- Automatically triggered by container
    
- Used for lifecycle, auditing, monitoring
    
- Declarative error handling is **enterprise standard**
    
- `web.xml` centralizes failure handling
    
- Clean separation of concerns
    
