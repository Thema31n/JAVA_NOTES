# **1. What Is Unit Testing?**

Unit testing is the process of testing **small, isolated pieces** of code (methods, classes) to ensure correctness, prevent regressions, and improve maintainability.

A unit:

- Is small
    
- Is deterministic
    
- Has no external dependencies
    

---

# **2. Rules for Good Unit Tests**

Good tests are:

- **Fast**
    
- **Isolated**
    
- **Deterministic**
    
- **Repeatable**
    
- **Readable & simple**
    
- **Self-validating** (assertions)
    

---

# **3. The FIRST Principle**

Acronym describing characteristics of high-quality unit tests:

|Letter|Meaning|
|---|---|
|**F**|Fast|
|**I**|Independent|
|**R**|Repeatable|
|**S**|Self-Validating|
|**T**|Timely|

---

# **4. Test Structuring Patterns**

### ✔ Arrange – Act – Assert (AAA)

```
Arrange  // prepare
Act      // execute
Assert   // verify
```

### ✔ Given – When – Then (GWT)

```
Given some initial state
When action performed
Then expected outcome occurs
```

### ✔ Build – Operate – Check (BOC)

```
Build   // create objects
Operate // invoke logic
Check   // assert behavior
```

All three help increase readability and consistency.

---

# **5. Unit Testing Libraries in Java**

Common tools:

- **JUnit 5** → main testing framework
    
- **JUnit 4** → older, still used
    
- **Mockito** → mocking/stubbing
    
- **AssertJ** → fluent assertions
    
- **Hamcrest** → matchers
    
- **TestNG** → alternative test framework
    

---

# 🧩 **6. JUnit 5 Architecture (Important!)**

JUnit 5 is built on **three major components**:

---

## ⭐ **1. JUnit Platform**

The foundation.

- Runs test engines
    
- Provides console launcher
    
- Integrates with IDEs and build tools
    
- Defines the TestEngine API
    

Think of it as the **kernel** that loads and executes tests.

---

## ⭐ **2. JUnit Jupiter**

The **new** programming & extension model.

Includes:

- New annotations (`@Test`, `@BeforeEach`, etc.)
    
- Assertions & assumptions
    
- New extension model
    

This is what you use when writing modern tests.

---

## ⭐ **3. JUnit Vintage**

A **backward-compatibility** engine.

Allows JUnit 4 tests to run on JUnit 5.

Useful in:

- Legacy systems
    
- Mixed JUnit 4 + 5 projects
    

---

### 🔍 Visual Summary of JUnit 5 Structure

```
        ┌────────────────────────────┐
        │      JUnit Platform        │
        │   (launching, test engine) │
        └───────────┬───────────────┘
                    │
     ┌──────────────┼────────────────┐
     │              │                │
┌────▼─────┐  ┌─────▼─────┐  ┌──────▼─────┐
│ Jupiter  │  │ Vintage    │  │  Other     │
│ (JUnit 5 │  │ (JUnit 4)  │  │  Engines   │
│  tests)  │  │ support)   │  │ (Spock...) │
└──────────┘  └────────────┘  └────────────┘
```

---

# **7. JUnit Test Class Structure Example**

```java
class CalculatorTest {

    @BeforeAll
    static void setupAll() {}

    @BeforeEach
    void setup() {}

    @Test
    void testAddition() {}

    @AfterEach
    void teardown() {}

    @AfterAll
    static void teardownAll() {}
}
```

---

# **8. Enabling JUnit 5 in a Project**

---

## **Maven**

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.0</version>
    <scope>test</scope>
</dependency>

<build>
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.0.0-M7</version>
    <configuration>
      <useModulePath>false</useModulePath>
    </configuration>
  </plugin>
</build>
```

---

## **Gradle**

```gradle
testImplementation 'org.junit.jupiter:junit-jupiter:5.10.0'

test {
    useJUnitPlatform()
}
```

---

## **IntelliJ IDEA**

- File → Project Structure → Modules → Dependencies
    
- Add `junit-jupiter` via Maven
    
- Mark test folder as “Test Sources Root”
    

---

# **9. Adding External Libraries to a Project**

### ✔ Maven

Add dependency to `pom.xml`.

### ✔ Gradle

Add dependency to `build.gradle`.

### ✔ IntelliJ

Project Structure → Libraries → Add → Select JAR.

---

# **10. How to Run JUnit Tests**

### ✔ IntelliJ

Right-click test class → **Run**  
Right-click project → **Run All Tests**

### ✔ Maven

```
mvn test
```

### ✔ Gradle

```
gradle test
```

### ✔ JUnit Console Launcher

```
java -jar junit-platform-console-standalone.jar --scan-classpath
```

---

# **11. First Unit Test Example**

### Production code:

```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
}
```

### Test code:

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
        assertEquals(5, result);
    }
}
```

Output:

```
✔ Test Passed
```

---

# 📌 **12. Summary

### ✔ Unit Testing

Small, isolated logic tests to ensure correctness.

### ✔ Rules

Fast, isolated, deterministic, repeatable, simple.

### ✔ FIRST Principle

Fast, Independent, Repeatable, Self-validating, Timely.

### ✔ Patterns

AAA, GWT, BOC.

### ✔ Libraries

JUnit, Mockito, AssertJ, Hamcrest.

### ✔ JUnit 5 Architecture

- **Platform** → engine loader & launcher
    
- **Jupiter** → modern test framework
    
- **Vintage** → JUnit 3/4 compatibility
    

### ✔ JUnit Structure

Annotations, setup/teardown lifecycle.

### ✔ Setup

Add libraries via Maven/Gradle/IntelliJ.

### ✔ Running Tests

IDE, Maven, Gradle, CLI.

### ✔ First Test

Simple calculator test with AAA pattern.
