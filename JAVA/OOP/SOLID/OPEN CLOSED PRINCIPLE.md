## **Definition**

> _Software entities (classes, modules, methods) should be **open for extension** but **closed for modification**._

This means:

- You **should be able to add new behavior**
    
- **WITHOUT modifying existing code**
    

Why?

- Code remains stable
    
- Less risk of bugs
    
- Easier to add future features
    

---

# ⭐ **What “open for extension” means**

You can **extend functionality** through:

- **Inheritance**
    
- **Interfaces**
    
- **Polymorphism**
    
- **Composition**
    
- **Strategy Pattern**, etc.
    

Meaning:  
You **ADD NEW CLASSES** instead of editing existing ones.

---

# ⭐ **What “closed for modification” means**

Once a class is **tested and working**, you should **not touch it**.

Why?

- Modifying existing code risks introducing bugs
    
- Forces retesting
    
- Can break other dependent modules
    

---

# ⭐ 💥 **Example of an OCP Violation (Wrong Design)**

```java
class AreaCalculator {

    double area(Object shape) {

        if (shape instanceof Circle) {
            Circle c = (Circle) shape;
            return Math.PI * c.r * c.r;
        }

        if (shape instanceof Rectangle) {
            Rectangle r = (Rectangle) shape;
            return r.w * r.h;
        }

        return 0;
    }
}
```

### ❌ Problems

- Adding a new shape (Triangle, Square…) requires **modifying AreaCalculator**
    
- Violates OCP
    
- Hard to maintain and scale
    

---

# ⭐ ✔ **Correct Design (Follows OCP)**

### Step 1: Create an abstraction

```java
interface Shape {
    double area();
}
```

### Step 2: Implement behavior in each class

```java
class Circle implements Shape {
    double r;

    public double area() {
        return Math.PI * r * r;
    }
}
```

```java
class Rectangle implements Shape {
    double w, h;

    public double area() {
        return w * h;
    }
}
```

### Step 3: Calculator depends on abstraction

```java
class AreaCalculator {

    double calculate(Shape shape) {
        return shape.area();
    }
}
```

💥 Now you can add `Triangle`, `Square`, etc.  
**WITHOUT modifying `AreaCalculator`.**

✔ Closed for modification  
✔ Open for extension

---

# ⭐ **Real-World Analogy**

A **smart TV** uses apps:

- Adding new apps → extending functionality
    
- No hardware modification → closed to modification
    

That’s exactly OCP.

---

# ⭐ **Why OCP Matters**

### ✔ Reduces bugs

Stable code remains untouched.

### ✔ Increases flexibility

New features = new classes.

### ✔ Improves maintainability

Less regression risk.

### ✔ Enhances testability

Each extension tested independently.

---

# ⭐ **Common Ways to Achieve OCP in Java**

- ✔ Interfaces & abstract classes
    
- ✔ Strategy Pattern
    
- ✔ Decorator Pattern
    
- ✔ Factory Pattern
    
- ✔ Enums with behavior
    

---

# ⭐ **Summary Table**

|Concept|Meaning|
|---|---|
|Open|You can add new behavior|
|Closed|Existing code remains unchanged|
|Achieved by|Polymorphism, interfaces, inheritance|
|Benefit|Stable, extensible, maintainable code|
