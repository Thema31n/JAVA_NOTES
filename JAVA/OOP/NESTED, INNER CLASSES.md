# ⭐ **Nested Class (General Term)**

A **nested class** is _any_ class defined **inside another class**.

So **inner classes are a type of nested class**.

Java has _two_ types of nested classes:

---

# ⭐ **1. Static Nested Class**

This is a **nested class declared with `static`**.

### ✔ Characteristics:

- Does **NOT** need an object of outer class to be created.
    
- Cannot access **non-static** members of the outer class directly.
    
- Behaves like a **normal static member**.
    

### ✔ Example:

```java
class Outer {
    static class StaticNested {
        void show() {
            System.out.println("Static nested class");
        }
    }
}
```

Use:

```java
Outer.StaticNested obj = new Outer.StaticNested();
```

---

# ⭐ **2. Inner Class**

A **nested class without `static`** is called an **inner class**.

It is tied to an instance of the outer class.

### ✔ Characteristics:

- Needs an **outer class object** to be created.
    
- Can access **all members** of the outer class, even private ones.
    
- Useful when the inner class is logically part of the outer class.
    

### ✔ Example:

```java
class Outer {
    class Inner {
        void show() {
            System.out.println(a); // can access outer members
        }
    }
}
```

Use:

```java
Outer o = new Outer();
Outer.Inner i = o.new Inner();
```

---

# ⭐ **Types of Inner Classes**

Inner classes have **3 types**:

### ✔ 1. Member Inner Class

Normal inner class inside a class.

### ✔ 2. Local Inner Class

Defined **inside a method**.

### ✔ 3. Anonymous Inner Class

Class without a name, created inline.

---

# 📌 **Main Differences (Summary Table)**

|Feature|Inner Class|Static Nested Class|
|---|---|---|
|Requires outer class instance|✔ Yes|✖ No|
|Can access outer class non-static members|✔ Yes|✖ No|
|Access to outer class static members|✔ Yes|✔ Yes|
|Behavior|Part of object|Part of class|
|Memory|Heavier|Lighter|
|Use case|Tight object relationship|Utility/helper classes|

---

# ⭐ **Easy Explanation**

- **Inner class** = needs an object of the outer class → can use everything from the outer object.
    
- **Static nested class** = belongs to the outer class itself → cannot use outer instance data.
    
