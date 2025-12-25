## **Consumer**

A functional interface that accepts **one input** and returns **no result**.

### **Method**

- `void accept(T t)`
    

### **Example**

```java
Consumer<String> printer = s -> System.out.println(s);
printer.accept("Hello");
```

---

## **BiConsumer<T, U>**

A functional interface that accepts **two inputs**, returns no result.

### **Method**

- `void accept(T t, U u)`
    

### **Example**

```java
BiConsumer<String, Integer> printPair =
    (name, age) -> System.out.println(name + " is " + age);
```

---

## **Iterator.forEachRemaining**

Executes a Consumer for each remaining element of the iterator.

### **Example**

```java
Iterator<String> it = List.of("A", "B", "C").iterator();
it.forEachRemaining(System.out::println);
```

---

## **Map.forEach**

Applies a BiConsumer to each key–value pair.

### **Example**

```java
Map<String, Integer> map = Map.of("A", 1, "B", 2);
map.forEach((key, value) -> System.out.println(key + "=" + value));
```

---

## **Collection.removeIf**

Removes all elements that match a given Predicate.

### **Example**

```java
List<Integer> nums = new ArrayList<>(List.of(1, 2, 3, 4, 5));
nums.removeIf(n -> n % 2 == 0);
```

---

## **Predicate**

Represents a boolean-valued function (one input → boolean).

### **Method**

- `boolean test(T t)`
    

### **Example**

```java
Predicate<Integer> isEven = n -> n % 2 == 0;
```

---

## **Predicate Operations**

---

### **and()**

Logical AND composition.

```java
Predicate<Integer> positive = n -> n > 0;
Predicate<Integer> even = n -> n % 2 == 0;

Predicate<Integer> positiveAndEven = positive.and(even);
```

---

### **or()**

Logical OR composition.

```java
Predicate<Integer> small = n -> n < 10;
Predicate<Integer> zero = n -> n == 0;

Predicate<Integer> smallOrZero = small.or(zero);
```

---

### **negate()**

Logical NOT of the predicate.

```java
Predicate<String> empty = String::isEmpty;
Predicate<String> notEmpty = empty.negate();
```

---

### **isEqual(target)**

Creates a predicate that tests if a value equals `target`.

```java
Predicate<String> isHello = Predicate.isEqual("hello");
```

---

### **not(predicate)**

Static utility introduced in Java 11.

```java
List<String> names = List.of("A", "", "B");
names.stream()
     .filter(Predicate.not(String::isEmpty))
     .forEach(System.out::println);
```

---

## **BiPredicate<T, U>**

Represents a boolean-valued function with **two inputs**.

### **Method**

- `boolean test(T t, U u)`
    

### **Example**

```java
BiPredicate<String, Integer> longerThan =
    (str, n) -> str.length() > n;

boolean result = longerThan.test("Java", 2); // true
```

---

## **Summary Table**

|Interface / Method|Inputs|Output|Typical Use|
|---|---|---|---|
|**Consumer**|1|void|Print/log/process value|
|**BiConsumer<T, U>**|2|void|Process key-value pairs|
|**Predicate**|1|boolean|Filtering logic|
|**BiPredicate<T, U>**|2|boolean|Combined validation checks|
|**Iterator.forEachRemaining**|elements|void|Consume remaining elements|
|**Map.forEach**|key, value|void|Process entries|
|**Collection.removeIf**|element|boolean (predicate)|Remove matched elements|
|**Predicate.and()**|2 predicates|boolean|Combine conditions (AND)|
|**Predicate.or()**|2 predicates|boolean|Combine conditions (OR)|
|**Predicate.negate()**|predicate|boolean|Logical NOT|
|**Predicate.isEqual()**|value|boolean|Equality predicate|
|**Predicate.not()**|predicate|boolean|Negated predicate|
