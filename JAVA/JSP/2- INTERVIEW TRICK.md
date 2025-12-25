# **Why going to `/pro.jsp` is different from going to `/pro`**

---

## **1. What `/pro.jsp` Means**

When you type:

```
http://localhost:8080/app/pro.jsp
```

### ✅ What the server does

1. The server **directly looks for a JSP file** named `pro.jsp`
    
2. JSP engine:
    
    - Translates `pro.jsp` → Servlet
        
    - Compiles it (if not already compiled)
        
    - Executes `_jspService()`
        
3. Returns **HTML response**
    

### 🔹 Example

```jsp
<!-- pro.jsp -->
<h1>Product Page</h1>
```

➡ Browser directly receives the rendered HTML.

---

## **2. What `/pro` Means**

When you type:

```
http://localhost:8080/app/pro
```

### ❌ There is **no file extension**

So the server must decide:

- Is `/pro` a **Servlet mapping**?
    
- Is it a **Controller route**?
    
- Is it mapped via `web.xml` or annotations?
    

### 🔹 Typical Case

```java
@WebServlet("/pro")
public class ProductServlet extends HttpServlet {
    protected void doGet(...) {
        request.getRequestDispatcher("pro.jsp")
               .forward(request, response);
    }
}
```

➡ `/pro` is handled by a **Servlet**, not a JSP.

---

## **3. Key Difference (Simple Table)**

|URL|Who Handles It|Purpose|
|---|---|---|
|`/pro.jsp`|JSP Engine|Direct view rendering|
|`/pro`|Servlet / Controller|Logic + routing|

---

## **4. Real-World MVC Example**

### 🔹 Best Practice (MVC)

```
Browser → /pro → Servlet → pro.jsp → HTML
```

**Servlet**

```java
@WebServlet("/pro")
public class ProductServlet extends HttpServlet {
    protected void doGet(HttpServletRequest req, HttpServletResponse res) {
        req.setAttribute("price", 100);
        req.getRequestDispatcher("pro.jsp").forward(req, res);
    }
}
```

**JSP**

```jsp
Price: ${price}
```

👉 User never accesses `pro.jsp` directly.

---

## **5. Why We Avoid Direct `.jsp` Access**

### ❌ Problems with `/pro.jsp`

- Bypasses business logic
    
- Security risk
    
- Hard to maintain
    
- Breaks MVC
    

### ✅ Benefits of `/pro`

- Centralized logic
    
- Validation & security
    
- Clean URLs
    
- Easy maintenance
    

---

## **6. Real Website Analogy**

Think of a **restaurant** 🍽️:

- `/pro.jsp` → **Kitchen directly**
    
- `/pro` → **Waiter (Servlet)** → Kitchen → Dish
    

You never go directly to the kitchen.

---

## **7. Internal Processing Comparison**

### `/pro.jsp`

```
Browser → JSP Engine → HTML
```

### `/pro`

```
Browser → Servlet → JSP → HTML
```

---

## **8. Why `/pro` is More Professional**

- SEO-friendly
    
- Clean URLs
    
- Framework standard (Spring MVC)
    
- Easier migration
    
- Better security
    

Example:

```
/product/123
```

instead of

```
/product.jsp?id=123
```

---

## **9. Interview-Ready Answer (Short)**

> `/pro.jsp` directly invokes a JSP page, while `/pro` is usually mapped to a Servlet or controller that processes logic and forwards to a JSP. Using `/pro` follows MVC architecture, improves security, and provides clean URLs.

---

## **10. Summary**

✔ `/pro.jsp` = Direct JSP access  
✔ `/pro` = Controller-based routing  
✔ `/pro` is **recommended**  
✔ `.jsp` should stay hidden inside `WEB-INF`
