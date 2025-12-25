## **1. JSTL Overview**

**JSTL** is a **collection of standard tags** that simplifies JSP development by **eliminating Java code (scriptlets)** from JSP pages.

Instead of writing:

```jsp
<%
for(int i=0;i<10;i++){
    out.println(i);
}
%>
```

You write:

```jsp
<c:forEach var="i" begin="1" end="10">
    ${i}
</c:forEach>
```

### 🔹 Why JSTL Exists

- JSP scriptlets are:
    
    - Hard to read
        
    - Hard to maintain
        
    - Against MVC principles
        
- JSTL + EL:
    
    - Clean
        
    - Secure
        
    - Declarative
        
    - Standardized
        

👉 JSTL is used mainly in the **View layer**.

---

## **2. Advantages & Disadvantages of JSTL**

### ✅ Advantages

1. **No Java code in JSP**
    
2. **Improves readability**
    
3. **Follows MVC architecture**
    
4. **Reusable & standardized**
    
5. **Works perfectly with EL**
    
6. **Reduces bugs**
    
7. **Easier maintenance**
    

### ❌ Disadvantages

1. Limited logic (not for complex operations)
    
2. SQL tags are **not recommended in real projects**
    
3. Slight learning curve
    
4. Depends on JSTL libraries
    
5. Not suitable for business logic
    

👉 JSTL is **for UI logic only**, not business logic.

---

## **3. JSTL Tag Classifications (ALL)**

JSTL is divided into **5 main tag libraries**:

|Library|Prefix|Purpose|
|---|---|---|
|Core|`c`|Logic, flow control|
|Formatting|`fmt`|i18n, numbers, dates|
|Functions|`fn`|String operations|
|SQL|`sql`|Database access|
|XML|`x`|XML processing|

---

## **4. JSTL Setup (Configuration)**

### 🔹 Step 1: Add JSTL Dependency

#### Maven

```xml
<dependency>
  <groupId>jakarta.servlet.jsp.jstl</groupId>
  <artifactId>jakarta.servlet.jsp.jstl-api</artifactId>
  <version>3.0.0</version>
</dependency>
```

OR (Older version)

```xml
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>jstl</artifactId>
  <version>1.2</version>
</dependency>
```

---

### 🔹 Step 2: Declare Taglib in JSP

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
```

---

## **5. JSTL Core Tags (Most Important)**

### 🔹 `<c:out>` – Output

```jsp
<c:out value="${user.name}" />
```

✔ Escapes HTML  
✔ Safer than `<%= %>`

---

### 🔹 `<c:set>` – Set Variable

```jsp
<c:set var="x" value="10" />
```

---

### 🔹 `<c:remove>` – Remove Variable

```jsp
<c:remove var="x" />
```

---

### 🔹 `<c:if>` – Conditional

```jsp
<c:if test="${age >= 18}">
    Adult
</c:if>
```

---

### 🔹 `<c:choose>` – Switch Case

```jsp
<c:choose>
  <c:when test="${role == 'admin'}">Admin</c:when>
  <c:when test="${role == 'user'}">User</c:when>
  <c:otherwise>Guest</c:otherwise>
</c:choose>
```

---

### 🔹 `<c:forEach>` – Loop

```jsp
<c:forEach var="p" items="${products}">
    ${p.name}
</c:forEach>
```

---

### 🔹 `<c:forTokens>` – Token Loop

```jsp
<c:forTokens items="Java,Spring,JSP" delims="," var="lang">
    ${lang}
</c:forTokens>
```

---

### 🔹 `<c:catch>` – Exception Handling

```jsp
<c:catch var="error">
    ${10 / 0}
