# 1️⃣ Types of Queries in JPA (Big Picture)

JPA supports **multiple query styles**, each solving a different problem.

|Type|Purpose|
|---|---|
|`Query`|Generic query|
|`TypedQuery<T>`|Type-safe query|
|`@NamedQuery`|Predefined JPQL|
|Native Query|Raw SQL|
|Criteria API|Dynamic, type-safe|
|JPQL|Object-oriented query language|

---

# 2️⃣ `Query` (Generic Query)

## What It Is

- Non-type-safe query
    
- Returns `Object` or `Object[]`
    

---

## Example

```java
Query query = em.createQuery(
    "SELECT u.name FROM User u"
);

List<String> names = query.getResultList();
```

---

## When to Use

✔ Quick queries  
✔ Prototypes  
❌ Not recommended for large codebases

---

# 3️⃣ `TypedQuery<T>` (RECOMMENDED)

## What It Is

- Type-safe version of `Query`
    
- Compile-time safety
    

---

## Example

```java
TypedQuery<User> query = em.createQuery(
    "SELECT u FROM User u WHERE u.status = :status",
    User.class
);

List<User> users = query
    .setParameter("status", Status.ACTIVE)
    .getResultList();
```

---

## Why It’s Better

✔ No casting  
✔ Safer refactoring  
✔ Cleaner code

---

# 4️⃣ `@NamedQuery`

## What It Is

- JPQL defined at entity level
    
- Parsed at startup
    
- Fail-fast if invalid
    

---

## Example

```java
@Entity
@NamedQuery(
    name = "User.findActive",
    query = "SELECT u FROM User u WHERE u.status = 'ACTIVE'"
)
public class User {
}
```

### Usage

```java
List<User> users =
    em.createNamedQuery("User.findActive", User.class)
      .getResultList();
```

---

## When to Use

✔ Reusable queries  
✔ Frequently used queries  
✔ Performance-critical paths

---

# 5️⃣ Native Query

## What It Is

- Raw SQL
    
- Bypasses JPQL
    

---

## Example

```java
List<User> users = em.createNativeQuery(
    "SELECT * FROM users WHERE status = 'ACTIVE'",
    User.class
).getResultList();
```

---

## When to Use

✔ Complex SQL  
✔ DB-specific features  
✔ Performance tuning

---

## Warning

❌ DB-dependent  
❌ Less portable

---

# 6️⃣ Criteria API

## What It Is

- Programmatic query construction
    
- Type-safe
    
- No string JPQL
    

---

## Example

```java
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<User> cq = cb.createQuery(User.class);
Root<User> root = cq.from(User.class);

cq.select(root)
  .where(cb.equal(root.get("status"), Status.ACTIVE));

List<User> users = em.createQuery(cq).getResultList();
```

---

## When to Use

✔ Dynamic search filters  
✔ Optional parameters  
❌ Not for readability

---

# 7️⃣ What Is JPQL?

## Definition

**JPQL (Java Persistence Query Language)** is:

- Object-oriented query language
    
- Works on **entities and fields**
    
- DB-independent
    

---

## JPQL vs SQL

|JPQL|SQL|
|---|---|
|Entity-based|Table-based|
|Portable|DB-specific|
|Uses object relations|Uses joins|

---

## Example

```java
SELECT u FROM User u WHERE u.email = :email
```

---

# 8️⃣ Key JPQL Features

### 1️⃣ Entity-Based Queries

```java
SELECT u FROM User u
```

---

### 2️⃣ Navigation Through Relationships

```java
SELECT o FROM Order o WHERE o.customer.name = :name
```

---

### 3️⃣ Joins

```java
SELECT o FROM Order o JOIN o.items i WHERE i.price > 100
```

---

### 4️⃣ Aggregation

```java
SELECT COUNT(u) FROM User u
```

---

### 5️⃣ Pagination

```java
query.setFirstResult(0);
query.setMaxResults(10);
```

---

### 6️⃣ Bulk Updates

```java
UPDATE User u SET u.status = 'INACTIVE'
```

⚠️ Bypasses persistence context

---

# 9️⃣ Showing SQL Sent to the Database

## Hibernate Properties

### Show SQL

```properties
spring.jpa.show-sql=true
```

### Format SQL

```properties
spring.jpa.properties.hibernate.format_sql=true
```

### Show Parameters

```properties
logging.level.org.hibernate.SQL=DEBUG
logging.level.org.hibernate.type.descriptor.sql=TRACE
```

---

## Why This Matters

✔ Debugging  
✔ Performance tuning  
✔ Understanding ORM behavior

---

# 🔟 Practical Real-World Exercises

---

## Exercise 1: Find Active Users

```java
TypedQuery<User> query = em.createQuery(
    "SELECT u FROM User u WHERE u.status = :status",
    User.class
);

query.setParameter("status", Status.ACTIVE);
List<User> users = query.getResultList();
```

---

## Exercise 2: Paginated Orders

```java
List<Order> orders = em.createQuery(
    "SELECT o FROM Order o ORDER BY o.createdAt DESC",
    Order.class
)
.setFirstResult(0)
.setMaxResults(20)
.getResultList();
```

---

## Exercise 3: Native Query for Reporting

```java
List<Object[]> results = em.createNativeQuery(
    "SELECT status, COUNT(*) FROM users GROUP BY status"
).getResultList();
```

---

## Exercise 4: Dynamic Search (Criteria API)

```java
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<User> cq = cb.createQuery(User.class);
Root<User> root = cq.from(User.class);

List<Predicate> predicates = new ArrayList<>();

if (status != null) {
    predicates.add(cb.equal(root.get("status"), status));
}

cq.where(predicates.toArray(new Predicate[0]));

List<User> users = em.createQuery(cq).getResultList();
```

---

# 🧠 FINAL MENTAL MODEL

- JPQL = object-oriented SQL
    
- TypedQuery > Query
    
- NamedQuery = reusable & validated
    
- NativeQuery = escape hatch
    
- Criteria API = dynamic power
    
- Always inspect generated SQL
    
