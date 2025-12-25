## **Summary: JEP 431 – Sequenced Collections**

### **What it is**

JEP 431 introduces a new abstraction in Java 21: _sequenced collections_. These are collections that have a _defined “encounter order”_, meaning you can speak about their “first” and “last” elements, and iterate both forwards and backwards. [openjdk.org+2Oracle Docs+2](https://openjdk.org/jeps/431?utm_source=chatgpt.com)

---

### **Why it matters**

- Before, Java’s collection framework lacked a **uniform interface** for collections with well-defined order. [openjdk.org+1](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    
- Although `List`, `Deque`, `LinkedHashSet`, `SortedSet` etc. have order, their APIs for first/last or reverse traversal were inconsistent or missing. [jeps.dev+1](https://jeps.dev/docs/jdk21/431/?utm_source=chatgpt.com)
    
- This made certain generic algorithms harder to write, because you couldn’t rely on a single interface for “sequenced-ness.” [Oracle Docs](https://docs.oracle.com/en/java/javase/21/core/creating-sequenced-collections-sets-and-maps.html?utm_source=chatgpt.com)
    

---

## **New Interfaces**

JEP 431 adds three main interfaces: [openjdk.org+1](https://openjdk.org/jeps/431?utm_source=chatgpt.com)

### **1. `SequencedCollection<E>`**

- Extends `Collection<E>`. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    
- Adds methods:
    
    - `SequencedCollection<E> reversed()` — returns a view of the collection in reverse encounter order. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
        
    - `addFirst(E)`, `addLast(E)`, `getFirst()`, `getLast()`, `removeFirst()`, `removeLast()` — these come from `Deque` semantics. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
        
- The add/remove-first/last are **optional** (may throw `UnsupportedOperationException`). [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    

---

### **2. `SequencedSet<E>`**

- Extends both `Set<E>` and `SequencedCollection<E>`. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    
- Overrides `reversed()` covariantly to return `SequencedSet<E>`. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    
- **Special behavior**: For `LinkedHashSet` (which implements `SequencedSet`), if you `addFirst(e)` or `addLast(e)` and `e` is already present, it’s **moved** to the appropriate end instead of being ignored. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    
- For `SortedSet` (e.g. `TreeSet`), positioning operations (`addFirst`, `addLast`) may throw `UnsupportedOperationException`, because its order is defined by comparison, not insertion. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    

---

### **3. `SequencedMap<K, V>`**

- Extends `Map<K, V>`. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    
- Adds methods for first / last entries, like `firstEntry()`, `lastEntry()`, `pollFirstEntry()`, `pollLastEntry()`. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    
- Adds `putFirst(K, V)` and `putLast(K, V)`: for e.g. `LinkedHashMap`, if the key already exists, these methods **relocate** the entry to front or back. [jeps.dev](https://jeps.dev/docs/jdk21/431/?utm_source=chatgpt.com)
    
- Provides sequenced-views: `sequencedKeySet()`, `sequencedValues()`, `sequencedEntrySet()`. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    

---

## **Integration in the Collections Hierarchy**

- `List` and `Deque` now directly extend `SequencedCollection` in the type hierarchy. [Oracle Docs+1](https://docs.oracle.com/en/java/javase/21/core/creating-sequenced-collections-sets-and-maps.html?utm_source=chatgpt.com)
    
- `LinkedHashSet` implements `SequencedSet`. [jeps.dev+1](https://jeps.dev/docs/jdk21/431/?utm_source=chatgpt.com)
    
- `SortedSet` also extends `SequencedSet`. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    
- `LinkedHashMap` implements `SequencedMap`. [Oracle Docs](https://docs.oracle.com/en/java/javase/21/core/creating-sequenced-collections-sets-and-maps.html?utm_source=chatgpt.com)
    
- `SortedMap` extends `SequencedMap`. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    

---

## **Unmodifiable Views**

The `Collections` utility class has new methods to create read-only wrappers for sequenced types:

- `Collections.unmodifiableSequencedCollection(...)`
    
- `Collections.unmodifiableSequencedSet(...)`
    
- `Collections.unmodifiableSequencedMap(...)` [Oracle Docs](https://docs.oracle.com/en/java/javase/21/core/creating-sequenced-collections-sets-and-maps.html?utm_source=chatgpt.com)
    

---

## **Limitations / Behavior Notes**

- Since not all implementations support all operations, some methods may throw `UnsupportedOperationException` (especially positional add/remove for sorted types). [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    
- The `equals()` / `hashCode()` contract is not redefined in `SequencedCollection` itself (due to conflicting definitions across subtypes) [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    
- The design is retrofit: this API is added into existing collection classes (rather than making brand-new concrete classes). [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    

---

## **Code Example: Using Sequenced Collections (Java 21+)**

```java
import java.util.*;
import java.util.Map.Entry;

public class SequencedCollectionsDemo {

    public static void main(String[] args) {

        // --- SequencedCollection example using LinkedList (List + Deque) ---
        Deque<String> deque = new LinkedList<>();
        // LinkedList is a SequencedCollection (because List and Deque implement it)
        deque.addLast("one");      // addLast
        deque.addFirst("zero");    // addFirst
        deque.addLast("two");
        System.out.println("Deque forward: " + deque);
        // Output: Deque forward: [zero, one, two]

        // get first / last
        String first = deque.getFirst();   // “zero”
        String last = deque.getLast();     // “two”
        System.out.println("First = " + first + ", Last = " + last);

        // remove first / last
        deque.removeFirst();  // removes “zero”
        deque.removeLast();   // removes “two”
        System.out.println("Deque after removes: " + deque);
        // Output: Deque after removes: [one]

        // reversed view
        Deque<String> reversedDeque = deque.reversed();
        System.out.println("Reversed deque: " + reversedDeque);

        // --- SequencedSet example with LinkedHashSet ---
        LinkedHashSet<String> linkedSet = new LinkedHashSet<>();
        linkedSet.add("A");
        linkedSet.add("B");
        linkedSet.add("C");
        System.out.println("LinkedSet: " + linkedSet);

        linkedSet.addFirst("B");
        System.out.println("After addFirst(B): " + linkedSet);

        linkedSet.addLast("A");
        System.out.println("After addLast(A): " + linkedSet);

        String firstSet = linkedSet.getFirst();
        String lastSet = linkedSet.getLast();
        System.out.println("First in set = " + firstSet + ", Last in set = " + lastSet);

        linkedSet.removeFirst();
        linkedSet.removeLast();
        System.out.println("Set after removes: " + linkedSet);

        SequencedSet<String> reversedSet = linkedSet.reversed();
        System.out.println("Reversed set: " + reversedSet);

        // --- Unmodifiable wrappers ---
        SequencedCollection<String> unmodDeque =
            Collections.unmodifiableSequencedCollection(deque);
        System.out.println("Unmodifiable deque forward: " + unmodDeque);

        SequencedSet<String> unmodSet =
            Collections.unmodifiableSequencedSet(linkedSet);
        System.out.println("Unmodifiable sequenced set: " + unmodSet);

        // --- SequencedMap example with LinkedHashMap ---
        LinkedHashMap<String, Integer> map = new LinkedHashMap<>();
        map.put("x", 1);
        map.put("y", 2);
        map.put("z", 3);
        System.out.println("Map: " + map);

        ((java.util.SequencedMap<String,Integer>) map).putFirst("y", 2);
        System.out.println("After putFirst(y): " + map);

        ((java.util.SequencedMap<String,Integer>) map).putLast("x", 1);
        System.out.println("After putLast(x): " + map);

        Entry<String, Integer> firstEntry =
            ((java.util.SequencedMap<String,Integer>)map).firstEntry();
        Entry<String, Integer> lastEntry =
            ((java.util.SequencedMap<String,Integer>)map).lastEntry();
        System.out.println("First entry = " + firstEntry + ", Last entry = " + lastEntry);

        Entry<String, Integer> polledFirst =
            ((java.util.SequencedMap<String,Integer>)map).pollFirstEntry();
        Entry<String, Integer> polledLast =
            ((java.util.SequencedMap<String,Integer>)map).pollLastEntry();
        System.out.println("Polled first = " + polledFirst + ", Polled last = " + polledLast);
        System.out.println("Map after polls = " + map);

        java.util.SequencedMap<String, Integer> reversedMap =
            ((java.util.SequencedMap<String, Integer>) map).reversed();
        System.out.println("Reversed map = " + reversedMap);

        java.util.SequencedMap<String, Integer> unmodSeqMap =
            Collections.unmodifiableSequencedMap((java.util.SequencedMap<String, Integer>) map);
        System.out.println("Unmodifiable sequenced map: " + unmodSeqMap);

        System.out.println("Iterating reversedSet via stream:");
        reversedSet.stream().forEach(e -> System.out.println("  " + e));

        System.out.println("Iterating reversedMap keys:");
        reversedMap.sequencedKeySet().stream().forEach(k -> System.out.println("  " + k));
    }
}
```

---

## **How Mutation Works (Mutable vs Immutable) in Sequenced Collections**

- **Mutable Sequenced Collections / Sets / Maps**  
    The new interfaces provide _optional_ mutation methods (`addFirst`, `addLast`, etc.). If the implementation supports it (e.g. `LinkedHashSet`, `LinkedList`, `LinkedHashMap`), you can mutate at both ends, and even **relocate** existing elements. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    
- **Immutable (Unmodifiable) Views**  
    Using `Collections.unmodifiableSequencedCollection(...)` (or Set / Map), you wrap an existing sequenced collection to disallow mutation. Attempting to call `addFirst`, `removeLast`, etc. on this wrapper throws `UnsupportedOperationException`. [Oracle Docs](https://docs.oracle.com/en/java/javase/21/core/creating-sequenced-collections-sets-and-maps.html?utm_source=chatgpt.com)
    
- **Unsupported Operations**  
    For sequenced types that do not support relevant mutations (e.g. sorted collections where order is defined by comparison), calling position-based mutation methods may throw `UnsupportedOperationException`. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    

---

## **How It Works Internally (Implementation Details)**

- The interfaces (`SequencedCollection`, `SequencedSet`, `SequencedMap`) are **retrofit** into existing collection types. [Oracle Docs+1](https://docs.oracle.com/en/java/javase/21/core/creating-sequenced-collections-sets-and-maps.html?utm_source=chatgpt.com)
    
- Default implementations are provided in the interfaces for many methods, so not every implementation needs to re-implement all behavior. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    
- In `LinkedHashSet` / `LinkedHashMap`, the add-first / add-last semantics are implemented by **removing and re-inserting** the element at the right end, to “move” the element. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    
- The `reversed()` method returns a _view_ -- it does not necessarily copy the collection, but gives a reversed traversal. [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    
- For `SequencedMap`, `firstEntry()`, `lastEntry()`, and the `poll…` methods access or remove entries from the two ends in a consistent way, regardless of underlying implementation (as long as it supports sequencing). [openjdk.org](https://openjdk.org/jeps/431?utm_source=chatgpt.com)
    