## **Function<T, R>**

Represents a function that takes **one input** of type `T` and returns a result of type `R`.

### **Key Method**

- `R apply(T t)`
    

### **Example**

```java
Function<String, Integer> lengthFn = s -> s.length();
int len = lengthFn.apply("Java");
```

---

## **BiFunction<T, U, R>**

Represents a function that takes **two inputs** of types `T` and `U` and returns type `R`.

### **Key Method**

- `R apply(T t, U u)`
    

### **Example**

```java
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;
int result = add.apply(10, 20);
```

---

# **Function Composition Utilities**

---

## **andThen()**

Chains a function **after** the current function.  
`output = secondFunction(firstFunction(input))`

### **Example**

```java
Function<Integer, Integer> times2 = x -> x * 2;
Function<Integer, Integer> plus3 = x -> x + 3;

Function<Integer, Integer> pipeline = times2.andThen(plus3);
int result = pipeline.apply(5); // (5*2)+3 = 13
```

---

## **compose()**

Chains a function **before** the current function.  
`output = currentFunction(previousFunction(input))`

### **Example**

```java
Function<Integer, Integer> times2 = x -> x * 2;
Function<Integer, Integer> plus3 = x -> x + 3;

Function<Integer, Integer> pipeline = times2.compose(plus3);
int result = pipeline.apply(5); // times2(5+3) = 16
```

---

## **identity()**

Returns a function that simply returns its input.  
Useful for mapping elements as-is.

### **Example**

```java
Function<String, String> identityFn = Function.identity();
String v = identityFn.apply("hello"); // "hello"
```

---

# **Lambda Expressions for Function & BiFunction**

### **Function**

```java
Function<Integer, String> toText = n -> "Value: " + n;
```

### **BiFunction**

```java
BiFunction<String, Integer, String> repeat = (s, n) -> s.repeat(n);
```

Lambda expressions implement functional interfaces concisely.

---

# **Method References to Implement Function or BiFunction**

---

### **Static Method Reference**

```java
Function<String, Integer> parser = Integer::parseInt;
```

---

### **Instance Method of Specific Object**

```java
String s = "hello";
Supplier<Integer> len = s::length;
```

---

### **Instance Method of Arbitrary Object (Used in Streams)**

```java
Function<String, String> upper = String::toUpperCase;
```

---

### **Constructor Reference**

```java
Supplier<List<String>> listMaker = ArrayList::new;
```

---

# **Comparator.comparing()**

Creates a comparator based on a key extractor function.

### **Basic Usage**

```java
Comparator<Person> byName = Comparator.comparing(Person::getName);
```

---

### **With a lambda**

```java
Comparator<Person> byAge = Comparator.comparing(p -> p.getAge());
```

---

### **Reversed order**

```java
Comparator<Person> byNameDesc =
        Comparator.comparing(Person::getName).reversed();
```

---

# **Summary Table**

|Concept|Description|Example|
|---|---|---|
|Function<T, R>|One input → one output|`Function<Integer, String>`|
|BiFunction<T, U, R>|Two inputs → one output|`BiFunction<Integer,Integer,Integer>`|
|andThen()|First f1, then f2|`f1.andThen(f2)`|
|compose()|First f2, then f1|`f1.compose(f2)`|
|identity()|Returns input unchanged|`Function.identity()`|
|Lambda|Inline function|`(x, y) -> x + y`|
|Method Reference|Shorthand for lambda|`String::toUpperCase`|
|Comparator.comparing()|Create comparator with key extractor|`Comparator.comparing(Person::getName)`|
