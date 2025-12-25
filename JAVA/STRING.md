## 🔹 1. What is a String?

- In Java, a **String** is an object (not a primitive).
    
- Defined in `java.lang.String`.
    
- **Immutable** → once created, it cannot be changed.
    
- Stored in a **special memory area** called the **String Pool** (inside the heap).
    

### Ways to Create a String:

```java
String s1 = "Hello";              // String literal → goes to String Pool
String s2 = new String("Hello");  // new object in heap, not in pool
```

---

## 🔹 2. Common String Methods

```java
String str = "Java Programming";
```

### Length & Characters

- `str.length()` → number of characters (`16`)
    
- `str.charAt(0)` → `'J'`
    
- `str.indexOf("a")` → `1` (first index)
    
- `str.lastIndexOf("a")` → `9` (last index)
    

---

### Substrings

- `str.substring(5)` → `"Programming"`
    
- `str.substring(0, 4)` → `"Java"`
    

---

### Case & Trim

- `str.toUpperCase()` → `"JAVA PROGRAMMING"`
    
- `str.toLowerCase()` → `"java programming"`
    
- `str.trim()` → removes spaces from start & end
    

---

### Replace

- `str.replace("Java", "C++")` → `"C++ Programming"`
    
- `str.replaceAll("a", "@")` → `"J@v@ Progr@mming"` (regex allowed)
    

---

### Split & Join

```java
String[] words = str.split(" ");          // ["Java", "Programming"]
String joined = String.join("-", words);  // "Java-Programming"
```

---

## 🔹 3. String Boolean Methods

These return `true`/`false`:

- `str.isEmpty()` → check if empty (`""`)
    
- `str.isBlank()` (Java 11+) → empty or only whitespace
    
- `str.contains("Java")` → true
    
- `str.startsWith("Java")` → true
    
- `str.endsWith("ing")` → true
    
- `str.matches(".*Java.*")` → true (regex match)
    

---

## 🔹 4. Comparing Strings

### `==` (reference comparison)

- Checks if both references point to the same object.
    

```java
String a = "Hello";
String b = "Hello";

System.out.println(a == b); // true (both in String Pool)
```

---

### `.equals()` (value comparison)

- Checks if contents are equal.
    

```java
String x = new String("Hello");
String y = new String("Hello");

System.out.println(x == y);       // false (different objects)
System.out.println(x.equals(y));  // true (same content)
```

---

### `.equalsIgnoreCase()`

```java
"hello".equalsIgnoreCase("HELLO"); // true
```

---

### `.compareTo()`

- Lexicographic comparison (`0 = equal`, `<0 = before`, `>0 = after`)
    

```java
"apple".compareTo("banana");   // negative
"banana".compareTo("apple");   // positive
```

---

## 🔹 5. `intern()` Method

- Puts the string in the **String Pool** (or returns the reference if it’s already there).
    

```java
String s1 = new String("Hello");
String s2 = s1.intern();
String s3 = "Hello";

System.out.println(s2 == s3); // true (both in String Pool)
```

👉 Use `intern()` when you want to save memory and ensure only one copy of the string literal exists in the pool.

---

## 🔹 6. String Formatting

### Using `String.format()`

```java
String name = "Mohammed";
int age = 25;

String msg = String.format(
    "My name is %s and I am %d years old.",
    name,
    age
);

System.out.println(msg); 
// My name is Mohammed and I am 25 years old.
```

---

### Format Specifiers

- `%s` → String
    
- `%d` → int
    
- `%f` → float/double
    
- `%.2f` → 2 decimal places
    
- `%n` → newline
    

Example:

```java
System.out.println(String.format("Pi = %.2f", Math.PI)); // Pi = 3.14
```

---

## 🔹 7. Regular Expressions with Strings

Java Strings support regex through methods:

- `matches(regex)` → full match.
    
- `replaceAll(regex, replacement)` → replace with regex.
    
- `split(regex)` → split based on regex.
    

### Examples:

```java
"abc123".matches("[a-z]+\\d+");        // true
"hello123".replaceAll("\\d", "*");     // "hello***"
"one,two,three".split(",");             // ["one","two","three"]
```

Also available: `java.util.regex.Pattern` and `Matcher` for advanced regex.

---

# ✅ Summary

- **String = immutable object stored in String Pool.**
    
- Use `.equals()` for value comparison, not `==`.
    
- Boolean helpers: `isEmpty()`, `isBlank()`, `contains()`, `startsWith()`, `endsWith()`.
    
- `intern()` → forces a string into the pool for memory efficiency.
    
- `String.format()` → for formatted strings (`%s`, `%d`, `%.2f`).
    
- Regex methods: `matches()`, `replaceAll()`, `split()`.