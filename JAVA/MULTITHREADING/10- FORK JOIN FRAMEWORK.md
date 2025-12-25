# Fork/Join Framework

The **Fork/Join Framework** is Java’s high-performance parallel computation framework optimized for **divide-and-conquer**, **recursive decomposition**, and **compute-intensive** workloads. It provides dynamic load balancing using **work-stealing**, allowing tasks to be broken into smaller pieces and executed across multiple processor cores efficiently.

---

# 1. Fork/Join Framework Overview

### Purpose

Supports efficient parallelization of algorithms where a task can be repeatedly divided into independent subtasks.

### Key Characteristics

- Uses **ForkJoinPool** instead of standard thread pools.
    
- Optimized for **many small, short-lived tasks**.
    
- **Work-stealing** distributes tasks dynamically across worker threads.
    
- Minimizes context switching and locking through per-thread task queues.
    

### Execution Model

1. Task splits itself into subtasks (**fork**).
    
2. Subtasks execute in parallel on worker threads.
    
3. Parent task **joins** results after subtasks complete.
    

---

# 2. ForkJoinPool Class

`ForkJoinPool` is the core executor of the framework.

### Key Features

- Implements a **work-stealing scheduler**.
    
- Manages a set of worker threads.
    
- Designed for recursive, fine-grained parallelism.
    

### Constructor (All Parameters)

```java
ForkJoinPool pool = new ForkJoinPool(
    parallelism,
    threadFactory,
    uncaughtExceptionHandler,
    asyncMode
);
```

Below are detailed explanations of each parameter.

---

## 2.1 parallelism

### Definition

Determines the **target number of worker threads** the pool will maintain.

### Notes

- Usually defaults to `Runtime.getRuntime().availableProcessors()`.
    
- Represents the **maximum degree of parallel execution**.
    
- The pool may create _fewer_ threads if threads are idle or tasks are light.
    

### When to change it

- Increase for CPU-bound workloads only when you know the system has spare cores.
    
- Decrease when running inside heavily loaded environments or with multiple pools.
    

---

## 2.2 threadFactory

### Definition

A factory responsible for creating **ForkJoinWorkerThread** instances.

### Purpose

- Customize worker-thread names.
    
- Assign specific thread priorities.
    
- Integrate with custom thread-tracking or monitoring.
    
- Apply security managers or class loaders.
    

### Typical Usage

```java
ForkJoinPool pool = new ForkJoinPool(
    4,
    ForkJoinPool.defaultForkJoinWorkerThreadFactory,
    null,
    false
);
```

You implement a custom factory by overriding `newThread(ForkJoinPool pool)`.

---

## 2.3 uncaughtExceptionHandler

### Definition

A handler invoked when a worker thread throws an exception that is not caught.

### Why it matters

- Fork/join tasks run inside worker threads; unhandled exceptions can terminate a worker and degrade parallelism.
    
- Lets you log or respond to failures centrally.
    

### Example

```java
Thread.UncaughtExceptionHandler handler = (t, e) ->
    System.err.println("Error in " + t.getName() + ": " + e);

ForkJoinPool pool = new ForkJoinPool(
    4,
    ForkJoinPool.defaultForkJoinWorkerThreadFactory,
    handler,
    false
);
```

---

## 2.4 asyncMode

### Definition

Determines whether tasks execute in **FIFO mode** (async) or **LIFO mode** (default).

### Modes

- `false` (default): **LIFO**, optimized for recursive divide-and-conquer tasks
    
- `true`: **FIFO**, used for async-style or event-driven workloads
    

### Usage Notes

- Keep `false` for traditional fork/join workloads.
    
- Use `true` for tasks resembling pipeline processing or dependency chains.
    

---

# 3. Work-Stealing Algorithm (Core Mechanism)

The Fork/Join Framework uses work-stealing to maximize CPU utilization.

### How It Works

- Each worker thread has a **private deque** (double-ended queue).
    
