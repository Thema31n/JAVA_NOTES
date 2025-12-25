## 1️⃣ What Is Caching?

### Definition

**Caching** is the practice of storing data in memory so Hibernate can **reuse it without hitting the database again**.

- Database calls = network + disk + locks
    
- Cache calls = in-memory pointer dereference
    

This difference is often **10–100x faster**.

---

### Why Caching Matters (Technical View)

✔ Reduces round trips  
✔ Reduces row-level locks  
✔ Improves DB CPU usage  
✔ Improves p95 / p99 latency  
✔ Enables horizontal scaling

---

## 2️⃣ Cache Structure in JPA / Hibernate (Big Picture)

Hibernate caching is **layered**, not a single mechanism.

```
Application
   ↓
Query Cache (query → IDs)
   ↓
Second-Level Cache (ID → entity state)
   ↓
First-Level Cache (entity identity)
   ↓
Database
```

Each layer avoids a different kind of work.

---

## 3️⃣ First-Level Cache (L1 Cache)

### What It Is

- Persistence Context
    
- Mandatory
    
- Per `EntityManager` / `Session`
    

Hibernate **guarantees identity consistency** inside a session.

---

### Internal Behavior

```java
User u1 = em.find(User.class, 1L);
User u2 = em.find(User.class, 1L);
```

- First call → SQL executed
    
- Entity stored in persistence context
    
- Second call → returned from memory
    
- `u1 == u2` → `true`
    

---

### Configuration

❌ **Cannot be disabled**  
❌ **No config options**

But you can control lifecycle:

```java
em.clear();   // clears L1 cache
em.detach(); // removes single entity
```

---

### When It Becomes Dangerous

- Long-running sessions
    
- Batch processing
    
- Memory leaks
    

👉 Solution: periodic `flush()` + `clear()`

---

## 4️⃣ Second-Level Cache (L2 Cache)

### What It Is

- Optional
    
- Shared across sessions
    
- Stores **entity state**, not object instances
    

Entities are **reconstructed**, not reused.

---

### Internal Behavior

1. Session loads entity
    
2. Hibernate checks L2 cache
    
3. If hit → hydrate entity from cached state
    
4. If miss → DB → cache populated
    

---

### Global Configuration (hibernate.cfg.xml)

```xml
<property name="hibernate.cache.use_second_level_cache">true</property>

<property name="hibernate.cache.region.factory_class">
  org.hibernate.cache.ehcache.EhCacheRegionFactory
</property>
```

Without this → all `@Cache` annotations are ignored.

---

### Entity-Level Configuration

```java
@Entity
@Cacheable
@Cache(usage = CacheConcurrencyStrategy.READ_WRITE)
public class Product {
}
```

If `@Cache` is missing → entity is never cached.

---

### What Can Be Cached in L2

✔ Entities  
✔ Collections (`@OneToMany`, `@ManyToMany`)

```java
@OneToMany
@Cache(usage = CacheConcurrencyStrategy.READ_WRITE)
private List<OrderItem> items;
```

---

### What Is NOT Cached

❌ Native queries  
❌ Projections / DTOs  
❌ Aggregates

---

## 5️⃣ Query Cache (VERY DETAILED)

### What It Is

- Caches **query → ordered list of entity IDs**
    
- Never stores entity state
    
- Always depends on L2 cache
    

---

### Why IDs Only (Design Reason)

If Hibernate cached entities directly:

- Cache duplication explodes
    
- Invalidation becomes impossible
    
- Consistency breaks across queries
    

So Hibernate splits responsibility:

|Layer|Stores|
|---|---|
|Query Cache|IDs|
|L2 Cache|Entity state|

---

### Internal Flow

**First execution**

1. Query runs against DB
    
2. Result IDs extracted
    
3. IDs stored in `standardQueryCache`
    
4. Entities stored in L2 cache
    

**Second execution**

1. Query hash + params matched
    
