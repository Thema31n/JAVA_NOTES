A **POJO** class means **Plain Old Java Object**.  
It’s a simple, ordinary Java class with **no special rules**, **no inheritance requirements**, and **no framework dependencies**.

Think of a POJO as:  
👉 a clean, lightweight data container  
👉 just fields + constructors + getters/setters

---

# 🔹 **Definition of a POJO**

A POJO is a class that:

✔ does **not** extend any special classes  
✔ does **not** implement any mandatory interfaces  
✔ does **not** require annotations  
✔ simply stores data or represents an entity

---

# 🔹 **Example of a POJO**

```java
public class Person {
    private String name;
    private int age;

    public Person() {}                       // default constructor

    public Person(String name, int age) {    // parameterized constructor
        this.name = name;
        this.age = age;
    }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```

This is a pure POJO.

---

# 🔹 **POJO Characteristics**

|Feature|Meaning|
|---|---|
|No restrictions|No need to extend a framework class|
|No annotations required|Works without @Entity, @Service, etc.|
|Encapsulated fields|Private fields + getters + setters|
|Simple|Only contains data, no heavy logic|
|Framework-independent|Works in any Java environment|

---

# 🔹 **POJO vs JavaBean (Important Difference!)**

|POJO|JavaBean|
|---|---|
|Simple Java class|Must follow strict rules|
|Any visibility for fields|Fields must be private|
|Getters/setters optional|Getters/setters required|
|Constructors can vary|Must have a no-arg constructor|
|No naming rules|Must follow naming conventions|

So **every JavaBean is a POJO**, but **not every POJO is a JavaBean**.

---

# 🔹 **Why POJOs are Useful?**

✔ They give **clean code**  
✔ They are easy to test  
✔ They avoid framework lock-in  
✔ They model real-world data  
✔ They are used everywhere (DTOs, Models, Entities, etc.)

Examples:

- Spring models
    
- Hibernate entities
    
- REST API request/response objects
    

---

# 🔹 **Where POJOs are used**

- MVC models
    
- Database entities (before adding @Entity)
    
- DTOs (Data Transfer Objects)
    
- Config classes
    
- JSON/XML serialization
    

---

# 📘 **Summary**

A **POJO class** is a simple, lightweight Java object with **plain fields** and **no special requirements**. It represents data cleanly without depending on frameworks.

---

Send more notes anytime — same rules, same clean format ✔️