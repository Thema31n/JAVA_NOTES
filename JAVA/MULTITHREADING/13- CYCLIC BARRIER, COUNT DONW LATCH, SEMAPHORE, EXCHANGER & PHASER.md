# 🟦 **CountDownLatch**

## **What it is**

A one-time synchronization gate. Threads wait until a fixed number of events occur.

## **When to Use**

- Wait for multiple tasks to finish before proceeding.
    
- Startup/shutdown coordination.
    
- One-time events.
    

## **How it behaves**

- Count decreases via `countDown()`
    
- When count reaches zero → all waiting threads proceed.
    
- Cannot be reset.
    

## **Main Functions**

|Method|Description|
|---|---|
|`CountDownLatch(int count)`|Create latch|
|`void countDown()`|Decrement count|
|`void await()`|Wait until count hits 0|
|`boolean await(timeout)`|Wait with timeout|
|`long getCount()`|Remaining count|

## **Example**

```java
CountDownLatch latch = new CountDownLatch(3);

Runnable service = () -> {
    try { Thread.sleep(500); } catch (Exception e) {}
    System.out.println(Thread.currentThread().getName() + " ready");
    latch.countDown();
};

new Thread(service, "DB").start();
new Thread(service, "Auth").start();
new Thread(service, "Cache").start();

latch.await();
System.out.println("All services initialized");
```

---

# 🟦 **CyclicBarrier — Declarative Summary**

## **What it is**

A reusable synchronization point where a fixed number of threads must meet before proceeding.

## **When to Use**

- Multi-step or phased algorithms.
    
- Simulations, game rounds, iterative algorithms.
    
- When threads must move forward **together**.
    

## **How it behaves**

- Threads call `await()` and wait.
    
- Once all arrive → optional barrier action executes.
    
- Barrier resets automatically.
    

## **Main Functions**

|Method|Description|
|---|---|
|`CyclicBarrier(int n)`|Create with n parties|
|`CyclicBarrier(int n, Runnable action)`|Run action once all arrive|
|`int await()`|Arrive and wait|
|`int await(timeout)`|Wait with timeout|
|`int getNumberWaiting()`|Threads currently waiting|
|`int getParties()`|Number of required threads|
|`void reset()`|Manually reset barrier|

## **Example**

```java
CyclicBarrier barrier = new CyclicBarrier(3,
    () -> System.out.println("Round starting"));

Runnable player = () -> {
    System.out.println(Thread.currentThread().getName() + " ready");
    try { barrier.await(); } catch (Exception e) {}
    System.out.println(Thread.currentThread().getName() + " playing");
};

new Thread(player, "P1").start();
new Thread(player, "P2").start();
new Thread(player, "P3").start();
```

---

# 🟦 **CyclicBarrier vs CountDownLatch**

|Feature|CountDownLatch|CyclicBarrier|
|---|---|---|
|Resettable|❌ No|✔ Yes|
|Who waits?|Threads wait for _events_|Threads wait for _each other_|
|Who triggers?|Other threads call `countDown()`|Same threads call `await()`|
|Action on trigger?|❌ No|✔ Yes|
|Reusability|One-time|Multi-use|

---

# 🟦 **Semaphore — Declarative Summary**

## **What it is**

A concurrency limiter based on **permits**, controlling how many threads can access a shared resource simultaneously.

## **When to Use**

- Connection pools
    
- Rate limiting
    
- Access control to limited resources
    
- Throttling
    

## **How it behaves**

- Threads **acquire** permits to enter the critical region.
    
- They **release** permits afterwards.
    
- Semaphore may be **fair** (FIFO) or **unfair** (default).
    

---

## **Creating a FAIR semaphore**

```java
Semaphore fairSemaphore = new Semaphore(permits, true);
```

- `true` → fairness enabled (FIFO)
    
- `false` (default) → threads may "barge" ahead
    

---

## **Acquiring MULTIPLE permits**

```java
semaphore.acquire(3);       // blocks until 3 permits available
semaphore.tryAcquire(3);    // doesn’t block
semaphore.tryAcquire(3, 2, TimeUnit.SECONDS);  // with timeout
```

## **Releasing MULTIPLE permits**

```java
semaphore.release(3);
```

---

## **Main Functions**

|Method|Description|
|---|---|
|`acquire()`|Block until one permit|
|`acquire(n)`|Block until n permits|
|`tryAcquire()`|Try without blocking|
|`tryAcquire(timeout)`|Try with timeout|
|`release()`|Return one permit|
|`release(n)`|Return multiple|
|`availablePermits()`|Remaining permits|

---

## **Example: fair semaphore + multi-permit acquire**

