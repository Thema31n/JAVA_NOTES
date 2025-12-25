## **Definition**

> _Clients should not be forced to depend on interfaces they do not use._

Simplest meaning:

### ✔ Don't make large, “fat” interfaces.

### ✔ Split them into smaller, more specific ones.

### ✔ Classes should implement only what they actually need.

---

# 🧠 **Why ISP exists?**

Before ISP, many designs had interfaces like:

```java
interface Worker {
    void work();
    void eat();
    void sleep();
}
```

Now imagine a **Robot** implementing this:

```java
class Robot implements Worker {
    public void work() { ... }
    public void eat() { ... }     // ❌ robot doesn't eat
    public void sleep() { ... }   // ❌ robot doesn't sleep
}
```

This violates ISP because Robot is **forced** to implement methods it does not need.

---

# ⭐ **What ISP says you should do**

Split the interface:

```java
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

interface Sleepable {
    void sleep();
}
```

Now each class only implements what it really needs.

---

# ⭐ **Real-world Example: Bad Interface Design (ISP Violation)**

```java
interface Printer {
    void print();
    void scan();
    void fax();
}
```

If you create:

```java
class SimplePrinter implements Printer {
    public void print() { ... }
    public void scan() { throw new UnsupportedOperationException(); }
    public void fax()  { throw new UnsupportedOperationException(); }
}
```

❌ This is an ISP violation.  
A simple printer is being forced to implement scan/fax which it does not support.

---

# ⭐ **Correct Design Following ISP**

Split the interface:

```java
interface Printable {
    void print();
}

interface Scannable {
    void scan();
}

interface Faxable {
    void fax();
}
```

Now:

```java
class SimplePrinter implements Printable {
    public void print() { ... }
}
```

Perfect. ✔

---

# ⭐ **Why ISP is important**

### ✔ Cleaner, smaller interfaces

Focused, easier to understand.

### ✔ Prevents unnecessary method implementation

No more empty or exception-throwing methods.

### ✔ Helps avoid breaking classes

If you add a method to a big interface, **all** implementations break.  
Small interfaces avoid this.

### ✔ Improves flexibility

Classes can be combined using multiple small interfaces.

### ✔ Encourages composition

Code becomes more modular.

---

# ⭐ **ISP Summary Table**

|Question|ISP Answer|
|---|---|
|What to avoid?|Fat interfaces with many methods|
|What to do?|Split interfaces by behavior|
|Goal|Classes depend only on what they need|
|Benefit|Cleaner design, fewer bugs, more flexibility|

---

# ⭐ **The Core Message of ISP**

> **Make interfaces small.**  
> **Make them specific.**  
> **Never force a class to implement unused methods.**
