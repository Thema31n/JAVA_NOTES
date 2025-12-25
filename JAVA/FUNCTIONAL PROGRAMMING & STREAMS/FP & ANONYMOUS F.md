# **Functional Programming in Java**

Functional Programming (FP) in Java emphasizes writing programs using **pure functions**, **immutability**, and **declarative style**.  
Although Java is not purely functional, Java 8+ supports many FP concepts through lambdas, functional interfaces, and method references.

---

## **Functional Programming Core Ideas in Java**

- **Immutability**: Prefer `final` variables and unmodifiable collections.
    
- **Pure Functions**: Methods without side effects and relying only on parameters.
    
- **Higher-Order Functions**: Functions can be passed as arguments (via lambdas and functional interfaces).
    
- **Declarative Style**: Use `Stream` API to express _what_ to do, not _how_.
    

### **Example**

```java
List<Integer> nums = List.of(1, 2, 3, 4);
nums.stream()
    .filter(n -> n % 2 == 0)
    .forEach(System.out::println);
```

---

# **Functional Interface**

A **functional interface** is an interface with **exactly one abstract method (SAM)**.

### **Examples**

- `Runnable` (run)
    
- `Callable<T>` (call)
    
- `Comparator<T>` (compare)
    
- `Supplier<T>`
    
- `Consumer<T>`
    
- `Function<T, R>`
    

### **Declare your own**

```java
@FunctionalInterface
interface Calculator {
    int operate(int a, int b);
}
```

### **Key properties**

- Can be used as the target for lambda expressions.
    
- May contain default or static methods.
    

---

# **Lambda Expressions**

**Lambda expressions** provide a concise way to implement functional interfaces using inline functions.

### **Syntax**

```java
(parameters) -> expression
(parameters) -> { statements }
```

### **Examples**

```java
// Simple lambda
Calculator add = (a, b) -> a + b;

// Runnable with lambda
Runnable r = () -> System.out.println("Running");

// Multiple statements
Consumer<String> c = s -> {
    System.out.println("First");
    System.out.println(s);
};
```

### **Uses**

- Stream API
    
- Event handling
    
- Inline functional behavior
    

### **Benefits**

- Reduced boilerplate
    
- More readable and expressive functional constructs
    

---

# **Method References**

A **method reference** is a shorthand for a lambda that calls an existing method.

### **Forms**

1. **Static method**  
    `ClassName::staticMethod`
    
2. **Instance method of specific object**  
    `object::instanceMethod`
    
3. **Instance method of arbitrary object**  
    `ClassName::instanceMethod`
    
4. **Constructor reference**  
    `ClassName::new`
    

### **Examples**

```java
// Static method
Function<String, Integer> parser = Integer::parseInt;

// Instance method
String str = "hello";
Supplier<Integer> length = str::length;

// Instance method of arbitrary object
Consumer<String> printer = System.out::println;

// Constructor reference
Supplier<List<String>> listSupplier = ArrayList::new;
```

### **Advantages**

- Cleaner and more readable code
    
- Eliminates unnecessary lambda wrappers
    

---

# **Quick Summary Table**

|Concept|Purpose|Example|
|---|---|---|
|Functional Programming|Declarative style, immutability, pure functions|`stream.filter(...)`|
|Functional Interface|Interface with one abstract method|`@FunctionalInterface`|
|Lambda Expression|Inline implementation of functional interface|`(x) -> x * 2`|
|Method Reference|Shorter form of lambda delegating to existing method|`System.out::println`|