2. Hibernate checks `updateTimestampsCache`
    
3. If valid → IDs returned
    
4. Entities loaded from L2 cache
    
5. **No SQL executed**
    

---

### Invalidation Mechanism (CRITICAL)

Hibernate maintains:

```text
updateTimestampsCache:
  table_name → last_update_time
```

If **any table used by query** has newer timestamp:

❌ Query cache entry invalidated  
✅ Query re-executed

This is **how correctness is preserved**.

---

### Global Configuration

```xml
<property name="hibernate.cache.use_query_cache">true</property>
```

⚠️ This only _enables_ query caching — it does NOT cache all queries.

---

### Per-Query Configuration (BEST PRACTICE)

```java
query.setHint("org.hibernate.cacheable", true);
```

Optional region:

```java
query.setHint("org.hibernate.cacheRegion", "productQueries");
```

---

### When to Use Query Cache

✔ Reference data queries  
✔ Read-mostly reporting  
✔ Static dropdowns  
✔ Admin screens

---

### When NOT to Use

❌ Pagination with offsets  
❌ High-write tables  
❌ Large result sets  
❌ User-specific queries

---

## 6️⃣ Cache Providers

### Configuration (Ehcache Example)

```xml
<property name="hibernate.cache.region.factory_class">
  org.hibernate.cache.ehcache.EhCacheRegionFactory
</property>
```

Other providers require different factory classes.

---

## 7️⃣ Cache Concurrency Strategies (WITH CONFIG)

---

### 🔹 READ_ONLY

#### Behavior

- No locks
    
- No versioning
    
- Immutable assumption
    

#### Configuration

```java
@Cache(usage = CacheConcurrencyStrategy.READ_ONLY)
```

#### Danger

Any update attempt → runtime exception.

---

### 🔹 READ_WRITE

#### Behavior

- Soft locks
    
- Version checks
    
- Strong consistency
    

#### Required Config

```java
@Version
private Long version;
```

```java
@Cache(usage = CacheConcurrencyStrategy.READ_WRITE)
```

---

### 🔹 NONSTRICT_READ_WRITE

#### Behavior

- No locks
    
- Cache invalidation on write
    
- Eventual consistency
    

#### Configuration

```java
@Cache(usage = CacheConcurrencyStrategy.NONSTRICT_READ_WRITE)
```

---

### 🔹 TRANSACTIONAL

#### Behavior

- Cache enlisted in JTA
    
- Two-phase commit
    

#### Configuration

```java
@Cache(usage = CacheConcurrencyStrategy.TRANSACTIONAL)
```

Requires:

- JTA
    
- XA datasource
    
- Transactional cache provider
    

---

## 8️⃣ Ehcache (Provider)

### Cache Region Configuration

```xml
<cache alias="product">
```

Region name must match:

- Entity name
    
- Collection role
    
- Query region name
    

---

## 9️⃣ Cache Eviction Policies

Configured at **provider level**, not Hibernate.

```xml
<expiry>
  <ttl unit="minutes">10</ttl>
</expiry>
```

---

## 🔟 Custom Ehcache Configuration

```xml
<cache alias="product">
  <resources>
    <heap unit="entries">1000</heap>
  </resources>
</cache>
```

Controls:

✔ Memory usage  
✔ Eviction  
✔ GC pressure

---
## 1️⃣1️⃣ Hibernate Internal Caches

Hibernate maintains **internal cache regions** used automatically to support query caching and correctness.  
These caches are **not directly accessed** by application code.

---

### 🔹 `standardQueryCache`

#### Description

- Stores **query keys mapped to ordered lists of entity IDs**
    
- Prevents re-execution of identical cacheable queries
    
- Works only when **query cache + second-level cache** are enabled
    

---

#### Example

When a cacheable query is executed:

```sql
SELECT * FROM product WHERE category = 'BOOKS';
```

Hibernate stores:

```
query + parameters → [1, 5, 9, 12]
```

---

#### Configuration

