# ☕ Primitive Variable Conversion in Java

Java has **8 primitive types**:

- `byte`, `short`, `int`, `long` (integers)
    
- `float`, `double` (decimals)
    
- `char` (characters → Unicode)
    
- `boolean` (true/false — no conversion with numbers)
    

---

## 🔹 1. Widening Conversion (Implicit Casting)

- Converting a **smaller type → larger type**.
    
- Done **automatically** by Java (safe, no data loss).
    

Examples:

```java
int x = 10;
long y = x;       // int → long
float f = y;      // long → float
double d = f;     // float → double
```

Order of widening:

```
byte → short → int → long → float → double
             char → int ...
```

---

## 🔹 2. Narrowing Conversion (Explicit Casting)

- Converting **larger type → smaller type**.
    
- Requires **manual cast** because it can lose data.
    

Example:

```java
double d = 9.78;
int i = (int) d;  // explicit cast, i = 9 (decimal lost)
```

Another case:

```java
int big = 130;
byte small = (byte) big; // result = -126 (overflow)
```

---

## 🔹 3. Numeric Promotion (in Expressions)

When doing arithmetic, Java promotes types automatically:

- If one operand is `double`, result is `double`.
    
- If one operand is `float`, result is `float`.
    
- If one operand is `long`, result is `long`.
    
- Otherwise → result is `int`.
    

Example:

```java
int a = 5;
double b = 2.5;
double result = a + b; // int promoted to double
```

---

## 🔹 4. char ↔ int Conversion

- `char` stores Unicode values (like `'A' = 65`).
    
- Can convert automatically to `int`.
    

```java
char c = 'A';
int code = c;        // 65

char d = (char) 66;  // 'B'
```

---

## 🔹 5. Wrapper Conversions (Autoboxing/Unboxing)

- Java provides wrapper classes (`Integer`, `Double`, etc.).
    
- Java auto-converts between primitives and wrappers.
    

```java
int a = 10;
Integer obj = a;     // autoboxing

int b = obj;         // unboxing
```

---

## 🔹 6. Boolean Exception

- `boolean` does **not** convert to/from numbers.
    

```java
boolean b = true;
// int x = (int) b; ❌ not allowed
```

---

# ✅ Summary

- **Widening (safe):** automatic, small → big.
    
- **Narrowing (unsafe):** manual, big → small, may lose data.
    
- **Numeric promotion:** smaller types promoted in expressions.
    
- **char ↔ int:** automatic, based on Unicode.
    
- **Autoboxing/unboxing:** automatic conversion between primitive ↔ wrapper.
    
- **boolean:** no conversion with numbers.