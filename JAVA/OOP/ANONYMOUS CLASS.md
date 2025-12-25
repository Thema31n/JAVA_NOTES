# ⭐ **Anonymous Classes in Java**

An **anonymous class** is a **class without a name** that is **declared and instantiated in a single expression**.

It is typically used when:

- You need a **one-time implementation**
    
- You don’t want to create a **separate named class**
    
- You want **quick customization** of behavior
    

---

## ✅ **Why Use Anonymous Classes?**

Anonymous classes are useful for:

### ✔ One-off behavior

You override methods **only for a single usage**.

### ✔ Reduced boilerplate

No need to create a full class file.

### ✔ Pre–Java 8 callbacks

Heavily used before lambdas (especially in Swing, listeners, threads).

---

## 🧱 **Basic Syntax**

```java
new SuperType() {
    // override methods here
};
```

Where **SuperType** can be:

- an **interface**
    
- an **abstract class**
    
- a **concrete class**
    

---

## ⭐ Example 1 — Implementing an Interface

```java
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running!");
    }
};

r.run();
```

✔ Creates a **one-time implementation** of `Runnable`.

---

## ⭐ Example 2 — Extending a Class (Event Handling)

```java
Button btn = new Button("Click me");

btn.addActionListener(new ActionListener() {
    @Override
    public void actionPerformed(ActionEvent e) {
        System.out.println("Button clicked!");
    }
});
```

✔ Very common in **Swing/AWT** programming.

---

## ⭐ Example 3 — Overriding a Method Temporarily

```java
Animal cat = new Animal() {
    @Override
    void speak() {
        System.out.println("Anonymous Meow!");
    }
};

cat.speak();
```

✔ No subclass created — behavior customized inline.

---

## 📌 **Key Characteristics**

### ✔ No class name

Defined inline and used immediately.

---

### ✔ Can access **final or effectively final** variables

```java
int x = 10;

Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println(x); // allowed (effectively final)
    }
};
```

---

### ✔ Can extend **one class** OR implement **one interface**

(Java does not support multiple inheritance.)

---

### ✔ Cannot have constructors

But you **can use instance initializer blocks**:

```java
new MyClass() {
    {
        System.out.println("Init block instead of constructor");
    }
};
```

---

## ⚠ **Limitations of Anonymous Classes**

- Harder to read if overused
    
- Cannot be reused
    
- No constructors
    
- Verbose syntax
    
- Mostly replaced by **lambdas** for functional interfaces
    

---

## ⭐ Anonymous Classes vs Lambdas

|Feature|Anonymous Class|Lambda|
|---|---|---|
|Extend a class|✔ Yes|❌ No|
|Override multiple methods|✔ Yes|❌ No|
|Syntax|Verbose|Concise|
|Functional interface|✔ Yes|✔ Yes|
|Readability|Lower|Higher|

---

## ✔ **When to Use Anonymous Classes Today**

Use anonymous classes when you need to:

- Extend a **concrete or abstract class**
    
- Override **more than one method**
    
- Add **small custom logic** inline
    
- Work with **legacy APIs**
    

Otherwise, prefer **lambdas**.

---

## ⭐ **Final Summary**

- Anonymous classes are **unnamed, one-time-use classes**
    
- Defined and instantiated **in a single expression**
    
- Used heavily before Java 8
    
- Still useful when:
    
    - lambdas are not sufficient
        
    - class inheritance is required
        
