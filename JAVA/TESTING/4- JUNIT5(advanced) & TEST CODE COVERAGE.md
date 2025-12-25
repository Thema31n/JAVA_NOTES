
# 🏷️ **1. @DisplayName (JUnit 5)**

`@DisplayName` allows you to give a **human-readable name** to a test class or method.

### ✔ Example:

```java
@DisplayName("Calculator Addition Tests")
class CalculatorTest {

    @Test
    @DisplayName("should add two numbers correctly")
    void addTest() { ... }
}
```

Benefits:

- Clearer test descriptions
    
- Better readability in test reports
    
- Useful for business-readable test names
    

---

# 🏷️ **2. @DisplayNameGeneration**

Allows you to control **automatic generation of display names** instead of writing `@DisplayName` manually.

### Built-in strategies:

- `IndicativeSentences`
    
- `ReplaceUnderscores`
    
- `Simple` (default)
    

### ✔ Example:

```java
@DisplayNameGeneration(DisplayNameGenerator.ReplaceUnderscores.class)
class CalculatorTest {

    @Test
    void addition_of_two_numbers_should_return_sum() {
    }
}
```

Display output:

```
addition of two numbers should return sum
```

---

# 🔗 **3. Grouped Assertions**

JUnit allows multiple assertions to run even if one fails.

### ✔ Example:

```java
assertAll("Person Properties",
    () -> assertEquals("Ahmed", person.getName()),
    () -> assertEquals(25, person.getAge()),
    () -> assertTrue(person.isActive())
);
```

Used when:

- Groups of related assertions
    
- Validating multiple properties of the same object
    

---

# 🔗 **4. Dependent Assertions**

Assertions inside scopes depending on earlier results:

```java
assertAll("Address",
    () -> {
        assertNotNull(address.getStreet());
        assertAll("Street Details",
            () -> assertTrue(address.getStreet().length() > 3),
            () -> assertTrue(address.getStreet().startsWith("A"))
        );
    }
);
```

If the outer assertion fails, inner ones won’t run.

---

# ⏱️ **5. Tests With Timeout**

JUnit 5 has two timeout methods:

### 1. `assertTimeout`

Runs the test and waits for completion.

```java
assertTimeout(Duration.ofMillis(500), () -> slowOperation());
```

### 2. `assertTimeoutPreemptively`

Interrupts if test exceeds timeout.

```java
assertTimeoutPreemptively(Duration.ofMillis(500), () -> slowOperation());
```

Use cautiously—can break thread-local state.

---

# ⚠️ **6. Assumptions**

Used to **conditionally run** tests only under certain conditions.

### ✔ Example:

```java
assumeTrue(System.getProperty("env").equals("dev"));
```

If assumption fails → test is **skipped**, not failed.

Useful for:

- OS-dependent tests
    
- Environment-dependent tests
    
- Tests requiring external systems
    

Other forms:

```java
assumeFalse(...)
assumingThat(condition, () -> { test code })
```

---

# 🔁 **7. Parameterized Tests**

Allow testing the **same logic with multiple inputs**.

### ✔ Enable in JUnit 5:

```java
@ParameterizedTest
@ValueSource(ints = {1, 2, 3})
void testWithMultipleValues(int number) {
    assertTrue(number > 0);
}
```

### Common parameter sources:

- `@ValueSource`
    
- `@CsvSource`
    
- `@CsvFileSource`
    
- `@EnumSource`
    
- `@MethodSource`
    

### Example with CSV:

```java
@ParameterizedTest
@CsvSource({"2,3,5", "10,10,20"})
void addTest(int a, int b, int result) {
    assertEquals(result, calculator.add(a, b));
}
```

---

# 🚫 **8. Disabling Tests**

Used to skip tests temporarily.

### ✔ Disable a test:

```java
@Disabled("Work in progress")
@Test
void testDisabled() {}
```

### ✔ Disable a class:

```java
@Disabled("Suite disabled")
class AllTestsDisabled { ... }
```

---

# 📊 **9. Test Code Coverage**

**Test coverage** measures how much of your production code is executed by tests.

Coverage types:

- **Line coverage** — lines executed
    
- **Branch coverage** — decision paths tested
    
- **Method coverage** — methods invoked
    
- **Condition coverage** — boolean expression variations
    

Common goals:

- 70–90% for critical modules
    
- Focus on meaningful tests, not chasing 100%
    

Coverage should:

- Expose missing tests
    
- Prevent dead code
    
- Increase reliability
    

---

# 📈 **10. EclEmma Plugin (JaCoCo)**

EclEmma is a **code coverage plugin** for Eclipse based on **JaCoCo**, the industry-standard Java coverage engine.

### ✔ Features:

- Shows coverage directly in editor
    
- Color-coded highlighting
    
- Generates reports (HTML, XML)
    
- Integrates with CI/CD
    

### ✔ IntelliJ Note:

IntelliJ uses **built-in JaCoCo** but can import JaCoCo data from EclEmma builds.

### ✔ Maven JaCoCo example:

```xml
<plugin>
  <groupId>org.jacoco</groupId>
  <artifactId>jacoco-maven-plugin</artifactId>
  <version>0.8.10</version>
  <executions>
    <execution>
      <goals>
        <goal>prepare-agent</goal>
      </goals>
    </execution>
    <execution>
      <id>report</id>
      <phase>test</phase>
      <goals>
        <goal>report</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```

---

# 📌 **Final Summary 

### ✔ Display Names

`@DisplayName` → readable test names  
`@DisplayNameGeneration` → auto-generates names

### ✔ Assertions

Group & dependent assertions for structured validation

### ✔ Timeouts

`assertTimeout` and `assertTimeoutPreemptively`

### ✔ Assumptions

Skip tests under unmet conditions

### ✔ Parameterized Tests

Reusable test logic over multiple inputs

### ✔ Disabling Tests

`@Disabled` for skipping tests

### ✔ Code Coverage

Line, branch, method, and condition coverage

### ✔ EclEmma (JaCoCo)

Coverage plugin for Eclipse; JaCoCo used widely in CI/CD