Enabled globally:

```xml
<property name="hibernate.cache.use_query_cache">true</property>
```

Per-query (recommended):

```java
query.setHint("org.hibernate.cacheable", true);
```

Optional region name:

```java
query.setHint("org.hibernate.cacheRegion", "productQueries");
```

---

### 🔹 `updateTimestampsCache`

#### Description

- Stores **last update timestamp per database table**
    
- Used to invalidate stale query cache entries
    
- Ensures query cache correctness
    

---

#### Example

If table `product` is updated:

```
product → 1699999999999
```

Any cached query depending on `product` becomes invalid.

---

#### Configuration

Automatically enabled when query cache is enabled:

```xml
<property name="hibernate.cache.use_query_cache">true</property>
```

Optional custom region (provider-level):

```xml
<cache alias="updateTimestampsCache">
  <resources>
    <heap unit="entries">1000</heap>
  </resources>
</cache>
```


---

## 1️⃣2️⃣ Persistence Strategies in Ehcache

Ehcache persistence strategies define **where cached data is stored** and **whether it survives JVM restarts**.  
They are configured **in the Ehcache provider XML**, not via Hibernate annotations.

---

### 🔹 `none` (Heap Only)

#### Description

- Cache entries are stored **only in JVM heap**
    
- Cleared on JVM restart
    
- Default strategy if persistence is not specified
    

---

#### Example

Used for small, fast, in-memory caches.

---

#### Configuration

```xml
<cache alias="product">
  <resources>
    <heap unit="entries">1000</heap>
  </resources>
</cache>
```

---

### 🔹 `localTempSwap` (Heap + Temporary Disk)

#### Description

- Cache uses heap first
    
- Overflows entries to disk when heap is full
    
- Disk data is **temporary** and cleared on restart
    

---

#### Example

Used when heap must be protected from large caches.

---

#### Configuration

```xml
<cache alias="product">
  <resources>
    <heap unit="entries">1000</heap>
    <disk unit="MB">500</disk>
  </resources>

  <persistence strategy="localTempSwap"/>
</cache>
```

---

### 🔹 `localRestartable` (Persistent Local Disk)

#### Description

- Cache entries stored on disk
    
- Survive JVM restart
    
- Cache is node-local (not shared)
    

---

#### Example

Used for expensive-to-load reference data.

---

#### Configuration

```xml
<cache alias="product">
  <resources>
    <heap unit="entries">1000</heap>
    <disk unit="GB">2</disk>
  </resources>

  <persistence strategy="localRestartable"/>
</cache>
```

Optional persistence directory:

```xml
<persistence directory="/var/ehcache/data"/>
```

---

### 🔹 `distributed` (Clustered Cache)

#### Description

- Cache is shared across multiple JVMs
    
- Requires clustered Ehcache (Terracotta)
    
- Supports distributed persistence
    

---

#### Example

Used in clustered or horizontally scaled systems.

---

#### Configuration (Conceptual)

```xml
<cache alias="product">
  <resources>
    <heap unit="entries">1000</heap>
    <offheap unit="GB">1</offheap>
  </resources>

  <persistence strategy="distributed"/>
</cache>
```

Cluster connection (simplified):

```xml
<service>
  <tc:cluster>
    <tc:connection url="terracotta://node1,node2"/>
  </tc:cluster>
</service>
```

---

## 1️⃣3️⃣ Common Production Mistakes

❌ Caching mutable data as READ_ONLY  
❌ Caching write-heavy entities  
❌ Global query cache enablement  
❌ No eviction strategy

---

## 🧠 FINAL MENTAL MODEL

- L1 = identity
    
- L2 = reuse
    
- Query cache = skip SQL
    
- Concurrency = correctness
    
- Eviction = memory safety
    

---

## ✅ Golden Rules

✔ Cache intentionally  
✔ Prefer immutability  
✔ Measure hit ratio  
✔ Treat cache as optimization, not storage
