# Java Threading 

## 1. Thread Lifecycle

A Java thread moves through the following `Thread.State` values:

### **NEW**

Thread object constructed but `start()` not yet invoked.

### **RUNNABLE**

Thread is eligible to run; includes both “ready to run” and “actually running.”

### **BLOCKED**

Thread waiting to gain a monitor lock to enter `synchronized` code.

### **WAITING**

Thread waiting indefinitely until another thread performs a specific action:

- `wait()` inside synchronized block
    
- `join()` with no timeout
    
- `LockSupport.park()`
    

### **TIMED_WAITING**

Thread waiting for a bounded time:

- `sleep(millis)`
    
- `wait(timeout)`
    
- `join(timeout)`
    
- `LockSupport.parkNanos()` / `parkUntil()`
    

### **TERMINATED**

Thread has finished execution. The `run()` method returned normally or by exception.

---

# 2. How to Start a Thread (5 Ways)

## 2.1 Extending `Thread` Class

```java
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("MyThread running");
    }
}

new MyThread().start();
```

Use only when you must override other Thread behavior.

---

## 2.2 Implementing `Runnable`

```java
class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Runnable running");
    }
}

Thread t = new Thread(new MyTask());
t.start();
```

The recommended approach: separates task from thread.

---

## 2.3 Using Anonymous Runnable

```java
Thread t = new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("Anonymous Runnable executing");
    }
});
t.start();
```

---

## 2.4 Using Lambda Runnable

```java
Thread t = new Thread(() -> System.out.println("Lambda thread running"));
t.start();
```

Works because Runnable is a functional interface.

---

## 2.5 Using Callable + FutureTask

```java
Callable<Integer> task = () -> 100;
FutureTask<Integer> ft = new FutureTask<>(task);
Thread t = new Thread(ft);
t.start();  // ft.get() returns the result
```

---

## 2.6 Using ExecutorService (Thread Pool)

```java
ExecutorService ex = Executors.newFixedThreadPool(2);
ex.submit(() -> System.out.println("Thread pool task"));
```

---

## 2.7 Using ForkJoinPool

```java
ForkJoinPool pool = new ForkJoinPool();
pool.submit(() -> System.out.println("ForkJoin task"));
```

---

# 3. Thread Class: Constructors and Parameters

### `Thread()`

Creates a thread with:

- default name (e.g., Thread-0)
    
- no target runnable
    
- default priority
    
- inherits daemon flag from parent
    

### `Thread(Runnable target)`

Assigns a runnable to execute when started.

### `Thread(Runnable target, String name)`

Sets both the runnable task and a custom thread name.

### `Thread(String name)`

Thread with no runnable but custom name.

### `Thread(ThreadGroup group, Runnable target, String name)`

Allow grouping threads for collective management.

### Parameters Summary:

- **ThreadGroup** → logical grouping for security, interruption, enumeration
    
- **Runnable target** → execution logic
    
- **Name** → thread identification
    
- **Stack size (optional)** → advisory, rarely used
    

---

# 4. Runnable Implementation and `run()` Method

### Minimal example:

```java
class Worker implements Runnable {
    @Override
    public void run() {
        System.out.println(
            "Worker executed by: " + Thread.currentThread().getName()
        );
    }
}
```

### Key points:

- `run()` contains the logic executed by the thread.
    
- Does **not** create a new thread when called directly.
    
- Only calling `start()` creates a new execution path.
    

---

# 5. Difference Between `start()` and `run()`

|Method|Behavior|
|---|---|
|**start()**|Spawns a new OS-level thread and then calls `run()` asynchronously.|
|**run()**|Executes normally in the current thread; no concurrency occurs.|

---

# 6. Thread Properties

### **ID**

```java
long id = t.getId();
```

Unique and immutable.

### **Name**

```java
t.setName("Worker-1");
```

### **Priority**

```java
t.setPriority(Thread.MAX_PRIORITY);
```

Scheduler hint; no guarantee.

### **Daemon flag**

```java
t.setDaemon(true);
```

Background thread. JVM exits when only daemon threads remain.

---

# 7. TimeUnit Usage

```java
TimeUnit.SECONDS.sleep(1);
TimeUnit.MILLISECONDS.toSeconds(5000);
```

---

# 8. Interrupting Threads

### How to interrupt:

```java
t.interrupt();
```

### How to check:

