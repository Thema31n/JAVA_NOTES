# 🧪 **1. Method Invocation Verification Order**

Mockito uses `InOrder` to verify method calls happen in a specific sequence.

### Example:

```java
InOrder inOrder = inOrder(repo, emailService);

inOrder.verify(repo).save("user@test.com");
inOrder.verify(emailService).send("user@test.com", "Welcome!");
```

If the order is different → test fails.

---

# 🔢 **2. Checking Number of Invocations**

Mockito allows strict or flexible verification:

### Exactly once

```java
verify(service).doWork();
```

### Exactly N times

```java
verify(service, times(2)).process();
```

### At least N times

```java
verify(service, atLeast(1)).log(any());
```

### At most N times

```java
verify(service, atMost(3)).stop();
```

### Never

```java
verify(service, never()).delete();
```

---

# 🚫 **3. Verify No More Interactions**

### No more interactions allowed:

```java
verify(service).update();
verifyNoMoreInteractions(service);
```

### No interactions at all:

```java
verifyNoInteractions(repo);
```

---

# 🔧 **4. Calling Real Methods on a Mock**

For non-void methods:

```java
when(service.compute()).thenCallRealMethod();
```

For void methods:

```java
doCallRealMethod().when(service).cleanup();
```

---

# 🧠 **5. Mockito Answer Policies**

Mockito can control mock behavior _globally_:

### RETURNS_DEFAULTS

(default)

### RETURNS_SMART_NULLS

Fails fast when null is misused.

### RETURNS_MOCKS

Automatically creates mocks for return types.

### ⭐ RETURNS_DEEP_STUBS

Allows chaining:

```java
@Mock(answer = Answers.RETURNS_DEEP_STUBS)
Car car;

when(car.getEngine().getStatus().isRunning())
    .thenReturn(true);
```

Useful but dangerous because:

- hides bad design
    
- creates fragile tests
    
- overly permissive
    

---

# 🔍 **6. Additional Matchers**

### String matchers:

```java
startsWith("ab")
contains("xyz")
matches("[a-z]+")
```

### Number matchers:

```java
gt(10)
lt(5)
geq(7)
```

### Custom matcher:

```java
argThat(user -> user.getAge() > 18)
```

---

# 📌 **7. Verifying Void Methods**

Basic:

```java
verify(logger).log("done");
```

Void stubbing:

```java
doNothing().when(logger).log(anyString());
doThrow(new RuntimeException()).when(logger).log("FAIL");
```

---

# ⛔ **8. Mockito Limitations**

Mockito cannot mock:

- private methods
    
- constructors
    
- static methods (unless using plugin)
    
- equals/hashCode logic
    

Also:

- Overusing mocks → fragile tests
    
- Spies can accidentally execute real code
    

---

# 🕵️‍♂️ **9. Spies in Mockito**

Partial mock:

```java
List<String> list = new ArrayList<>();
List<String> spyList = spy(list);
```

Real methods run:

```java
spyList.add("A");
```

Stub specific methods:

```java
when(spyList.size()).thenReturn(100);
```

Verify real behavior:

```java
verify(spyList).add("A");
```


Below is **ONE BIG TEST CLASS** showing **EVERY FEATURE** with comments explaining exactly what is happening.

---

