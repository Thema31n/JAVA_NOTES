# 1️⃣ **Exception Hierarchy (Basic)**

```
Object
 └── Throwable
      ├── Error   (unchecked)
      │     ├── OutOfMemoryError              (unchecked)
      │     ├── StackOverflowError            (unchecked)
      │     └── ThreadDeath                   (unchecked)
      │
      └── Exception                           (checked, unless RuntimeException)
            ├── IOException                   (checked)
            ├── SQLException                  (checked)
            └── RuntimeException              (unchecked)
                  ├── NullPointerException            (unchecked)
                  ├── IllegalArgumentException        (unchecked)
                  ├── ArithmeticException             (unchecked)
                  ├── NoSuchElementException          (unchecked)
                  ├── ClassCastException              (unchecked)
                  └── ArrayIndexOutOfBoundsException  (unchecked)
```

---

# 2️⃣ **Checked vs Unchecked**

### ✔ Checked Exceptions

- Must use **try-catch** OR **throws**
    
- Handled at **compile time**
    
- Examples: `IOException`, `SQLException`
    

### ✔ Unchecked Exceptions

- Extend `RuntimeException`
    
- Do **not** need to be caught
    
- Happen due to programming errors (logic bugs)
    
- Examples: `NullPointerException`, `ArithmeticException`
    

---

# 3️⃣ **Important Exception Methods**

|Method|What it does|
|---|---|
|`getMessage()`|Returns the error message|
|`getCause()`|Returns original wrapped exception|
|`getStackTrace()`|Returns stack trace data as objects|
|`printStackTrace()`|Prints the stack trace to console|

---

# 4️⃣ **try–catch Block**

### Basic structure:

```java
try {
    // risky code
} catch (ExceptionType e) {
    // handle exception
}
```

### Multiple catches:

```java
try {
    ...
} catch (IOException e) {
    ...
} catch (SQLException e) {
    ...
}
```

### Catching multiple types:

```java
catch (IOException | SQLException e)
```

### Catch order rule:

➡ More specific exception FIRST  
➡ More general exception LAST

---

# 5️⃣ **finally Block**

### ✔ Always runs

Whether exception occurs or not.

### Used for:

- Closing files
    
- Closing DB connections
    
- Releasing resources
    

Example:

```java
try {
    readFile();
} catch (IOException e) {
    System.out.println(e.getMessage());
} finally {
    closeFile();
}
```

### Even runs after `return`:

```java
try {
    return 10;
} finally {
    System.out.println("Always runs");
}
```

---

# 6️⃣ **throw vs throws**

### ✔ `throw`

- Used INSIDE a method
    
- Throws a specific exception object
    

Example:

```java
if(age < 0) {
    throw new IllegalArgumentException("Age cannot be negative");
}
```

---

### ✔ `throws`

- Used in method signature
    
- Tells the compiler "this method may throw"
    

Example:

```java
public void readFile() throws IOException {
    ...
}
```

---

# 7️⃣ **Custom Exception**

### Custom Checked Exception:

```java
class InvalidAgeException extends Exception {
    public InvalidAgeException(String msg) {
        super(msg);
    }
}
```

### Custom Unchecked Exception:

```java
class InvalidAgeRuntimeException extends RuntimeException {
    public InvalidAgeRuntimeException(String msg) {
        super(msg);
    }
}
```

---

# 8️⃣ **Best Practices**

✔ Never catch generic `Exception` unless necessary  
✔ Always log exceptions  
✔ Use custom exceptions to make meaning clearer  
✔ Use `finally` or **try-with-resources** to close resources  
✔ Avoid empty catch blocks  
✔ Throw exceptions only for exceptional conditions (not for flow control)

---

# 9️⃣ **Common Interview Questions (with answers)**

### **Q1: Difference between checked and unchecked exceptions?**

- Checked: must be handled, compile-time checked
    
- Unchecked: runtime errors, extend `RuntimeException`
    

---

### **Q2: Difference between `throw` and `throws`?**

- `throw` → throw an exception manually
    
- `throws` → tells compiler this method may throw
    

---

