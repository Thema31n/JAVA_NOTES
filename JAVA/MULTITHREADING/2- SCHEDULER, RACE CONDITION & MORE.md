# Java Thread Scheduler, Scheduling Algorithms, Race Conditions, Daemon Threads, ThreadGroup, Priority, Yield, Exceptions in run()

---

# 1. Thread Scheduler

The **thread scheduler** is part of the JVM + OS cooperation responsible for deciding which thread runs at any given moment.  
Java itself does **not** define a deterministic scheduling policy; real scheduling behavior depends heavily on the operating system.

### Scheduler Responsibilities

- Selects the next RUNNABLE thread to execute
    
- Preempts threads according to OS time slicing
    
- Respects thread **priority** (but not guaranteed)
    
- May favor fairness or throughput depending on OS configuration
    

### Key Points

- Scheduling is not deterministic.
    
- JVM makes _no guarantees_ about thread order.
    
- High-priority threads may (or may not) receive more CPU time.
    
- Scheduling can vary across platforms (Linux vs Windows vs macOS).
    

---

# 2. Thread Scheduling Algorithms (Conceptual, not guaranteed)

Although Java does not specify which algorithm is used, typical OS-based algorithms include:

## 2.1 **Round Robin**

- Each thread gets a small time slice (quantum).
    
- After its time expires, the scheduler rotates to the next thread.
    
- Ensures fairness among RUNNABLE threads.
    

## 2.2 **Preemptive Priority Scheduling**

- Threads with higher priority are favored.
    
- Lower-priority threads may starve if high-priority threads never yield.
    
- Most desktop/server OS schedulers use a blend of this with round robin.
    

## 2.3 **Multilevel Feedback Queue (MLFQ)**

- Threads are placed into queues based on priority + CPU usage.
    
- CPU-intensive tasks may be temporarily deprioritized.
    

## 2.4 **Work-stealing (for ForkJoinPool)**

- Used by Java's Fork/Join framework.
    
- Idle worker threads “steal” tasks from busier workers.
    

---

# 3. Race Condition

A **race condition** occurs when:

1. Two or more threads operate on shared data
    
2. At least one modifies the data
    
3. Execution order affects correctness
    

### Example Race Condition:

```java
int counter = 0;

Thread t1 = new Thread(() -> { counter++; });
Thread t2 = new Thread(() -> { counter++; });
```

Threads may interleave so that the final counter is **1 instead of 2**.

### Causes

- Unsynchronized access to shared variables
    
- Non-atomic operations (`counter++`)
    
- Missing locks or memory barriers
    

### Avoidance Techniques

- `synchronized` blocks
    
- `ReentrantLock`
    
- Atomic variables (`AtomicInteger`)
    
- Thread confinement (only one thread owns the data)
    
- Immutable objects
    

---

# 4. Daemon Threads

A **daemon thread** is a background service thread.  
The JVM **exits** when only daemon threads remain.

### Characteristics

- Low-priority background services (e.g., GC, JIT, cleanup)
    
- Terminated abruptly when JVM exits
    
- Not suitable for critical application logic
    

### Setting daemon status

Must be set **before** `start()`:

```java
Thread t = new Thread(() -> {});
t.setDaemon(true);
t.start();
```

### Differences: Daemon vs User Thread

|Property|User Thread|Daemon Thread|
|---|---|---|
|JVM waits for it to finish|Yes|No|
|Suitable for critical tasks|Yes|No|
|Intended usage|Application logic|Background services|

---

# 5. ThreadGroup

`ThreadGroup` is a hierarchical structure for managing multiple threads as a unit.  
Modern Java discourages its use, but it is still part of the API.

### Features

- Group-level interruption (`group.interrupt()`)
    
- Enumerating threads in a group
    
- Setting max priority for all threads in a group
    
- Security + isolation (legacy)
    

### Creating a group

```java
ThreadGroup group = new ThreadGroup("MyGroup");
Thread t = new Thread(group, () -> {}, "Worker");
t.start();
```

### Methods

- `activeCount()` – approximate number of active threads
    
- `enumerate(Thread[])` – fill an array with references
    
- `interrupt()` – interrupt all threads in group
    
- `setMaxPriority(int p)` – limit all future threads
    

### Limitations

- Not thread-safe
    
- Not widely used in modern concurrency
    
- Executors + thread pools have replaced it
    

---

# 6. Priority Field

The thread priority is a scheduler **hint**, not a guarantee.

### Priority Range

```java
Thread.MIN_PRIORITY = 1
Thread.NORM_PRIORITY = 5
Thread.MAX_PRIORITY = 10
```

### Setting Priority:

```java
Thread t = new Thread(() -> {});
t.setPriority(Thread.MAX_PRIORITY);
```

### Key Notes

- Higher priority does NOT guarantee more CPU time.
    
- Behavior differs across OSes.
    
- Avoid relying on priority for correctness.
    

---

# 7. `yield()`

`Thread.yield()` suggests to the scheduler that the current thread is willing to pause its execution, allowing others to run.

### Behavior

- Moves thread from RUNNING → RUNNABLE
    
- Scheduler may ignore the hint
    
- Often used for performance tuning—not correctness
    

### Example:

```java
for (int i = 0; i < 1000; i++) {
    Thread.yield(); // hint for scheduler
}
```

### When to use

- Debugging concurrency behavior
    
- Fine-tuning performance under heavy contention
    

### When NOT to use

- To implement synchronization
    
- To enforce ordering (unreliable, platform-dependent)
    

---

# 8. Throwing Exceptions from `run()`

### Checked Exceptions

`run()` **cannot** declare checked exceptions:

```java
public void run() throws IOException { }   // illegal
```

Thus, checked exceptions must be caught inside `run()`.

### Unchecked Exceptions (RuntimeException)

If thrown inside `run()`, they will:

- Terminate the thread
    
- Print stack trace to stderr
    
- Not propagate to the calling thread
    

### Example:

```java
@Override
public void run() {
    throw new RuntimeException("Error in thread");
}
```

### Important Behavior

- Exceptions in threads do **not** kill the JVM.
    
- They do not affect other threads unless shared data is corrupted.
    
- ExecutorService allows capturing exceptions via `Future.get()`.
    

Example:

```java
Future<?> f = executor.submit(() -> {
    throw new RuntimeException("Task failed");
});

try {
    f.get(); // throws ExecutionException
} catch (ExecutionException e) {
    System.out.println("Task exception: " + e.getCause());
}
```

---

# 9. Detailed Behavior Summary Table

|Feature|Description|Key Notes|
|---|---|---|
|Thread Scheduler|Controls which thread runs|Platform-dependent|
|Scheduling Algorithm|RR, Priority, MLFQ|Not specified by Java|
|Race Condition|Incorrect shared-state access|Fix with synchronization|
|Daemon|Background thread|JVM exits if only daemons remain|
|ThreadGroup|Legacy grouping mechanism|Replaced by executors|
|Priority|Scheduler hint|Not guaranteed|
|yield()|Hint to pause|Might be ignored|
|Throwing exceptions|Stops thread|Does not propagate|
