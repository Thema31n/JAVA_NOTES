## **Collection**

```
Collection
 ├── List
 │    ├── ArrayList
 │    ├── LinkedList
 │    ├── Vector               (LEGACY)
 │    └── Stack                (LEGACY)
 │
 ├── Set
 │    ├── HashSet
 │    ├── LinkedHashSet
 │    └── TreeSet
 │
 └── Queue / Deque
      ├── PriorityQueue
      ├── ArrayDeque
      ├── LinkedList
      │
      └── Blocking Queues
           ├── ArrayBlockingQueue
           ├── LinkedBlockingQueue
           ├── PriorityBlockingQueue
           ├── DelayQueue
           ├── SynchronousQueue
           ├── LinkedTransferQueue
           └── LinkedBlockingDeque
```

---

## **Map (NOT part of Collection)**

```
Map   (NOT part of Collection)
 ├── HashMap
 ├── LinkedHashMap
 ├── TreeMap
 ├── WeakHashMap
 ├── IdentityHashMap
 │
 ├── Hashtable                (LEGACY)
 ├── ConcurrentHashMap
 └── ConcurrentSkipListMap
```

---

## **Legacy / Deprecated Collections (Pre-Java 1.2)**

```
Legacy / Deprecated Collections (Pre-Java 1.2)
 ├── Dictionary               (DEPRECATED CLASS)
 │    └── Hashtable           (Legacy, replaced by HashMap)
 │
 ├── Vector                   (Legacy, replaced by ArrayList)
 ├── Stack                    (Legacy, replaced by Deque/ArrayDeque)
 │
 ├── Enumeration              (Legacy iterator, replaced by Iterator)
 └── Properties               (Part of legacy but still used for config files)
```

---

### 🚫 **Deprecated:**

|Collection|Status|Replacement|
|---|---|---|
|**Dictionary**|Deprecated|`Map`, `HashMap`|
|**Enumeration**|Legacy|`Iterator`, `ListIterator`|

---

### ⚠️ **Legacy (Not deprecated but obsolete):**

|Collection|Still usable?|Replacement|
|---|---|---|
|**Vector**|Yes (synchronized)|`ArrayList`|
|**Stack**|Yes|`ArrayDeque`|
|**Hashtable**|Yes (synchronized)|`HashMap` or `ConcurrentHashMap`|
