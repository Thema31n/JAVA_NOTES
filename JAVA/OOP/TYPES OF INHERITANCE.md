# ✅ **1. Single Inheritance**

A class inherits from **one** parent class.

```
A └── B
```

### Example:

```java
class Animal { }

class Dog extends Animal { }
```

✔ Allowed  
✔ Most common  
✔ Simple parent–child relationship

---

# ✅ **2. Multilevel Inheritance**

A class inherits from another class, which inherits from another class.

```
A └── B
    └── C
```

### Example:

```java
class Animal { }

class Mammal extends Animal { }

class Dog extends Mammal { }
```

✔ Allowed  
✔ Forms a chain  
✔ “Grandparent → Parent → Child”

---

# ⚠️ **3. Multiple Inheritance (NOT allowed with classes)**

A class inherits from **more than one class**.

```
A   / \
B       C
 \     /
     D
```

### Example (INVALID in Java):

```java
class A { }
class B { }

class C extends A, B { } // ❌ Not allowed
```

❌ **Java does NOT allow this with classes** because of the _diamond problem_.  
✔ Java **does allow multiple inheritance with interfaces**, though.

### Valid form (with interfaces):

```java
interface A { }
interface B { }

class C implements A, B { } // ✔ Allowed
```

---

# ✅ **4. Hierarchical Inheritance**

Multiple classes inherit from the **same** parent class.

```
      A
     / \
    B   C
   /     \
  D       E
```

### Example:

```java
class Animal { }

class Dog extends Animal { }
class Cat extends Animal { }
class Bird extends Animal { }
```

✔ Allowed  
✔ One parent → multiple children

---

# ⭐ **5. Hybrid Inheritance (Allowed with interfaces only)**

Combination of multiple + multilevel + hierarchical.

Java **disallows hybrid inheritance with classes**,  
but **interfaces** support it.

### Example:

```
Interface A

Interface B extends A

Interface C
Interface D

Class E implements C, D
```

Example in code:

```java
interface A { }

interface B extends A { }

interface C { }
interface D { }

class E implements B, C, D { }
```

✔ Allowed  
✔ Only possible using interfaces  
❌ Not allowed using classes due to multiple inheritance restrictions

---

# 📌 **Final Summary Table**

|Inheritance Type|Allowed in Java?|With Classes|With Interfaces|
|---|---|---|---|
|Single|✔ Yes|✔ Yes|✔ Yes|
|Multilevel|✔ Yes|✔ Yes|✔ Yes|
|Multiple|⚠️ Only with interfaces|❌ No|✔ Yes|
|Hierarchical|✔ Yes|✔ Yes|✔ Yes|
|Hybrid|⚠️ Only with interfaces|❌ No|✔ Yes|