### **Q3: Does finally always run?**

Yes, except:

- JVM crash
    
- `System.exit()`
    
- Power failure
    

---

### **Q4: Can we have `try` without `catch`?**

Yes:

```java
try {  
} finally {  
}
```

---

### **Q5: Can constructor throw exceptions?**

Yes:

```java
public MyClass() throws IOException {}
```

---

### **Q6: What is try-with-resources?**

Automatically closes resources:

```java
try (FileInputStream f = new FileInputStream("x.txt")) {
    ...
}
```

---

### **Q7: Why use custom exceptions?**

To make the error more meaningful and readable.

---

# 🔟 **Small End-to-End Example**

```java
public void processFile() {
    try {
        readData();
    } catch (IOException e) {
        System.out.println("IO Error: " + e.getMessage());
        e.printStackTrace();
    } finally {
        System.out.println("Cleanup done.");
    }
}

public void readData() throws IOException {
    throw new IOException("File not found");
}
```

---

# ⭐ **11️⃣ Creating Real-World Custom Exceptions**

## 🔶 **Step 1: Choose a real-world domain scenario**

Example: **Banking System**  
A user tries to withdraw more than their current balance.

We create a meaningful custom exception:

- `InsufficientBalanceException` (checked)
    

---

## 🔶 **Step 2: Create the Custom Exception Class**

```java
public class InsufficientBalanceException extends Exception {

    private double balance;
    private double amount;

    public InsufficientBalanceException(String message, double balance, double amount) {
        super(message);
        this.balance = balance;
        this.amount = amount;
    }

    public double getBalance() {
        return balance;
    }

    public double getAmount() {
        return amount;
    }
}
```

✔ Fields store useful info  
✔ Constructor passes context and message  
✔ Realistic and meaningful

---

## 🔶 **Step 3: Throw the Exception in a Business Method**

```java
public class BankAccount {

    private double balance;

    public BankAccount(double balance) {
        this.balance = balance;
    }

    public void withdraw(double amount) throws InsufficientBalanceException {
        if (amount > balance) {
            throw new InsufficientBalanceException(
                "Insufficient funds",
                balance,
                amount
            );
        }
        balance -= amount;
    }
}
```

---

## 🔶 **Step 4: Handle the Exception**

```java
try {
    account.withdraw(150);
} catch (InsufficientBalanceException e) {
    System.out.println("❌ Withdrawal failed");
    System.out.println("Balance: " + e.getBalance());
    System.out.println("Attempted: " + e.getAmount());
    System.out.println("Message: " + e.getMessage());
}
```

---

Below is your **same content**, **same words**, only **cleanly formatted**.  
All code is now properly placed in **`.java` code blocks**.  
❗No word has been changed.

---

# ⭐ **12️⃣ Advanced Exception Handling Tricks & Interview Questions**

---

## **🔶 Q1. What happens if both catch and finally have return statements?**

**finally ALWAYS wins.**

Example:

```java
public int test() {
    try {
        return 1;
    } catch (Exception e) {
        return 2;
    } finally {
        return 3;
    }
}
```

**Output = 3**  
The value in `finally` overrides everything.

---

## **🔶 Q2. Can you catch an exception thrown in a finally block?**

Yes — but **only if `finally` is inside another try**.

Example:

```java
try {
    ...
} finally {
    try {
        throw new RuntimeException();
    } catch (Exception e) {
        ...
    }
}
```

---

## **🔶 Q3. What happens if both the try block and finally block throw an exception?**

The exception in **finally** OVERRIDES the one in try.

Example:

```java
try {
    throw new IOException("try");
} finally {
    throw new RuntimeException("finally");
}
```

**Result:**  
RuntimeException("finally")  
→ the IOException is lost.

---

## **🔶 Q4. What is Exception Chaining?**

Attaching the **original exception** to a new one:

```java
try {
    risky();
} catch (IOException e) {
    throw new RuntimeException("Wrapped exception", e);
}
```

Retrieve using:

```java
e.getCause();
```

---

## **🔶 Q5. Why shouldn’t you catch Exception or Throwable?**

