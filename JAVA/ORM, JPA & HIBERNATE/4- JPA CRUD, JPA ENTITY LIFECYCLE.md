# 1️⃣ Inserting a JPA Entity (CREATE)

## What “Insert” Means in JPA

- You **do NOT write INSERT SQL**
    
- You create a Java object
    
- JPA tracks it and generates SQL at the right time
    

---

## Real-World Example: Create a User

```java
User user = new User();
user.setName("Ahmed");
user.setStatus(Status.ACTIVE);
```

At this point:  
❌ No SQL  
❌ No DB interaction

The object exists **only in memory**

---

## Persisting the Entity

```java
em.getTransaction().begin();
em.persist(user);
em.getTransaction().commit();
```

### What Actually Happens Internally

1. `persist()` registers the entity
    
2. Entity becomes **MANAGED**
    
3. INSERT SQL is generated
    
4. SQL is executed at **flush/commit**
    

---

## Important Real-World Rule

> `persist()` ≠ immediate DB insert  
> SQL happens at **flush time**

---

# 2️⃣ Selecting an Entity (READ)

## Basic `find()`

```java
User user = em.find(User.class, 1L);
```

### What Happens

1. JPA checks **persistence context (cache)**
    
2. If not found → SQL SELECT
    
3. Entity becomes **MANAGED**
    

---

## All Types of Find in JPA (WITH REAL EXAMPLES)

---

## 1️⃣ `EntityManager.find(Class<T>, Object id)`

```java
User user = em.find(User.class, 1L);
```

**Behavior**

- Immediate SELECT (if not cached)
    
- Returns `null` if not found
    
- Entity is MANAGED
    

**Real-world usage**

- CRUD operations
    
- Service-layer logic
    
- Safe lookups
    

---

## 2️⃣ `EntityManager.find(Class<T>, Object id, LockModeType)`

```java
User user = em.find(
    User.class,
    1L,
    LockModeType.PESSIMISTIC_WRITE
);
```

**Behavior**

- SELECT with database lock
    
- Prevents concurrent updates
    

**Real-world usage**

- Banking
    
- Inventory systems
    
- Critical consistency paths
    

---

## 3️⃣ `EntityManager.find(Class<T>, Object id, Map<String, Object>)`

```java
Map<String, Object> hints = new HashMap<>();
hints.put("jakarta.persistence.fetchgraph", entityGraph);

User user = em.find(User.class, 1L, hints);
```

**Behavior**

- Uses query hints
    
- Custom fetch strategies
    

**Real-world usage**

- Performance tuning
    
- Controlling lazy/eager loading
    

---

## 4️⃣ `EntityManager.getReference(Class<T>, Object id)`

```java
User user = em.getReference(User.class, 1L);
```

**Behavior**

- No SQL initially
    
- Returns proxy object
    
- SQL runs on first field access
    

```java
user.getName(); // SELECT here
```

**Real-world usage**

- Setting relationships
    
- Avoid unnecessary SELECTs
    

⚠️ Throws exception if entity doesn’t exist (on access)

---

## 5️⃣ JPQL `getSingleResult()`

```java
User user = em.createQuery(
    "SELECT u FROM User u WHERE u.email = :email",
    User.class
).setParameter("email", "a@b.com")
 .getSingleResult();
```

**Behavior**

- Entity-based query
    
- Returns managed entity
    
- Throws exception if not found
    

**Real-world usage**

- Business rules
    
- Lookup by non-ID fields
    

---

## 6️⃣ JPQL `getResultList()`

```java
List<User> users = em.createQuery(
    "SELECT u FROM User u WHERE u.status = :status",
    User.class
).setParameter("status", Status.ACTIVE)
 .getResultList();
```

**Behavior**

- Returns list of managed entities
    

**Real-world usage**

- Dashboards
    
- Filtering
    
- Reporting
    

---

## 7️⃣ Criteria API Find

