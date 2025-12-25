## **HashMap**

- Hash-table based key/value map
    
- No predictable iteration order
    
- O(1) average for get/put/remove
    
- Allows one null key
    
- Best for fast, unordered lookup
    

---

## **LinkedHashMap**

- Extends HashMap
    
- Maintains a doubly-linked list of entries
    
- Predictable iteration order:
    
    - insertion order (default)
        
    - access order (optional)
        
- O(1) average for get/put/remove
    
- Useful for near-LRU caching, predictable iteration, and eviction policies
    

---

### **Ordering difference**

|Type|Ordering|
|---|---|
|HashMap|No order guarantee|
|LinkedHashMap|insertion order OR access order|

---

# **accessOrder in LinkedHashMap**

## **What it does**

If accessOrder = true:

- Every time get(), put(), or putIfAbsent() is called
    
- The accessed entry is moved to the end (tail) of internal list
    
- Head = least recently accessed
    
- Tail = most recently accessed
    

This turns LinkedHashMap into a natural LRU sequence.

---

## **Constructor**

```java
new LinkedHashMap<>(initialCapacity, loadFactor, accessOrder)
```

### **Example**

```java
LinkedHashMap<Integer,String> map =
    new LinkedHashMap<>(16, 0.75f, true);
```

=> true enables access-order

---

## **Operational mechanics**

- read = counts as “use”
    
- update = counts as “use”
    
- insertion = added at tail
    
- iteration order now reflects recency
    

---

# **How this helps hashing strategies**

- Supports **temporal locality** recognition
    
- Can approximate LRU logic in O(1)
    
- Enables:
    
    - lightweight caches
        
    - eviction by recency
        
    - hot-set detection
        
    - memory-bound maps
        
- No need for priority queues or sorted structures
    

---

# **removeEldestEntry**

## **Purpose**

Defines automatic eviction behavior.

Override inside subclass:

```java
LinkedHashMap<K,V> cache = new LinkedHashMap<>(capacity, 0.75f, true) {
    @Override
    protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
        return size() > maxEntries;
    }
};
```

- eldest = the head (least recent)
    
- return true => eldest entry removed automatically
    
- return false => keep
    

This is the canonical Java LRU cache structure.

---

# **Quick reference: when to use what**

## **Use HashMap when**

- maximum performance required
    
- no ordering concern
    
- pure key→value hash structure
    

---

## **Use LinkedHashMap when**

- need iteration predictability
    
- need access-ordered recency tracking
    
- need to build LRU behavior
    
- need eviction logic tied to recency
    

---

# **Memorization bullets**

- LinkedHashMap = HashMap + bidirectional list
    
- ordering = insertion by default
    
- accessOrder=true = move touched entries to tail
    
- removeEldestEntry = auto eviction hook
    
- perfect for fixed-size cache
    
- still O(1) average for core ops
    

---

## **loadFactor**

**loadFactor** = the ratio of the current number of stored entries to the current bucket capacity that triggers resizing.

Formally:

```java
loadFactor = threshold / capacity
```

where:

- capacity = number of buckets
    
- threshold = maximum number of elements allowed before resize
    

When the number of entries exceeds the threshold, the hash table grows (typically doubles its capacity) and all entries are rehashed.

---

# **Default value**

The **default loadFactor is 0.75**.

This is chosen as a good balance between:

- memory usage
    
- lookup speed
    
- reasonable collision rate
    

---

# **Rationale**

A low loadFactor:

- keeps the table less filled
    
- reduces collisions
    
- increases overall speed
    
- increases memory usage
    

A high loadFactor:

- increases collisions
    
- reduces lookup performance
    
- uses less memory
    

---

# **Behavior**

Example:

Initial capacity = 16  
loadFactor = 0.75

threshold = 16 * 0.75 = 12

When the 13th element is inserted:

- resize
    
- rehash entries
    
- new capacity = 32
    
- new threshold = 32 * 0.75 = 24
    

---

# **When to change it**

You modify loadFactor only if:

- you know your dataset characteristics
    
- you need fewer resizes for large maps
    
- you need max throughput in a specific collision pattern
    

Otherwise, keep default (0.75).
