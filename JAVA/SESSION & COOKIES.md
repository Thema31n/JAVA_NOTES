# 1. What is a Session?

### Definition

A **session** is a way to **store user-specific data on the server** across multiple requests.

👉 HTTP is **stateless**  
👉 Sessions make it **stateful**

---

### Real-World Problem

User logs in → clicks profile → adds to cart → checks out  
Without session → server forgets user every request ❌

---

### Solution

Session stores:

- User ID
    
- Login status
    
- Shopping cart
    
- Preferences
    

---

### Analogy

🧾 **Session = hospital patient file**  
Each request = doctor visit, but file stays until discharged

---

# 2. Session Tracking Techniques

Since HTTP is stateless, server must **track the client**.

---

## 2.1 Cookies (MOST COMMON)

- Server sends a cookie with session ID
    
- Browser sends it back automatically
    

```
JSESSIONID=ABC123
```

✔ Default & preferred  
✔ Works transparently

---

## 2.2 URL Rewriting

```
/dashboard;jsessionid=ABC123
```

✔ Used when cookies are disabled  
❌ Ugly & insecure

---

## 2.3 Hidden Form Fields

```html
<input type="hidden" name="sessionId" value="ABC123">
```

✔ Legacy systems  
❌ Only works with forms

---

## 2.4 SSL Session Tracking

- Uses HTTPS
    
- Rarely used manually
    

---

### Interview Tip ⭐

> **Session uses cookies internally by default**

---

# 3. Cookies

### What is a Cookie?

A **cookie** is a small key–value pair stored in the **browser**.

---

### Example

```
user=ahmed
theme=dark
```

---

### Real-World Use

- Remember login
    
- Language preference
    
- Theme selection
    
- Tracking & analytics
    

---

# 4. Types of Cookies

---

## 4.1 Session Cookies

- Temporary
    
- Deleted when browser closes
    
- Used for sessions
    

```java
cookie.setMaxAge(-1);
```

---

## 4.2 Persistent Cookies

- Stored on disk
    
- Survive browser restart
    

```java
cookie.setMaxAge(60 * 60 * 24 * 7); // 7 days
```

---

## 4.3 Secure Cookies

- Sent only over HTTPS
    

```java
cookie.setSecure(true);
```

---

## 4.4 HttpOnly Cookies

- Not accessible via JavaScript
    
- Prevents XSS attacks
    

```java
cookie.setHttpOnly(true);
```

---

### Interview Table ⭐

|Cookie Type|Use|
|---|---|
|Session|Login|
|Persistent|Remember me|
|Secure|Banking apps|
|HttpOnly|Security|

---

# 5. What is GDPR?

### GDPR = General Data Protection Regulation

📍 EU law (but impacts **global apps**)

---

### Purpose

- Protect user privacy
    
- Control how personal data is stored
    

---

### Cookie Impact

Web apps must:

- Inform users about cookies
    
- Get consent for non-essential cookies
    
- Allow opt-out
    

---

### Real-World Example

🍪 “This website uses cookies. Accept?”

---

# 6. `HttpSession` Interface

### What is `HttpSession`?

Represents **server-side session object**.

```java
jakarta.servlet.http.HttpSession
```

---

### What can it do?

- Store attributes
    
- Get session ID
    
- Invalidate session
    
- Track creation & access time
    

---

# 7. How to Create a Session

```java
HttpSession session = request.getSession();
```

✔ Creates new session if not exists  
✔ Returns existing if already exists

---

### Safer Version

```java
HttpSession session = request.getSession(false);
```

✔ Returns `null` if session does not exist

---

# 8. Storing & Reading Session Data

### Store Data

```java
session.setAttribute("user", "ahmed");
```

### Read Data

```java
String user = (String) session.getAttribute("user");
```

---

# 9. Real-World Login Example (Session Creation)

```java
protected void doPost(HttpServletRequest request,
                      HttpServletResponse response)
        throws IOException {

    String username = request.getParameter("username");
    String password = request.getParameter("password");

    if ("admin".equals(username) && "123".equals(password)) {

        HttpSession session = request.getSession();
        session.setAttribute("user", username);

        response.sendRedirect("dashboard");
    } else {
        response.sendError(401, "Invalid credentials");
    }
}
```

---

# 10. Session Validation (IMPORTANT)

### Check if User is Logged In

```java
HttpSession session = request.getSession(false);

if (session == null || session.getAttribute("user") == null) {
    response.sendRedirect("login.html");
    return;
}
```

---

### Real-World Use

- Protect dashboard
    
- Protect admin pages
    
- Prevent session hijacking
    

---

# 11. Session Timeout Configuration (`web.xml`)

### What is Session Timeout?

Time after which inactive session expires.

---

### `web.xml` Config

```xml
<session-config>
    <session-timeout>20</session-timeout>
</session-config>
```

⏱ Value in **minutes**

---

### Real-World

|App|Timeout|
|---|---|
|Banking|5–10 min|
|E-commerce|30 min|
|Forums|60 min|

---

# 12. Cookies — How to Create & Add

```java
Cookie cookie = new Cookie("theme", "dark");
cookie.setMaxAge(60 * 60 * 24); // 1 day
response.addCookie(cookie);
```

---

# 13. How to Read Cookies

```java
Cookie[] cookies = request.getCookies();

if (cookies != null) {
    for (Cookie c : cookies) {
        if ("theme".equals(c.getName())) {
            System.out.println(c.getValue());
        }
    }
}
```

---

# 14. How to Remove a Cookie

### Step 1: Create Same Cookie

### Step 2: Set Max Age = 0

```java
Cookie cookie = new Cookie("theme", "");
cookie.setMaxAge(0);
response.addCookie(cookie);
```

✔ Browser deletes it immediately

---

# 15. Logout Example (Session + Cookie Cleanup)

```java
protected void doGet(HttpServletRequest request,
                     HttpServletResponse response)
        throws IOException {

    HttpSession session = request.getSession(false);
    if (session != null) {
        session.invalidate();
    }

    Cookie cookie = new Cookie("JSESSIONID", "");
    cookie.setMaxAge(0);
    response.addCookie(cookie);

    response.sendRedirect("login.html");
}
```

---

# 16. Session vs Cookies (Interview Favorite ⭐)

|Session|Cookie|
|---|---|
|Server-side|Client-side|
|More secure|Less secure|
|Larger data|Small data|
|Needs cookie/URL|Stored in browser|

---

# 17. Key Interview Takeaways ⭐⭐⭐

- Session = server-side state
    
- Cookies = client-side storage
    
- `JSESSIONID` tracks session
    
- `getSession(false)` avoids creating new session
    
- Session timeout configured in `web.xml`
    
- GDPR requires cookie consent
    
- HttpOnly & Secure cookies improve security
    
