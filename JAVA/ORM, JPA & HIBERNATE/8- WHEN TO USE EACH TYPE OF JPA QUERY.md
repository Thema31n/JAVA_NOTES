# 1️⃣ `TypedQuery<T>` (JPQL) — **DEFAULT CHOICE**

## When to Use

✔ 80–90% of application queries  
✔ CRUD + business logic  
✔ Fetch entities or simple projections  
✔ When you know query structure at compile time

---

## Real-World Examples

- Find user by email
    
- List orders for a customer
    
- Filter active records
    

```java
TypedQuery<User> q = em.createQuery(
    "SELECT u FROM User u WHERE u.status = :status",
    User.class
);
```

---

## Why It’s Preferred

✔ Type-safe  
✔ Readable  
✔ Refactor-friendly  
✔ Portable across databases

---

## Avoid When

❌ Highly dynamic queries  
❌ DB-specific features needed

---

# 2️⃣ `Query` (Untyped JPQL) — **RARE, QUICK USE**

## When to Use

✔ Prototypes  
✔ Simple scalar queries  
✔ One-off admin queries

---

## Example

```java
Query q = em.createQuery(
    "SELECT COUNT(u) FROM User u"
);
Long count = (Long) q.getSingleResult();
```

---

## Why It’s Rare

❌ No compile-time safety  
❌ Requires casting

---

# 3️⃣ `@NamedQuery` — **STABLE, REUSABLE QUERIES**

## When to Use

✔ Frequently reused queries  
✔ Performance-critical paths  
✔ Queries unlikely to change

---

## Real-World Examples

- `User.findActive`
    
- `Order.findByCustomer`
    
- Reporting queries
    

---

## Example

```java
@NamedQuery(
    name = "User.findActive",
    query = "SELECT u FROM User u WHERE u.status = 'ACTIVE'"
)
```

---

## Why It’s Good

✔ Validated at startup  
✔ Centralized  
✔ Easy to reuse

---

## Avoid When

❌ Query is highly dynamic  
❌ Needs runtime conditions

---

# 4️⃣ Native Query — **ESCAPE HATCH**

## When to Use

✔ Complex SQL (window functions, CTEs)  
✔ DB-specific features  
✔ Performance-critical reporting

---

## Real-World Examples

- Financial reports
    
- Analytics queries
    
- Legacy stored procedures
    

---

## Example

```java
em.createNativeQuery(
    "SELECT * FROM users WHERE status = 'ACTIVE'",
    User.class
);
```

---

## Trade-Offs

✔ Maximum control  
❌ DB-dependent  
❌ Harder to maintain

---

# 5️⃣ Criteria API — **DYNAMIC QUERY BUILDER**

## When to Use

✔ Search screens with optional filters  
✔ Advanced filtering logic  
✔ User-driven queries

---

## Real-World Examples

- Search page with 10+ filters
    
- Admin dashboards
    
- Advanced reports
    

---

## Example Scenario

```java
if (status != null) {
    predicates.add(cb.equal(root.get("status"), status));
}
```

---

## Why It Exists

✔ Type-safe  
✔ Dynamic  
❌ Verbose

---

## Avoid When

❌ Query is static  
❌ Readability matters more than flexibility

---

# 6️⃣ JPQL `JOIN FETCH` — **FIX N+1 PROBLEM**

## When to Use

✔ Need related data immediately  
✔ Avoid lazy loading overhead

---

## Example

```java
SELECT o FROM Order o
JOIN FETCH o.items
WHERE o.id = :id
```

---

## Real-World Use

- Order details page
    
- Invoice generation
    

---

## Caution

❌ Can cause cartesian explosion  
❌ Not for pagination

---

# 7️⃣ Bulk JPQL (`UPDATE` / `DELETE`) — **ADMIN & BATCH JOBS**

## When to Use

✔ Large updates  
✔ Background jobs  
✔ Maintenance scripts

---

## Example

```java
UPDATE User u SET u.status = 'INACTIVE'
```

---

## Important Warning

❌ Bypasses persistence context  
✔ Must clear EntityManager after

---

# 8️⃣ Spring Data Repository Queries — **BUSINESS DEFAULT**

## When to Use

✔ Spring Boot projects  
✔ Simple queries  
✔ Clean service layers

---

## Examples

```java
findByEmail(String email)
findByStatus(Status status)
```

---

## Why It’s Popular

✔ Minimal code  
✔ Easy to read  
✔ Built on JPA

---

# 🧠 DECISION TABLE (SAVE THIS)

|Situation|Use This|
|---|---|
|Standard CRUD|TypedQuery / Repository|
|Reused static query|@NamedQuery|
|Complex SQL|Native Query|
|Dynamic filters|Criteria API|
|Avoid N+1|JPQL JOIN FETCH|
|Batch update/delete|Bulk JPQL|
|Spring Boot app|Repository methods|

---

# 🔥 GOLDEN RULES (IMPORTANT)

1️⃣ Default to **TypedQuery**  
2️⃣ Avoid Native unless necessary  
3️⃣ Use Criteria only for dynamic queries  
4️⃣ Inspect generated SQL always  
5️⃣ Optimize for readability first
