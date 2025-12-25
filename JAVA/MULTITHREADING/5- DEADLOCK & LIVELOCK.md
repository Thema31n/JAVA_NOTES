# 1. Deadlock

## Definition

A **deadlock** occurs when two or more threads are permanently blocked because each thread is waiting for a resource that another thread holds.  
None can proceed; the system is stuck.

### The Four Conditions Required for Deadlock

(These must _all_ be true simultaneously.)

1. **Mutual Exclusion**  
    Resource can be held by one thread at a time.
    
2. **Hold and Wait**  
    A thread holds a resource and waits for another.
    
3. **No Preemption**  
    Resources cannot be forcibly taken away.
    
4. **Circular Wait**  
    A chain of threads wait on each other.
    

If you break **any** one condition → deadlock cannot occur.

---

# 2. Deadlock Reproduction Example

Two locks: `lockA` and `lockB`.  
Two threads acquire locks in opposite order.

### Example Code (Classic Deadlock)

```java
public class DeadlockDemo {
    private final Object lockA = new Object();
    private final Object lockB = new Object();

    public void methodA() {
        synchronized (lockA) {
            System.out.println("Thread 1: Holding lockA...");
            sleep(100);

            synchronized (lockB) {
                System.out.println("Thread 1: Holding lockB...");
            }
        }
    }

    public void methodB() {
        synchronized (lockB) {
            System.out.println("Thread 2: Holding lockB...");
            sleep(100);

            synchronized (lockA) {
                System.out.println("Thread 2: Holding lockA...");
            }
        }
    }

    private void sleep(long ms) {
        try {
            Thread.sleep(ms);
        } catch (InterruptedException e) {}
    }

    public static void main(String[] args) {
        DeadlockDemo d = new DeadlockDemo();

        new Thread(d::methodA).start();
        new Thread(d::methodB).start();
    }
}
```

### What Happens

- Thread 1: holds `lockA`, waits for `lockB`
    
- Thread 2: holds `lockB`, waits for `lockA`
    
- Both wait forever → deadlock
    

---

# 3. Avoiding Deadlock

### 3.1 Establish a Global Lock Order (Best Practice)

Always acquire locks in the same order across all threads.

Example:

```java
Object lockA = new Object();
Object lockB = new Object();
// always acquire lockA → lockB
```

### 3.2 Use Try-Lock with Timeout (ReentrantLock)

```java
Lock lockA = new ReentrantLock();
Lock lockB = new ReentrantLock();

if (lockA.tryLock(100, TimeUnit.MILLISECONDS)) {
    try {
        if (lockB.tryLock(100, TimeUnit.MILLISECONDS)) {
            try {
                // critical section
            } finally {
                lockB.unlock();
            }
        }
    } finally {
        lockA.unlock();
    }
}
```

No circular wait → no deadlock.

### 3.3 Break "Hold and Wait"

Acquire all locks at once (lock bundling pattern).

### 3.4 Use Non-Blocking Algorithms

Atomic variables, CAS, concurrent collections.

### 3.5 Use Higher-Level Concurrency Utilities

- `Semaphore`
    
- `BlockingQueue` (producer-consumer without locks)
    
- `Phaser`, `CyclicBarrier`
    

---

# 4. Livelock

## Definition

Threads are **not blocked**, but they keep responding to each other’s actions and **never make progress**.  
They are _active but stuck_.

### Analogy

Two people move side-to-side trying to avoid collision, mirroring each other forever.

---

# 5. Livelock Reproduction Example

Two threads repeatedly trying to acquire a lock, but backing off to avoid conflict.

### Example Code (Livelock)

```java
public class LivelockDemo {

    static class Resource {
        private volatile boolean locked = false;

        public synchronized boolean tryLock() {
            if (!locked) {
                locked = true;
                return true;
            }
            return false;
        }

        public synchronized void unlock() {
            locked = false;
        }
    }

    private static final Resource resource = new Resource();

    public static void main(String[] args) {

        Runnable r = () -> {
            String name = Thread.currentThread().getName();

            while (true) {
                if (resource.tryLock()) {
                    System.out.println(name + ": Acquired lock, working...");
                    sleep(200);
                    resource.unlock();
                    break;
                } else {
                    System.out.println(name + ": Lock busy → backing off");
                    sleep(50);  // both threads back off constantly → livelock
                }
            }
        };

        new Thread(r, "Thread-1").start();
        new Thread(r, "Thread-2").start();
    }

    private static void sleep(long ms) {
        try {
            Thread.sleep(ms);
        } catch (InterruptedException e) {}
    }
}
```

### Why this causes livelock

- Both threads detect lock is busy.
    
- Both back off at the same time.
    
- Both retry at the same time.
    
- They continue oscillating.
    

---

# 6. Avoiding Livelock

### 6.1 Use Randomized Backoff (Jitter)

Instead of synchronized backoff:

```java
sleep((long) (Math.random() * 100));
```

Different timing breaks the symmetry.

### 6.2 Design Deterministic Priorities

Assign one thread a lower/higher retry priority.

### 6.3 Limit the Number of Retries

If too many attempts → fallback → block → use wait/notify.

### 6.4 Utilize Blocking Locks Instead of Try-Lock

Blocking removes the repeated retry loop.

### 6.5 Avoid Symmetric Reactions

Ensure not all threads behave identically.

---

# 7. Deadlock vs Livelock Summary Table

|Aspect|Deadlock|Livelock|
|---|---|---|
|Threads blocked?|Yes|No|
|Progress made?|No|No|
|CPU usage|Low (waiting)|High (busy-looping)|
|Detectable by JVM?|Hard|Very hard|
|Example behavior|Waiting on each other|Constantly retrying or backing off|
|Cause|Circular wait|Symmetric behavior / retries|

---

# 8. Visual Diagram

### Deadlock

```
Thread A ──holds──> Lock 1
    ▲                │
    │                ▼
needs               holds
Lock 2 <────holds──── Thread B
```

### Livelock

```
Thread A: steps left → sees conflict → steps right
Thread B: steps right → sees conflict → steps left
(repeat forever)
```

---

# 9. Summary

- **Deadlock** = threads wait forever.  
    Fix by ordering locks, using tryLock, or avoiding circular waits.
    
- **Livelock** = threads keep working but never progress.  
    Fix by random backoff, asymmetry, or blocking primitives.
    

These patterns are critical in systems programming, real-time systems, and highly concurrent Java applications.
