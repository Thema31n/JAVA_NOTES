# 1️⃣ What is the N+1 Problem?

The **N+1 problem** occurs when:

1. You execute **1 query** to fetch a list of parent entities
    
2. Then **N additional queries** are executed — **one for each parent** — to fetch related child entities
    

👉 Total queries = **1 + N**

---

## Real-World Example (E-commerce)

**Scenario**:  
You want to fetch all `Order`s and print their `Customer` names.

### Entities

```java
@Entity
public class Order {

    @Id
    @GeneratedValue
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    private Customer customer;
}
```

```java
@Entity
public class Customer {

    @Id
    @GeneratedValue
    private Long id;

    private String name;
}
```

---

## Repository Query

```java
List<Order> orders = orderRepository.findAll();
```

---

## Accessing Lazy Association

```java
for (Order order : orders) {
    System.out.println(order.getCustomer().getName());
}
```

---

## What Happens Behind the Scenes (SQL)

```sql
-- 1 query to fetch orders
SELECT * FROM orders;

-- N queries (one per order)
SELECT * FROM customer WHERE id = ?;
SELECT * FROM customer WHERE id = ?;
SELECT * FROM customer WHERE id = ?;
...
```

⚠️ **If you have 1,000 orders → 1,001 queries**

---

# 2️⃣ Why Is the N+1 Problem Important?

### 🔥 Performance Impact

|Issue|Explanation|
|---|---|
|Slow response time|Multiple DB round-trips|
|High DB load|Unnecessary queries|
|Scalability issues|Fails under load|
|Hidden bug|Appears only with data growth|

👉 Many production outages are caused by **accidental N+1 queries**.

---

# 3️⃣ Why Does N+1 Happen?

### Root Causes

✔ `FetchType.LAZY` (default for collections)  
✔ Accessing relations in loops  
✔ ORM trying to be “helpful”  
✔ Lack of fetch planning

❗ **LAZY is good — but dangerous if not controlled**

---

# 4️⃣ How to Detect N+1 Problems

### Enable SQL Logging

```properties
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

Or better:

```properties
logging.level.org.hibernate.SQL=DEBUG
logging.level.org.hibernate.type.descriptor.sql.BasicBinder=TRACE
```

### Look for This Pattern

```text
SELECT ... FROM parent
SELECT ... FROM child WHERE parent_id = ?
SELECT ... FROM child WHERE parent_id = ?
SELECT ... FROM child WHERE parent_id = ?
```

---

# 5️⃣ Common Solutions to N+1 Problem

## ❌ Bad Solution: EAGER Fetching

```java
@ManyToOne(fetch = FetchType.EAGER)
private Customer customer;
```

⚠️ Problems:

- Fetches even when not needed
    
- Causes Cartesian explosion
    
- Hard to control
    

👉 **Never fix N+1 by changing everything to EAGER**

---

## ✅ Solution 1: Fetch Join (JPQL)

### Repository

```java
@Query("SELECT o FROM Order o JOIN FETCH o.customer")
List<Order> findAllWithCustomer();
```

### SQL Generated

```sql
SELECT o.*, c.*
FROM orders o
JOIN customer c ON o.customer_id = c.id;
```

✔ One query  
✔ Fast  
✔ Explicit

### ❌ Drawback

- Query-specific
    
- Not reusable
    
- Harder with multiple associations
    

---

## ✅ Solution 2: Entity Graphs (Recommended)

Entity Graphs allow **dynamic fetch plans** without changing entity fetch types.

---

# 6️⃣ What Is an Entity Graph?

An **Entity Graph** tells JPA:

> “When fetching this entity, also fetch these attributes eagerly **for this query only**.”

✔ No N+1  
✔ No global EAGER  
✔ Clean & reusable

---

# 7️⃣ `@NamedEntityGraph` – Define Fetch Plans

### Example: Order → Customer

```java
@Entity
@NamedEntityGraph(
    name = "order-with-customer",
    attributeNodes = {
        @NamedAttributeNode("customer")
    }
)
public class Order {

    @Id
    @GeneratedValue
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    private Customer customer;
}
```

---

## Using the Entity Graph

### Repository

```java
@EntityGraph(value = "order-with-customer")
@Query("SELECT o FROM Order o")
List<Order> findAllOrdersWithCustomer();
```

### Result

✔ Single SQL query  
✔ No N+1  
✔ Clean code

---

# 8️⃣ `@NamedAttributeNode` Explained

```java
@NamedAttributeNode("customer")
```

Means:

> “Fetch the `customer` association eagerly **as part of this graph**”

You can add **multiple attributes**:

```java
@NamedEntityGraph(
    name = "order-with-details",
    attributeNodes = {
        @NamedAttributeNode("customer"),
        @NamedAttributeNode("items")
    }
)
```

---

# 9️⃣ Nested Entity Graphs (Advanced)

### Order → Customer → Address

```java
@NamedEntityGraph(
    name = "order-with-customer-address",
    attributeNodes = {
        @NamedAttributeNode(
            value = "customer",
            subgraph = "customer-subgraph"
        )
    },
    subgraphs = {
        @NamedSubgraph(
            name = "customer-subgraph",
            attributeNodes = {
                @NamedAttributeNode("address")
            }
        )
    }
)
```

✔ Deep fetch  
✔ Still one query (or minimal queries)

---

# 🔟 Dynamic Entity Graph (Without Annotation)

```java
EntityGraph<Order> graph = entityManager.createEntityGraph(Order.class);
graph.addAttributeNodes("customer");

Map<String, Object> hints = new HashMap<>();
hints.put("javax.persistence.fetchgraph", graph);

Order order = entityManager.find(Order.class, 1L, hints);
```

✔ Useful for dynamic scenarios  
✔ No annotations required

---

# 1️⃣1️⃣ Entity Graph vs Fetch Join

|Feature|Fetch Join|Entity Graph|
|---|---|---|
|Reusability|❌|✅|
|Clean repositories|❌|✅|
|Dynamic|❌|✅|
|Readability|⚠️|✔|
|Recommended|⚠️|⭐⭐⭐⭐|

👉 **Best practice**:

- Use **Entity Graphs** for most cases
    
- Use **Fetch Join** for complex reporting queries
    

---

# 1️⃣2️⃣ Best Practices Summary

✅ Keep associations **LAZY**  
✅ Detect N+1 early  
✅ Use **Entity Graphs**  
✅ Avoid global `EAGER`  
✅ Use SQL logs in dev  
✅ Test with large datasets

---

# 1️⃣3️⃣ Quick Mental Rule

> If you loop over entities and access a relation → **STOP** → check for N+1
