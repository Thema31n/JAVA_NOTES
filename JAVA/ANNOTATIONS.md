
## **1. What Is an Annotation?**

A **Java annotation** is metadata that provides additional information to the compiler, tools, or runtime.

- They **do not affect program logic directly**.
    
- They can be used for **documentation, code analysis, configuration, dependency injection, serialization**, etc.
    

Examples: `@Override`, `@Deprecated`, `@SuppressWarnings`.

---

## **2. Where to Use Annotations**

Annotations can be applied to:

- Classes
    
- Methods
    
- Fields
    
- Parameters
    
- Local variables
    
- Packages
    
- Modules
    
- Constructors
    
- Type uses (e.g., generics, casts)
    

---

# **3. What Can Be Annotated? — `ElementType` Enum**

`java.lang.annotation.ElementType` defines all valid targets:

|ElementType|Meaning|
|---|---|
|**TYPE**|Class, interface, enum|
|**FIELD**|Fields (including enum constants)|
|**METHOD**|Methods|
|**PARAMETER**|Method parameters|
|**CONSTRUCTOR**|Constructors|
|**LOCAL_VARIABLE**|Local variables|
|**ANNOTATION_TYPE**|Another annotation|
|**PACKAGE**|Package declaration|
|**TYPE_PARAMETER**|Generic type parameters|
|**TYPE_USE**|Any use of a type (casts, generics, etc.)|
|**MODULE**|Java 9 modules|
|**RECORD_COMPONENT**|Record components (Java 16+)|

---

## **4. Annotation Lifetime — `RetentionPolicy` Enum**

Controls how long an annotation is kept:

|RetentionPolicy|Meaning|
|---|---|
|**SOURCE**|Removed by compiler → not in `.class` file|
|**CLASS** (default)|In `.class` file → not visible at runtime|
|**RUNTIME**|Available at Runtime via Reflection|

---

# **5. Annotation Basics**

- An annotation starts with **@**.
    
- It can contain **fields (elements)**.
    
- Elements can have **default values**.
    
- Must use `@Target` and `@Retention` to define usage and lifetime.
    

---

# **6. Annotation Format**

### **6.1 Marker Annotation**

No fields:

```java
@interface MyMarker {}
```

Usage:

```java
@MyMarker
class Test {}
```

### **6.2 Single-Element Annotation**

```java
@interface Author {
    String value();
}
```

Usage:

```java
@Author("Ahmed")
class Book {}
```

### **6.3 Full Annotation with Multiple Elements**

```java
@interface Info {
    String name();
    int version() default 1;
}
```

Usage:

```java
@Info(name = "PaymentService", version = 2)
class Service {}
```

---

# **7. Repeatable Annotations**

Java allows repeating the same annotation on one element.

### Step 1: Create the _container_ annotation

```java
@interface Tags {
    Tag[] value();
}
```

### Step 2: Create the repeatable annotation

```java
@Repeatable(Tags.class)
@interface Tag {
    String value();
}
```

### Usage:

```java
@Tag("fast")
@Tag("network")
class Engine {}
```

---

# **8. How to Create a Custom Annotation**

### **Step-by-Step Example**

```java
import java.lang.annotation.*;

@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
public @interface CustomAnnotation {
    String author();
    String date();
    int revision() default 1;
}
```

### Usage:

```java
@CustomAnnotation(
    author = "Ahmed",
    date = "2025-01-01",
    revision = 3
)
public class Demo {
    
    @CustomAnnotation(author = "Ahmed", date = "2025-01-02")
    public void run() {}
}
```

---

# ✔ Summary Checklist

- **Annotations = metadata** used by compiler, tools, frameworks.
    
- Can be placed on many code elements (controlled by `ElementType`).
    
- How long they last = `RetentionPolicy`.
    
- Support default values and multiple elements.
    
- Can be **repeatable** (via `@Repeatable`).
    
- Custom annotations require:
    
    - `@interface` keyword
        
    - `@Target`
        
    - `@Retention`
        
    - Optional fields and default values
        
