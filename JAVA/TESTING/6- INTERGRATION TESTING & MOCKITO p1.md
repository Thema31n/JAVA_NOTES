## 🧪 **1. What Is Integration Testing?**

**Integration Testing** checks how two or more components **work together**.  
Instead of testing isolated units (classes), integration testing verifies:

- Modules → collaborate correctly
    
- Layers → communicate properly
    
- Services → exchange data
    

The focus is on interactions, not isolated logic.

### ✔ Example:

Testing a **Service + Repository** call together:

- `UserService` calls `UserRepository`
    
- Database connection is real OR simulated
    
- Testing actual application flow
    

Integration tests reveal:

- Misconfigured beans
    
- Serialization issues
    
- Real DB problems
    
- API endpoint issues
    
- Incorrect wiring
    

---

# 🧪 **2. Unit Testing vs Integration Testing**

|Feature|Unit Test|Integration Test|
|---|---|---|
|Scope|One class or one method|Multiple classes/modules|
|Uses|Test doubles (mocks)|Real dependencies (or partial mocks)|
|Speed|Fast (ms)|Slower (seconds)|
|Purpose|Check logic correctness|Check collaboration between components|
|Isolation|Fully isolated|Only partially isolated|
|Database|No|Often yes|
|HTTP calls|No|Yes (or simulated with WireMock)|

### Simple rule:

> **Unit tests test logic.  
> Integration tests test wiring.**

---

# 🧑‍🔬 **3. What Are Test Doubles?**

A **test double** is any object used in tests to replace a real dependency.

Why?  
Because real dependencies:

- Are slow
    
- Are unstable
    
- Require external systems
    
- Make tests flaky
    
- Make tests complex
    

Test doubles improve reliability and speed.

---

# 💠 **4. Advantages of Test Doubles**

✔ Faster tests  
✔ Reliable (no network/db failures)  
✔ Repeatable  
✔ Easy to simulate tricky scenarios  
✔ Focus on the unit under test  
✔ Avoid side effects (email sending, DB writes)  
✔ Make code more testable → better design

---

# 🧩 **5. Types of Test Doubles**

Common test double types used in unit testing:

---

## ⭐ 1. **Dummy**

Used only to fill parameter lists, not used.

```java
new Email("example@test.com")
```

---

## ⭐ 2. **Stub**

Provides fixed responses.

```java
when(repo.findUser()).thenReturn(new User("A"));
```

---

## ⭐ 3. **Mock**

Simulates behavior _and verifies interaction_.

```java
verify(emailService).send(anyString());
```

---

## ⭐ 4. **Spy**

Partial mock: calls real methods unless stubbed.

```java
UserService spy = spy(new UserService());
```

---

## ⭐ 5. **Fake**

Works like real, but simplified.

Example: in-memory database:

```java
class InMemoryUserRepo implements UserRepo {
    Map<Long,User> db = new HashMap<>();
}
```

---

# 🔗 **6. Types of Integration Testing**

Different ways to structure integration tests.

---

## ⭐ 1. **Big Bang Integration**

All modules integrated at once.  
(Not recommended.)

---

## ⭐ 2. **Top-Down Integration**

Start from highest-level modules → go downward.

---

## ⭐ 3. **Bottom-Up Integration**

Start from base modules → go upward.

---

## ⭐ 4. **Sandwich (Hybrid)**

Mix of top-down and bottom-up.

---

## ⭐ 5. **Contract Integration Testing**

Ensure two services’ API contracts match (consumer-driven tests).

---

# 🛠 **7. Frameworks for Integration Testing**

- **Spring Boot Test**
    
- **Testcontainers**
    
- **WireMock**
    
- **RestAssured**
    
- **H2 Database**
    
- **Arquillian / Jakarta EE**
    

---

# 🧩 **8. @ExtendWith (JUnit 5)**

JUnit 5 uses **extensions** instead of `@RunWith`.

```java
@ExtendWith(MockitoExtension.class)
class MyTest {}
```

Extensions provide:

- Lifecycle hooks
    
- Parameter injection
    
- Mockito support
    

---

# 🧰 **9. Mockito Overview**

Mockito is the **most popular mocking library in Java**.

Used for:

- Mocks
    
