## **1. Custom Tags in JSP – Overview**

**Custom Tags** allow developers to create **their own JSP tags** (similar to JSTL tags) to **encapsulate reusable logic** and **remove Java code from JSP pages**.

Instead of writing logic repeatedly:

```jsp
<%
if(user != null){
   out.print("Welcome " + user.getName());
}
%>
```

You create a custom tag:

```jsp
<app:welcome user="${user}" />
```

### 🔹 Why Custom Tags Exist

- Scriptlets are bad practice
    
- JSTL does not cover all use cases
    
- Reusability
    
- Clean, readable JSP
    
- MVC compliance
    

👉 Custom tags = **Reusable UI logic**

---

## **2. Tag Library (TLD – Tag Library Descriptor)**

A **Tag Library** is a collection of custom tags defined using a **TLD file** (`.tld`).

### 🔹 Purpose of TLD

- Defines:
    
    - Tag name
        
    - Tag handler class
        
    - Attributes
        
    - Body type
        

### 🔹 Example: `mytags.tld`

```xml
<taglib>
  <tlib-version>1.0</tlib-version>
  <short-name>app</short-name>
  <uri>/WEB-INF/mytags.tld</uri>

  <tag>
    <name>welcome</name>
    <tag-class>com.app.tags.WelcomeTag</tag-class>
    <body-content>empty</body-content>
    <attribute>
      <name>user</name>
      <required>true</required>
      <rtexprvalue>true</rtexprvalue>
    </attribute>
  </tag>
</taglib>
```

---

## **3. Tag Handler**

A **Tag Handler** is a **Java class** that implements the logic of a custom tag.

### 🔹 Responsibilities

- Read attributes
    
- Process logic
    
- Generate output
    
- Control tag lifecycle
    

---

## **4. Classic Tag Handlers**

Classic tag handlers are based on **`javax.servlet.jsp.tagext.Tag`** or **`BodyTag`**.

### 🔹 Characteristics

- Verbose
    
- Complex lifecycle methods
    
- Hard to maintain
    
- Older style (JSP 1.x)
    

### 🔹 Example (Classic)

```java
public class HelloTag extends TagSupport {
    public int doStartTag() {
        JspWriter out = pageContext.getOut();
        out.print("Hello World");
        return SKIP_BODY;
    }
}
```

❌ Not recommended for new projects.

---

## **5. Simple Tag Handlers (Recommended)**

Introduced in **JSP 2.0**, based on **`SimpleTag`** API.

### 🔹 Advantages

- Cleaner
    
- Less boilerplate
    
- Easy to read
    
- EL-friendly
    

### 🔹 Base Class

```java
extends SimpleTagSupport
```

---

### 🔹 Example: Simple Custom Tag

**Tag Handler**

```java
public class WelcomeTag extends SimpleTagSupport {
    private User user;

    public void setUser(User user) {
        this.user = user;
    }

    @Override
    public void doTag() throws IOException {
        JspWriter out = getJspContext().getOut();
        out.print("Welcome " + user.getName());
    }
}
```

**JSP**

```jsp
<app:welcome user="${user}" />
```

---

## **6. Simple vs Classic Tag Handlers**

|Feature|Classic|Simple|
|---|---|---|
|API|Tag / BodyTag|SimpleTag|
|Complexity|High|Low|
|Boilerplate|More|Less|
|Recommended|❌|✅|

---

## **7. Advantages & Disadvantages of Custom Tags**

### ✅ Advantages

- Clean JSP pages
    
- Reusable logic
    
- MVC compliance
    
- Encapsulation
    
- EL support
    
- Maintainable
    

### ❌ Disadvantages

- Initial setup complexity
    
- Learning curve
    
- Overkill for small apps
    
- Harder debugging
    

---

## **8. JSP Custom Tags API**

### 🔹 Key Interfaces & Classes

|API|Purpose|
|---|---|
|Tag|Basic classic tag|
|BodyTag|Tag with body|
|SimpleTag|Simplified tag|
|TagSupport|Helper class|
|SimpleTagSupport|Helper class|
|JspContext|Access JSP scopes|
|JspWriter|Write output|
|JspFragment|Tag body|

---

## **9. Simple Attributes**

Simple attributes are **primitive or object values** passed to tags.

### 🔹 Example

```jsp
<app:price value="100" currency="USD" />
```

**Handler**

```java
private int value;
private String currency;

public void setValue(int value) {
    this.value = value;
}

public void setCurrency(String currency) {
    this.currency = currency;
}
```

**Output**

```
Price: 100 USD
```

---

## **10. Fragment Attributes (JspFragment)**

Fragment attributes allow **passing JSP body content** to tags.

### 🔹 Real-World Example

Reusable layout wrapper.

**JSP**

```jsp
<app:panel title="Login">
   <form>...</form>
</app:panel>
```

---

### 🔹 Handler

```java
public class PanelTag extends SimpleTagSupport {
    private String title;

    public void setTitle(String title) {
        this.title = title;
    }

    @Override
    public void doTag() throws IOException, JspException {
        JspWriter out = getJspContext().getOut();
        out.print("<h3>" + title + "</h3>");
        getJspBody().invoke(null);
    }
}
```

---

## **11. Dynamic Attributes**

Dynamic attributes allow **unknown attribute names** at runtime.

### 🔹 Used for HTML-like flexibility

**Handler**

```java
public class InputTag extends SimpleTagSupport 
        implements DynamicAttributes {

    private Map<String, Object> attrs = new HashMap<>();

    @Override
    public void setDynamicAttribute(String uri, String name, Object value) {
        attrs.put(name, value);
    }

    @Override
    public void doTag() throws IOException {
        JspWriter out = getJspContext().getOut();
        out.print("<input ");
        for(String key : attrs.keySet()){
            out.print(key + "='" + attrs.get(key) + "' ");
        }
        out.print("/>");
    }
}
```

---

### 🔹 JSP Usage

```jsp
<app:input type="text" name="username" placeholder="Enter name" />
```

---

## **12. Real-World Custom Tag Example (MVC)**

### 🔹 Problem

Repeated user welcome logic across JSPs.

### 🔹 Solution

Create custom tag:

```jsp
<app:userInfo user="${user}" />
```

### 🔹 Benefits

- Single logic point
    
- Easy updates
    
- Cleaner UI code
    

---

## **13. Custom Tags vs JSTL**

|Feature|JSTL|Custom Tags|
|---|---|---|
|Standard|Yes|No|
|Flexibility|Limited|Unlimited|
|Java Code|No|Yes (inside tag)|
|Reusability|Medium|High|

---

## **14. Best Practices**

✔ Use SimpleTagSupport  
✔ Keep logic UI-related  
✔ Avoid business logic  
✔ Combine with EL  
✔ Document tags well

---

## **Conclusion**

- Custom tags extend JSP capabilities
    
- Ideal for reusable UI logic
    
- Remove scriptlets completely
    
- Powerful when JSTL is insufficient
    
- Used in large JSP-based systems
    
