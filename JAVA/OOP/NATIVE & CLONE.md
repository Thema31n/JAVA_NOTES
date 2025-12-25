## ✅ What is `native`?

`native` tells Java that a method is **implemented in another language**, such as:

- C
    
- C++
    
- Assembly
    

Java only declares the method; the real implementation exists in a native library (`.dll`, `.so`, `.dylib`).

### Example:

```java
public class Test {
    public native void printSomething();
}
```

This means:

- Java knows the method exists
    
- But Java **does not** implement it
    
- Implementation must be in a **native library**
    

You must load the native code using:

```java
static {
    System.loadLibrary("MyLibrary");
}
```

---

# 🔥 **2. JNI (Java Native Interface)**

## ✅ What is JNI?

JNI is a framework that allows:

- Java to call native (C/C++) code
    
- C/C++ to call Java code
    

### Why JNI exists?

Because Java cannot directly access:

- low-level OS APIs
    
- hardware devices
    
- high-performance native code
    
- legacy systems written in C/C++
    

### How JNI works (high-level)

1. Java declares a `native` method.
    
2. You generate a C/C++ header using `javac` and `javah` (or `javac -h` in newer JVMs).
    
3. You write the C/C++ implementation.
    
4. You compile to a native library.
    
5. Java loads the library and calls the native code.
    

### Example JNI flow

**Java file:**

```java
public class SystemInfo {
    public native int getCPUCount();

    static {
        System.loadLibrary("sysinfo");
    }
}
```

**Native header (auto-generated):**

```java
JNIEXPORT jint JNICALL Java_SystemInfo_getCPUCount(JNIEnv*, jobject);
```

**Native C implementation:**

```java
JNIEXPORT jint JNICALL Java_SystemInfo_getCPUCount(JNIEnv* env, jobject obj) {
    return 8; // or actual CPU query
}
```

---

# 🔥 Why use JNI?

|Reason|Use Case|
|---|---|
|Performance|Fast C/C++ math libraries|
|Hardware Access|USB, Serial ports, GPU|
|OS Interaction|System calls not supported by Java|
|Legacy Integration|Old C systems|

---

# ❗ Danger of JNI

- Manual memory management (no garbage collector)
    
- Unsafe code can crash JVM
    
- Requires correct architecture (32/64-bit, OS-specific)
    
- Much harder to debug
    

---

# 🌟 **3. `clone()` Method**

## ✔ What is `clone()`?

The `clone()` method in Java creates a **copy of an existing object**.

But cloning in Java is complicated and often misunderstood.

---

# 🚀 **How cloning works**

To use `clone()`:

1. The class must implement `Cloneable`
    
2. You must override `clone()`
    
3. Call `super.clone()`
    

Example:

```java
class Person implements Cloneable {
    int age;
    String name;

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone(); // shallow copy
    }
}
```

Usage:

```java
Person p1 = new Person();
Person p2 = (Person) p1.clone();
```

---

# 🧠 **Types of Cloning**

## 1. **Shallow Clone** (default)

- Copies **primitives**
    
- Copies **references**, not the objects they refer to
    

Example:

```java
class A {
    B b;
}
```

Cloning A:

- new `A` object
    
- same `b` reference → shared object
    

---

## 2. **Deep Clone**

- Everything is copied recursively
    
- No shared objects
    

Manually implemented:

```java
@Override
protected Object clone() throws CloneNotSupportedException {
    Person cloned = (Person) super.clone();
    cloned.address = (Address) address.clone();
    return cloned;
}
```

Or using:

- Serialization
    
- Copy constructor
    

---

# ❗ Problems with `clone()`

- Broken design in Java (Joshua Bloch)
    
- Throws weird exception (`CloneNotSupportedException`)
    
- Needs too much boilerplate
    
- Default shallow copy may cause bugs
    
- Not intuitive
    
- Many developers avoid it
    

---

# ✔ Alternatives to `clone()`

Recommended by many experts:

### 1. **Copy constructor**

```java
public Person(Person other) {
    this.age = other.age;
    this.name = other.name;
}
```

### 2. **Static factory**

```java
public static Person copyOf(Person p) {
    Person clone = new Person();
    clone.age = p.age;
    clone.name = p.name;
    return clone;
}
```

---

# 📚 **Summary Table**

|Feature|Explanation|
|---|---|
|**native**|Declares a method implemented in C/C++|
|**JNI**|Framework allowing Java ↔ Native communication|
|**clone()**|Creates copies of objects (shallow by default)|
|**Cloneable**|Marker interface required for cloning|
|Deep vs Shallow|Deep copies nested objects; shallow does not|