- Stubbing
    
- Verifying
    
- Argument capturing
    

---

# ⚙️ **10. Setup Mockito Environment**

## ✔ Maven

```xml
<dependency>
  <groupId>org.mockito</groupId>
  <artifactId>mockito-core</artifactId>
  <version>5.8.0</version>
  <scope>test</scope>
</dependency>

<dependency>
  <groupId>org.mockito</groupId>
  <artifactId>mockito-junit-jupiter</artifactId>
  <version>5.8.0</version>
  <scope>test</scope>
</dependency>
```

---

## ✔ Gradle

```gradle
testImplementation 'org.mockito:mockito-core:5.8.0'
testImplementation 'org.mockito:mockito-junit-jupiter:5.8.0'
```

---

# 🧪 **11. Mockito Extension for JUnit 5**

```java
@ExtendWith(MockitoExtension.class)
class MyTest {}
```

Enables:

- `@Mock`
    
- `@InjectMocks`
    
- `@Captor`
    
- Argument matchers
    
- Verification
    

---

# 🏗 **12. How to Create Mocks**

```java
@Mock EmailService emailService;
```

or

```java
EmailService email = mock(EmailService.class);
```

---

# 🔧 **13. Stubbing Behavior**

### ✔ Return values

```java
when(repo.save("A")).thenReturn(true);
```

### ✔ Throw exceptions

```java
when(repo.save(anyString())).thenThrow(new RuntimeException());
```

---

# 👁 **14. Verify Behavior**

```java
verify(emailService).send("mail@test.com");
```

With times:

```java
verify(emailService, times(2)).send(anyString());
```

---

# 🎯 **15. @InjectMocks**

Injects mocks into a real object automatically.

```java
@InjectMocks
SignupService signupService;
```

---

# 🎭 **16. Argument Matchers**

- `any()`
    
- `anyString()`
    
- `anyInt()`
    
- `eq("value")`
    

---

# 🎯 **17. Stubbing Exceptions**

```java
when(emailService.send(anyString())).thenThrow(new IllegalStateException());
```

---

# 🎒 **18. ArgumentCaptor**

```java
@Captor ArgumentCaptor<String> captor;

verify(emailService).send(captor.capture());
assertEquals("test@mail.com", captor.getValue());
```

---

# 📧 **19. Real-Life Email Service Example**

See code in the next section.

---

# 🧪 **20. Mockito + JUnit Jupiter**

Activated with:

```java
@ExtendWith(MockitoExtension.class)
```

---

# 🎯 **FULL DETAILED SUMMARY IS NOW COMPLETE.**

---

# 🧩 **BIG FULLY COMMENTED CODE EXAMPLE USING EVERYTHING**

This example uses:

- integration test concepts
    
- mocks
    
- stubbing
    
- exceptions
    
- verification
    
- captors
    
- parameterized tests
    
- repeated tests
    
- conditional tests
    
- ordering
    
- temp folder
    
- hamcrest matchers
    
- test suite
    
- mocking email service
    
- mockito + junit jupiter
    

---

