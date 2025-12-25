# 1️⃣ Spring Data – Overview

## What is Spring Data?

**Spring Data** is a Spring ecosystem project that **simplifies data access** by reducing boilerplate code and providing a **consistent repository abstraction** across different data stores.

### The Core Problem It Solves

Before Spring Data:

- You wrote repetitive DAO code
    
- You handled `EntityManager`, transactions, queries manually
    
- Each data store (JDBC, JPA, Mongo, Redis) felt completely different
    

Spring Data:

- Eliminates boilerplate
    
- Provides **standard CRUD operations**
    
- Uses **method name conventions** to generate queries
    
- Integrates deeply with Spring transactions and AOP
    

### Key Idea to Memorize

> **Spring Data = Repository abstraction + store-specific implementations**

---

## Real-World Example

An e-commerce system may use:

- **JPA** for orders & users
    
- **Redis** for caching sessions
    
- **MongoDB** for product catalogs
    

Spring Data allows **the same repository style** across all of them.

---

# 2️⃣ Spring Data Modules

Spring Data is **not one thing** — it’s a **family of modules**.

## Common Modules (You MUST know these)

### 1. Spring Data JPA

- For relational databases (MySQL, PostgreSQL, Oracle)
    
- Built on **JPA + Hibernate**
    
- Most commonly used in enterprises
    

### 2. Spring Data JDBC

- Lightweight alternative to JPA
    
- No lazy loading, no persistence context
    
- Better control, simpler mental model
    

### 3. Spring Data MongoDB

- For document databases
    
- Schema-flexible
    
- Common in analytics & content platforms
    

### 4. Spring Data Redis

- For caching, sessions, fast lookups
    
- Often used with REST APIs
    

### 5. Spring Data Elasticsearch

- Search-heavy systems
    
- Logging, monitoring, full-text search
    

---

## Interview Tip

> **Spring Data JPA ≠ Spring Data JDBC**

Many candidates confuse them.  
If you do → red flag in interviews.

---

# 3️⃣ Spring Data JPA – Overview

## What is Spring Data JPA?

Spring Data JPA is a **wrapper around JPA** that:

- Uses **Hibernate (usually)** under the hood
    
- Implements repositories automatically at runtime
    
- Generates queries from method names
    

### Stack Breakdown (Memorize This)

```
Spring Data JPA
   ↓
JPA (Specification)
   ↓
Hibernate (Implementation)
   ↓
Database
```

---

## What Spring Data JPA Gives You

- Auto-implemented repositories
    
- Pagination & sorting
    
- Query derivation
    
- JPQL & native queries
    
- Auditing (createdBy, createdDate)
    
- Specifications (dynamic queries)
    

---

## Real-World Example

User management system:

- Save users
    
- Find by email
    
- Paginate users
    
- Filter users dynamically
    

All **without writing SQL** for common cases.

---

# 4️⃣ Repository Pattern (VERY IMPORTANT)

## What is the Repository Pattern?

> A **repository** mediates between the domain and data mapping layers using a collection-like interface.

### Why It Matters

- Decouples business logic from persistence
    
- Makes code testable
    
- Allows switching data stores easily
    

---

## Classic Repository Pattern (Before Spring Data)

```java
public interface UserRepository {
    User findById(Long id);
    void save(User user);
}
```

You had to implement it manually.

---

## Spring Data Repository Pattern

Spring Data **generates the implementation for you**.

```java
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
}
```

✔ No implementation  
✔ No boilerplate  
✔ Clean domain logic

---

## Interview Tip

> Repository is **NOT** a service  
> Repository contains **data access logic only**

---

# 5️⃣ JPA Repositories (Core Interfaces)

## Repository Hierarchy (MEMORIZE)

```
Repository
 └─ CrudRepository
     └─ PagingAndSortingRepository
         └─ JpaRepository
```

---

## 1. CrudRepository

Basic CRUD:

```java
save()
findById()
findAll()
deleteById()
```

### Use When:

- Simple CRUD
    
- No pagination needed
    

---

## 2. PagingAndSortingRepository

Adds:

```java
findAll(Pageable pageable)
findAll(Sort sort)
```

### Use When:

- Large datasets
    
- APIs returning lists
    

---

## 3. JpaRepository (MOST USED)

Adds:

- Batch operations
    
- Flush
    
- EntityManager features
    

### Real-world usage:

> **90% of production apps use JpaRepository**

---

## Things to Memorize

- `JpaRepository` extends all others
    
- Supports pagination & sorting
    
- Tightly coupled to JPA & Hibernate
    

---

# 6️⃣ Query Methods (REAL-WORLD USAGE)

## Method Name Queries

```java
List<User> findByStatusAndAgeGreaterThan(
    Status status, int age);
```

Generated automatically.

### How It Works

- Spring parses method name
    
- Builds JPQL
    
- Executes at runtime
    

---

## When NOT to Use Method Queries

❌ Very complex queries  
❌ Too many conditions  
❌ Dynamic filters

→ Use **@Query** or **Specifications**

---

## Interview Trap

> Method name queries are parsed **at startup**, not runtime

Wrong answer = fail

---

# 7️⃣ Spring Data JPA – Advantages

## 1. Massive Productivity Boost

- No DAO implementations
    
- Less code, fewer bugs
    

## 2. Clean Architecture

- Clear separation of layers
    
- Easy testing
    

## 3. Strong Spring Integration

- Transactions
    
- AOP
    
- Security
    
- Caching
    

## 4. Industry Standard

- Used in banks, startups, SaaS
    
- Interviewers expect it
    

---

# 8️⃣ Spring Data JPA – Disadvantages (VERY IMPORTANT)

## 1. Hidden Performance Issues

- N+1 query problem
    
- Lazy loading surprises
    

## 2. Less Control Over SQL

- Hibernate decides query plans
    
- Harder to optimize complex queries
    

## 3. Complex Debugging

- Generated queries are not obvious
    
- Stack traces can be deep
    

## 4. Not Always the Best Tool

- High-performance systems may prefer JDBC
    
- Reporting-heavy apps often avoid JPA
    

---

## Real-World Warning

> Many production outages come from **bad JPA usage**, not JDBC

---

# 9️⃣ Interview Gold – Common Questions

### Q1: Why use Spring Data JPA instead of Hibernate directly?

**Answer:**

- Reduces boilerplate
    
- Standard repository abstraction
    
- Faster development
    

---

### Q2: When should you NOT use Spring Data JPA?

**Answer:**

- Complex reporting queries
    
- Performance-critical batch jobs
    
- Legacy schemas with poor design
    

---

### Q3: Difference between CrudRepository and JpaRepository?

**Answer:**

- `JpaRepository` adds pagination, flushing, batch operations
    

---

# 🔟 Things You MUST Memorize

- Spring Data = abstraction, not ORM
    
- Spring Data JPA builds on JPA + Hibernate
    
- Repository pattern separates business & persistence
    
- `JpaRepository` is most used
    
- Method name queries parsed at startup
    
- JPA is powerful but dangerous if misused
    
