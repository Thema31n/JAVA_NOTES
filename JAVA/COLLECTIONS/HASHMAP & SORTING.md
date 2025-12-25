# **1. HashMap (baseline)**

HashMap does NOT provide ordering.  
Elements are stored in buckets and iteration order is undefined.

### **Example**

```java
Map<String,Integer> map = new HashMap<>();
map.put("C", 3);
map.put("A", 1);
map.put("B", 2);
```

Iteration order is arbitrary.

---

# **2. Sorting Map by keys (keys implement Comparable)**

If the key type already implements Comparable (String, Integer, etc.), you can create a sorted view:

### **Using TreeMap**

```java
Map<String,Integer> hash = new HashMap<>();
hash.put("C",3);
hash.put("A",1);
hash.put("B",2);

Map<String,Integer> sorted = new TreeMap<>(hash);
System.out.println(sorted);

// Output: {A=1, B=2, C=3}
```

TreeMap automatically sorts by natural ordering of keys.

---

# **3. Sorting Map by keys with custom Comparator**

Example – descending:

```java
Map<String,Integer> hash = new HashMap<>();
hash.put("C",3);
hash.put("A",1);
hash.put("B",2);

Map<String,Integer> sortedDesc =
        new TreeMap<>(Comparator.reverseOrder());
sortedDesc.putAll(hash);

System.out.println(sortedDesc);

// Output: {C=3, B=2, A=1}
```

You can provide arbitrary comparators.

---

# **4. Sorting a Map via stream by keys**

```java
Map<String,Integer> hash = ...

Map<String,Integer> sorted =
        hash.entrySet()
            .stream()
            .sorted(Map.Entry.comparingByKey())
            .collect(Collectors.toMap(
                Map.Entry::getKey,
                Map.Entry::getValue,
                (a,b)->a,
                LinkedHashMap::new
            ));

System.out.println(sorted);
```

Benefits: preserves order in LinkedHashMap.

---

# **5. Sorting Map whose key is a custom class**

Assume class:

```java
class Person {
    String name;
    int age;
}
```

### **Option A – make Person Comparable**

```java
class Person implements Comparable<Person>{
    String name;
    int age;

    @Override
    public int compareTo(Person o){
        return this.age - o.age;
    }
}
```

Then:

```java
TreeMap<Person,String> map = new TreeMap<>();
```

### **Option B – provide Comparator at TreeMap construction:**

```java
TreeMap<Person,String> map =
        new TreeMap<>(Comparator.comparingInt(p -> p.age));
```

Both work.

---

# **6. Sorting Map by values using Comparable (ASC)**

Important:  
Map sorts by KEY, not value, so we must reorganize entries.

Using streams:

```java
Map<String,Integer> map = ...

Map<String,Integer> sorted =
        map.entrySet()
           .stream()
           .sorted(Map.Entry.comparingByValue())
           .collect(Collectors.toMap(
             Map.Entry::getKey,
             Map.Entry::getValue,
             (a,b)->a,
             LinkedHashMap::new
           ));
```

Now the values are sorted:

```java
Output: A=1, B=2, C=3
```

---

# **7. Sorting Map by values with custom Comparator**

Descending values:

```java
Map<String,Integer> sorted =
    map.entrySet()
       .stream()
       .sorted(Map.Entry.<String,Integer>comparingByValue().reversed())
       .collect(Collectors.toMap(
         Map.Entry::getKey,
         Map.Entry::getValue,
         (a,b)->a,
         LinkedHashMap::new
       ));
```

---

# **8. Sorting maps by TreeMap summary**

TreeMap sorts by KEY always.

If you want:  
• key sorting → TreeMap  
• value sorting → stream + LinkedHashMap

---

# **9. Full Demonstration Code (covers everything)**

```java
import java.util.*;
import java.util.stream.Collectors;

public class SortMapDemo {
    public static void main(String[] args) {

        Map<String,Integer> map = new HashMap<>();
        map.put("C",3);
        map.put("A",1);
        map.put("B",2);

        System.out.println("Original: " + map);

        // 1) Sort by key using TreeMap
        Map<String,Integer> sortedKey = new TreeMap<>(map);
        System.out.println("TreeMap sorted: " + sortedKey);

        // 2) Sort by key descending
        Map<String,Integer> sortedKeyDesc =
                new TreeMap<>(Comparator.reverseOrder());
        sortedKeyDesc.putAll(map);
        System.out.println("TreeMap desc: " + sortedKeyDesc);

        // 3) Sort by key using stream
        Map<String,Integer> sortedKeyStream =
                map.entrySet().stream()
                   .sorted(Map.Entry.comparingByKey())
                   .collect(Collectors.toMap(
                       Map.Entry::getKey,
                       Map.Entry::getValue,
                       (a,b)->a,
                       LinkedHashMap::new));
        System.out.println("Stream Key Sort: " + sortedKeyStream);

        // 4) Sort by values
        Map<String,Integer> sortedValue =
                map.entrySet().stream()
                   .sorted(Map.Entry.comparingByValue())
                   .collect(Collectors.toMap(
                       Map.Entry::getKey,
                       Map.Entry::getValue,
                       (a,b)->a,
                       LinkedHashMap::new));
        System.out.println("Stream Value Sort: " + sortedValue);

        // 5) Sort value descending
        Map<String,Integer> sortedValueDesc =
                map.entrySet().stream()
                   .sorted(Map.Entry.<String,Integer>comparingByValue().reversed())
                   .collect(Collectors.toMap(
                       Map.Entry::getKey,
                       Map.Entry::getValue,
                       (a,b)->a,
                       LinkedHashMap::new));
        System.out.println("Stream Value Desc Sort: " + sortedValueDesc);
    }
}
```

---

# **10. Practical exam memorization**

Sorting MAP BY KEY:  
• TreeMap  
• stream sorted(comparingByKey)

Sorting MAP BY VALUE:  
• streams only, NOT TreeMap  
• sorted(comparingByValue)  
• collect into LinkedHashMap

Sorting custom key object:  
• Comparable OR Comparator