- It pushes its own subtasks to the top.
    
- It pops from the top (LIFO) to maintain cache locality.
    
- If a worker runs out of work:
    
    - It **steals** a task from the **bottom** (FIFO end) of another worker’s deque.
        

### Benefits

- Automatic load balancing.
    
- Reduces contention compared to shared queues.
    
- Supports extremely fine-grained task decomposition.
    

---

# 4. RecursiveAction (Task Without a Return Value)

### Signature

`RecursiveAction` is used when no result is produced.

### Key Method

```java
protected abstract void compute();
```

### Use Case

- Performing operations such as printing, modifying arrays, or updating shared structures (with parallel-safe logic).
    

### Example

```java
class PrintTask extends RecursiveAction {
    private final int start, end;

    @Override
    protected void compute() {
        if (end - start <= 10) {
            for (int i = start; i < end; i++) System.out.println(i);
        } else {
            int mid = (start + end) / 2;
            invokeAll(new PrintTask(start, mid), new PrintTask(mid, end));
        }
    }
}
```

---

# 5. RecursiveTask (Task With a Return Value)

### Signature

`RecursiveTask` is used when tasks return results.

```java
protected abstract V compute();
```

### Example

Parallel sum of an array:

```java
class SumTask extends RecursiveTask<Integer> {
    @Override
    protected Integer compute() {
        if (end - start <= 1000) {
            int sum = 0;
            for (int i = start; i < end; i++) sum += arr[i];
            return sum;
        } else {
            int mid = (start + end) / 2;
            SumTask left = new SumTask(arr, start, mid);
            SumTask right = new SumTask(arr, mid, end);

            left.fork();
            int rightResult = right.compute();
            int leftResult = left.join();

            return leftResult + rightResult;
        }
    }
}
```

---

# 6. newWorkStealingPool()

### Definition

Creates an ExecutorService backed by a **ForkJoinPool** that uses work-stealing.

```java
ExecutorService pool = Executors.newWorkStealingPool();
```

### Behavior

- Parallelism = number of available processors.
    
- Ideal for running many independent tasks in parallel.
    
- Suitable for adaptively balancing irregular workloads.
    

### Key Note

Although it returns an `ExecutorService`, tasks still follow ForkJoin semantics.

---

# 7. Fork/Join Critique

## Strengths

1. Highly efficient for **CPU-bound** workloads.
    
2. Work-stealing ensures impressive **load balancing**.
    
3. Lightweight task scheduling.
    
4. Integrates with Java parallel streams.
    
5. Minimal thread contention.
    

## Weaknesses

1. **Not suitable for blocking tasks** (I/O, networking, sleeps).
    
2. More complex to design compared to executor services.
    
3. Debugging is more difficult due to nondeterministic scheduling.
    
4. Excessive splitting harms performance.
    
5. Using the global commonPool may cause interference between components.
    

---

# 8. Best Practices

- Use **thresholds** to stop splitting when subtasks become too small.
    
- Do not block inside the `compute()` method.
    
- Use `RecursiveTask` when returning a value; otherwise use `RecursiveAction`.
    
- Prefer custom ForkJoinPools to avoid interfering with commonPool.
    
- Understand task granularity; oversplitting hurts performance.
    

---

# 9. Full Example

```java
public class ForkJoinExample {
    public static void main(String[] args) {
        ForkJoinPool pool = new ForkJoinPool(
            4,
            ForkJoinPool.defaultForkJoinWorkerThreadFactory,
            (t, e) -> System.err.println("Uncaught: " + e),
            false
        );

        int[] data = new int[10_000];
        Arrays.fill(data, 1);

        SumTask task = new SumTask(data, 0, data.length);
        int result = pool.invoke(task);

        System.out.println("Sum = " + result);
        System.out.println("Pool size = " + pool.getPoolSize());
        System.out.println("Steal count = " + pool.getStealCount());
    }
}
```
``