## ⭐ **ABSTRACT CLASS — What it CAN contain**

### ✔ Abstract Methods

Methods without implementation that **must be overridden** by subclasses.

```java
abstract void run();
```

---

### ✔ Concrete (Normal) Methods

Methods with full implementation.

```java
void walk() {
    // normal method
}
```

---

### ✔ Constructors

Abstract classes **can have constructors**.

> You cannot instantiate an abstract class,  
> but constructors are used when subclasses are created.

```java
abstract class Animal {
    Animal() {
        System.out.println("Animal created");
    }
}
```

---

### ✔ Instance Variables

Fields that belong to object instances.

```java
int speed;
```

---

### ✔ Static Variables

Class-level fields.

```java
static int count;
```

---

### ✔ Static Methods

Methods that belong to the class, not instances.

```java
static void info() {
    // static method
}
```

---

### ✔ Final Methods

Methods that **cannot be overridden** by subclasses.

```java
final void stop() {
    // cannot be overridden
}
```

---

### ✔ Any Access Modifiers

Abstract classes support:

- `public`
    
- `protected`
    
- `private`
    

---

### ✔ Code Blocks

Both static and instance initializer blocks are allowed.

```java
static {
    // static block
}

{
    // instance initializer block
}
```

---

## ❌ **ABSTRACT CLASS — What it CANNOT contain**

### ✖ Cannot Be Instantiated

You cannot create objects directly.

```java
new Animal(); // ❌ compile-time error
```

---

### ✖ Cannot Be Final

Because final classes cannot be inherited.

```java
final abstract class Animal { } // ❌ illegal
```

---

# ⭐ **INTERFACE — What it CAN contain**

---

### ✔ Abstract Methods (Default Before Java 8)

All methods are **implicitly public and abstract**.

```java
void run(); // public abstract by default
```

---

### ✔ Default Methods (Java 8+)

Concrete methods with implementation.

```java
default void walk() {
    // default behavior
}
```

---

### ✔ Static Methods (Java 8+)

Belong to the interface itself.

```java
static void info() {
    // static interface method
}
```

---

### ✔ Private Methods (Java 9+)

Used internally by default methods.

```java
private void helper() {
    // helper logic
}
```

---

### ✔ Constants (public static final)

All fields in interfaces are **implicitly constants**.

```java
int MAX = 10; // public static final
```

---

## ❌ **INTERFACE — What it CANNOT contain**

---

### ✖ Constructors

Interfaces cannot be instantiated.

```java
interface Test {
    Test() { } // ❌ illegal
}
```

---

### ✖ Instance Variables

Only constants are allowed.

```java
int x; // ❌ must be public static final
```

---

### ✖ Instance Initializer Blocks

Only classes support initializer blocks.

---

### ✖ Protected or Private Fields

All fields are automatically:

```java
public static final
```

---

### ✖ Normal Concrete Instance Methods

Methods must be:

- abstract
    
- default
    
- static
    

---

# ⭐ **Comparison Table**

|Feature|Abstract Class|Interface|
|---|---|---|
|Abstract methods|✔ Yes|✔ Yes|
|Concrete methods|✔ Yes|✔ Default only|
|Static methods|✔ Yes|✔ Yes|
|Private methods|✔ Yes|✔ Yes (Java 9+)|
|Constructors|✔ Yes|✖ No|
|Instance variables|✔ Yes|✖ No|
|Static variables|✔ Yes|✔ Constants only|
|Multiple inheritance|✖ No|✔ Yes|
|Can be instantiated|✖ No|✖ No|

---

# ⭐ **When to Use Which?**

---

## ✔ Use **Abstract Class** when:

- You need **shared state (fields)**
    
- You want **partial implementation**
    
- You need **constructors**
    
- Classes are **closely related**
    
- You want controlled inheritance
    

---

## ✔ Use **Interface** when:

- You need **multiple inheritance**
    
- You want **pure behavior contracts**
    
- You want **loosely coupled design**
    
- You define **capabilities** (Runnable, Comparable, Serializable)
    

---

# ⭐ **One-Line Rule to Remember**

> **Abstract class = "is-a with shared state"**  
> **Interface = "can-do behavior"**