</c:catch>
```

---

### 🔹 `<c:import>` – Include URL

```jsp
<c:import url="header.jsp" />
```

---

### 🔹 `<c:redirect>` – Redirect

```jsp
<c:redirect url="login.jsp" />
```

---

### 🔹 `<c:url>` – URL Rewriting

```jsp
<c:url value="/home" />
```

---

## **6. JSTL Formatting Tags (`fmt`)**

Used for **internationalization (i18n)**, numbers, and dates.

### 🔹 `<fmt:formatNumber>`

```jsp
<fmt:formatNumber value="12345.678" type="currency" />
```

---

### 🔹 `<fmt:formatDate>`

```jsp
<fmt:formatDate value="${date}" pattern="dd-MM-yyyy" />
```

---

### 🔹 `<fmt:parseDate>`

```jsp
<fmt:parseDate value="2025-01-01" pattern="yyyy-MM-dd" />
```

---

### 🔹 `<fmt:setLocale>`

```jsp
<fmt:setLocale value="en_US" />
```

---

### 🔹 `<fmt:setBundle>`

```jsp
<fmt:setBundle basename="messages" />
```

---

### 🔹 Real-World i18n Example

```jsp
<fmt:message key="welcome.message" />
```

---

## **7. JSTL Functions (`fn`)**

Used with **EL** for string manipulation.

### 🔹 Common Functions

|Function|Description|
|---|---|
|fn:length|Length|
|fn:toUpperCase|Uppercase|
|fn:toLowerCase|Lowercase|
|fn:contains|Check substring|
|fn:startsWith|Starts with|
|fn:endsWith|Ends with|
|fn:substring|Extract|
|fn:replace|Replace|
|fn:split|Split|
|fn:trim|Trim|

---

### 🔹 Examples

```jsp
${fn:length(name)}
${fn:toUpperCase(name)}
${fn:contains(email, "@")}
```

---

## **8. JSTL SQL Tags (Educational Only ⚠️)**

❗ **Not recommended in real projects**

### 🔹 `<sql:setDataSource>`

```jsp
<sql:setDataSource
    var="db"
    driver="com.mysql.cj.jdbc.Driver"
    url="jdbc:mysql://localhost:3306/test"
    user="root"
    password="root"/>
```

---

### 🔹 `<sql:query>`

```jsp
<sql:query dataSource="${db}" var="rs">
    SELECT * FROM users
</sql:query>
```

---

### 🔹 `<sql:forEach>`

```jsp
<c:forEach var="row" items="${rs.rows}">
    ${row.name}
</c:forEach>
```

👉 **Never mix DB logic with JSP in production**.

---

## **9. JSTL XML Tags**

Used for parsing and processing XML.

### 🔹 `<x:parse>`

```jsp
<x:parse xml="${xmlData}" var="doc" />
```

---

### 🔹 `<x:out>`

```jsp
<x:out select="$doc/books/book/title" />
```

---

### 🔹 `<x:forEach>`

```jsp
<x:forEach select="$doc/books/book" var="b">
    <x:out select="$b/title" />
</x:forEach>
```

---

## **10. Real-World JSTL Example (MVC)**

### 🔹 Servlet

```java
List<Product> products = service.getProducts();
request.setAttribute("products", products);
request.getRequestDispatcher("products.jsp").forward(request, response);
```

---

### 🔹 JSP (JSTL + EL)

```jsp
<c:forEach var="p" items="${products}">
    <tr>
        <td>${p.name}</td>
        <td>${p.price}</td>
    </tr>
</c:forEach>
```

✔ Clean  
✔ No Java code  
✔ MVC compliant

---

## **11. JSTL vs Scriptlets**

|Feature|Scriptlet|JSTL|
|---|---|---|
|Java Code|Yes|No|
|Readability|Poor|Excellent|
|Security|Low|High|
|Recommended|❌|✅|

---

## **12. Best Practices**

✔ Use JSTL + EL only  
✔ Keep JSP inside `WEB-INF`  
✔ Use Servlets/Spring Controllers for logic  
✔ Avoid SQL tags  
✔ Use formatting tags for i18n

---

## **Conclusion**

- JSTL is **essential for modern JSP**
    
- Makes JSP **clean, readable, maintainable**
    
- Perfect companion to **EL**
    
- Enforces **MVC architecture**
    
- Industry-standard approach
    
