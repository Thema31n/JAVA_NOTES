## **HashSet**

- Internal structure: **Hash table + buckets**
    
- Bucket selection uses `hashCode()`
    
- Collision resolution via singly linked lists (or tree bins when large)
    
- Extremely fast membership operations
    
- Order is undefined and unstable
    
- Requires consistent `hashCode()`/`equals()`
    

---

## **LinkedHashSet**

- Hash table + **double-linked list**
    
- Nodes preserve insertion order
    
- Slight additional memory overhead
    
- Useful when deterministic traversal order needed
    

---

## **TreeSet**

- Backed by **NavigableMap** (Red-Black balanced binary search tree)
    
- Guarantees global ordering
    
- All operations O(log n)
    
- Requires:
    
    - element implements `Comparable<T>` OR
        
    - constructor accepts a `Comparator<T>`
        

---

## **EnumSet**

- Backed by **bit-vectors**
    
- Uses raw integer bit positions to represent flags
    
- Fastest & most memory compact Set in Java
    
- Only supports enums of a single enum class
    
- Optimal for flag-style state sets
    

---

## **ConcurrentSkipListSet**

- Concurrent (lock-free) **skip-list**
    
- Sorted ordering
    
- Good for high contention multi-thread reads
    
- O(log n) membership
    

---

## **CopyOnWriteArraySet**

- Backed by an **array copy on every write**
    
- Iteration lock-free and extremely fast
    
- Best for sets that rarely mutate but are read constantly
    
- Writes are expensive
    

---

# **Revised Code With Outputs as Comments**

This code is intentionally verbose for teaching and reference.

```java
import java.util.*;
import java.util.concurrent.*;

public class SetMasterDemo2 {

    static final class Person {
        final int id;
        final String name;
        Person(int id, String name) { this.id = id; this.name = name; }

        @Override public boolean equals(Object o) {
            if (this == o) return true;
            if (!(o instanceof Person)) return false;
            Person p = (Person)o;
            return id == p.id && Objects.equals(name, p.name);
        }

        @Override public int hashCode() {
            return Objects.hash(id, name);
        }

        @Override public String toString(){
            return name + "#" + id;
        }
    }

    enum Status { NEW, ACTIVE, BLOCKED }

    public static void main(String[] args) {

        // Standard Sets
        Set<String> hash = new HashSet<>();
        Set<String> linked = new LinkedHashSet<>();
        Set<String> tree = new TreeSet<>();

        hash.add("A");
        hash.add("B");
        hash.add("C");
        hash.add("A");              // duplicate ignored
        
        // after adds:
        // HashSet output example: [A, B, C]  (unordered)

        linked.addAll(List.of("B","A","C"));
        // LinkedHashSet output: [B, A, C] (insertion order)

        tree.addAll(List.of("C","B","A"));
        // TreeSet output: [A, B, C] (sorted)

        // Concurrent Sets
        Set<String> concurrentSorted = new ConcurrentSkipListSet<>();
        Set<String> copyOnWrite = new CopyOnWriteArraySet<>();

        concurrentSorted.addAll(List.of("X","Y","Z"));
        // output: [X, Y, Z] (sorted)

        copyOnWrite.addAll(List.of("X","Y","Z"));
        // output: [X, Y, Z] (no defined order guarantee, but stable)

        // EnumSet
        EnumSet<Status> enumSet = EnumSet.of(Status.NEW, Status.ACTIVE);
        enumSet.add(Status.BLOCKED);
        enumSet.remove(Status.NEW);
        // output: [ACTIVE, BLOCKED]

        // Custom class set
        Set<Person> people = new HashSet<>();
        people.add(new Person(1,"Adam"));
        people.add(new Person(1,"Adam"));  // suppressed
        people.add(new Person(2,"Zara"));
        // output: [Adam#1, Zara#2]

        // set algebra
        Set<String> s1 = new HashSet<>(Set.of("A","B","C","D"));
        Set<String> s2 = new HashSet<>(Set.of("C","D","E"));

        Set<String> union = new HashSet<>(s1);
        union.addAll(s2);
        // union: [A, B, C, D, E]

        Set<String> intersection = new HashSet<>(s1);
        intersection.retainAll(s2);
        // intersection: [C, D]

        Set<String> difference = new HashSet<>(s1);
        difference.removeAll(s2);
        // difference: [A, B]

        // core API demonstration
        boolean containsA = hash.contains("A");       // true
        boolean containsAllBC = hash.containsAll(Set.of("B","C")); // true
        int sizeBefore = hash.size();                // 3
        boolean emptyBefore = hash.isEmpty();        // false
        Object[] arr = hash.toArray();               // ["A","B","C"] order not guaranteed

        // iteration
        Iterator<String> it = hash.iterator();
        while(it.hasNext()){
            String v = it.next();  // prints A B C (unordered)
        }

        hash.forEach(v -> {
            // prints values
        });

        hash.stream().forEach(v -> {
            // prints values
        });

        // clear
        hash.clear();
        boolean emptyAfter = hash.isEmpty();         // true
        
        // output snapshots
        System.out.println("HashSet: " + hash);                       
        System.out.println("LinkedHashSet: " + linked);               
        System.out.println("TreeSet: " + tree);                       
        System.out.println("ConcurrentSkipListSet: " + concurrentSorted); 
        System.out.println("CopyOnWriteArraySet: " + copyOnWrite);    
        System.out.println("EnumSet: " + enumSet);                    
        System.out.println("People: " + people);                      

        System.out.println("Union: " + union);                        
        System.out.println("Intersection: " + intersection);          
        System.out.println("Difference: " + difference);              

        System.out.println("contains(\"A\"): " + containsA);
        System.out.println("containsAll(B,C): " + containsAllBC);
        System.out.println("sizeBeforeClear: " + sizeBefore);
        System.out.println("emptyBefore: " + emptyBefore);
        System.out.println("emptyAfterClear: " + emptyAfter);
        System.out.println("toArray(): " + Arrays.toString(arr));
    }
}
```
