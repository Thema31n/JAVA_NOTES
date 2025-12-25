# Java Lock API 

## 1. Why use the Lock API?

`java.util.concurrent.locks` gives more advanced locking than `synchronized`.

**Advantages**

- `tryLock()` (don’t block)
    
- `tryLock(timeout)`
    
- `lockInterruptibly()`
    
- Fairness
    
- Multiple `Condition` objects
    
- Non-block-structured locking
    

**Disadvantages**

- Must manually call `unlock()`
    
- More error-prone
    
- Harder than `synchronized`
    

---

# 2. `synchronized` Blocks vs `Lock`

## `synchronized` Example

```java
public class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;  // critical section
    }

    public synchronized int getCount() {
        return count;
    }
}
```

## `Lock` Example (ReentrantLock)

```java
import java.util.concurrent.locks.*;

public class Counter {
    private int count = 0;
    private final Lock lock = new ReentrantLock();

    public void increment() {
        lock.lock();
        try {
            count++;  // critical section
        } finally {
            lock.unlock();
        }
    }

    public int getCount() {
        lock.lock();
        try {
            return count;
        } finally {
            lock.unlock();
        }
    }
}
```

---

# 3. Lock Interface Overview

```java
Lock lock = new ReentrantLock();

if (lock.tryLock()) {
    try {
        System.out.println("Acquired lock without blocking");
    } finally {
        lock.unlock();
    }
} else {
    System.out.println("Could not acquire lock");
}
```

```java
lock.lockInterruptibly();  // can be interrupted while waiting
```

```java
lock.tryLock(5, TimeUnit.SECONDS);  // wait up to 5s
```

---

# 4. ReentrantLock Example

```java
import java.util.concurrent.locks.*;

public class BankAccount {
    private int balance = 1000;
    private final ReentrantLock lock = new ReentrantLock();

    public void deposit(int amount) {
        lock.lock();
        try {
            balance += amount;
        } finally {
            lock.unlock();
        }
    }

    public void withdraw(int amount) {
        lock.lock();
        try {
            if (balance >= amount) {
                balance -= amount;
            }
        } finally {
            lock.unlock();
        }
    }
}
```

---

# 5. Lock Fairness Example

```java
ReentrantLock fairLock = new ReentrantLock(true);
ReentrantLock unfairLock = new ReentrantLock(false);
```

```java
fairLock.lock();
try {
    System.out.println("Fair lock ensures FIFO ordering");
} finally {
    fairLock.unlock();
}
```

---

# 6. Conditions Example

Implementing a **bounded blocking queue**:

```java
import java.util.concurrent.locks.*;
import java.util.*;

public class BoundedQueue<T> {
    private final Queue<T> queue = new LinkedList<>();
    private final int capacity;

    private final Lock lock = new ReentrantLock();
    private final Condition notEmpty = lock.newCondition();
    private final Condition notFull  = lock.newCondition();

    public BoundedQueue(int capacity) {
        this.capacity = capacity;
    }

    public void put(T item) throws InterruptedException {
        lock.lock();
        try {
            while (queue.size() == capacity) {
                notFull.await();
            }
            queue.add(item);
            notEmpty.signal();    // signal a waiting consumer
        } finally {
            lock.unlock();
        }
    }

    public T take() throws InterruptedException {
        lock.lock();
        try {
            while (queue.isEmpty()) {
                notEmpty.await();
            }
            T item = queue.remove();
            notFull.signal();     // signal a waiting producer
            return item;
        } finally {
            lock.unlock();
        }
    }
}
```

---

# 7. ReadWriteLock Interface Example

```java
ReadWriteLock rwLock = new ReentrantReadWriteLock();
Lock rLock = rwLock.readLock();
Lock wLock = rwLock.writeLock();
```

---

# 8. ReentrantReadWriteLock Example

```java
import java.util.concurrent.locks.*;

public class PriceStore {
    private final Map<String, Integer> prices = new HashMap<>();
    private final ReentrantReadWriteLock rw = new ReentrantReadWriteLock();
    private final Lock readLock = rw.readLock();
    private final Lock writeLock = rw.writeLock();

    public int getPrice(String product) {
        readLock.lock();
        try {
            return prices.getOrDefault(product, -1);
        } finally {
            readLock.unlock();
        }
    }

    public void setPrice(String product, int price) {
        writeLock.lock();
        try {
            prices.put(product, price);
        } finally {
            writeLock.unlock();
        }
    }
}
```

---

# 9. StampedLock Examples

## **A) Optimistic Read Example**

```java
import java.util.concurrent.locks.*;

public class Point {
    private double x, y;
    private final StampedLock lock = new StampedLock();

    public double distanceFromOrigin() {
        long stamp = lock.tryOptimisticRead();
        double currentX = x;
        double currentY = y;

        if (!lock.validate(stamp)) {   // a write occurred
            stamp = lock.readLock();
            try {
                currentX = x;
                currentY = y;
            } finally {
                lock.unlockRead(stamp);
            }
        }
        return Math.sqrt(currentX * currentX + currentY * currentY);
    }
}
```

## **B) Write Lock Example**

```java
public void move(double dx, double dy) {
    long stamp = lock.writeLock();
    try {
        x += dx;
        y += dy;
    } finally {
        lock.unlockWrite(stamp);
    }
}
```

---

# 10. Quick Cheat Sheet (with examples)

## For simple critical sections

```java
synchronized(obj) {
    shared++;  
}
```

## Need advanced control

```java
lock.tryLock(1, TimeUnit.SECONDS)
```

## Many readers, few writers

```java
readLock.lock();
value = map.get(key);
readLock.unlock();
```

## Read-heavy + high performance

```java
long stamp = lock.tryOptimisticRead();
if (!lock.validate(stamp)) stamp = lock.readLock();
```
