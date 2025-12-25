# **1. Core Concept**

A **Map** associates unique keys to values, enabling efficient key-based lookup.

Keys are unique, values may repeat.  
Map is not part of the Collection hierarchy.

---

# **2. Map Hierarchy (Conceptual Overview)**

```
Map
├─ HashMap
│   └─ LinkedHashMap
│
├─ SortedMap (interface)
│   └─ NavigableMap (interface)
│       └─ TreeMap
│       └─ ConcurrentSkipListMap
│
├─ Hashtable
│   └─ Dictionary (parent)
│
└─ ConcurrentMap (interface)
    └─ ConcurrentHashMap
    └─ ConcurrentSkipListMap
```

Note:  
ConcurrentSkipListMap is both a ConcurrentMap and a NavigableMap, and is sorted.

---

# **3. Map Interface – methods to know**

### **Query:**

• size(), isEmpty()  
• containsKey(), containsValue()  
• get()

### **Mutation:**

• put(), putAll()  
• remove(), clear()

### **Views:**

• keySet()  
• values()  
• entrySet()

### **Java 8+ defaults:**

• getOrDefault(), putIfAbsent()  
• compute(), computeIfAbsent(), computeIfPresent()  
• merge()  
• replace(), replaceAll()  
• forEach()

---

# **4. Map.Entry**

Represents a single key→value mapping.

### **Methods:**

• getKey()  
• getValue()  
• setValue()

Efficient iteration technique:

```java
for (Map.Entry<K,V> e : map.entrySet()) { ... }
```

---

# **5. SortedMap**

A Map where keys are sorted ascending.

### **Key methods:**

• comparator()  
• firstKey(), lastKey()  
• subMap(), headMap(), tailMap()

Implementation example: TreeMap

---

# **6. NavigableMap**

Extends SortedMap with richer navigation.

### **Key methods:**

• lowerKey(), floorKey(), ceilingKey(), higherKey()  
• firstEntry(), lastEntry()  
• pollFirstEntry(), pollLastEntry()  
• descendingMap()

### **Key implementations:**

• TreeMap  
• ConcurrentSkipListMap

---

# **7. Implementation Profiles (extended)**

## **HashMap**

• hashing  
• O(1) average operations  
• allows null key  
• unspecified iteration order

## **LinkedHashMap**

• HashMap + doubly-linked list  
• preserves insertion or access order  
• same complexity as HashMap  
• enables LRU eviction strategies

## **TreeMap**

• Red-Black tree  
• sorted by key  
• does not allow null key  
• O(log N) operations  
• implements NavigableMap

## **ConcurrentHashMap**

• high-performance concurrent hashing  
• thread-safe  
• no null keys/values  
• O(1) average operations  
• weakly consistent iterators  
• replaces Hashtable

## **ConcurrentSkipListMap**

• concurrent sorted map  
• skip-list structure  
• scalable concurrency  
• NavigableMap + ConcurrentMap  
• O(log N) operations  
• predictable ordering

## **Hashtable**

• legacy synchronized hash table  
• slower  
• no null keys or values  
• use ConcurrentHashMap instead

## **Dictionary**

• pre-Map legacy class  
• parent of Hashtable  
• obsolete

---

# **8. Ordering Comparison**

|Implementation|Ordering Characteristic|
|---|---|
|HashMap|undefined|
|LinkedHashMap|insertion order or access order|
|TreeMap|sorted|
|ConcurrentSkipListMap|sorted|
|ConcurrentHashMap|undefined|
|Hashtable|undefined|

---

# **9. Complexity Comparison (general)**

|Type|Avg Lookup|Ordered|Thread Safe|
|---|---|---|---|
|HashMap|O(1)|No|No|
|LinkedHashMap|O(1)|insertion/access|No|
|TreeMap|O(log N)|Yes|No|
|ConcurrentHashMap|O(1)|No|Yes|
|ConcurrentSkipListMap|O(log N)|Yes|Yes|
|Hashtable|O(1)|No|Yes (legacy)|

---

# **10. Study Notes (to memorize)**

• TreeMap + ConcurrentSkipListMap = sorted families  
• LinkedHashMap = ordering, not sorting  
• ConcurrentMap is the correct thread-safe abstraction  
• ConcurrentHashMap supersedes Hashtable  
• NavigableMap adds proximity navigation operations  
• Map.Entry is critical for performant iteration  
• Map has view collections: keySet, values, entrySet

---

## **Full Demonstration Code**

