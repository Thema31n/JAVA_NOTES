# 1️⃣ Why Do We Need Locking?

## The Real Problem (Concurrency)

In real systems:

- Many users
    
- Many threads
    
- Same data accessed at the same time
    

### Real-World Example (Bank Account)

```
Balance = 100
User A withdraws 50
User B withdraws 50
```

Without locking:

- Both read balance = 100
    
- Both subtract 50
    
- Both write back 50  
    ❌ Final balance = 50 (WRONG)
    

This is called **lost update**.

---

# 2️⃣ What Is a Database Lock?

A **database lock** is a mechanism to:

- Control concurrent access to data
    
- Protect consistency
    
- Enforce isolation
    

Locks are applied on:

- Row
    
- Table
    
- Page (DB-specific)
    

---

# 3️⃣ Physical (Database) Locks

These are **real locks at DB level**.

---

## 3️⃣1️⃣ Shared Lock (READ LOCK)

### Characteristics

- Multiple transactions can read
    
- No transaction can write
    

### Example

```sql
SELECT * FROM users WHERE id = 1 FOR SHARE;
```

### Use Case

- Reporting
    
- Consistent reads
    

---

## 3️⃣2️⃣ Exclusive Lock (WRITE LOCK)

### Characteristics

- Only ONE transaction allowed
    
- Blocks reads & writes from others
    

### Example

```sql
SELECT * FROM users WHERE id = 1 FOR UPDATE;
```

### Use Case

- Money transfer
    
- Inventory update
    

---

# 4️⃣ Logical Locks (Application-Level)

Logical locks are **NOT enforced by DB engine** directly.

They are:

- Version-based
    
- Conflict-detection-based
    
- Managed by ORM (like JPA)
    

➡ This is where **Optimistic Locking** lives.

---

# 5️⃣ Locking in JPA (Big Picture)

JPA supports **two locking strategies**:

|Type|Based On|Blocking|
|---|---|---|
|Optimistic|Versioning|❌|
|Pessimistic|DB locks|✅|

---

# 6️⃣ Optimistic Locking (MOST COMMON)

## Philosophy

> “Conflicts are rare, let everyone work, detect problems later”

---

## How It Works

- No DB locks
    
- Uses a **version column**
    
- Checks version at commit time
    
- If version changed → exception
    

---

## Real-World Use Case

- REST APIs
    
- Microservices
    
- Web apps
    
- High-read, low-write systems
    

---

# 7️⃣ `@Version` Annotation

## Purpose

Marks a field as **version counter**.

### Example

```java
@Entity
public class Account {

    @Id
    private Long id;

    private BigDecimal balance;

    @Version
    private Long version;
}
```

---

## What JPA Does Internally

### Initial state

```
id=1, balance=100, version=0
```

### Transaction A reads → version 0

### Transaction B reads → version 0

Transaction A updates:

```
UPDATE account
SET balance=50, version=1
WHERE id=1 AND version=0;
```

Transaction B updates:

```
UPDATE account
SET balance=50, version=1
WHERE id=1 AND version=0;
```

❌ Affects 0 rows → **OptimisticLockException**

---

## Coding Example (Optimistic Lock)

```java
em.getTransaction().begin();

Account acc = em.find(Account.class, 1L);
acc.setBalance(acc.getBalance().subtract(new BigDecimal("50")));

em.getTransaction().commit(); // may throw OptimisticLockException
```

---

## Important Rules

✔ No DB blocking  
✔ Very scalable  
✔ Must handle exceptions

---

# 8️⃣ Pessimistic Locking

## Philosophy

> “Conflicts WILL happen — block others early”

---

## How It Works

- Uses **real DB locks**
    
- Blocks other transactions
    
- Prevents concurrent access
    

---

# 9️⃣ `LockModeType` ENUM — DETAILED DESCRIPTION (IMPORTANT)

---

## 🔹 OPTIMISTIC

**Description**

- Uses version column
    
- Version is checked **only at commit time**
    
- No version increment unless entity is modified
    

**Behavior**

- No DB lock
    
- Throws `OptimisticLockException` if version changed
    

**When to use**

- Default optimistic locking
    
- Protects against lost updates
    

```java
em.lock(entity, LockModeType.OPTIMISTIC);
```

---

## 🔹 OPTIMISTIC_FORCE_INCREMENT

**Description**

- Uses version column
    
- **Forces version increment even if no data changed**
    

**Behavior**

- Detects concurrent readers
    
- Triggers conflict intentionally
    

**When to use**

- Auditing
    
- Signaling that entity was “touched”
    
- Enforcing strict consistency
    

```java
em.lock(entity, LockModeType.OPTIMISTIC_FORCE_INCREMENT);
```

---

## 🔹 PESSIMISTIC_READ

**Description**

- Acquires a shared DB lock
    
- Other transactions can read
    
- Other transactions CANNOT write
    

**Behavior**

- Blocks updates
    
- No version increment
    

**When to use**

- Consistent reads
    
- Reporting
    
- Prevent updates during read
    

```java
em.find(Entity.class, id, LockModeType.PESSIMISTIC_READ);
```

---

## 🔹 PESSIMISTIC_WRITE

**Description**

- Acquires exclusive DB lock
    
- Blocks both reads and writes
    

**Behavior**

- Strongest lock
    
- High consistency
    

**When to use**

- Financial operations
    
- Inventory management
    
- Seat reservations
    

```java
em.find(Entity.class, id, LockModeType.PESSIMISTIC_WRITE);
```

---

## 🔹 PESSIMISTIC_FORCE_INCREMENT

**Description**

- Acquires DB write lock
    
- Forces version increment
    

**Behavior**

- Combines pessimistic + optimistic
    
- Prevents concurrent access AND signals update
    

**When to use**

- Rare
    
- Extremely strict consistency rules
    

```java
em.lock(entity, LockModeType.PESSIMISTIC_FORCE_INCREMENT);
```

---

# 🔟 Explicit Pessimistic Lock Example

```java
em.getTransaction().begin();

Account acc = em.find(
    Account.class,
    1L,
    LockModeType.PESSIMISTIC_WRITE
);

acc.setBalance(acc.getBalance().subtract(new BigDecimal("50")));

em.getTransaction().commit();
```

---

# 1️⃣1️⃣ Explicit Locking After Find

```java
Account acc = em.find(Account.class, 1L);
em.lock(acc, LockModeType.PESSIMISTIC_WRITE);
```

---

# 1️⃣2️⃣ Optimistic vs Pessimistic Locking (REAL COMPARISON)

|Aspect|Optimistic|Pessimistic|
|---|---|---|
|DB blocking|❌|✅|
|Performance|High|Lower|
|Scalability|Excellent|Limited|
|Failure handling|Exception|Blocking|
|Use case|Web apps|Financial systems|

---

# 1️⃣3️⃣ When to Use Which (IMPORTANT)

### Use Optimistic Locking when:

✔ High concurrency  
✔ Mostly reads  
✔ REST APIs  
✔ Microservices

### Use Pessimistic Locking when:

✔ Money involved  
✔ Inventory updates  
✔ Seat reservations  
✔ Critical consistency

---

# 🧠 FINAL MENTAL MODEL

- **Database locks** = physical protection
    
- **Optimistic locking** = detect conflict
    
- **Pessimistic locking** = prevent conflict
    
- `@Version` = conflict detector
    
- `LockModeType` = explicit control
    