Because:

- It catches **too many things** (even programming bugs)
    
- It hides errors like OutOfMemoryError
    
- Hard to debug
    
- Violates the rule of catching only what you can handle
    

---

## **🔶 Q6. Can a constructor throw exceptions?**

Yes.

```java
public Person() throws IOException {}
```

---

## **🔶 Q7. Can an interface method declare throws?**

Yes.

```java
interface A {
    void read() throws IOException;
}
```

The implementing class must obey or handle it.

---

## **🔶 Q8. Can overridden methods throw more exceptions?**

**No.**  
They can only throw:

- Fewer exceptions
    
- Narrower (subclass) exceptions
    
- Or none
    

Example:

```java
class A {
    void m() throws IOException {}
}

class B extends A {
    // allowed
    void m() throws FileNotFoundException {}

    // NOT allowed
    void m() throws Exception {}
}
```

---

## **🔶 Q9. What is a suppressed exception?**

When try-with-resources catches an exception thrown in `close()`  
**but preserves the original one.**

```java
try (MyResource r = new MyResource()) {
    throw new IOException("main");
}
```

You can get suppressed ones:

```java
e.getSuppressed();
```

---

## **🔶 Q10. Explain try-with-resources and how it works internally.**

Compiler converts:

```java
try (Resource r = new Resource()) {
    ...
}
```

Into:

```java
Resource r = new Resource();
try {
    ...
} finally {
    r.close();
}
```

And if `close()` throws an exception, it becomes **suppressed**, not lost.

---

## **🔶 Q11. Can we have multiple finally blocks?**

No.

But you can nest try–finally:

```java
try {
    ...
} finally {
    try {
        ...
    } finally {
        ...
    }
}
```

---

## **🔶 Q12. Difference between Throw Early vs Catch Late?**

**Throw early:**  
Detect problem at source — throw immediately.

**Catch late:**  
Handle exceptions at the highest level where recovery is possible.

This makes code cleaner.

---

## **🔶 Q13. Can finally block change the return value?**

Yes — and this is dangerous.

Example:

```java
public int test() {
    int x = 5;
    try {
        return x;  // returns 5
    } finally {
        x = 10;   // ignored — because primitive copied
    }
}
```

BUT if you return a **mutable object**, finally _can_ modify it:

```java
public List<Integer> test() {
    List<Integer> list = new ArrayList<>();
    try {
        return list;
    } finally {
        list.add(100);  // modifies returned object!
    }
}
```

---

## **🔶 Q14. What is the best way to log exceptions?**

Use:

```java
logger.error("Message", exception);
```

NOT:

```java
e.printStackTrace(); // bad practice in production
```

---

## **🔶 Q15. Can you rethrow the same exception?**

Yes.

```java
try {
    ...
} catch (IOException e) {
    throw e;
}
```

Java 7 also allows **more precise rethrow**:

```java
catch (Exception e) {
    throw e; // compiler knows exact type
}
```

---

## **🔶 Q16. What is the difference between fail-fast and fail-safe exceptions?**

- **Fail-fast** iterators throw `ConcurrentModificationException`
    
- **Fail-safe** iterators don’t throw; they work on cloned data
    

---

## **🔶 Q17. Can finally block skip running?**

Yes, ONLY if:

- JVM crash
    
- `System.exit(0)`
    
- Power failure
    
- Hardware failure
    

Otherwise it ALWAYS runs.

---

## **🔶 Q18. What is exception translation?**

Turning low-level exceptions into meaningful ones:

```java
catch (SQLException e) {
    throw new DataAccessException("DB failure", e);
}
```

---

## **🔶 Q19. How to create immutable custom exceptions?**

Make fields **final**:

```java
class InvalidDataException extends Exception {
    private final int code;
    public InvalidDataException(String msg, int code) {
        super(msg);
        this.code = code;
    }
}
```

---

## **🔶 Q20. Why are errors unchecked?**

Because:

- They represent **fatal** problems (OutOfMemoryError, StackOverflowError)
    
- The program **cannot** recover
    
- Catching them is useless and dangerous