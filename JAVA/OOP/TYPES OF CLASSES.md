# **1. Top-Level Classes**

## 1.1 **Normal Class**

A standard class with no restrictions.

```java
public class Car {
    void drive() {
        System.out.println("Driving...");
    }
}
```

---

## 1.2 **Abstract Class**

Cannot be instantiated; may contain abstract methods.

```java
abstract class Animal {
    abstract void sound();
}
```

---

## 1.3 **Final Class**

Cannot be extended (no subclasses).

```java
public final class MathUtils {
    public static int sum(int a, int b) {
        return a + b;
    }
}
```

---

## 1.4 **Public Class**

Accessible everywhere.

```java
public class Student { }
```

---

## 1.5 **Package-Private Class** (default)

No access modifier → visible only within its package.

```java
class Helper { }
```

---

# ✅ **2. Nested Classes**

## 2.1 **Static Nested Class**

Does NOT require an instance of the outer class.

```java
class Outer {
    static class Helper {
        void show() {
            System.out.println("Static nested");
        }
    }
}
```

---

## 2.2 **Inner Class** (Non-static)

Requires an instance of the outer class.

```java
class Outer {
    class Inner {
        void hello() {
            System.out.println("Inner class");
        }
    }
}
```

Usage:

```java
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
```

---

# ✅ **3. Local Class**

A class defined **inside a method**.

```java
void process() {
    class Local {
        void run() {
            System.out.println("Local class");
        }
    }

    Local l = new Local();
    l.run();
}
```

---

# ✅ **4. Anonymous Class**

A class with **no name**, created as an expression.

### Implements interface

```java
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running...");
    }
};
```

### Extends abstract class

```java
Animal cat = new Animal() {
    @Override
    void sound() {
        System.out.println("Meow");
    }
};
```

### Extends concrete class

```java
Car fastCar = new Car() {
    @Override
    void drive() {
        System.out.println("Fast driving!");
    }
};
```

---

# ✅ **5. Special Class-Like Types**

## 5.1 **Enum**

Defines fixed constants.

```java
enum Direction {
    NORTH, SOUTH, EAST, WEST
}
```

---

## 5.2 **Record** (Java 16+)

Immutable data class with automatic constructor & methods.

```java
public record Point(int x, int y) { }
```

---

## 5.3 **Annotation Type**

Used to mark classes/methods.

```java
public @interface Test { }
```

---

# 🚫 **Types you CANNOT use for anonymous classes**

- Final classes
    
- Enums
    
- Annotations
    
- Primitive types
    

---

# 🧠 **SUMMARY: All Java Class Types**

|Category|Types|
|---|---|
|**Top-Level Classes**|Normal, Abstract, Final, Public, Package-Private|
|**Nested Classes**|Static Nested, Inner|
|**Local Classes**|Defined inside methods|
|**Anonymous Classes**|Implementing interface / Extending abstract / Extending concrete|
|**Special Types**|Enums, Records, Annotations|
