## 1. Overview

Generics allow classes, interfaces, and methods to operate on **parameterized types**. They enable stronger type checks at compile time and eliminate most explicit casts.

---

## 2. Generic Types (Classes & Interfaces)

```java
public class Box<T> {
    private T value;

    public void set(T value) {
        this.value = value;
    }

    public T get() {
        return value;
    }
}
```

Generic interfaces:

```java
public interface Mapper<T, R> {
    R map(T input);
}
```

---

## 3. Generic Methods

Generic methods declare their own type parameters before the return type:

```java
public static <T> void print(T element) {
    System.out.println(element);
}
```

---

## 4. Multiple Bounds

You can restrict a type parameter using multiple bounds.  
**Syntax:**

```java
<T extends A & B & C>
```

Rules:

- Only **one** class may appear, and it must be first.
    
- Multiple interfaces may follow.
    

---

### Example – Single Class + Interface

```java
public <T extends Number & Comparable<T>> void process(T value) { }
```

---

### Example – Extending Comparable and Comparator

A type parameter _cannot_ extend both `Comparable<T>` and `Comparator<T>` simultaneously if both are interfaces with conflicting type contracts. However, technically Java allows it if needed:

```java
public <T extends Comparable<T> & Comparator<T>> void sort(T item) { }
```

This is uncommon, but syntactically legal as long as the constraints make sense.

---

## 5. Bounded Type Parameters

### Upper Bound

```java
<T extends Number>
```

### Lower Bound (wildcards only)

```java
List<? super Integer>
```

Upper bounds are useful when you want to **read** values.  
Lower bounds are useful when you want to **write** values.

---

## 6. Wildcards

Wildcards introduce flexibility in assigning parameterized types.

### Unbounded: `?`

```java
List<?> items;
```

---

### Upper-Bounded: `? extends T`

Covariant: you can read elements as type `T`.

```java
List<? extends Number> nums;
```

---

### Lower-Bounded: `? super T`

Contravariant: you can write elements of type `T`.

```java
List<? super Integer> ints;
```

---

### PECS Rule

- **Producer Extends**
    
- **Consumer Super**
    

---

## 7. Raw Types

Raw types are legacy pre-Java-5 generics usage.

```java
List list = new ArrayList(); // raw
```

Issues:

- Eliminates type safety.
    
- Requires manual casting.
    
- Can cause runtime `ClassCastException`.
    

Modern code should avoid raw types except in special cases (interop with legacy APIs).

---

## 8. Type Erasure

Java implements generics via _type erasure_, meaning all generic type information is removed at runtime.

Effects:

- No runtime type of type parameters (`T` is erased to `Object` or upper bound).
    
- Cannot instantiate type parameters:
    

```java
T obj = new T(); // invalid
```

- Cannot create arrays of parameterized types:
    

```java
List<String>[] arr = new List<String>[10]; // invalid
```

- Overloading based only on generic type parameters is impossible:
    

```java
void m(List<String>)
void m(List<Integer>)   // invalid — erasure makes both m(List)
```

---

## 9. Generics with Comparable and Comparator

### Extending Comparable

Used for natural ordering:

```java
public class Person implements Comparable<Person> {
    public int compareTo(Person other) {
        ...
    }
}
```

Generic method using Comparable:

```java
public static <T extends Comparable<T>> T max(T a, T b) {
    return a.compareTo(b) > 0 ? a : b;
}
```

---

### Using Comparator

Used for custom ordering:

```java
Comparator<Person> byAge = Comparator.comparingInt(Person::getAge);
```

Generic methods with Comparator:

```java
public static <T> T max(T a, T b, Comparator<? super T> cmp) {
    return cmp.compare(a, b) > 0 ? a : b;
}
```

---

### Combining Comparable & Comparator Bounds

Syntactically allowed:

```java
public <T extends Comparable<T> & Comparator<T>> void validate(T t) { }
```

But rarely used because a type typically implements one or the other, not both.

---

## 10. Practical Guidelines

- Use generics to enforce compile-time type safety.
    
- Avoid raw types except for legacy compatibility.
    
- Use upper-bounded wildcards for **read-only** collections.
    
- Use lower-bounded wildcards for **write-only** scenarios.
    
- Do not rely on runtime generic type info, because of erasure.
    
- Prefer generic methods when only the method depends on the type parameter.