```java
// -------------------------------------------------------------
// FULL INTEGRATED EXAMPLE USING EVERYTHING FROM THE SUMMARY
// -------------------------------------------------------------

package com.example;

import org.junit.jupiter.api.*;
import org.junit.jupiter.api.condition.EnabledOnOs;
import org.junit.jupiter.api.condition.OS;
import org.junit.jupiter.api.io.TempDir;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;
import org.junit.jupiter.api.RepeatedTest;
import org.junit.jupiter.api.MethodOrderer;
import org.junit.jupiter.api.TestMethodOrder;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;
import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.*;

import org.mockito.*;
import org.mockito.junit.jupiter.MockitoExtension;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.time.Duration;

// -------------------------------------------------------------
// PRODUCTION CLASSES UNDER TEST
// -------------------------------------------------------------

class EmailService {
    void send(String to, String message) {
        // pretend to send email
    }
}

interface UserRepository {
    boolean save(String email);
}

class SignupService {

    private final EmailService email;
    private final UserRepository repo;

    SignupService(EmailService email, UserRepository repo) {
        this.email = email;
        this.repo = repo;
    }

    public void signup(String emailAddress) {
        if (!repo.save(emailAddress)) {
            throw new IllegalStateException("Saving user failed");
        }
        email.send(emailAddress, "Welcome!");
    }
}

// -------------------------------------------------------------
// TEST SUITE (groups multiple classes)
// -------------------------------------------------------------

@Suite
@SelectClasses({ SignupServiceTest.class })
class MyTestSuite {}


// -------------------------------------------------------------
// FULL TEST CLASS USING EVERYTHING
// -------------------------------------------------------------

@ExtendWith(MockitoExtension.class)
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
@Tag("integration")
class SignupServiceTest {

    // Mocks
    @Mock EmailService emailService;
    @Mock UserRepository userRepository;

    // InjectMocks
    @InjectMocks SignupService signupService;

    // ArgumentCaptor
    @Captor ArgumentCaptor<String> captor;

    // Temporary folder
    @TempDir Path temp;

    // ---------------------------------------------------------
    @Test
    @Order(1)
    @DisplayName("1️⃣ signup should call save before sending email")
    void testOrderAndVerification() {

        // Stub save behavior
        when(userRepository.save("user@test.com")).thenReturn(true);

        signupService.signup("user@test.com");

        // Verify order: save → send
        InOrder order = inOrder(userRepository, emailService);
        order.verify(userRepository).save("user@test.com");
        order.verify(emailService).send("user@test.com", "Welcome!");
    }

    // ---------------------------------------------------------
    @Test
    @Order(2)
    @DisplayName("2️⃣ capture email argument using ArgumentCaptor")
    void testArgumentCaptor() {

        when(userRepository.save(anyString())).thenReturn(true);

        signupService.signup("captor@test.com");

        verify(emailService).send(captor.capture(), eq("Welcome!"));
        assertEquals("captor@test.com", captor.getValue());
    }

    // ---------------------------------------------------------
    @Test
    @Order(3)
    @DisplayName("3️⃣ should throw exception when saving user fails")
    void testExceptionStubbing() {

        when(userRepository.save(any())).thenReturn(false);

        assertThrows(IllegalStateException.class, () ->
            signupService.signup("bad@test.com")
        );
    }

    // ---------------------------------------------------------
    @ParameterizedTest
    @Order(4)
    @DisplayName("4️⃣ parameterized test for multiple emails")
    @ValueSource(strings = {"a@test.com", "b@test.com", "c@test.com"})
    void testParameterized(String email) {

        when(userRepository.save(email)).thenReturn(true);

        signupService.signup(email);

        verify(emailService).send(email, "Welcome!");
    }

    // ---------------------------------------------------------
    @RepeatedTest(3)
    @Order(5)
    @DisplayName("5️⃣ repeated test demonstration")
    void testRepeated() {
        when(userRepository.save(anyString())).thenReturn(true);
        signupService.signup("repeat@test.com");
        verify(emailService, atLeastOnce()).send(anyString(), anyString());
    }

    // ---------------------------------------------------------
    @Test
    @Order(6)
    @EnabledOnOs(OS.WINDOWS)
    @DisplayName("6️⃣ conditional test: runs only on Windows")
    void testConditional() {
        assertTrue(System.getProperty("os.name").toLowerCase().contains("win"));
    }

    // ---------------------------------------------------------
    @Test
    @Order(7)
    @DisplayName("7️⃣ hamcrest matchers test")
    void testHamcrest() {
        String welcome = "Hello and Welcome!";
        assertThat(welcome, containsString("Welcome"));
        assertThat(welcome, startsWith("Hello"));
        assertThat(10, greaterThan(5));
    }

    // ---------------------------------------------------------
    @Test
    @Order(8)
    @DisplayName("8️⃣ timeout test")
    void testTimeout() {
        assertTimeout(Duration.ofMillis(300),
            () -> Thread.sleep(100)
        );
    }

    // ---------------------------------------------------------
    @Test
    @Order(9)
    @DisplayName("9️⃣ TempDir example")
    void testTempDir() throws IOException {

        Path file = temp.resolve("file.txt");
        Files.writeString(file, "Hello Temp!");

        assertTrue(Files.exists(file));
        assertEquals("Hello Temp!", Files.readString(file));
    }
}
```