```java
Semaphore parkingSpots = new Semaphore(5, true); // fair semaphore

Runnable car = () -> {
    try {
        System.out.println(Thread.currentThread().getName() + " wants 2 spots...");
        parkingSpots.acquire(2);  // acquire 2 permits
        System.out.println(Thread.currentThread().getName() + " parked using 2 spots");
        Thread.sleep(1000);
    } catch (Exception e) {}
    finally {
        parkingSpots.release(2);
        System.out.println(Thread.currentThread().getName() + " left (released 2 spots)");
    }
};

new Thread(car, "Car A").start();
new Thread(car, "Car B").start();
new Thread(car, "Car C").start();
```

---

# 🟦 **Exchanger — Declarative Summary**

## **What it is**

A synchronization tool where **exactly two threads** meet and exchange data with each other.

## **When to Use**

- Double-buffering
    
- Producer–consumer data swapping
    
- Two tasks needing synchronized data exchange
    

## **How it behaves**

- Thread A calls `exchange(Adata)`
    
- Thread B calls `exchange(Bdata)`
    
- They block until both arrive
    
- They receive the other’s data
    

## **Main Functions**

|Method|Description|
|---|---|
|`exchange(data)`|Exchange with another thread|
|`exchange(data, timeout)`|Exchange with timeout|

## **Example**

```java
Exchanger<String> exchanger = new Exchanger<>();

Runnable producer = () -> {
    try {
        String data = "Produced";
        System.out.println("Producer sends: " + data);
        String received = exchanger.exchange(data);
        System.out.println("Producer got: " + received);
    } catch (Exception e) {}
};

Runnable consumer = () -> {
    try {
        String data = "Consumed";
        System.out.println("Consumer sends: " + data);
        String received = exchanger.exchange(data);
        System.out.println("Consumer got: " + received);
    } catch (Exception e) {}
};

new Thread(producer).start();
new Thread(consumer).start();
```

---

# 🟦 **Phaser — Declarative Summary**

## **What it is**

A flexible, dynamic **multi-phase synchronizer**.  
Allows threads to register/deregister while coordinating across many phases.

## **When to Use**

- Multi-step workflows (load → process → save)
    
- Algorithms requiring multiple rounds
    
- Variable number of participants
    
- When CyclicBarrier is too limited
    

## **How it behaves**

- Threads `register()` to join.
    
- They call `arriveAndAwaitAdvance()` to sync at each phase.
    
- On completion threads may `arriveAndDeregister()`.
    

## **Main Functions**

|Method|Description|
|---|---|
|`register()`|Add new party|
|`bulkRegister(n)`|Add n parties|
|`arrive()`|Arrive without waiting|
|`arriveAndAwaitAdvance()`|Arrive + wait|
|`arriveAndDeregister()`|Arrive + leave|
|`getPhase()`|Current phase number|
|`getRegisteredParties()`|Total participants|

## **Example**

```java
Phaser phaser = new Phaser(1); // register main thread

Runnable worker = () -> {
    phaser.register();

    System.out.println(Thread.currentThread().getName() + " loading...");
    sleep();
    phaser.arriveAndAwaitAdvance();

    System.out.println(Thread.currentThread().getName() + " processing...");
    sleep();
    phaser.arriveAndAwaitAdvance();

    System.out.println(Thread.currentThread().getName() + " saving...");
    sleep();
    phaser.arriveAndDeregister();
};

for (int i = 1; i <= 3; i++)
    new Thread(worker, "Worker " + i).start();

phaser.arriveAndAwaitAdvance();
phaser.arriveAndAwaitAdvance();
phaser.arriveAndAwaitAdvance();

System.out.println("All phases completed.");
```

---

# 🟦 **Phaser vs CyclicBarrier**

|Feature|Phaser|CyclicBarrier|
|---|---|---|
|# of participants|Dynamic|Fixed|
|# of phases|Unlimited|One per cycle|
|Barrier action|❌ No|✔ Yes|
|Can deregister?|✔ Yes|❌ No|
|Flexibility|⭐⭐⭐⭐⭐|⭐⭐⭐|
|Best for|Multi-step pipelines|Fixed-step synchronization|

---

# 🟩 **FINAL MASTER CHEAT-SHEET**

### **CountDownLatch**

- One-time event gate.
    
- Threads wait for events.
    
- `await()`, `countDown()`.
    

### **CyclicBarrier**

- Threads wait for each other.
    
- Reusable per cycle.
    
- `await()`, `reset()`.
    

### **Semaphore**

- Controls number of concurrent accesses.
    
- Fair semaphore: `new Semaphore(n, true)`.
    
- Multi-permit: `acquire(n)`, `release(n)`.
    

### **Exchanger**

- Two threads swap data.
    
- `exchange(x)`.
    

### **Phaser**

- Multi-phase synchronization.
    
- Dynamic registration/deregistration.
    
- `arriveAndAwaitAdvance()`.
    
