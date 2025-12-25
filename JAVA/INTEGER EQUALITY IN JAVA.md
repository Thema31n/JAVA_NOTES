## 🔹 1. Two Ways to Compare

- **`==` operator** → compares **object references** (do they point to the same object in memory?).
    
- **`.equals()` method** → compares **values** inside the objects.
    

---

## 🔹 2. Integer Caching (the tricky part)

Java caches `Integer` objects for values in the range **-128 to 127** (the most common values).

That means:

- Autoboxing (`Integer x = 10;`) will reuse the same object for numbers in that range.
    
- Outside that range, a **new object** is created.
    

---

## 🔹 3. Examples

### Case 1: Within cache range (-128 to 127)

```java
Integer a = 100;
Integer b = 100;

System.out.println(a == b);      // true ✅ (same cached object)
System.out.println(a.equals(b)); // true ✅ (same value)
```

---

### Case 2: Outside cache range

```java
Integer a = 200;
Integer b = 200;

System.out.println(a == b);      // false ❌ (different objects)
System.out.println(a.equals(b)); // true ✅ (same value)
```

---

### Case 3: Using `new Integer()`

```java
Integer a = new Integer(100);
Integer b = new Integer(100);

System.out.println(a == b);      // false ❌ (forced new objects)
System.out.println(a.equals(b)); // true ✅
```

---

### Case 4: Comparing with primitive `int`

```java
Integer a = 200;
int b = 200;

System.out.println(a == b); // true ✅ (unboxing happens, compares values)
```

---

## 🔹 4. Rules of Thumb

- Use **`.equals()`** to compare `Integer` values (safe for all ranges).
    
- **`==` only works reliably for:**
    
    - Comparing with primitives (because of unboxing).
        
    - Cached values in range `-128` to `127`.
        

---

# ✅ Summary

- `==` → checks reference (same object?).
    
- `.equals()` → checks value (same number?).
    
- Java caches `Integer` objects from **-128 to 127** → so `==` may be true in that range.
    
- Outside that range, `==` is usually false unless both point to the same object.
    

---

## 1. What is `Integer`?

- A **final class** in `java.lang` that wraps a primitive `int` in an object.
    
- Allows you to:
    
    - Use `int` in places that need objects (Collections, Generics).
        
    - Convert between strings and numbers.
        
    - Work with constants and utility functions.
        

---

## 🔹 2. Constants

- `Integer.MIN_VALUE` → `-2,147,483,648` (lowest `int`)
    
- `Integer.MAX_VALUE` → `2,147,483,647` (highest `int`)
    
- `Integer.SIZE` → `32` (number of bits)
    
- `Integer.BYTES` → `4` (number of bytes)
    
- `Integer.TYPE` → class type of primitive `int`
    

---

## 🔹 3. Constructors (deprecated ⚠️)

```java
Integer i = new Integer(10);   // Old way ❌ (deprecated)
Integer j = Integer.valueOf(10); // ✅ Recommended
```

---

## 🔹 4. Value Conversion Methods

These return primitive types from an `Integer` object:

```java
int i = obj.intValue();        // to int
long l = obj.longValue();      // to long
float f = obj.floatValue();    // to float
double d = obj.doubleValue();  // to double
byte b = obj.byteValue();      // to byte
short s = obj.shortValue();    // to short
```

---

## 🔹 5. Static Utility Methods

### Parsing & Conversion

- `Integer.parseInt("123")` → `123` (int)
    
- `Integer.valueOf("123")` → `Integer` object
    
- `Integer.toString(123)` → `"123"`
    
- `Integer.toString(123, 2)` → `"1111011"` (binary string)
    
- `Integer.toHexString(255)` → `"ff"`
    
- `Integer.toOctalString(8)` → `"10"`
    
- `Integer.toBinaryString(5)` → `"101"`
    

---

### Bitwise Utilities

- `Integer.bitCount(x)` → number of 1 bits (Hamming weight).
    
- `Integer.highestOneBit(x)` → highest set bit as a power of 2.
    
- `Integer.lowestOneBit(x)` → lowest set bit as a power of 2.
    
- `Integer.numberOfLeadingZeros(x)` → count of leading 0 bits.
    
- `Integer.numberOfTrailingZeros(x)` → count of trailing 0 bits.
    
- `Integer.reverse(x)` → reverses all bits.
    
- `Integer.reverseBytes(x)` → reverses bytes.
    
- `Integer.rotateLeft(x, n)` → left bit rotation.
    
- `Integer.rotateRight(x, n)` → right bit rotation.
    
- `Integer.signum(x)` → `-1`, `0`, or `1`.
    

---

### Comparisons

- `Integer.compare(a, b)` → returns -1, 0, or 1.
    
- `Integer.compareUnsigned(a, b)` → unsigned comparison.
    
- `Integer.max(a, b)` / `Integer.min(a, b)` → bigger/smaller.
    
- `Integer.sum(a, b)` → addition.
    

---

## 🔹 6. Object Methods

- `equals(Object o)` → checks if two Integers have the same value.
    
- `hashCode()` → consistent with int value.
    
- `toString()` → returns string form.
    
- `compareTo(Integer another)` → natural ordering (for sorting).
    

---

## 🔹 7. Autoboxing & Unboxing

Java automatically converts between `int` and `Integer`:

```java
Integer obj = 10;   // autoboxing
int num = obj;      // unboxing
```

---

## 🔹 8. Special Features

- **Caching:** values from `-128` to `127` are cached.
    

```java
Integer a = 100, b = 100;
System.out.println(a == b); // true

Integer x = 200, y = 200;
System.out.println(x == y); // false
```

- **Unsigned methods (Java 8+):**
    
    - `Integer.toUnsignedLong(x)`
        
    - `Integer.divideUnsigned(a,b)`
        
    - `Integer.remainderUnsigned(a,b)`
        

---

# ✅ Quick Cheat-Sheet

### Constants

`MIN_VALUE`, `MAX_VALUE`, `SIZE`, `BYTES`, `TYPE`

### Create

`Integer.valueOf(int)`, `Integer.valueOf(String)`

### Convert

`intValue()`, `longValue()`, `doubleValue()`, …  
`parseInt(String)`, `toString(int, radix)`

### Math Utils

`sum()`, `max()`, `min()`, `compare()`, `compareUnsigned()`

### Bitwise

`bitCount()`, `highestOneBit()`, `lowestOneBit()`, `reverse()`, `rotateLeft()`, `rotateRight()`

### Object

`equals()`, `hashCode()`, `compareTo()`, `toString()`