|Method|Description|
|---|---|
|**interrupt()**|Sets the interrupt flag.|
|**isInterrupted()**|Returns flag without clearing.|
|**interrupted()**|Static; checks current thread & clears flag.|

---

# 9. Stopping Execution

### Preferred: interruption + exit conditions

```java
volatile boolean running = true;

public void run() {
    while (running && !Thread.currentThread().isInterrupted()) {
        // work
    }
}
```

### Deprecated (never use):

- `stop()` – unsafe
    
- `suspend()` – deadlock risk
    
- `resume()` – unsafe
    

---

# 10. `sleep()`

```java
Thread.sleep(1000);
```

- Causes TIMED_WAITING
    
- Does not release locks
    
- Throws `InterruptedException`
    

---

# 11. `join()`

Waits for a thread to finish:

```java
t.join();
t.join(5000); // timed wait
```

---

# 12. `currentThread()`

```java
Thread t = Thread.currentThread();
```

Returns the thread executing the current code.

---

# 13. DIFFERENCE SUMMARY TABLE

### Interrupt checks

|Method|Clears Flag?|Static?|Checks Which Thread?|
|---|---|---|---|
|**interrupt()**|No|No|Target thread|
|**isInterrupted()**|No|No|Target thread|
|**interrupted()**|Yes|Yes|Current thread only|

---

# 14. Code Example

```java
public class ThreadDemo {

    // Example Runnable implementing run()
    static class Worker implements Runnable {
        @Override
        public void run() {
            Thread t = Thread.currentThread();
            System.out.println("[Worker] Running in: " + t.getName());
            System.out.println("[Worker] ID: " + t.getId());
            System.out.println("[Worker] Priority: " + t.getPriority());
            System.out.println("[Worker] Daemon: " + t.isDaemon());

            for (int i = 0; i < 5; i++) {
                if (t.isInterrupted()) {
                    System.out.println("[Worker] Interrupted detected, stopping loop.");
                    return;
                }
                System.out.println("[Worker] Working iteration " + i);
                try {
                    TimeUnit.MILLISECONDS.sleep(300);
                } catch (InterruptedException e) {
                    System.out.println("[Worker] Interrupted during sleep.");
                    Thread.currentThread().interrupt(); // restore flag
                    return;
                }
            }
        }
    }

    public static void main(String[] args) throws Exception {

        // 1. Using Thread with Runnable implementation
        Thread t1 = new Thread(new Worker(), "Worker-Thread");
        t1.setPriority(Thread.NORM_PRIORITY + 1);

        // 2. Using Thread with anonymous Runnable
        Thread t2 = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("[Anon] Running in: " + Thread.currentThread().getName());
            }
        }, "Anonymous-Thread");

        // 3. Using lambda Runnable
        Thread t3 = new Thread(() -> {
            System.out.println("[Lambda] Executing in: " + Thread.currentThread().getName());
        }, "Lambda-Thread");

        // 4. Using Thread subclass
        Thread t4 = new Thread("Subclass-Thread") {
            @Override
            public void run() {
                System.out.println("[ThreadSubclass] Running in: " + getName());
            }
        };

        // Demonstrate start vs run
        System.out.println("Calling run() directly (NO new thread created)...");
        t3.run(); // runs in main thread

        System.out.println("Calling start() (new thread created)...");
        t3.start();

        t1.start();
        t2.start();
        t4.start();

        // Interrupt t1 after 1 second
        TimeUnit.SECONDS.sleep(1);
        System.out.println("Interrupting Worker-Thread...");
        t1.interrupt();

        // Using join
        System.out.println("Waiting for t1 to finish...");
        t1.join();
        System.out.println("Worker-Thread has finished.");

        System.out.println("Main thread interrupted? " + Thread.interrupted()); // clears flag
    }
}
```

---

# 15. Code Behavior Notes

- `run()` called manually runs on main thread.
    
- `start()` launches new OS-level thread.
    
- `interrupt()` sets interrupt flag and triggers `InterruptedException` during blocking operations.
    
- `isInterrupted()` checks without clearing.
    
- `interrupted()` checks current thread and clears.
    
- `join()` forces caller to wait for another thread.
    
- `sleep()` puts current thread into TIMED_WAITING.
    
- `TimeUnit` provides cleaner temporal operations.
    
- Daemon threads do not prevent JVM exit.
    
