## 🔹 **1. What `static` means**

`static` makes a member belong to the **class**, not the objects.  
So a static member is shared by **all instances** of that class.

---

# 🔹 **2. Static Variables (Class Variables)**

- Shared by all objects of a class.
    
- Stored in the **Method Area** of the JVM.
    
- Created when the class is **loaded**, destroyed when the program ends.
    
- Can be accessed using the class name:
    

```java
ClassName.variable;
```

Example:

```java
static int count = 0;
```

---

# 🔹 **3. Static Methods**

- Can be called without creating an object.
    
- Can only access:
    
    - static variables
        
    - static methods
        
- Cannot use `this` or `super`.
    

Example:

```java
static void printCount() {
    System.out.println(count);
}
```

---

# 🔹 **4. Static Blocks**

- Run **once** when the class is loaded.
    
- Used for initialization of static variables.
    

Example:

```java
static {
    System.out.println("Static block runs once");
}
```

---

# 🔹 **5. Static Nested Classes**

A class declared `static` inside another class:

```java
class Outer {
    static class Inner { }
}
```

✔ Can be created without an instance of `Outer`  
✔ Behaves like a normal class but logically grouped

---

# 🔹 **6. Static Members Are Inherited (but not overridden)**

Static methods can be **hidden**, not overridden.

```java
class A {
    static void show() {}
}

class B extends A {
    static void show() {}
}
```

Calling:

```java
A a = new B();
a.show();  // calls A.show()
```

---

# 🔹 **7. Static Final Variables = Constants**

```java
static final double PI = 3.14;
```

`final` means the value cannot change.

---

# 🔹 **8. Static Import**

Allows using static members without the class name.

### Import one member:

```java
import static java.lang.Math.sqrt;
```

### Import all static members:

```java
import static java.lang.Math.*;
```

Use:

```java
double x = sqrt(25);
double y = PI;
```

✔ Avoids writing `Math.`  
✔ Great for tests (JUnit), math, enums  
❌ Avoid overuse to prevent confusion

---

# 🔹 **9. Common Uses of `static`**

- Utility classes (e.g., `Math`, `Arrays`)
    
- Factories and helpers
    
- Singleton pattern (using static inner class)
    
- Constants
    
- Shared counters or IDs
    

---

# 🔹 **10. Important Notes**

- Static members load when the class is loaded.
    
- Static blocks run only once.
    
- Static methods cannot use instance members.
    
- Overusing static reduces OOP benefits.
    
- Static variables can cause memory leaks in long-running apps.
    

---

# 📚 **Quick Summary Table**

|Feature|Meaning|
|---|---|
|`static variable`|Shared across all objects|
|`static method`|Called without objects|
|`static block`|Runs once at class loading|
|`static class`|Nested class, no outer instance needed|
|`static import`|Use static members without class prefix|
