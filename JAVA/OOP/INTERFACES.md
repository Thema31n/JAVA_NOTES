## **1. What is an Interface?**

- A **contract** that defines _what a class must do_, not how.
    
- Contains **abstract methods**, **default methods**, **static methods**, and **constants**.
    
- A class **implements** an interface.
    

---

## **2. Key Characteristics**

- All methods are **public** by default.
    
- All fields are **public static final** (constants).
    
- A class can **implement multiple interfaces** → supports multiple inheritance of type.
    
- Interfaces cannot be instantiated.
    

---

## **3. Types of Methods in an Interface**

### **a) Abstract methods**

```java
void start();
```

### **b) Default methods**

- Have a body.
    
- Allow adding new methods without breaking old classes.
    

```java
default void log() {
    System.out.println("Logging...");
}
```

### **c) Static methods**

- Called on the interface, not the implementing class.
    

```java
static void help() {
    System.out.println("Help info");
}
```

### **d) Private methods (Java 9+)**

- Used internally by default/static methods.
    

```java
private void internal() { }
```

---

## **4. Why Use Interfaces?**

- Achieve **abstraction**.
    
- Allow **multiple inheritance** of behavior.
    
- Define **capabilities** (e.g., Comparable, Runnable).
    
- Decouple code → easier to test and extend.
    

---

## **5. Interface vs Abstract Class (Interview Hot Topic)**

|Feature|Interface|Abstract Class|
|---|---|---|
|Multiple inheritance|✔ Yes|✖ No|
|Constructors|✖ No|✔ Yes|
|Fields|Only constants|Any fields|
|Methods|abstract, default, static, private|abstract + normal methods|
|When to use?|Capabilities|Shared state + behavior|

---

## **6. Multiple Inheritance Example**

```java
interface A {
    void a();
}

interface B {
    void b();
}

class C implements A, B {
    public void a() {}
    public void b() {}
}
```

---

## **7. Interface Inheritance**

Interfaces can extend other interfaces.

```java
interface A {
    void a();
}

interface B extends A {
    void b();
}
```

---

## **8. Diamond Problem (Resolved in Java)**

Default methods must be overridden if there is a conflict.

```java
interface A {
    default void m() {}
}

interface B {
    default void m() {}
}

class C implements A, B {
    @Override
    public void m() {
        A.super.m(); // resolve conflict
    }
}
```

---

## **9. Marker Interface**

Interface with **no methods** — used for metadata.  
Examples:

- `Serializable`
    
- `Cloneable`
    

```java
interface Tag { }
```

---

## **10. Functional Interfaces (1 abstract method)**

Used in lambdas.  
Examples:

- Runnable
    
- Callable
    
- Comparator
    
- Supplier, Consumer
    

```java
@FunctionalInterface
interface Calculator {
    int add(int a, int b);
}
```

---

# ⭐ **FINAL INTERVIEW SUMMARY**

- Interface = **contract** with abstract + default + static + private methods.
    
- A class can **implement multiple interfaces**.
    
- Fields in interfaces = **public static final** (constants).
    
- Default methods allow extending interfaces without breaking old code.
    
- Interfaces support **multiple inheritance** and **behavioral abstraction**.
    
- Functional interfaces are key for **lambda expressions**.
    
- Marker interfaces provide metadata.
    
- Use interfaces for **capabilities**, not shared state.
    
