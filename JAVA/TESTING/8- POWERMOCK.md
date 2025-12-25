# 🧨 **1. Overview of PowerMock Library**

PowerMock is a powerful mocking framework built on top of:

- **Mockito**
    
- **EasyMock**
    

It extends their capabilities by allowing you to mock things that Mockito **cannot** mock by default.

PowerMock is used when you MUST mock:

- static methods
    
- final classes
    
- final methods
    
- private methods
    
- constructors
    
- native methods
    
- new object creation
    

**PowerMock = Mockito + Superpowers**

---

# 💡 **2. Why PowerMock Exists**

Mockito **cannot** mock:

❌ static methods  
❌ final classes  
❌ final methods (older Java versions)  
❌ private methods  
❌ constructors  
❌ native methods

PowerMock solves these limitations through **bytecode manipulation**, which lets it instrument classes at runtime.

---

# ⚙️ **3. Environment Setup & Dependency Configuration**

PowerMock setup depends on your unit testing framework:

- JUnit 4 (most common)
    
- JUnit 5 (still limited support)
    

### ✔ Maven (JUnit + Mockito)

```xml
<dependency>
    <groupId>org.powermock</groupId>
    <artifactId>powermock-module-junit4</artifactId>
    <version>2.0.9</version>
</dependency>

<dependency>
    <groupId>org.powermock</groupId>
    <artifactId>powermock-api-mockito2</artifactId>
    <version>2.0.9</version>
</dependency>
```

### ✔ Gradle

```gradle
testImplementation 'org.powermock:powermock-module-junit4:2.0.9'
testImplementation 'org.powermock:powermock-api-mockito2:2.0.9'
```

> **Note:** JUnit 5 support is experimental. JUnit 4 is still the recommended runner for PowerMock.

---

# 🏗 **4. PowerMock Test Class Setup**

Typical JUnit 4 setup:

```java
@RunWith(PowerMockRunner.class)
@PrepareForTest({ StaticClass.class, FinalClass.class })
public class PowerMockDemoTest { }
```

Where:

- `@RunWith(PowerMockRunner.class)`  
    → activates bytecode manipulation
    
- `@PrepareForTest`  
    → tells PowerMock which classes you want to mock at JVM bytecode level
    

---

# 🧊 **5. Mocking Static Methods**

Static methods are **impossible** to mock with plain Mockito.  
PowerMock enables this:

```java
PowerMockito.mockStatic(Utils.class);

when(Utils.calculate()).thenReturn(99);

assertEquals(99, myService.callStatic());
```

### Usage Flow:

1. mockStatic(class)
    
2. stub static calls
    
3. verify static call (optional)
    

---

# 🧱 **6. Mocking Final Classes**

```java
FinalClass mock = PowerMockito.mock(FinalClass.class);
when(mock.run()).thenReturn("OK");
```

Useful when working with:

- legacy SDKs
    
- third-party libraries
    
- final models
    

---

# 🛠 **7. Mocking Final Methods**

PowerMock can mock final methods inside non-final classes:

```java
FinalMethodClass mock = PowerMockito.mock(FinalMethodClass.class);
when(mock.finalMethod()).thenReturn("mocked");
```

PowerMock modifies bytecode to remove the `final`.

---

# 🔐 **8. Mocking Private Methods**

Private methods cannot be called directly, but can be mocked using PowerMock:

```java
PowerMockito.spy(service);

PowerMockito.doReturn("hidden")
        .when(service, "privateMethod");
```

This is extremely useful for legacy code where:

- refactoring is not possible
    
- private logic needs isolation
    
- behavior-based testing is needed
    

---

# 🧬 **9. Mocking Constructors**

One of the most powerful PowerMock features.

### Example:

```java
whenNew(User.class)
        .withArguments("Ahmed")
        .thenReturn(mockUser);
```

Meaning:

> When someone writes `new User("Ahmed")`, return `mockUser` instead.

Use cases:

- Replacing new objects
    
- Preventing real resource creation
    
- Mocking costly objects (DB connections, IO)
    

---

# 🌐 **10. Mocking Native Methods**

Native methods come from C/C++ via JNI and cannot be mocked normally.

With PowerMock:

```java
NativeClass mock = PowerMockito.mock(NativeClass.class);
when(mock.nativeMethod()).thenReturn("mocked");
```

---

# 🧩 **11. Mocking equals() and hashCode()**

Mockito **cannot mock** equals() and hashCode() because they are final in `java.lang.Object`.

PowerMock can:

```java
PowerMockito.mockStatic(Object.class);
when(mock.equals(any())).thenReturn(true);
```

Or:

```java
MyFinalClass mock = PowerMockito.mock(MyFinalClass.class);
when(mock.hashCode()).thenReturn(12345);
```

Useful when equality logic prevents isolation.

---

# 🕵️‍♂️ **12. Partial Mocking / Spy with PowerMock**

Unlike Mockito spies, PowerMock spies allow mocking private & final methods.

```java
MyService spy = PowerMockito.spy(new MyService());

PowerMockito.doReturn("override")
    .when(spy, "privateMethod");

String res = Whitebox.invokeMethod(spy, "privateMethod");
```

This supports:

- private methods
    
- final methods
    
- static private calls (via static mocks)
    

---

# 🔥 **13. Mocking Static Private Methods**

Combine:

```java
PowerMockito.mockStatic(StaticClass.class);
PowerMockito.doReturn("hello")
    .when(StaticClass.class, "privateStaticMethod");
```

---

# ⛔ **14. PowerMock Limitations**

