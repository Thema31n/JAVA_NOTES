# 1️⃣ Why Do We Need Mapping in JPA?

## The Core Problem

Relational databases store data as:

- Tables
    
- Rows
    
- Foreign keys
    

Java works with:

- Objects
    
- References
    
- Collections
    

👉 **Mapping connects these two worlds.**

---

## Real-World Example

Database:

```
orders
customers
```

Java:

```java
Order → Customer
```

Without mapping:

- Manual joins
    
- Manual foreign keys
    
- Boilerplate code
    

With mapping:

```java
order.getCustomer().getName();
```

---

## When to Use Mapping (Use-Case Driven)

✔ You need object navigation  
✔ You need consistency  
✔ You want ORM benefits

❌ For pure reporting queries  
❌ For very complex joins

---

# 2️⃣ Types of Relationships in Relational Databases

|Relationship|Example|
|---|---|
|One-to-One|User ↔ Profile|
|One-to-Many|Customer → Orders|
|Many-to-One|Order → Customer|
|Many-to-Many|Student ↔ Course|

---

# 3️⃣ Mapping Direction (VERY IMPORTANT)

## Unidirectional

- Navigation in ONE direction only
    
- Simpler
    
- Fewer bugs
    

## Bidirectional

- Navigation in BOTH directions
    
- More complex
    
- Must maintain both sides
    

---

### Rule of Thumb

> **Start with unidirectional. Add bidirectional only if needed.**

---

# 4️⃣ Types of Mapping in JPA (Overview)

|JPA Annotation|Relationship|
|---|---|
|`@OneToOne`|One-to-One|
|`@OneToMany`|One-to-Many|
|`@ManyToOne`|Many-to-One|
|`@ManyToMany`|Many-to-Many|

---

# 5️⃣ `@OneToOne` Mapping

## Real-World Example

User ↔ Profile

---

### Database

```
users
profiles (user_id FK)
```

---

### Entity Mapping

```java
@Entity
public class User {

    @Id
    private Long id;

    @OneToOne
    @JoinColumn(name = "profile_id")
    private Profile profile;
}
```

```java
@Entity
public class Profile {

    @Id
    private Long id;
}
```

---

## When to Use

✔ Truly 1-to-1  
❌ Often better as `@ManyToOne`

---

# 6️⃣ `@ManyToOne` Mapping (MOST COMMON)

## Real-World Example

Order → Customer

---

### Database

```
orders (customer_id FK)
customers
```

---

### Entity Mapping

```java
@Entity
public class Order {

    @Id
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "customer_id")
    private Customer customer;
}
```

---

## Why `@ManyToOne` Is Common

- Simple
    
- Efficient
    
- Natural FK mapping
    

---

# 7️⃣ `@OneToMany` Mapping

## Real-World Example

Customer → Orders

---

### Bidirectional Mapping (Recommended)

```java
@Entity
public class Customer {

    @OneToMany(mappedBy = "customer")
    private List<Order> orders = new ArrayList<>();
}
```

```java
@Entity
public class Order {

    @ManyToOne
    @JoinColumn(name = "customer_id")
    private Customer customer;
}
```

---

### Why `mappedBy` Matters

- Avoids extra join table
    
- Defines owning side
    
- Prevents duplicate updates
    

---

# 8️⃣ `@ManyToMany` Mapping

## Real-World Example

Student ↔ Course

---

### Database

```
students
courses
student_course
```

---

### Entity Mapping

```java
@Entity
public class Student {

    @ManyToMany
    @JoinTable(
        name = "student_course",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    private Set<Course> courses = new HashSet<>();
}
```

```java
@Entity
public class Course {

    @ManyToMany(mappedBy = "courses")
    private Set<Student> students = new HashSet<>();
}
```

---

## Real-World Warning

❌ Avoid `@ManyToMany` in complex domains  
✔ Replace with join entity (`Enrollment`)

---

# 9️⃣ Cascading Operations (VERY IMPORTANT)

## What Is Cascade?

Cascade = **propagate operations from parent to child**

---

## Real-World Example

Order → OrderItems

Saving order should save items.

---

### Example

```java
@OneToMany(
    mappedBy = "order",
    cascade = CascadeType.ALL
)
private List<OrderItem> items;
```

---

# 🔟 `CascadeType` Enum (FULL)

|Type|Meaning|
|---|---|
|PERSIST|Save child when parent saved|
|MERGE|Merge child|
|REMOVE|Delete child|
|REFRESH|Refresh child|
|DETACH|Detach child|
|ALL|All of the above|

---

## Real-World Cascade Rules

✔ Cascade from aggregate root  
❌ Never cascade REMOVE to shared entities

---

# 1️⃣1️⃣ Fetch Types (EAGER vs LAZY)

## `FetchType.LAZY` (RECOMMENDED)

```java
@ManyToOne(fetch = FetchType.LAZY)
private Customer customer;
```

- Loaded on access
    
- Better performance
    
- Prevents over-fetching
    

---

## `FetchType.EAGER`

```java
@OneToOne(fetch = FetchType.EAGER)
private Profile profile;
```

- Loaded immediately
    
- Can cause N+1 problem
    

---

## Default Fetch Types

|Mapping|Default|
|---|---|
|@ManyToOne|EAGER|
|@OneToOne|EAGER|
|@OneToMany|LAZY|
|@ManyToMany|LAZY|

> Always override defaults explicitly.

---

# 1️⃣2️⃣ `@JoinColumn`

## Purpose

Defines **foreign key column**.

---

### Example

```java
@JoinColumn(
    name = "customer_id",
    nullable = false
)
```

---

## Real-World Usage

- Naming control
    
- Constraints
    
- FK clarity
    

---

# 1️⃣3️⃣ `@JoinTable`

## Purpose

Defines **join table** (usually for many-to-many).

---

### Example

```java
@JoinTable(
    name = "student_course",
    joinColumns = @JoinColumn(name = "student_id"),
    inverseJoinColumns = @JoinColumn(name = "course_id")
)
```

---

# 1️⃣4️⃣ Real-World Mapping Guidelines (CRITICAL)

✔ Prefer `@ManyToOne`  
✔ Avoid `@ManyToMany`  
✔ Use LAZY by default  
✔ Cascade carefully  
✔ Keep aggregates small  
✔ Control owning side

---

# 🧠 FINAL MENTAL MODEL

- Mapping = object navigation
    
- Owning side controls FK
    
- Cascade ≠ relationship
    
- LAZY protects performance
    
- EAGER can kill your app
    
