## 🔹 **What is BigDecimal?**

- A **class in `java.math` package**.
    
- Used for **high-precision arithmetic** with floating-point numbers.
    
- Unlike `float` and `double`, it avoids rounding errors caused by binary representation.
    

👉 Example of the problem with `double`:

```java
System.out.println(0.1 + 0.2); // 0.30000000000000004 ❌
```

With `BigDecimal`:

```java
BigDecimal a = new BigDecimal("0.1");
BigDecimal b = new BigDecimal("0.2");
System.out.println(a.add(b)); // 0.3 ✅
```

---

## 🔹 **Creating BigDecimal**

- **From String (best way, avoids precision issues):**
    

```java
BigDecimal bd1 = new BigDecimal("123.45");
```

- **From int/long:**
    

```java
BigDecimal bd2 = BigDecimal.valueOf(100L);
```

- **Avoid using double constructor** (precision issues):
    

```java
BigDecimal bd3 = new BigDecimal(0.1); // ❌ imprecise
```

---

## 🔹 **Arithmetic Methods**

BigDecimal is **immutable** → every operation returns a new object.

- **Addition**
    

```java
BigDecimal result = bd1.add(bd2);
```

- **Subtraction**
    

```java
bd1.subtract(bd2);
```

- **Multiplication**
    

```java
bd1.multiply(bd2);
```

- **Division**
    

```java
bd1.divide(bd2, RoundingMode.HALF_UP);
```

- Must specify a `RoundingMode` if result is non-terminating.
    
- **Power**
    

```java
bd1.pow(2); // bd1 squared
```

---

## 🔹 **Rounding and Scale**

- **Scale:** number of digits after the decimal point.
    
- **setScale:** adjust precision.
    

```java
BigDecimal bd = new BigDecimal("123.4567");
BigDecimal rounded = bd.setScale(2, RoundingMode.HALF_UP);  // 123.46
```

Common `RoundingMode`s:

- `HALF_UP` → standard rounding (5 rounds up).
    
- `HALF_DOWN` → 5 rounds down.
    
- `HALF_EVEN` → "Banker’s rounding".
    
- `FLOOR`, `CEILING`, `DOWN`, `UP`.
    

---

## 🔹 **Comparison**

- `equals()` → checks value + scale.
    
- `compareTo()` → recommended for numerical comparison.
    

```java
BigDecimal x = new BigDecimal("2.0");
BigDecimal y = new BigDecimal("2.00");

System.out.println(x.equals(y));    // false ❌ (scale differs)
System.out.println(x.compareTo(y)); // 0 ✅ (numerically equal)
```

---

## 🔹 **Constants**

- `BigDecimal.ZERO`
    
- `BigDecimal.ONE`
    
- `BigDecimal.TEN`
    

---

## 🔹 **Useful Utilities**

- **max / min**
    

```java
bd1.max(bd2);
bd1.min(bd2);
```

- **abs**
    

```java
bd1.abs();
```

- **movePointLeft / movePointRight**
    

```java
BigDecimal bd = new BigDecimal("12345");

System.out.println(bd.movePointLeft(2));  // 123.45
System.out.println(bd.movePointRight(3)); // 12345000
```

---

# ✅ **When to Use BigDecimal**

- Financial calculations (money, currency).
    
- Scientific/engineering calculations where **precision matters more than performance**.
    
- Anywhere floating-point rounding errors are unacceptable.
    

---

⚡ **Downside:** Slower than `double` (because it’s an object, not hardware primitive). For performance-sensitive code, only use it where precision is critical.
