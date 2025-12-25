# 🟦 **Introduction to Virtual Threads & JEP 444**

Virtual Threads are lightweight, user-mode threads introduced as a **final feature in Java 21** under **JEP 444**.  
They are designed to enable **massive concurrency**—millions of threads—while keeping the **traditional synchronous programming style**.

They represent the biggest shift in Java concurrency since the inception of the JVM.

> _Virtual threads bring back simple blocking programming with the scalability of asynchronous frameworks._

---

# 🟦 **Recap of Traditional Java Threads & Their Limitations**

## **Platform Threads**

Traditional threads (before Loom) map **1:1** with OS threads.

### Limitations:

- High memory usage (≈1 MB per thread)
    
- Slow context switching (managed by OS)
    
- Hard concurrency (requires async libs)
    
- Blocking I/O consumes OS threads
    
- Hard to scale beyond 5k–10k threads
    
- Thread pools become bottlenecks
    

These limitations caused widespread use of:

- CompletableFuture
    
- Reactive frameworks
    
- Callback-based async code  
    … all of which complicate development.
    

---

# 🟦 **Motivation for Virtual Threads (Project Loom)**

Project Loom aims to provide:

- Simple **blocking code** with async performance
    
- Millions of concurrently blocking tasks
    
- No need for complex async frameworks
    
- Eliminate callback hell
    
- Simplify thread management and cancellation
    

Essential insight:

> Most modern Java workloads are I/O bound, not CPU bound.  
> Virtual threads make blocking I/O inexpensive.

---

# 🟦 **Core Concepts: Virtual Threads & Carrier Threads**

## **Virtual Thread**

- Scheduled by JVM, not OS
    
- Very cheap to create
    
- Can number in the millions
    
- Ideal for request-per-thread models
    

## **Carrier Thread**

An underlying OS thread that **executes** virtual threads.

### How they work:

- Virtual threads run on carrier threads.
    
- Virtual threads performing **blocking I/O unmount** from carrier threads.
    
- Carrier threads are freed and reused.
    
- Virtual threads resume later.
    

This allows **blocking without blocking OS threads**.

---

# 🟦 **Virtual Threads vs Platform Threads**

|Feature|Platform Thread|Virtual Thread|
|---|---|---|
|Backing|OS thread|JVM continuation|
|Scheduler|OS|JVM|
|Max count|Thousands|Millions|
|Blocking cost|High|Near-zero|
|Memory|~1MB|Few KB|
|Best for|CPU work|I/O concurrency|

Virtual threads **do not replace** platform threads for CPU-bound work.

---

# 🟦 **Structured Concurrency (JEP 453)**

Structured concurrency treats concurrent tasks as structured code blocks.

Benefits:

- Programmers think in terms of tasks, not threads.
    
- Exceptions propagate cleanly.
    
- Ensures that subtasks finish before scope exits.
    
- Pairs naturally with virtual threads.
    

Example pattern:

```java
try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
    var future1 = scope.fork(() -> taskA());
    var future2 = scope.fork(() -> taskB());
    scope.join();
    scope.throwIfFailed();
}
```

---

# 🟦 **Creating & Running Virtual Threads**

## 1. One-liner

```java
Thread.startVirtualThread(() -> work());
```

## 2. Builder style

```java
Thread.ofVirtual().start(() -> work());
```

## 3. Executor

```java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    executor.submit(() -> handleRequest());
}
```

## 4. Using structured concurrency (Java 21+)

```java
try (var scope = new StructuredTaskScope<>()) {
    scope.fork(() -> fetchUser());
    scope.join();
}
```

---

# 🟦 **Parallel Task Execution with Virtual Threads**

```java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    IntStream.range(0, 1_000_000)
        .forEach(i -> executor.submit(() -> Thread.sleep(100)));
}
```

Handling **1 million** blocking tasks is now trivial.

---

# 🟦 **Virtual Threads Performance Comparison**

## Virtual Threads:

- Perfect for **I/O-bound** workloads.
    
- Excellent scaling (100k–1M threads).
    
- Almost no overhead on blocking.
    

## Platform Threads:

- Best for **CPU-bound** work.
    
- Very limited concurrency.
    

---

# 🟦 **Virtual Threads vs Blocking & Non-Blocking I/O**

### **Blocking I/O** (Recommended with VT)

Virtual threads can safely use:

- `Socket.read()`
    
- `HTTPClient.send()`
    
- JDBC (when supported)
    
- `Files.readAllLines()`  
    because they unmount on blocking.
    

### **Non-blocking I/O (NIO)**

Still useful for:

- High-performance data streaming
    
- Custom protocol handling
    
- Very high throughput transfer
    

But with VTs:

> Blocking I/O is the new default for simplicity and performance.

---

