## **1. Overview**

The **Stream API** (introduced in Java 8, located in `java.util.stream`) provides a **functional, declarative, pipeline-based** approach to processing collections and data sources.

A stream:

- Is **not a data structure**.
    
- Represents a **conveyor belt** of data flowing from a source through intermediate transformations to a final terminal output.
    
- Supports **lazy evaluation**, **parallel execution**, and **functional operations**.
    

---

## **2. Key Concepts**

---

### **2.1 Stream Pipeline (The “Conveyor Belt”)**

A stream pipeline consists of:

1. **Source** (collection, array, generator, file, etc.)
    
2. **Intermediate (Non-terminal) operations** – lazy transformations that return a new stream:
    
    - `map`, `filter`, `sorted`, `distinct`, `limit`, `skip`, `flatMap`, etc.
        
3. **Terminal operations** – consume the stream and produce a result:
    
    - `collect`, `reduce`, `forEach`, `count`, `findFirst`, `anyMatch`, etc.
        

Once a terminal operation executes, the stream is **closed**.

---

## **3. Creating Streams**

---

### **3.1 From Arrays**

```java
String[] arr = {"A", "B", "C"};
Stream<String> stream = Arrays.stream(arr);
```

---

### **3.2 From Collections**

```java
List<Integer> list = List.of(1, 2, 3);
Stream<Integer> stream = list.stream();
```

---

### **3.3 Using `Stream.of()`**

```java
Stream<String> stream = Stream.of("X", "Y", "Z");
```

---

### **3.4 Infinite Streams**

```java
Stream<Integer> infinite = Stream.iterate(0, n -> n + 1);
```

---

## **4. Common Intermediate Operations (Non-terminal)**

|Operation|Description|
|---|---|
|`filter(Predicate)`|Keeps elements matching condition|
|`map(Function)`|Transforms elements|
|`flatMap(Function)`|Flattens nested structures|
|`sorted()` / `sorted(Comparator)`|Sorts|
|`distinct()`|Removes duplicates|
|`limit(n)`|Takes first n elements|
|`skip(n)`|Skips first n elements|
|`peek(Consumer)`|Debug step (does not modify data)|

---

## **5. Common Terminal Operations**

|Operation|Description|
|---|---|
|`forEach(Consumer)`|Executes action per element|
|`collect(Collector)`|Converts to list, map, set…|
|`reduce(...)`|Aggregates to a single value|
|`count()`|Counts elements|
|`findFirst()`|Fetches first element|
|`anyMatch(...)`|Boolean checks|
|`toArray()`|Converts stream to array|

---

## **6. Collectors**

`Collectors` provides predefined collectors such as:

- `Collectors.toList()`
    
- `Collectors.toSet()`
    
- `Collectors.toMap(keyMapper, valueMapper)`
    
- `Collectors.joining()`
    
- `Collectors.groupingBy(...)`
    
- `Collectors.partitioningBy(...)`
    
- `Collectors.summingInt(...)`
    

---

## **7. Converting Map to List**

```java
Map<String, Integer> map = Map.of("A", 1, "B", 2);

List<String> keys = map.keySet().stream().toList();
List<Integer> values = map.values().stream().toList();

List<String> entries = map.entrySet()
        .stream()
        .map(e -> e.getKey() + "=" + e.getValue())
        .toList();
```

---

## **8. Parallel Streams**

Use `parallelStream()` or `.parallel()`:

```java
List<Integer> list = IntStream.rangeClosed(1, 10).boxed().toList();

int sum = list.parallelStream()
              .mapToInt(Integer::intValue)
              .sum();
```

Parallel streams divide the workload across multiple threads. Useful for CPU-bound operations on large datasets.

---

# **FULL COMPREHENSIVE CODE – Demonstrates All Major Stream Functions**

```java
import java.util.*;
import java.util.stream.*;

public class StreamDemo {

    public static void main(String[] args) {

        List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

        System.out.println("=== Creating Streams ===");
        Stream<Integer> s1 = numbers.stream();
        Stream<Integer> s2 = Stream.of(10, 20, 30);
        Stream<String> s3 = Arrays.stream(new String[]{"A", "B", "C"});

        System.out.println("Examples:");
        s3.forEach(System.out::println);

        System.out.println("\n=== Intermediate Operations ===");

        List<Integer> evens = numbers.stream()
                .filter(n -> n % 2 == 0)
                .toList();
        System.out.println("filter -> even numbers: " + evens);

        List<Integer> doubled = numbers.stream()
                .map(n -> n * 2)
                .toList();
        System.out.println("map -> doubled: " + doubled);

        List<Integer> sortedDesc = numbers.stream()
                .sorted(Comparator.reverseOrder())
                .toList();
        System.out.println("sorted(reverse) -> " + sortedDesc);

        List<Integer> noDuplicates = Stream.of(1,2,2,3,3,3,4)
                .distinct()
                .toList();
        System.out.println("distinct -> " + noDuplicates);

        List<Integer> limit3 = numbers.stream()
                .limit(3)
                .toList();
        System.out.println("limit(3) -> " + limit3);

        List<Integer> skip3 = numbers.stream()
                .skip(3)
                .toList();
        System.out.println("skip(3) -> " + skip3);

        System.out.println("\n=== Terminal Operations ===");

        long count = numbers.stream().count();
        System.out.println("count -> " + count);

        boolean anyEven = numbers.stream().anyMatch(n -> n % 2 == 0);
        System.out.println("anyMatch(even) -> " + anyEven);

        Optional<Integer> first = numbers.stream().findFirst();
        System.out.println("findFirst -> " + first.orElse(null));

        int sum = numbers.stream().reduce(0, Integer::sum);
        System.out.println("reduce(sum) -> " + sum);

        System.out.println("\n=== Collectors ===");

        List<Integer> collectedList = numbers.stream()
                .collect(Collectors.toList());
        System.out.println("collect(toList) -> " + collectedList);

        Set<Integer> collectedSet = numbers.stream()
                .collect(Collectors.toSet());
        System.out.println("collect(toSet) -> " + collectedSet);

        String joined = numbers.stream()
                .map(String::valueOf)
                .collect(Collectors.joining(", "));
        System.out.println("collect(joining) -> " + joined);

        Map<Integer, String> mapped = numbers.stream()
                .collect(Collectors.toMap(n -> n, n -> "Value:" + n));
        System.out.println("collect(toMap) -> " + mapped);

        System.out.println("\n=== flatMap Example ===");

        List<List<Integer>> nested = List.of(
                List.of(1,2),
                List.of(3,4),
                List.of(5,6)
        );

        List<Integer> flat = nested.stream()
                .flatMap(List::stream)
                .toList();
        System.out.println("flatMap -> flattened list: " + flat);

        System.out.println("\n=== Parallel Stream ===");

        int parallelSum = numbers.parallelStream()
                .mapToInt(Integer::intValue)
                .sum();
        System.out.println("parallelStream sum -> " + parallelSum);

        System.out.println("\n=== Map to List Conversions ===");

        Map<String,Integer> sampleMap = Map.of("A",1,"B",2,"C",3);

        List<String> keyList = sampleMap.keySet().stream().toList();
        System.out.println("Keys -> " + keyList);

        List<Integer> valueList = sampleMap.values().stream().toList();
        System.out.println("Values -> " + valueList);

        List<String> entryList = sampleMap.entrySet()
                .stream()
                .map(e -> e.getKey() + "=" + e.getValue())
                .toList();
        System.out.println("Entries -> " + entryList);

        System.out.println("\n=== All Done ===");
    }
}
```
