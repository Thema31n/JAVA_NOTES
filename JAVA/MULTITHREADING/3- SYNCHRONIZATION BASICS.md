# Java Synchronization and Concurrency Control

---

# 1. Critical Section

A **critical section** is a segment of code that accesses shared data and must not be executed by more than one thread at the same time.

### Why critical sections matter

- Prevent race conditions
    
- Ensure data integrity
    
- Enforce predictable behavior in multi-threaded execution
    

### Example of a critical section (unsafe):

```java
counter++;     // NOT atomic, NOT safe
```

---

# 2. Atomic Operations

An **atomic operation** is an operation that executes as a single, indivisible step.  
No other thread can observe it partially executed.

### Atomic Examples in Java

- Reading or writing:
    
    - `boolean`
        
    - `byte`
        
    - `char`
        
    - `short`
        
    - `int`
        
    - `float`
        
    - **reference variables**
        

These are guaranteed atomically by the JVM on all platforms.

### Non-Atomic Examples

- `long` and `double` on some older JVMs (not guaranteed atomic on all platforms)
    
- Compound operations:
    
    - `counter++`
        
    - `x = x + 5`
        
    - `balance = balance - amount`
        
    - `if (flag) { ... }` combined with updates
        
    - Any read-modify-write sequence
        

### Example: Non-atomic increment

```java
counter++;
```

Equivalent to:

```java
read counter
add 1
write result
```

### Example: Atomic using AtomicInteger

```java
AtomicInteger counter = new AtomicInteger(0);
counter.incrementAndGet();    // atomic, thread-safe
```

---

# 3. Synchronization (Concept)

**Synchronization** ensures that only one thread accesses a critical section at a time.  
Java implements synchronization through **monitors**, which are associated with every object.

### What synchronization provides:

- Mutual exclusion (mutex)
    
- Visibility guarantees (happens-before)
    
- Ordering of operations
    

---

# 4. Monitor

A **monitor** is a synchronization construct that contains:

- A mutual exclusion lock (intrinsic lock)
    
- A waiting set (for `wait()`)
    
- A notification system (`notify`, `notifyAll`)
    

In Java:

- Every object is implicitly a monitor.
    
- `synchronized(obj)` acquires obj’s monitor lock.
    
- Only one thread may hold a monitor lock at a time.
    

### Monitor responsibilities

- Allow only one thread in synchronized code
    
- Coordinate inter-thread communication (`wait/notify`)
    

---

# 5. Mutex

A **mutex (mutual exclusion lock)** is a lock guaranteeing exclusive access.

### Monitor vs Mutex (Summary)

|Feature|Monitor (Java)|Mutex|
|---|---|---|
|Has intrinsic lock|Yes|Yes|
|Has wait/notify|Yes|No (usually separate condition variables)|
|Language-level support|Yes (`synchronized`)|Sometimes (but often library-level)|
|Supports condition queues|Yes|Not always|
|Used in Java|Everywhere|Rare (ReentrantLock acts like a mutex with extras)|

A Java monitor is **a mutex + condition variable + waiting mechanism** in one object.

---

# 6. `synchronized` Keyword

The `synchronized` keyword enforces mutual exclusion and memory visibility.

### Two ways to use it:

1. **Synchronized methods**
    
2. **Synchronized blocks**
    

---

## 6.1 Synchronized Method

### Instance-level lock

```java
public synchronized void increment() {
    counter++;
}
```

Lock is on `this`.

### Static synchronized method

```java
public static synchronized void log() {
    // lock on Class object
}
```

Lock is on `ClassName.class`.

---

## 6.2 Synchronized Block

More flexible and often more efficient:

```java
private final Object lock = new Object();

void update() {
    synchronized (lock) {
        // critical section
    }
}
```

Advantages:

- Reduces lock scope
    
- Allows locking on custom objects
    
- Avoids locking entire methods
    

---

## Basic Sync Example

### Without synchronization:

```java
class Counter {
    int count = 0;

    void increment() {
        count++;
    }
}
```

Not thread-safe due to race conditions.

### With synchronization:

```java
class Counter {
    int count = 0;

    synchronized void increment() {
        count++;
    }
}
```

Now multiple threads safely update the `count`.

---

# 7. Inter-Thread Communication: `wait`, `notify`, `notifyAll`

Java uses monitor methods for communication between threads waiting for conditions.

## 7.1 `wait()`

- Releases the monitor lock
    
- Moves thread to the wait set
    
- Thread will resume only when:
    
    - `notify()` wakes it, or
        
    - `notifyAll()` wakes all, or
        
    - Interrupted
        

### Must be inside synchronized block

```java
synchronized (lock) {
    lock.wait();
}
```

---

## 7.2 `notify()`

- Wakes **one** waiting thread (chosen by JVM)
    
- Does **not** release lock immediately
    

```java
synchronized (lock) {
    lock.notify();
}
```

---

## 7.3 `notifyAll()`

- Wakes **all** waiting threads
    
- They compete for the lock afterwards
    

```java
synchronized (lock) {
    lock.notifyAll();
}
```

---

# 8. Producer–Consumer Example: wait/notifyAll

```java
class Buffer {
    private int data;
    private boolean hasValue = false;

    public synchronized void put(int val) throws InterruptedException {
        while (hasValue) {
            wait(); // wait until buffer is empty
        }
        data = val;
        hasValue = true;
        System.out.println("Produced: " + val);
        notifyAll(); // wake consumers
    }

    public synchronized int get() throws InterruptedException {
        while (!hasValue) {
            wait(); // wait until buffer has value
        }
        int val = data;
        hasValue = false;
        System.out.println("Consumed: " + val);
        notifyAll(); // wake producer
        return val;
    }
}
```

### Notes:

- Use `while` instead of `if` to handle spurious wakeups
    
- `wait()` releases lock; `notify/notifyAll` does NOT
    
- After `notifyAll`, threads must reacquire the lock before proceeding
    

---

# 9. Synchronization Summary Table

|Concept|Description|Key Notes|
|---|---|---|
|Critical section|Code accessing shared resources|Must be protected|
|Atomic ops|Indivisible operations|`++` is NOT atomic|
|Monitor|Object managing locks & waits|Every object is a monitor|
|Mutex|Exclusive lock|Part of the monitor|
|`synchronized`|Acquires intrinsic lock|Enforces mutual exclusion|
|sync block|Fine-grained locking|Preferred vs method-level|
|wait()|Releases lock, waits|Must be in synchronized block|
|notify()|Wakes one waiter|Does not release lock|
|notifyAll()|Wakes all waiters|More predictable|
