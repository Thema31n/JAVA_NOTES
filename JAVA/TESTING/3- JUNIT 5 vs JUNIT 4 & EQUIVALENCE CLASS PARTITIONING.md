# 🧪 **JUnit Assertions Overview (Top Section)**

JUnit assertions help verify that code behaves as expected.  
They belong to:

- **JUnit 5:** `org.junit.jupiter.api.Assertions`
    
- **JUnit 4:** `org.junit.Assert`
    

### ✔ Common JUnit 5 Assertions

```java
assertEquals(expected, actual);
assertNotEquals(a, b);
assertTrue(condition);
assertFalse(condition);
assertNull(value);
assertNotNull(value);
assertSame(obj1, obj2);
assertNotSame(obj1, obj2);
```

### ✔ Collections / Arrays

```java
assertArrayEquals(expectedArray, actualArray);
assertIterableEquals(expectedList, actualList);
```

### ✔ Exception Assertions

```java
assertThrows(IllegalArgumentException.class, () -> method());
```

### ✔ Grouped Assertions

```java
assertAll(
   () -> assertEquals(5, x),
   () -> assertTrue(flag)
);
```

### ✔ Timeouts

```java
assertTimeout(Duration.ofMillis(100), () -> method());
```

These provide:

- Clear pass/fail outcomes
    
- Self-validating tests
    
- Better debugging with messages
    

---

# 🧪 **JUnit Lifecycle Annotations (JUnit 4 & 5)**

---

# **1. JUnit 5 Lifecycle Annotations**

### ✔ @BeforeEach

Runs **before every test**.

```java
@BeforeEach void setup() {}
```

### ✔ @AfterEach

Runs **after every test**.

```java
@AfterEach void teardown() {}
```

### ✔ @BeforeAll

Runs **once before all tests** (must be static unless using per-class lifecycle).

```java
@BeforeAll static void setupAll() {}
```

### ✔ @AfterAll

Runs **once after all tests**.

```java
@AfterAll static void teardownAll() {}
```

### ✔ @Test

Marks a test method.

---

# **2. JUnit 4 Lifecycle Annotations**

|JUnit 4|JUnit 5 Equivalent|
|---|---|
|`@Before`|`@BeforeEach`|
|`@After`|`@AfterEach`|
|`@BeforeClass`|`@BeforeAll`|
|`@AfterClass`|`@AfterAll`|
|`@Test`|`@Test`|

JUnit 4’s `@BeforeClass` and `@AfterClass` **must be static**.

---

# **3. JUnit 4 vs JUnit 5 (Quick Comparison)**

|Feature|JUnit 4|JUnit 5|
|---|---|---|
|Architecture|Single JAR|Modular (Platform, Jupiter, Vintage)|
|Test Engine|Built-in|Plugin system (TestEngine API)|
|Annotations|Older style|Richer, clearer lifecycle|
|Assertions|Basic|Rich, grouped, timeout, exception handling|
|Parameterized tests|External|Built-in|
|Extensions|@RunWith, @Rule|Unified Extension Model|
|Backward compatibility|N/A|Vintage engine runs JUnit 4|

---

# 🧪 **4. JUnit 5 Architecture (Platform, Jupiter, Vintage)**

### ⭐ **JUnit Platform**

Foundation that discovers & runs tests.  
Integrates with IDEs, build tools.

### ⭐ **JUnit Jupiter**

The API you use to write modern tests:

- `@Test`, `@BeforeEach`, etc.
    
- Assertions
    
- Extension model
    

### ⭐ **JUnit Vintage**

Allows JUnit 3 & 4 tests to run insideJUnit 5.

---

# 🧰 **5. Testing Exceptions**

### ✔ JUnit 5 — Best approach

```java
assertThrows(NumberFormatException.class, () -> {
    Integer.parseInt("abc");
});
```

### ✔ JUnit 4 — Legacy approach

```java
@Test(expected = NumberFormatException.class)
public void testException() {
    Integer.parseInt("abc");
}
```

---

# 🧪 **6. Test Structuring Patterns**

### ✔ Arrange – Act – Assert (AAA)

```
Arrange  // create object and input
Act      // call method
Assert   // verify behavior
```

### ✔ Given – When – Then (GWT)

```
Given initial state
When action occurs
Then verify outcome
```

### ✔ Build – Operate – Check (BOC)

```
Build object
Operate (invoke logic)
Check result
```

---

# 🧠 **7. Equivalence Class Partitioning (ECP)**

**ECP = test design technique** that divides input values into groups where each member behaves the same.

Example for valid range 1–100:

- Valid: 1–100 → test: 50
    
- Invalid low: <1 → test: 0
    
- Invalid high: >100 → test: 101
    

Benefits:

- Reduces number of tests
    
- Ensures good coverage
    
- Avoids redundancy
    

---

# 🧪 **8. Your First Unit Test Example**

### Production code:

```java
public class Calculator {
    public int add(int a, int b) { return a + b; }
}
```

### JUnit 5 Test:

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class CalculatorTest {

    @Test
    void testAdd() {
        // Arrange
        Calculator calc = new Calculator();

        // Act
        int result = calc.add(2, 3);

        // Assert
        assertEquals(5, result, "2 + 3 should equal 5");
    }
}
```

---

# 🧪 **9. Running JUnit Tests**

### IntelliJ

Right-click test → Run

### Maven

```
mvn test
```

### Gradle

```
gradle test
```

### Console Launcher

```
java -jar junit-platform-console-standalone.jar --scan-classpath
```

---

# 📌 **10. Summary 

- JUnit assertions verify correctness (equals, true/false, exceptions, timeouts, grouped).
    
- Lifecycle annotations differ between JUnit4 and JUnit5.
    
- JUnit 5 has three layers: **Platform, Jupiter, Vintage**.
    
- JUnit 5 is superior: more powerful, modular, and modern.
    
- Test structure patterns: AAA, Given-When-Then, Build-Operate-Check.
    
- ECP reduces test count while keeping broad coverage.
    
- Writing your first unit test is simple and follows AAA.
    
