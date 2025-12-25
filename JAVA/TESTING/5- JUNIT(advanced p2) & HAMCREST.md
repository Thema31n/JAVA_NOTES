# ⭐ 1. Test Suites (Group Tests)

A **test suite** lets you run a group of tests together.

### Example (JUnit 5):

```java
@Suite
@SelectClasses({ CalculatorTest.class, UserTest.class })
class MySuite {}
```

You can also select whole packages.

Useful for:

- Running only integration tests
    
- Running only unit tests
    
- Structuring test sets
    

---

# 🏷️ 2. Tagging Tests (Grouping by Category)

Use `@Tag` to mark tests as:

- `fast`
    
- `slow`
    
- `integration`
    
- `api`
    

### Example:

```java
@Test
@Tag("slow")
void shouldLoadDataFromServer() {}
```

Then run:

- Only slow tests
    
- Only fast tests
    
- Only API tests
    

Helpful for CI/CD pipelines.

---

# 🔢 3. Test Execution Order

By default, JUnit runs tests in **any order** (random).  
To control order, you must specify a **MethodOrderer**.

```java
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
```

### Available orderers:

---

## ✔ OrderAnnotation

You choose the order manually using `@Order`.

```java
@Test @Order(1) void init() {}
@Test @Order(2) void process() {}
```

---

## ✔ MethodName

Sorts tests alphabetically by method name.

---

## ✔ DisplayName

Sorts tests alphabetically by the `@DisplayName` text.

---

## ✔ Random

Runs tests in random order to reveal hidden dependencies.

---

## ✔ Alphanumeric

Sorts tests by names containing numbers + letters.  
Example:  
`test1`, `test2`, `test10`

---

⚠ **Important:**  
Ordered tests weaken isolation. Use them only when absolutely needed.

---

# 🔀 4. Conditional Test Execution

JUnit can automatically **enable or disable** tests based on:

### ✔ Operating System

```java
@EnabledOnOs(OS.WINDOWS)
```

### ✔ Java version

```java
@EnabledOnJre(JRE.JAVA_17)
```

### ✔ Environment variables

```java
@EnabledIfEnvironmentVariable(named="ENV", matches="DEV")
```

### ✔ System properties

```java
@EnabledIfSystemProperty(named="browser", matches="chrome")
```

### ✔ Assumptions (dynamic conditions)

```java
assumeTrue(env.equals("DEV"));
```

If assumption fails → test is **skipped**.

---

# 🔁 5. Repeated Tests

Run the same test multiple times:

```java
@RepeatedTest(5)
void testMultipleRuns() {}
```

Useful for:

- flaky tests
    
- random values
    
- checking stability
    

---

# 📁 6. Using Temporary Folders (`@TempDir`)

JUnit 5 automatically creates a safe temp directory.

```java
@Test
void testTemp(@TempDir Path temp) throws IOException {
    Path file = temp.resolve("data.txt");
    Files.writeString(file, "Hello");
}
```

- Auto-cleaned
    
- No need to delete manually
    
- Perfect for file-based testing
    

---

# 🧰 7. Hamcrest Matchers (Readable Assertions)

Hamcrest makes tests **more expressive**.

Add dependency → then use:

```java
assertThat(value, is(10));
```

---

## ✔ Common Matchers (Simple Explanations)

### `is(x)`

Same as `equalTo(x)` but more readable.  
→ _“value should be 5”_

### `equalTo(x)`

Checks equality.  
→ _“value must equal x”_

### `not(x)`

Opposite of a matcher.  
→ _“value must NOT be x”_

### `containsString("abc")`

Check if text contains substring.

### `startsWith("http")`

Check prefix.

### `hasSize(n)`

Check collection size.

### `hasItem(x)`

Check if a list contains x.

### `greaterThan(x)` / `lessThan(x)`

Compare numbers.

---

## ✔ Example:

```java
assertThat("hello world", containsString("world"));
assertThat(List.of(1,2,3), hasSize(3));
assertThat(price, lessThan(100.0));
```

Hamcrest makes tests **more readable than assertEquals()**.

---

# 🔧 8. @RunWith (JUnit 4 Only)

In JUnit 4, `@RunWith` is used to activate a special test runner.

Example:

```java
@RunWith(MockitoJUnitRunner.class)
class MyTest {}
```

Common runners:

- Mockito
    
- Spring
    
- Parameterized tests
    

JUnit 5 **does not use @RunWith**.  
Use `@ExtendWith` instead.

---

# ⚠️ 9. Typical Testing Mistakes

### ❌ Overusing mocks

Only mock external dependencies (DB, web services).

### ❌ Writing slow tests

Keep most tests fast and isolated.

### ❌ Testing too many things in one test

Each test should check **one behavior**.

### ❌ Forgetting assertions

A test without assert = not a test.

### ❌ Hardcoding order of tests

Tests should work independently.

### ❌ Testing trivial getters/setters

No value.

### ❌ Poor naming

Test names should describe behavior clearly.

### ❌ Leaving temporary files undeleted

Use `@TempDir`.

---

# 📌 **Final Summary 
### ✔ Test Suites

Group multiple test classes with `@Suite`.

### ✔ Tagging Tests

Mark tests as fast, slow, integration, API, etc. using `@Tag`.

### ✔ Test Ordering

Order tests with:

- `OrderAnnotation`
    
- `MethodName`
    
- `DisplayName`
    
- `Random`
    
- `Alphanumeric`
    

### ✔ Conditional Execution

Enable/disable tests based on OS, JRE, env variables, or assumptions.

### ✔ Repeated Tests

Use `@RepeatedTest` to run a test many times.

### ✔ Temporary Folder

`@TempDir` gives you a safe folder that gets cleaned automatically.

### ✔ Hamcrest Matchers

Readable, expressive tests:

- `is()`, `equalTo()`, `not()`
    
- `containsString()`, `startsWith()`
    
- `hasSize()`, `hasItem()`
    
- `greaterThan()`, `lessThan()`
    

### ✔ @RunWith (JUnit 4)

Old mechanism for custom runners (use `@ExtendWith` in JUnit 5).

### ✔ Typical Mistakes

Over-mocking, slow tests, bad names, testing too much at once, relying on test order.
