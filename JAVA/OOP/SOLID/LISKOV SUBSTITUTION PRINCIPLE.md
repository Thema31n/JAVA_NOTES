## **Definition**

> _Objects of a superclass should be replaceable with objects of its subclasses **without breaking the program**._

In simple words:

### ✔ If `B` is a subclass of `A`

### ✔ You should be able to use `B` anywhere `A` is expected

### ✔ Without unexpected behavior or errors

If a subclass **cannot behave like** its parent,  
it **violates LSP**.

---

# ⭐ **Why is LSP important?**

- Maintains correct behavior in inheritance
    
- Prevents broken polymorphism
    
- Ensures subclass is truly a valid type of the parent
    
- Stabilizes code that depends on abstractions
    
- Helps avoid runtime surprises
    

---

# ⭐ **Example of LSP Violation**

### (Famous Example: Rectangle & Square)

```java
class Rectangle {
    protected int width;
    protected int height;

    public void setWidth(int w) {
        width = w;
    }

    public void setHeight(int h) {
        height = h;
    }
}
```

```java
class Square extends Rectangle {

    @Override
    public void setWidth(int w) {
        width = height = w;
    }

    @Override
    public void setHeight(int h) {
        width = height = h;
    }
}
```

Now watch what happens:

```java
Rectangle r = new Square();
r.setWidth(5);
r.setHeight(10);

System.out.println(r.width);   // 10 ?
System.out.println(r.height);  // 10 ?
```

This is **wrong behavior** for a rectangle.  
The subclass **Square** violates the parent class expectations.

❌ Substitution failed → **LSP violated**

---

# ⭐ **Correct Fix**

Instead of inheriting, use **composition**, not inheritance:

```java
class Square {
    int side;
}

class Rectangle {
    int width;
    int height;
}
```

Or extract a proper interface.

---

# ⭐ **Another Example of LSP Violation**

```java
class Bird {
    void fly() { }
}
```

```java
class Penguin extends Bird {

    @Override
    void fly() {
        throw new UnsupportedOperationException("Penguins cannot fly");
    }
}
```

Usage:

```java
Bird b = new Penguin();
b.fly();   // 💥 Runtime crash
```

LSP is broken because **Penguin is not a true Bird** in this hierarchy.

---

# ⭐ **What counts as an LSP violation?**

A subclass should **NOT**:

### ❌ Strengthen preconditions

Child should not require **more conditions** than parent.

### ❌ Weaken postconditions

Child should not guarantee **less** than parent.

### ❌ Throw unexpected exceptions

Child should not throw exceptions not declared/expected by parent.

### ❌ Break invariants

Rules of the parent class must still hold.

---

# ⭐ **How to Respect LSP**

### ✔ Use inheritance only when the subtype is truly the same kind

“A Square **is not** a Rectangle.”

### ✔ Follow the “is-a” relationship strictly

### ✔ Ensure overridden methods preserve behavior

### ✔ Prefer interfaces for polymorphism

### ✔ Use composition when inheritance is questionable

---

# ⭐ **Real-World Analogy**

Imagine a function designed for **cars**:

- It expects every car can:
    
    - start
        
    - stop
        
    - accelerate
        

Now create a subclass **ToyCar**:

- Cannot start (no engine)
    
- Cannot accelerate
    

Using `ToyCar` where a real `Car` is expected breaks the program.

❌ **ToyCar violates LSP**

---

# ⭐ **Summary**

|Concept|Meaning|
|---|---|
|LSP|Subtypes must be usable in place of supertypes|
|Goal|Safe inheritance + stable polymorphism|
|Violation|Subclass breaks expected parent behavior|
|Fix|Proper inheritance or composition|