```java
import java.util.*;
import java.util.concurrent.*;

public class MapDemo {
    public static void main(String[] args) {

        /*==========================================================
         = 1) HashMap
         ==========================================================*/
        Map<String, Integer> hashMap = new HashMap<>();
        hashMap.put("A", 1);
        hashMap.put("B", 2);
        hashMap.put("C", 3);

        System.out.println("--- HashMap ---");
        System.out.println("size: " + hashMap.size());                       // 3
        System.out.println("contains key B: " + hashMap.containsKey("B"));   // true
        System.out.println("get A: " + hashMap.get("A"));                    // 1
        System.out.println("remove C: " + hashMap.remove("C"));              // 3
        System.out.println("keySet: " + hashMap.keySet());                   // [A, B]
        System.out.println("values: " + hashMap.values());                   // [1, 2]
        System.out.println("entrySet: " + hashMap.entrySet());               // [A=1, B=2]
        System.out.println();

        /*==========================================================
         = 2) LinkedHashMap (order preserving)
         ==========================================================*/
        Map<String, Integer> linkedHashMap = new LinkedHashMap<>();
        linkedHashMap.put("X", 10);
        linkedHashMap.put("Y", 20);
        linkedHashMap.put("Z", 30);

        System.out.println("--- LinkedHashMap (preserves insertion order) ---");
        for (Map.Entry<String, Integer> e : linkedHashMap.entrySet()) {
            System.out.println(e.getKey() + " = " + e.getValue());
        }
        System.out.println();

        /*==========================================================
         = 3) TreeMap (SortedMap + NavigableMap)
         ==========================================================*/
        NavigableMap<Integer, String> treeMap = new TreeMap<>();
        treeMap.put(10, "ten");
        treeMap.put(20, "twenty");
        treeMap.put(30, "thirty");
        treeMap.put(40, "forty");

        System.out.println("--- TreeMap (Sorted + Navigable) ---");
        System.out.println("firstKey: " + treeMap.firstKey());               
        System.out.println("lastKey: " + treeMap.lastKey());                 
        System.out.println("lowerKey(20): " + treeMap.lowerKey(20));         
        System.out.println("floorKey(20): " + treeMap.floorKey(20));         
        System.out.println("ceilingKey(20): " + treeMap.ceilingKey(20));     
        System.out.println("higherKey(20): " + treeMap.higherKey(20));       
        System.out.println("subMap(15,35): " + treeMap.subMap(15, 35));      
        System.out.println("descendingMap: " + treeMap.descendingMap());     
        System.out.println();

        /*==========================================================
         = 4) ConcurrentHashMap
         ==========================================================*/
        ConcurrentMap<String, String> concurrentHashMap = new ConcurrentHashMap<>();
        concurrentHashMap.put("k1", "v1");
        concurrentHashMap.put("k2", "v2");

        System.out.println("--- ConcurrentHashMap ---");
        System.out.println("get k1: " + concurrentHashMap.get("k1"));
        System.out.println("putIfAbsent(k2,xxx): " + concurrentHashMap.putIfAbsent("k2","xxx"));
        System.out.println("putIfAbsent(k3,yyy): " + concurrentHashMap.putIfAbsent("k3","yyy"));
        System.out.println("map content: " + concurrentHashMap);
        System.out.println();

        /*==========================================================
         = 5) ConcurrentSkipListMap (Concurrent + Sorted)
         ==========================================================*/
        ConcurrentSkipListMap<Double, String> skipListMap = new ConcurrentSkipListMap<>();
        skipListMap.put(1.1, "A");
        skipListMap.put(2.2, "B");
        skipListMap.put(3.3, "C");

        System.out.println("--- ConcurrentSkipListMap (Concurrent + Sorted) ---");
        System.out.println("firstEntry: " + skipListMap.firstEntry());
        System.out.println("lastEntry: " + skipListMap.lastEntry());
        System.out.println("floorEntry(2.2): " + skipListMap.floorEntry(2.2));
        System.out.println("descendingMap: " + skipListMap.descendingMap());
        System.out.println();

        /*==========================================================
         = 6) Java 8 Default methods
         ==========================================================*/
        Map<String, Integer> defaultMethodsMap = new HashMap<>();
        defaultMethodsMap.put("A", 5);

        System.out.println("--- Java 8 Convenience ---");
        defaultMethodsMap.compute("A", (k,v) -> v + 10);
        defaultMethodsMap.computeIfAbsent("B", k -> 100);
        defaultMethodsMap.computeIfPresent("A", (k,v) -> v * 2);
        defaultMethodsMap.merge("C", 50, Integer::sum);
        defaultMethodsMap.merge("C", 10, Integer::sum);

        defaultMethodsMap.forEach((k,v) -> System.out.println(k + " -> " + v));
        System.out.println();

        /*==========================================================
         = 7) Map.Entry usage
         ==========================================================*/
        System.out.println("--- Iteration via Map.Entry ---");
        for (Map.Entry<String,Integer> e : defaultMethodsMap.entrySet()) {
            System.out.println("Key: " + e.getKey() + " | Value: " + e.getValue());
        }
    }
}
```
