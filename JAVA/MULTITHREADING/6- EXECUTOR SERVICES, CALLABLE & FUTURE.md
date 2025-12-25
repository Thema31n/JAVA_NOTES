## 1. Executor Interface

### Definition

A minimal abstraction for asynchronous task execution.

### Core Method

```java
void execute(Runnable command)
```

### Purpose

- Provides a uniform API to submit tasks.
    
- Does **not** return results.
    
- Does **not** manage thread lifecycle.
    
- Implementations determine how tasks are executed (new thread, thread pool, synchronous execution, etc.).
    

---

## 2. ExecutorService Interface

### Definition

A richer extension of `Executor` that supports:

- task submission with results,
    
- lifecycle management,
    
- bulk execution.
    

### Submission Methods

```java
Future<?> submit(Runnable task)
Future<T> submit(Callable<T> task)
Future<T> submit(Runnable task, T result)
```

### Purpose

- Allows asynchronous execution with result retrieval.
    
- Supports both Runnable and Callable.
    

### Lifecycle Methods

```java
void shutdown()
List<Runnable> shutdownNow()
boolean isShutdown()
boolean isTerminated()
boolean awaitTermination(long timeout, TimeUnit unit)
```

### Purpose

- **shutdown()**: stop accepting tasks; allow running tasks to finish.
    
- **shutdownNow()**: attempt to interrupt running tasks & return queued tasks.
    
- **awaitTermination()**: wait for all tasks to finish.
    
- **isShutdown() / isTerminated()**: inspect shutdown state.
    

---

## 3. Thread Pools

### Provided by `Executors`

```java
Executors.newFixedThreadPool(n)
Executors.newCachedThreadPool()
Executors.newSingleThreadExecutor()
Executors.newScheduledThreadPool(n)
```

### Purpose

- Efficient reuse of threads.
    
- Reduced thread creation overhead.
    
- Provides scalability and controlled concurrency.
    

---

## 4. Starting Threads with ExecutorService

Instead of:

```java
new Thread(runnable).start();
```

Use:

```java
executor.execute(runnable);
```

or

```java
Future<T> f = executor.submit(callable);
```

### Purpose

- Centralized concurrency control.
    
- Enables result handling through `Future`.
    
- Integrates with lifecycle and cancellation management.
    

---

# 5. Callable vs Runnable

## Runnable

### Interface

```java
void run();
```

### Declarative Purpose

- Performs an action.
    
- Does **not** return a value.
    
- Cannot throw checked exceptions.
    

---

## Callable

### Interface

```java
T call() throws Exception;
```

### Declarative Purpose

- Produces a value of type T.
    
- May throw checked exceptions.
    
- Works naturally with `Future<T>`.
    

## Comparison Table

|Feature|Runnable|Callable|
|---|---|---|
|Returns a result|No|Yes|
|Throws checked exceptions|No|Yes|
|Method|run()|call()|
|Works with Future|Only as `submit(Runnable)` with `Future<?>`|Direct and natural|
|Use case|Fire-and-forget tasks|Tasks needing results|

---

# 6. Future

## cancel(boolean mayInterruptIfRunning)

Purpose:  
Attempts to cancel execution of the task.

- If task has not started → will never run.
    
- If running and `mayInterruptIfRunning = true` → thread interruption is requested.
    

---

## isCancelled()

Purpose:  
Returns true if the Future was cancelled before completing normally.

---

## isDone()

Purpose:  
Returns true if:

- task completed normally, OR
    
- task threw exception, OR
    
- the task was cancelled.
    

_(Done ≠ Successful)_

---

## get()

Purpose:  
Blocks until the task completes.

- On success → returns the result.
    
- On exception → throws `ExecutionException`.
    
- On interruption → throws `InterruptedException`.
    

---

## get(timeout, unit)

Purpose:  
Same as `get()`, but only waits the specified time.

- On timeout → throws `TimeoutException`.
    

---

# 7. invokeAll

### Definition

Executes a collection of `Callable` tasks and waits until **all** complete.

### Purpose

- Executes tasks in parallel and returns a `List<Future<T>>`.
    

---

# 8. invokeAny

### Definition

Executes a collection of tasks and returns the **first successful result**.

### Purpose

- Competitive execution.
    
- Cancels the remaining tasks automatically.
    

---

# 9. Thread Interruption with ExecutorService

### Cancellation via Future

```java
future.cancel(true);  // requests thread interruption
```

### Cancellation via shutdownNow()

```java
executor.shutdownNow();
```

### Declarative Purpose

- Provides cooperative cancellation.
    
- Tasks must check interruption or catch InterruptedException.
    

---

# 10. Full Java Example Demonstrating All Functions

(Every function + comments with expected behavior)

```java
import java.util.concurrent.*;
import java.util.*;

public class ConcurrencySummaryDemo {
    public static void main(String[] args) {
        ExecutorService es = Executors.newCachedThreadPool();

        System.out.println("=== Normal Callable with get() ===");
        Future<Integer> f1 = es.submit(() -> {
            Thread.sleep(500);
            return 10 + 5;
        });

        try {
            System.out.println("f1.get(): " + f1.get());
            // EXPECTED: prints 15 after ~0.5 sec
        } catch (Exception e) {
            e.printStackTrace();
        }

        System.out.println("\n=== Timeout Example ===");
        Future<String> f2 = es.submit(() -> {
            Thread.sleep(3000);
            return "Finished after delay";
        });

        try {
            System.out.println(f2.get(1, TimeUnit.SECONDS));
            // EXPECTED: TimeoutException thrown
        } catch (TimeoutException e) {
            System.out.println("Timeout occurred for f2");
        } catch (Exception e) {
            e.printStackTrace();
        }

        System.out.println("\n=== Cancellation Example ===");
        Future<Integer> f3 = es.submit(() -> {
            while (!Thread.currentThread().isInterrupted()) {
            }
            return 999; // never reached
        });

        System.out.println("Cancel result: " + f3.cancel(true));
        // EXPECTED: true (task cancelled and interruption requested)
        System.out.println("isCancelled(): " + f3.isCancelled());
        System.out.println("isDone(): " + f3.isDone());

        System.out.println("\n=== invokeAll Example ===");
        try {
            List<Callable<Integer>> tasks = List.of(
                () -> 1,
                () -> 2,
                () -> 3
            );
            List<Future<Integer>> results = es.invokeAll(tasks);
            for (Future<Integer> f : results) {
                System.out.println("invokeAll result: " + f.get());
            }
            // EXPECTED: prints 1, 2, 3
        } catch (Exception e) {
            e.printStackTrace();
        }

        System.out.println("\n=== invokeAny Example ===");
        try {
            Integer fastest = es.invokeAny(List.of(
                () -> { Thread.sleep(300); return 300; },
                () -> { Thread.sleep(100); return 100; },
                () -> { Thread.sleep(200); return 200; }
            ));
            System.out.println("invokeAny fastest result: " + fastest);
            // EXPECTED: 100
        } catch (Exception e) {
            e.printStackTrace();
        }

        System.out.println("\n=== Shutdown Example ===");
        es.shutdown();
        System.out.println("isShutdown(): " + es.isShutdown());
        try {
            boolean done = es.awaitTermination(5, TimeUnit.SECONDS);
            System.out.println("awaitTermination(): " + done);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        // Demonstrate shutdownNow
        ExecutorService es2 = Executors.newCachedThreadPool();
        es2.submit(() -> { while(true){} });
        List<Runnable> notRun = es2.shutdownNow();
        System.out.println("shutdownNow(): remaining queued tasks = " + notRun.size());
    }
}
```