# 🟦 **Native Blocking Pitfalls & Thread Pinning**

### Thread pinning occurs when:

- Inside a `synchronized` block
    
- Native code performs blocking
    
- JNI calls block
    
- Certain monitors block
    

Pinned virtual thread = **temporarily tied to a carrier thread**  
→ Reduces concurrency.

### Detect pinning:

```
jcmd <pid> Thread.pin
```

---

# 🟦 **Diagnostic Tools**

## Enable native access:

```
--enable-native-access=ALL-UNNAMED
```

## Check pinned threads:

```
jcmd <pid> Thread.pin
```

---

# 🟦 **Why ThreadLocal Is No Longer Ideal**

ThreadLocal assumes:

- Few long-lived threads doing many tasks.
    

Virtual threads:

- Many short-lived threads doing one task each.
    

### Problems:

- Memory explosion with millions of VTs
    
- Leaks if not cleaned
    
- Wrong mental model for VT architecture
    

---

# 🟦 **Scoped Values (JEP 429)**

A modern replacement for ThreadLocals.

### Benefits:

- Immutable
    
- Safe
    
- Lexically scoped
    
- Perfect for virtual threads
    

### Example:

```java
static final ScopedValue<String> USER = ScopedValue.newInstance();

ScopedValue.where(USER, "Ahmed").run(() -> {
    System.out.println(USER.get());
});
```

---

# 🟦 **Migration Strategies for Legacy ThreadLocal Code**

1. Replace ThreadLocal with **ScopedValue**
    
2. Avoid long-lived thread pooling
    
3. Convert blocking operations to virtual-thread-safe form
    
4. Remove thread identity assumptions
    
5. Audit native/JNI blocking
    
6. Convert async code to synchronous VT-friendly code
    

---

# 🟩 **Final Master Cheat-Sheet**

- **Virtual Threads (JEP 444)** = millions of lightweight threads.
    
- **Platform Threads** = heavy OS-managed threads.
    
- **Goal** = scalable blocking concurrency.
    
- **Carrier threads** mount/unmount VTs.
    
- **Structured Concurrency** simplifies task management.
    
- **Scoped Values (JEP 429)** replace ThreadLocal.
    
- **Thread pinning** = avoid synchronized/native blocking.
    
- **VTs excel at I/O-bound workloads**.
    

---

# 🟧 **EXAMPLE USING ALL VIRTUAL THREAD FEATURES**

```java
import java.time.Duration;
import java.util.concurrent.*;
import java.util.stream.IntStream;
import jdk.incubator.concurrent.*;

public class VirtualThreadDemo {

    static final ScopedValue<String> REQUEST_ID = ScopedValue.newInstance();

    public static void main(String[] args) throws Exception {

        // ----------------------------------------
        // 1. Simple virtual thread
        // ----------------------------------------
        Thread.startVirtualThread(() ->
                System.out.println("Hello from VT: " + Thread.currentThread())
        );

        // ----------------------------------------
        // 2. Virtual Thread per Task Executor
        // ----------------------------------------
        try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
            executor.submit(() -> {
                Thread.sleep(100);
                System.out.println("VT executor task complete");
            });
        }

        // ----------------------------------------
        // 3. Structured Concurrency
        // ----------------------------------------
        try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {

            Future<String> user = scope.fork(() -> fetch("User"));
            Future<String> orders = scope.fork(() -> fetch("Orders"));

            scope.join();          // wait for both
            scope.throwIfFailed(); // propagate exceptions

            System.out.println("Results: " + user.result() + " & " + orders.result());
        }

        // ----------------------------------------
        // 4. Scoped Values instead of ThreadLocal
        // ----------------------------------------
        ScopedValue.where(REQUEST_ID, "REQ-1234").run(() -> {
            Thread.startVirtualThread(() -> {
                System.out.println("Scoped Request ID = " + REQUEST_ID.get());
            });
        });

        // ----------------------------------------
        // 5. Running many virtual threads in parallel
        // ----------------------------------------
        try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
            IntStream.range(0, 100_000)
                    .forEach(i ->
                            executor.submit(() -> {
                                Thread.sleep(10);
                                return i;
                            })
                    );
        }

        // ----------------------------------------
        // 6. Detecting thread pinning (run with jcmd)
        // ----------------------------------------
        synchronized (VirtualThreadDemo.class) {
            Thread.sleep(50);  // pinned area
        }
    }

    static String fetch(String name) throws Exception {
        Thread.sleep(200); // Blocking I/O simulated
        return "Fetched " + name;
    }
}
```

This example shows ALL key elements:

- Creating virtual threads
    
- Executors with 1-VT-per-task
    
- Structured concurrency
    
- ScopedValues
    
- Parallel execution
    
- Blocking I/O with virtual threads
    
- A synchronized region (pinning simulation)
    
