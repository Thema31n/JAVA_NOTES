## **1. What is `Optional`?**

`Optional<T>` is a **container object** that may or may not hold a **non-null value**.  
It was introduced in **Java 8** to help avoid `NullPointerException` and express _optional_ return types more clearly.

---

## **2. Purpose of Optional**

### ❗ Why does Optional exist?

- To avoid **null** as a return value
    
- To help write **cleaner**, **null-safe** code
    
- To explicitly show when a method’s result **might be absent**
    

Example:

```java
Optional<User> findUserById(int id);
```

Instead of returning `null`, the method returns an empty `Optional`.

---

## **3. Optional API (Most Important Methods)**

### ✔ Creation

- `Optional.of(value)` → value must NOT be null
    
- `Optional.ofNullable(value)` → accepts null
    
- `Optional.empty()` → empty optional
    

### ✔ Access & transformation

- `get()` → returns value (throws if empty)
    
- `isPresent()` → true if value exists
    
- `isEmpty()` → true if empty
    
- `ifPresent(Consumer)` → run code only if value exists
    

### ✔ Fallbacks

- `orElse(default)`
    
- `orElseGet(supplier)` → lazy version
    
- `orElseThrow()`
    
- `orElseThrow(Supplier)`
    

### ✔ Functional API

- `map(Function)` → transform value
    
- `flatMap(Function)` → transform Optional to Optional
    
- `filter(Predicate)` → keep only matching values
    

---

## **4. When to Use Optional**

### ✔ Good use cases:

- Method return values that _may_ be empty
    
- Chaining transformations (functional style)
    
- Avoiding nested null checks
    

### ❌ Do NOT use Optional for:

- Fields in entities (memory overhead)
    
- Method parameters
    
- Inside loops for performance-critical code
    
- Serialization (not supported natively)
    

---

## **5. Tips & Tricks**

### ⭐ Never use `Optional.get()` unless you're 100% sure it is present

Use `orElseThrow()` instead.

### ⭐ Prefer `map()` and `flatMap()` to avoid manual checks

Functional style keeps code compact.

### ⭐ Use `ifPresentOrElse()` (Java 9+)

### ⭐ Prefer `orElseGet()` when default value creation is expensive

Because `orElse()` always executes its argument.

---

## **6. Alternatives to Optional**

### ✔ 1. Return `null` (classic)

Simple and efficient, but risk of `NullPointerException`.

### ✔ 2. Throw custom exceptions

Example: `UserNotFoundException`

### ✔ 3. Use Result wrapper classes

Custom wrapper:

```java
class Result<T> {
    boolean success;
    T data;
    String error;
}
```

### ✔ 4. Use functional libraries

- Vavr `Option`
    
- Apache Commons `Optional`
    

---

# 7. Full Code Example — Optional API (with commented output)

```java
import java.util.Optional;

public class OptionalDemo {

    public static void main(String[] args) {

        // ----------------------------
        // Creating Optionals
        // ----------------------------
        Optional<String> opt1 = Optional.of("Hello");        // non-null value
        Optional<String> opt2 = Optional.ofNullable(null);   // empty
        Optional<String> opt3 = Optional.empty();            // empty


        // ----------------------------
        // Checking presence
        // ----------------------------
        System.out.println(opt1.isPresent()); // true
        System.out.println(opt2.isEmpty());   // true


        // ----------------------------
        // Getting values
        // ----------------------------
        System.out.println(opt1.get());       // "Hello"
        // opt2.get(); // would throw NoSuchElementException


        // ----------------------------
        // ifPresent
        // ----------------------------
        opt1.ifPresent(value -> System.out.println("Value: " + value));
        // prints: Value: Hello

        // Java 9+
        opt2.ifPresentOrElse(
            value -> System.out.println(value),
            () -> System.out.println("No value found")
        ); // prints: No value found


        // ----------------------------
        // orElse, orElseGet, orElseThrow
        // ----------------------------
        System.out.println(opt2.orElse("Default"));               // "Default"
        System.out.println(opt2.orElseGet(() -> "Lazy Default")); // "Lazy Default"

        // Throws if empty
        // opt2.orElseThrow(() -> new RuntimeException("No value!"));

        // ----------------------------
        // map and flatMap
        // ----------------------------
        Optional<String> upper = opt1.map(String::toUpperCase);
        System.out.println(upper.get()); // "HELLO"

        Optional<Integer> length = opt1.map(String::length);
        System.out.println(length.get()); // 5

        // flatMap: return another Optional
        Optional<String> flat = opt1.flatMap(val -> Optional.of(val + "!"));
        System.out.println(flat.get()); // "Hello!"


        // ----------------------------
        // filter
        // ----------------------------
        Optional<String> filtered = opt1.filter(str -> str.startsWith("H"));
        System.out.println(filtered.isPresent()); // true

        Optional<String> filtered2 = opt1.filter(str -> str.startsWith("X"));
        System.out.println(filtered2.isPresent()); // false


        // ----------------------------
        // Combining everything
        // ----------------------------
        String result = Optional.of("chatGPT")
                .map(String::toUpperCase)
                .filter(s -> s.length() > 3)
                .orElse("fallback");

        System.out.println(result); // "CHATGPT"
    }
}
```

---

# 8. Quick Summary

- **Optional** = container that may or may not hold a value
    
- Purpose: avoid nulls, provide safe API
    
- Key methods: `of`, `ofNullable`, `isPresent`, `map`, `flatMap`, `filter`, `orElse`, `ifPresentOrElse`
    
- Use Optional only for **return values**, not fields/params
    
- Prefer functional chain instead of manual null checks
    
- Alternatives: null, exceptions, Result wrapper, Vavr Option
    