```java
package com.example;

import org.junit.jupiter.api.*;
import org.junit.jupiter.api.condition.*;
import org.junit.jupiter.api.io.TempDir;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;

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
import java.util.*;

// -------------------------------------------------------------
// Production classes used for examples
// -------------------------------------------------------------

class Logger {
    void log(String message) {
        System.out.println(message);
    }
}

class EngineStatus {
    boolean isRunning() { return false; }
}

class Engine {
    EngineStatus getStatus() { return new EngineStatus(); }
}

class Car {
    Engine getEngine() { return new Engine(); }
}

class EmailService {
    void send(String to, String message) {}
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
// TEST SUITE (combines test classes)
// -------------------------------------------------------------

@Suite
@SelectClasses({ FullMockitoDemoTest.class })
class DemoSuite {}


// -------------------------------------------------------------
// FULL MOCKITO EXAMPLE WITH ALL FEATURES
// -------------------------------------------------------------

@ExtendWith(MockitoExtension.class)
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
class FullMockitoDemoTest {

    // BASIC MOCKS
    @Mock EmailService emailService;

    @Mock UserRepository userRepository;

    // DEEP STUB MOCK
    @Mock(answer = Answers.RETURNS_DEEP_STUBS)
    Car car;

    // PARTIAL MOCK (SPY)
    @Spy
    Logger loggerSpy = new Logger();

    // Inject mocks into real class
    @InjectMocks SignupService signupService;

    // Captor
    @Captor ArgumentCaptor<String> captor;

    // Temp folder
    @TempDir Path temp;

    // ---------------------------------------------------------
    @Test
    @Order(1)
    @DisplayName("1️⃣ Stubbing, signup logic, verify order")
    void testSignupFlow() {

        when(userRepository.save("admin@test.com"))
                .thenReturn(true); // fake save success

        signupService.signup("admin@test.com");

        // VERIFY ORDER
        InOrder order = inOrder(userRepository, emailService);

        order.verify(userRepository).save("admin@test.com");
        order.verify(emailService).send("admin@test.com", "Welcome!");

        // NO EXTRA CALLS
        verifyNoMoreInteractions(emailService);
    }

    // ---------------------------------------------------------
    @Test
    @Order(2)
    @DisplayName("2️⃣ ArgumentCaptor captures real values")
    void testCaptor() {

        when(userRepository.save(anyString()))
                .thenReturn(true);

        signupService.signup("captor@test.com");

        verify(emailService).send(captor.capture(), eq("Welcome!"));

        assertEquals("captor@test.com", captor.getValue());
    }

    // ---------------------------------------------------------
    @Test
    @Order(3)
    @DisplayName("3️⃣ Stubbing Exceptions")
    void testExceptionStubbing() {

        when(userRepository.save(any()))
                .thenReturn(false); // simulate DB failure

        assertThrows(IllegalStateException.class, () ->
                signupService.signup("error@test.com")
        );
    }

    // ---------------------------------------------------------
    @ParameterizedTest
    @Order(4)
    @ValueSource(strings = {"a@test.com", "b@test.com"})
    @DisplayName("4️⃣ Parameterized test with matchers")
    void testParameterized(String email) {

        when(userRepository.save(email)).thenReturn(true);

        signupService.signup(email);

        verify(emailService).send(eq(email), anyString());
    }

    // ---------------------------------------------------------
    @RepeatedTest(2)
    @Order(5)
    @DisplayName("5️⃣ Repeated test")
    void repeatedTest() {
        when(userRepository.save(anyString())).thenReturn(true);

        signupService.signup("repeat@test.com");

        verify(emailService, atLeastOnce()).send(anyString(), anyString());
    }

    // ---------------------------------------------------------
    @Test
    @Order(6)
    @EnabledOnOs(OS.WINDOWS)
    @DisplayName("6️⃣ Conditional test example")
    void windowsOnlyTest() {
        assertTrue(true);
    }

    // ---------------------------------------------------------
    @Test
    @Order(7)
    @DisplayName("7️⃣ Additional matchers with Hamcrest")
    void testHamcrest() {
        assertThat("Hello Mockito", containsString("Mockito"));
        assertThat(99, greaterThan(50));
        assertThat("Start", startsWith("Sta"));
    }

    // ---------------------------------------------------------
    @Test
    @Order(8)
    @DisplayName("8️⃣ Timeout")
    void timeoutTest() {
        assertTimeout(Duration.ofMillis(300),
                () -> Thread.sleep(100));
    }

    // ---------------------------------------------------------
    @Test
    @Order(9)
    @DisplayName("9️⃣ TempDir example")
    void tempDirTest() throws IOException {

        Path file = temp.resolve("test.txt");
        Files.writeString(file, "Hello!");

        assertTrue(Files.exists(file));
        assertEquals("Hello!", Files.readString(file));
    }

    // ---------------------------------------------------------
    @Test
    @Order(10)
    @DisplayName("🔟 Deep stubs example")
    void deepStubsExample() {

        // Deep stubs allow chaining without NullPointer
        when(car.getEngine().getStatus().isRunning())
                .thenReturn(true);

        assertTrue(car.getEngine().getStatus().isRunning());
    }

    // ---------------------------------------------------------
    @Test
    @Order(11)
    @DisplayName("1️⃣1️⃣ Spies (partial mocks)")
    void spyExample() {

        // Real method call
        loggerSpy.log("This is real");

        // Stub method on spy
        doNothing().when(loggerSpy).log("NO-OP");

        // Should do nothing
        loggerSpy.log("NO-OP");

        // Verify real call executed
        verify(loggerSpy).log("This is real");
    }
}
```

---