```java
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<User> cq = cb.createQuery(User.class);
Root<User> root = cq.from(User.class);

cq.select(root)
  .where(cb.equal(root.get("name"), "Ahmed"));

User user = em.createQuery(cq).getSingleResult();
```

**Behavior**

- Type-safe
    
- Programmatic queries
    

**Real-world usage**

- Dynamic search screens
    
- Optional filters
    

---

## 8️⃣ Spring Data JPA `findById()`

```java
Optional<User> user = userRepository.findById(1L);
```

**Behavior**

- Wraps `EntityManager.find()`
    
- Returns `Optional`
    

**Real-world usage**

- Spring Boot services
    
- Clean service logic
    

---

## Persistence Context Cache (VERY IMPORTANT)

```java
User u1 = em.find(User.class, 1L);
User u2 = em.find(User.class, 1L);
```

➡ Only **ONE SQL SELECT**

Because:

- Same EntityManager
    
- Same persistence context
    

---

# 3️⃣ Updating an Entity (UPDATE)

## The Most Important Concept: DIRTY CHECKING

```java
em.getTransaction().begin();

User user = em.find(User.class, 1L);
user.setName("New Name");

em.getTransaction().commit();
```

✔ No `update()`  
✔ No SQL written  
✔ Changes detected automatically

---

## How Dirty Checking Works

1. Entity is MANAGED
    
2. Snapshot stored
    
3. Field changes
    
4. Comparison at flush
    
5. UPDATE SQL generated
    

---

# 4️⃣ Deleting an Entity (DELETE)

```java
em.getTransaction().begin();

User user = em.find(User.class, 1L);
em.remove(user);

em.getTransaction().commit();
```

❌ Cannot remove DETACHED entity

---

# 5️⃣ States of a JPA Entity

---

## NEW / TRANSIENT

```java
User user = new User();
```

- Not managed
    
- No DB row
    

---

## MANAGED / PERSISTENT

```java
em.persist(user);
// or
em.find(User.class, 1L);
```

- Tracked
    
- Auto-updated
    

---

## DETACHED

```java
em.close();
```

- Not tracked
    
- Changes ignored
    

---

## MERGE (Reattach)

```java
User managed = em.merge(user);
```

Returns **new managed instance**

---

## REMOVED

```java
em.remove(user);
```

Scheduled for DELETE

---

# Entity State Flow

```
NEW
 ↓ persist()
MANAGED
 ↓ detach()
DETACHED
 ↓ merge()
MANAGED
 ↓ remove()
REMOVED
 ↓ commit
DELETED
```

---

# 1️⃣1️⃣ Entity Lifecycle Events

|Annotation|Trigger|
|---|---|
|@PrePersist|Before INSERT|
|@PostPersist|After INSERT|
|@PreUpdate|Before UPDATE|
|@PostUpdate|After UPDATE|
|@PreRemove|Before DELETE|
|@PostRemove|After DELETE|
|@PostLoad|After SELECT|

---

## Lifecycle Example

```java
@Entity
public class User {

    @PrePersist
    public void beforeInsert() {
        createdAt = new Date();
    }

    @PreUpdate
    public void beforeUpdate() {
        updatedAt = new Date();
    }
}
```

---

## Entity Listener

```java
@EntityListeners(AuditListener.class)
@Entity
public class User { }
```

---

# 1️⃣4️⃣ Real-World CRUD Flow

```java
User user = new User();   // NEW
em.persist(user);         // MANAGED
em.flush();               // INSERT
em.clear();               // DETACHED
User managed = em.merge(user);
em.remove(managed);       // DELETE
```

---

# ✅ FINAL TAKEAWAY

✔ JPA works with **states**, not SQL  
✔ MANAGED entities are auto-synced  
✔ `find()` vs `getReference()` matters  
✔ SQL executes at flush/commit  
✔ Lifecycle callbacks automate logic
