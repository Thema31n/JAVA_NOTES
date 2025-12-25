# 🔥 **Java Access Modifiers 

Java provides **four access levels** that control **visibility and accessibility** of classes, methods, variables, and constructors.

### The four access modifiers are:

1. **public**
    
2. **protected**
    
3. **default** (package-private, no keyword)
    
4. **private**
    

---

## 1️⃣ **public**

### ✔ Characteristics

- Accessible **from anywhere**
    
- No restrictions
    
- Most permissive access level
    

### Example

```java
public class A {
    public void show() {
        System.out.println("Public method");
    }
}
```

Usage:

```java
A obj = new A();
obj.show(); // works from any package
```

---

## 2️⃣ **private**

### ✔ Characteristics

- Accessible **only within the same class**
    
- Not visible to subclasses
    
- Strongest encapsulation
    

### Example

```java
class A {
    private int x = 10;

    private void test() {
        System.out.println("private");
    }
}
```

Invalid access:

```java
A a = new A();
a.x;      // ❌ compile-time error
a.test(); // ❌ compile-time error
```

---

## 3️⃣ **default (package-private)**

### ✔ Characteristics

- **No keyword**
    
- Accessible **only within the same package**
    
- Not accessible outside the package
    
- More open than private, but package-restricted
    

### Example

```java
class A {          // default class
    void show() {  // default method
        System.out.println("Default access");
    }
}
```

Access behavior:

```java
// Same package → ✔ allowed
A a = new A();
a.show();

// Different package → ❌ not allowed
```

---

## 4️⃣ **protected**

### ✔ Characteristics

Accessible from:

- Same class
    
- Same package
    
- Subclasses (even in different packages)
    

✔ Most inheritance-friendly modifier

### Example

```java
class A {
    protected void display() {
        System.out.println("protected");
    }
}
```

Subclass in another package:

```java
class B extends A {
    void test() {
        display(); // ✔ allowed
    }
}
```

But this is **not allowed**:

```java
new A().display(); // ❌ not allowed from different package without inheritance
```

---

## ⭐ **Access Level Summary Table (Very Important)**

|Modifier|Same Class|Same Package|Subclass (diff pkg)|Other Packages|
|---|---|---|---|---|
|**private**|✔ YES|❌ NO|❌ NO|❌ NO|
|**default**|✔ YES|✔ YES|❌ NO|❌ NO|
|**protected**|✔ YES|✔ YES|✔ YES|❌ NO|
|**public**|✔ YES|✔ YES|✔ YES|✔ YES|

---

## ⭐ **Access Modifiers for Classes**

Top-level classes can only be:

- **public**
    
- **default (package-private)**
    

|Modifier|Meaning|
|---|---|
|public class|Accessible everywhere|
|default class|Accessible only within the same package|

> 🔹 **private** and **protected** are **NOT allowed** for top-level classes  
> 🔹 Inner classes _can_ use all access modifiers

---

## ⭐ **Access Modifiers for Variables, Methods & Constructors**

Allowed modifiers:

- public
    
- protected
    
- default
    
- private
    

### Common Best Practices

- **private fields** → encapsulation
    
- **public getters/setters** → controlled access
    
- **protected methods** → inheritance support
    
- **public methods** → API exposure
    

---

## ⭐ **Complete Example Showing All Access Levels**

### Package: `animals`

```java
package animals;

public class Animal {

    private int age = 5;            // class only
    int legs = 4;                   // package only
    protected String color = "Brown"; // package + subclasses
    public String name = "Dog";     // everywhere

    private void privateMethod() {}
    void defaultMethod() {}
    protected void protectedMethod() {}
    public void publicMethod() {}
}
```

---

### Package: `zoo`

```java
package zoo;

import animals.Animal;

class Tiger extends Animal {
    void test() {
        // age;   ❌ private
        // legs;  ❌ default (different package)

        System.out.println(color); // ✔ protected
        System.out.println(name);  // ✔ public
    }
}
```

---

## ⭐ **Interview-Ready Summary**

- **public** → accessible everywhere
    
- **private** → same class only
    
- **default** → same package only
    
- **protected** → same package + subclasses
    
- Use:
    
    - `private` → data hiding
        
    - `public` → APIs
        
    - `protected` → inheritance hooks
        