Although powerful, PowerMock has limitations:

### ❌ not compatible with:

- JUnit 5 extensions fully
    
- Mockito-inline
    
- Java 17+ in some cases
    

### ❌ slows down test performance

Because it modifies bytecode at runtime.

### ❌ encourages bad design

If you're mocking constructors and private methods often → code needs refactoring.

---

# 🎯 **FINAL SUMMARY (SHORT & CLEAN IN POINTS)**

- PowerMock extends Mockito's mocking limits
    
- Lets you mock static, final, private, constructors, native methods
    
- Setup with `@RunWith(PowerMockRunner.class)`
    
- Static mocking requires `mockStatic()`
    
- Constructor mocking requires `whenNew()`
    
- Private methods via `spy` + name
    
- Deep legacy code is PowerMock’s main target
    
- Should be used sparingly due to complexity & performance cost
    

---

# 💥 NOW THE FULL CODE EXAMPLE (ALL FEATURES WITH COMMENTS) 💥

This example demonstrates _everything_ from above.

---

```java
package com.example;

import org.junit.*;
import org.junit.runner.RunWith;

import static org.junit.Assert.*;
import static org.mockito.Mockito.*;
import static org.powermock.api.mockito.PowerMockito.*;

import org.powermock.core.classloader.annotations.PrepareForTest;
import org.powermock.modules.junit4.PowerMockRunner;

// -------------------------------------------------------------
// Production Classes (Fake)
// -------------------------------------------------------------

class Utils {
    static int calculate() { return 42; }
}

final class FinalClass {
    String run() { return "real"; }
}

class FinalMethodClass {
    final String secret() { return "real-secret"; }
}

class NativeClass {
    native String nativeMethod();
}

class User {
    User(String name) {}
}

class ConstructorService {
    User buildUser() {
        return new User("Ahmed"); // we will mock this
    }
}

class PrivateMethodService {
    private String hidden() { return "HIDDEN"; }
    public String expose() { return hidden(); }
}

class HashEqualsClass {
    @Override public boolean equals(Object o) { return false; }
    @Override public int hashCode() { return 0; }
}


// -------------------------------------------------------------
// FULL TEST CLASS SHOWING EVERYTHING
// -------------------------------------------------------------

@RunWith(PowerMockRunner.class)
@PrepareForTest({ Utils.class, FinalClass.class, FinalMethodClass.class,
                  PrivateMethodService.class, ConstructorService.class,
                  NativeClass.class, User.class })
public class PowerMockDemoTest {

    // ---------------------------------------------------------
    @Test
    public void testStaticMocking() {
        mockStatic(Utils.class);

        when(Utils.calculate()).thenReturn(99);

        assertEquals(99, Utils.calculate());
        verifyStatic(Utils.class);
        Utils.calculate();
    }

    // ---------------------------------------------------------
    @Test
    public void testFinalClassMock() {
        FinalClass mock = mock(FinalClass.class);
        when(mock.run()).thenReturn("mocked");

        assertEquals("mocked", mock.run());
    }

    // ---------------------------------------------------------
    @Test
    public void testFinalMethodMock() {
        FinalMethodClass mock = mock(FinalMethodClass.class);
        when(mock.secret()).thenReturn("override");

        assertEquals("override", mock.secret());
    }

    // ---------------------------------------------------------
    @Test
    public void testMockNativeMethod() {
        NativeClass mock = mock(NativeClass.class);
        when(mock.nativeMethod()).thenReturn("native-mocked");

        assertEquals("native-mocked", mock.nativeMethod());
    }

    // ---------------------------------------------------------
    @Test
    public void testMockConstructor() throws Exception {
        User fakeUser = mock(User.class);

        whenNew(User.class).withArguments("Ahmed").thenReturn(fakeUser);

        ConstructorService cs = new ConstructorService();
        User user = cs.buildUser();  // returns fakeUser instead of new User("Ahmed")

        assertSame(fakeUser, user);
    }

    // ---------------------------------------------------------
    @Test
    public void testMockPrivateMethod() throws Exception {
        PrivateMethodService spy = spy(new PrivateMethodService());

        doReturn("opened")
                .when(spy, "hidden");

        assertEquals("opened", spy.expose());
    }

    // ---------------------------------------------------------
    @Test
    public void testMockHashEquals() {
        HashEqualsClass mock = mock(HashEqualsClass.class);

        when(mock.equals(any())).thenReturn(true);
        when(mock.hashCode()).thenReturn(777);

        assertTrue(mock.equals(new Object()));
        assertEquals(777, mock.hashCode());
    }

    // ---------------------------------------------------------
    @Test
    public void testPartialMocking() throws Exception {
        PrivateMethodService spy = spy(new PrivateMethodService());

        doReturn("modified-hide")
                .when(spy, "hidden");

        assertEquals("modified-hide", spy.expose());
    }
}
```

---

# 🎉 WHAT THIS CODE DEMONSTRATES

|Feature|Shown In Code|
|---|---|
|Mock static method|`Utils.calculate()`|
|Mock final class|`FinalClass`|
|Mock final method|`FinalMethodClass.secret()`|
|Mock private method|`PrivateMethodService.hidden()`|
|Mock constructor|`whenNew(User.class)`|
|Mock native method|`NativeClass.nativeMethod()`|
|Mock equals/hashCode|`HashEqualsClass`|
|Partial mocking (spy)|`spy(private service)`|
|PowerMock runner setup|`@RunWith(PowerMockRunner.class)`|
|Prepare for test|`@PrepareForTest({ ... })`|

Everything is demonstrated.
