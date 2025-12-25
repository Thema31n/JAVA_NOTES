## 🔹 1. `Math.random()`

- Easiest way.
    
- Returns a **double** in range `[0.0, 1.0)`.
    

Example:

```java
double r = Math.random();
System.out.println(r); // e.g. 0.3456789123
```

👉 To get numbers in a range:

```java
int r = (int)(Math.random() * 10);      // 0–9
int r2 = (int)(Math.random() * 50) + 1; // 1–50
```

⚠️ Limitation: not customizable, always returns `double`, not suitable for cryptography.

---

## 🔹 2. `java.util.Random`

- More flexible.
    
- Can generate `int`, `long`, `float`, `double`, `boolean`.
    
- Optional **seed** → repeatable results.
    

### Example:

```java
import java.util.Random;

Random rand = new Random();

// random int
int r1 = rand.nextInt();

// random int in range 0–99
int r2 = rand.nextInt(100);

// random boolean
boolean b = rand.nextBoolean();

// random double 0.0–1.0
double d = rand.nextDouble();

// random float 0.0–1.0
float f = rand.nextFloat();
```

### Using seed:

```java
Random rand1 = new Random(123);
Random rand2 = new Random(123);

System.out.println(rand1.nextInt()); // same as rand2.nextInt()
```

👉 Useful for testing (predictable results).

---

## 🔹 3. `ThreadLocalRandom` (Java 7+)

- Faster in multithreaded environments.
    
- Avoids contention between threads.
    
- Used with static call `current()`.
    

### Example:

```java
import java.util.concurrent.ThreadLocalRandom;

int r = ThreadLocalRandom.current().nextInt(1, 101); // 1–100
double d = ThreadLocalRandom.current().nextDouble(5.0, 10.0); // 5.0–10.0
```

---

## 🔹 4. `SecureRandom` (java.security)

- For **cryptography** (passwords, tokens, encryption keys).
    
- Provides strong, unpredictable random values.
    

### Example:

```java
import java.security.SecureRandom;
import java.util.Arrays;

SecureRandom secRand = new SecureRandom();

// random int
int r = secRand.nextInt(100); // 0–99

// random bytes
byte[] bytes = new byte[16];
secRand.nextBytes(bytes);
System.out.println(Arrays.toString(bytes));
```

⚡ Much slower than `Random`, but cryptographically safe.

---

## 🔹 5. Random Streams (Java 8+)

- `Random` can generate **streams of random numbers** (useful with lambdas).
    

Example:

```java
import java.util.Random;

Random rand = new Random();
rand.ints(5, 0, 10)   // 5 numbers, range 0–9
    .forEach(System.out::println);
```

---

# ✅ Summary of Options

|Class/Method|Range/Type|Use Case|
|---|---|---|
|`Math.random()`|double [0.0,1.0)|Simple, quick random doubles|
|`Random`|int, long, float, double, boolean|General random numbers|
|`ThreadLocalRandom`|Same as Random|Multi-threaded apps (faster)|
|`SecureRandom`|Strong cryptographic random|Security, tokens, passwords|
|Random Streams (Java 8+)|int/long/double streams|Functional programming|

---

# ⚡ Example: Dice Roll Simulator

```java
import java.util.Random;

public class Dice {
    public static void main(String[] args) {
        Random rand = new Random();
        int dice = rand.nextInt(6) + 1; // 1–6
        System.out.println("You rolled: " + dice);
    }
}
```