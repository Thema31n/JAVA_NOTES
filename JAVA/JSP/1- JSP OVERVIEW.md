## **1. JSP Overview**

**JSP (JavaServer Pages)** is a **server-side Java technology** used to create **dynamic web pages**.

- JSP allows **HTML + Java** together
    
- Runs on a **web container** (Tomcat, GlassFish)
    
- Used mainly for **View layer** in MVC
    
- Converted internally into a **Servlet**
    

### 🔹 Real-World Example

An **online banking system**:

- Servlet → validates user
    
- JSP → displays balance, username, transactions
    

```jsp
Welcome, <%= session.getAttribute("userName") %>
```

---

## **2. JSP vs HTML**

|Feature|HTML|JSP|
|---|---|---|
|Content|Static|Dynamic|
|Java Code|❌ No|✅ Yes|
|Server Execution|❌ No|✅ Yes|
|Database|❌ No|✅ Yes|

### 🔹 Example

**HTML**

```html
<h1>Welcome User</h1>
```

**JSP**

```jsp
<h1>Welcome <%= session.getAttribute("user") %></h1>
```

👉 HTML cannot change dynamically.

---

## **3. JSP vs Servlets**

|Feature|JSP|Servlet|
|---|---|---|
|Coding Style|HTML-based|Java-based|
|Best Use|UI|Business Logic|
|Readability|High|Low|
|Implicit Objects|Yes|No|

### 🔹 Real-World Flow

```text
Browser → Servlet (logic) → JSP (UI)
```

**Servlet**

```java
request.setAttribute("name","Ahmed");
request.getRequestDispatcher("welcome.jsp").forward(request,response);
```

**JSP**

```jsp
Welcome ${name}
```

---

## **4. JSP vs JavaScript**

|Feature|JSP|JavaScript|
|---|---|---|
|Runs On|Server|Browser|
|Security|High|Low|
|DB Access|Yes|No|
|Purpose|Page generation|UI behavior|

### 🔹 Example

**JSP**

```jsp
<%= new java.util.Date() %>
```

**JavaScript**

```html
<script>
document.write(new Date());
</script>
```

---

## **5. JSP Advantages & Disadvantages**

### ✅ Advantages

- Easy for Java developers
    
- Automatic servlet handling
    
- Platform independent
    
- Good MVC support
    
- Secure (server-side)
    

### ❌ Disadvantages

- Debugging is difficult
    
- Bad practice can mix logic + UI
    
- Slower than pure servlets
    
- Not ideal for large systems
    

---

## **6. JSP Lifecycle**

1️⃣ Translation (JSP → Servlet)  
2️⃣ Compilation  
3️⃣ Class Loading  
4️⃣ Instantiation  
5️⃣ `jspInit()`  
6️⃣ `_jspService()`  
7️⃣ `jspDestroy()`

```java
jspInit()
_jspService()
jspDestroy()
```

👉 `_jspService()` executes **per request**

---

## **7. JSP Elements**

### 🔹 Scriptlet

```jsp
<%
int x = 10;
out.println(x);
%>
```

### 🔹 Expression

```jsp
<%= x %>
```

### 🔹 Declaration

```jsp
<%! int count = 0; %>
```

### 🔹 Directive

```jsp
<%@ page import="java.util.*" %>
```

---

## **8. JSP Implicit Objects**

|Object|Description|
|---|---|
|request|Client request|
|response|Server response|
|session|User session|
|application|App-wide data|
|out|Output stream|
|config|Servlet config|
|page|JSP instance|
|pageContext|All scopes|
|exception|Error object|

### 🔹 Example

```jsp
User IP: <%= request.getRemoteAddr() %>
```

---

## **9. JSP → Servlet Translation Example**

### 🔹 JSP

```jsp
Hello <%= request.getParameter("name") %>
```

### 🔹 Generated Servlet

```java
out.write("Hello ");
out.print(request.getParameter("name"));
```

👉 JSP is **not magic**, it’s converted into Servlet code.

---

## **10. Expression Language (EL)**

EL removes Java code from JSP and makes it **clean & readable**.

```jsp
${expression}
```

---

## **11. EL Implicit Objects (ALL)**

### 🔹 Scope Maps

|EL Object|Description|
|---|---|
|pageScope|Page attributes|
|requestScope|Request attributes|
|sessionScope|Session attributes|
|applicationScope|App attributes|

```jsp
${sessionScope.user}
```

---

### 🔹 Parameter Maps

|EL Object|Description|
|---|---|
|param|Request parameter|
|paramValues|Parameter array|

```jsp
${param.username}
```

---

### 🔹 Header Maps

|EL Object|Description|
|---|---|
|header|Request header|
|headerValues|Header array|

```jsp
${header["user-agent"]}
```

---

### 🔹 Cookie Map

```jsp
${cookie.JSESSIONID.value}
```

---

### 🔹 Init Parameters

```jsp
${initParam.dbName}
```

---

### 🔹 Page Context

```jsp
${pageContext.request.method}
```

---

## **12. EL Keywords / Operators (ALL IMPORTANT)**

### 🔹 Arithmetic

|Operator|Meaning|
|---|---|
|+|Add|
|-|Subtract|
|*|Multiply|
|/ or div|Divide|
|% or mod|Modulus|

```jsp
${10 + 5}
```

---

### 🔹 Relational

|Operator|Alternative|
|---|---|
|==|eq|
|!=|ne|
|<|lt|
|>|gt|
|<=|le|
|>=|ge|

```jsp
${age gt 18}
```

---

### 🔹 Logical

|Operator|Meaning|
|---|---|
|&&|and|
|||
|!|not|

```jsp
${isLoggedIn and isAdmin}
```

---

### 🔹 Conditional (Ternary)

```jsp
${age >= 18 ? "Adult" : "Minor"}
```

---

### 🔹 Empty Keyword

```jsp
${empty user}
```

👉 Returns `true` if null or empty.

---

## **13. EL vs JSP Scriptlet**

|Feature|Scriptlet|EL|
|---|---|---|
|Readability|Low|High|
|Security|Medium|High|
|Java Code|Yes|No|
|Recommended|❌ No|✅ Yes|

---

## **14. Real-World EL Example**

```jsp
Welcome ${user.name}
Balance: ${user.balance}
```

Bean:

```java
public class User {
    private String name;
    private double balance;
}
```

---

## **Conclusion**

✔ JSP = View technology  
✔ Servlet = Controller  
✔ EL + JSTL = Clean code  
✔ Avoid Java logic inside JSP  
✔ Best used in MVC architecture
