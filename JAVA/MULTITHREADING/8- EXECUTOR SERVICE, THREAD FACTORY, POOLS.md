## 1. Executor Interface (High-Level Task Submission)

`Executor` is the simplest abstraction.

```java
void execute(Runnable command);
```

### What it is used for

- Submit tasks without managing threads directly.
    
- No lifecycle management.
    
- No task result retrieval.
    

### Example

```java
Executor executor = Runnable::run;  // trivial executor, runs on caller thread
executor.execute(() -> System.out.println("Task executed"));
```

---

# 2. ExecutorService (Managed Thread Execution)

`ExecutorService` extends `Executor` with:

- lifecycle control (`shutdown`, `shutdownNow`, `awaitTermination`)
    
- task submission (`submit`)
    
- task batches (`invokeAll`, `invokeAny`)
    

### Key functions and what they are used for

|Function|Purpose|
|---|---|
|`submit(Callable/Runnable)`|Execute a task; returns a `Future`|
|`shutdown()`|Graceful stop: finish existing tasks, accept no new tasks|
|`shutdownNow()`|Force stop: interrupt tasks; return tasks not started|
|`awaitTermination(timeout)`|Block until executor finishes after shutdown|
|`invokeAll(Collection)`|Run all tasks, return list of Futures|
|`invokeAny(Collection)`|Return result of the first successful task|

---

# 3. ThreadPoolExecutor (Core Implementation)

This is the **underlying engine** used by all pool factories.

### Main constructor (full form)

```java
ThreadPoolExecutor(
    int corePoolSize,
    int maximumPoolSize,
    long keepAliveTime,
    TimeUnit unit,
    BlockingQueue<Runnable> workQueue,
    ThreadFactory threadFactory,
    RejectedExecutionHandler handler
)
```

### What each part is for

- `corePoolSize`: number of always-alive worker threads.
    
- `maximumPoolSize`: maximum threads allowed under load.
    
- `keepAliveTime`: how long non-core threads stay idle before removal.
    
- `workQueue`: where tasks wait.
    
- `threadFactory`: creates new threads (names, priorities, daemons).
    
- `RejectedExecutionHandler`: handles tasks when queue is full.
    

### Observability / runtime metrics

Use these to inspect pool health:

```java
executor.getPoolSize();           // number of actual running threads
executor.getQueue().size();       // number of waiting tasks
executor.getActiveCount();        // threads currently executing tasks
executor.getCompletedTaskCount(); // completed tasks
```

---

# 4. Fixed Thread Pool (Deterministic Throughput)

### Purpose

Stable number of threads for CPU-bound or moderately I/O-bound workloads.

### Creation via factory

```java
ExecutorService es = Executors.newFixedThreadPool(4);
```

### Equivalent via constructor

```java
ExecutorService es =
    new ThreadPoolExecutor(
        4,
        4,
        0L,
        TimeUnit.MILLISECONDS,
        new LinkedBlockingQueue<>()
    );
```

### Behavior

- Always 4 threads.
    
- Each thread reused.
    
- Tasks queue up if all busy.
    

---

# 5. Cached Thread Pool (Dynamic, Elastic)

### Purpose

- Many short tasks.
    
- Highly variable workloads.
    
- Tasks that often block.
    

### Factory

```java
ExecutorService es = Executors.newCachedThreadPool();
```

### Equivalent constructor

```java
ExecutorService es =
    new ThreadPoolExecutor(
        0,
        Integer.MAX_VALUE,
        60L,
        TimeUnit.SECONDS,
        new SynchronousQueue<>()
    );
```

### Behavior

- No queue: tasks create new threads if all busy.
    
- Threads removed after 60 seconds idle.
    
- Can scale massively; risk of overload if uncontrolled.
    

---

# 6. Single Thread Executor (Sequential Processing)

### Factory

```java
ExecutorService es = Executors.newSingleThreadExecutor();
```

### Equivalent constructor

```java
ExecutorService es =
    new ThreadPoolExecutor(
        1,
        1,
        0L,
        TimeUnit.MILLISECONDS,
        new LinkedBlockingQueue<>()
    );
```

### Behavior

- Exactly one worker.
    
- All tasks executed sequentially.
    
- No concurrency issues for tasks using shared state.
    

---

# 7. Scheduled Thread Pool (Periodic/Delayed Execution)

### Factory

```java
ScheduledExecutorService ses = Executors.newScheduledThreadPool(4);
```

### Common methods

|Method|Purpose|
|---|---|
|`schedule(task, delay)`|Execute once after delay|
|`scheduleAtFixedRate(task, initial, period)`|Run repeatedly with **constant period**; start times fixed|
|`scheduleWithFixedDelay(task, initial, delay)`|Run repeatedly with **constant delay** after each execution|

### scheduleAtFixedRate vs scheduleWithFixedDelay

|Feature|scheduleAtFixedRate|scheduleWithFixedDelay|
|---|---|---|
|Interval|Between **start times**|Between **end and next start**|
|Useful For|Time-driven periodic tasks|Work-driven periodic tasks|
|Risk|Task overrun → thread drift|Safer for long tasks|

Example:

```java
ses.scheduleAtFixedRate(() -> doWork(), 0, 1, TimeUnit.SECONDS);
// Executes exactly every 1 second, regardless of task duration.

ses.scheduleWithFixedDelay(() -> doWork(), 0, 1, TimeUnit.SECONDS);
// Executes after 1 second has passed since the previous run finished.
```

---

# 8. Thread Factory (Control Thread Creation)

Used to:

- name threads
    
- set daemon mode
    
- set priority
    

Example:

```java
ThreadFactory factory = r -> {
    Thread t = new Thread(r);
    t.setName("worker-thread-" + t.getId());
    t.setDaemon(false);
    return t;
};

ExecutorService es = new ThreadPoolExecutor(
    4, 4, 0, TimeUnit.MILLISECONDS,
    new LinkedBlockingQueue<>(),
    factory
);
```

---

# 9. Thread Starvation

Occurs when:

- All pool threads are blocked
    
- Tasks depend on other tasks that cannot start
    
- Queue is full (in bounded pools with blocking tasks)
    

### Causes

- Using fixed pool with insufficient threads
    
- Deadlocks due to submitting tasks that wait on each other
    
- CPU-bound tasks oversubscribing the CPU
    

---

# 10. How Many Threads Should You Create?

## 10.1 CPU-bound formula

```text
Number of threads = Number of cores * Target CPU utilization
```

Example:

- Cores = 8
    
- Target utilization = 0.9
    

```text
Threads = 8 * 0.9 ≈ 7
```

## 10.2 For blocking tasks (includes network, IO, DB)

Use the **blocking coefficient formula**:

```text
Threads = Cores * Utilization * (1 + (WaitTime / ServiceTime))
```

- WaitTime = average blocking time
    
- ServiceTime = average CPU compute time
    
- WaitTime / ServiceTime = B (blocking coefficient)
    

### Example (IO-heavy workload)

- 8 cores
    
- Utilization = 0.9
    
- WaitTime = 100 ms (IO)
    
- ServiceTime = 20 ms
    

```text
Blocking coefficient B = 100 / 20 = 5
Threads = 8 * 0.9 * (1 + 5)
        = 8 * 0.9 * 6
        = 43
```

→ A pool of around **40–45 threads** is optimal.

---

# 11. Runtime.getRuntime().availableProcessors()

Used to detect CPU cores:

```java
int cores = Runtime.getRuntime().availableProcessors();
System.out.println("Available processors = " + cores);
```

Used in pool sizing.

---

# 12. Full Demonstration Code

Uses:

- pool sizing
    
- queue size
    
- pool size
    
- submit
    
- shutdown vs shutdownNow
    
- awaitTermination
    
- scheduling
    
- thread factory
    
- multiple constructors
    

```java
import java.util.*;
import java.util.concurrent.*;

public class ExecutorDemo {
    public static void main(String[] args) throws Exception {

        int cores = Runtime.getRuntime().availableProcessors();
        System.out.println("Cores: " + cores);

        // Example blocking coefficient scenario
        double wait = 100;   // ms
        double service = 20; // ms
        double utilization = 0.9;
        int optimalThreads =
            (int)(cores * utilization * (1 + (wait / service)));

        System.out.println("Optimal threads: " + optimalThreads);

        // Custom ThreadFactory
        ThreadFactory factory = r -> {
            Thread t = new Thread(r);
            t.setName("pool-worker-" + t.getId());
            return t;
        };

        ThreadPoolExecutor executor =
            new ThreadPoolExecutor(
                4,                 // core size
                optimalThreads,    // max size
                60,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<>(100), // bounded queue
                factory
            );

        // Submit several tasks
        Future<Integer> f1 = executor.submit(() -> {
            Thread.sleep(200);
            return 42;
        });

        Future<?> f2 = executor.submit(() -> {
            Thread.sleep(100);
            System.out.println("Runnable done");
        });

        System.out.println("Pool size: " + executor.getPoolSize());
        System.out.println("Queue size: " + executor.getQueue().size());

        System.out.println("Result f1: " + f1.get(1, TimeUnit.SECONDS));

        // InvokeAll example
        List<Callable<String>> batch = List.of(
            () -> "A",
            () -> "B",
            () -> "C"
        );

        List<Future<String>> results = executor.invokeAll(batch);
        for (Future<String> fr : results) {
            System.out.println("InvokeAll result: " + fr.get());
        }

        // Graceful shutdown
        executor.shutdown();
        if (!executor.awaitTermination(5, TimeUnit.SECONDS)) {
            System.out.println("Executor did not shut down gracefully.");
            executor.shutdownNow();
        }

        // Scheduled executor example
        ScheduledExecutorService ses =
            Executors.newScheduledThreadPool(2);

        ses.scheduleAtFixedRate(() ->
            System.out.println("Fixed rate: " + System.currentTimeMillis()),
            0, 1, TimeUnit.SECONDS);

        ses.scheduleWithFixedDelay(() ->
            System.out.println("Fixed delay: " + System.currentTimeMillis()),
            0, 1, TimeUnit.SECONDS);

        Thread.sleep(3500); // observe some outputs
        ses.shutdownNow();
    }
}
```
``