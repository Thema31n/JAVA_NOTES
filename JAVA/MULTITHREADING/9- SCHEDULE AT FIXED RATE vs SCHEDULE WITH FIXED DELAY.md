# scheduleAtFixedRate vs scheduleWithFixedDelay

(Using **initial delay ≠ 0**)

Assume we now call:

```java
ses.scheduleAtFixedRate(task, 500, 1000, TimeUnit.MILLISECONDS);
ses.scheduleWithFixedDelay(task, 500, 1000, TimeUnit.MILLISECONDS);
```

Where:

- Initial delay = **500 ms**
    
- Period/Delay = **1000 ms**
    

---

# 1. Conceptual Summary

## scheduleAtFixedRate

Runs tasks based on a **fixed period between scheduled start times**, beginning after the **initial delay**.

### Meaning of `(task, 500, 1000)`

- First execution starts **500 ms** after scheduling.
    
- Subsequent executions target fixed intervals at:  
    `500 ms, 1500 ms, 2500 ms, 3500 ms, …`
    
- Execution overruns can cause the actual start to drift, but the scheduler still _calculates_ times based on fixed intervals.
    

---

## scheduleWithFixedDelay

Runs tasks based on a **fixed delay after the previous execution finishes**, after the **initial delay**.

### Meaning of `(task, 500, 1000)`

- First execution starts **500 ms** after scheduling.
    
- After the first execution finishes:
    
    - Wait **1000 ms**
        
    - Then run the next execution
        
- No drift; the cycle is determined by:  
    `nextStart = previousEnd + 1000 ms`
    

---

# 2. Timing Diagrams (with initial delay 500 ms)

Assume:

- Task duration = 400 ms
    
- Initial delay = 500 ms
    
- Period/Delay = 1000 ms
    

---

## (A) scheduleAtFixedRate(task, 500, 1000)

```
t=0        [500ms delay]
t=500ms     |---400ms---|   Task1
t=1500ms                 |---400ms---|  Task2
t=2500ms                                 |---400ms---| Task3
```

Observations:

- Start times remain targeted at **500 + n*1000**.
    
- Actual execution always tries to follow the global timeline.
    
- Overruns produce late starts but not schedule recalculation.
    

---

## (B) scheduleWithFixedDelay(task, 500, 1000)

```
t=0        [500ms delay]
t=500ms     |---400ms---| Task1
t=900ms   [Task ends]
           [1000ms wait]
t=1900ms    |---400ms---| Task2
t=2300ms [Task ends]
           [1000ms wait]
t=3300ms    |---400ms---| Task3
```

Start times:

```
Task1: 500 ms
Task2: 1900 ms
Task3: 3300 ms
```

Observations:

- Start times depend on **previousEnd + delay**.
    
- No attempt to maintain a global cadence.
    
- No drift or schedule pressure.
    

---

# 3. Overrun Behavior (Using Initial Delay)

### scheduleAtFixedRate

Overruns still cause tasks to start late relative to the fixed timeline, but the scheduler keeps calculating:

```
startTime(n) = initialDelay + n * period
```

If a task takes 1500 ms and period = 1000 ms:

- Task1 starts at 500 ms
    
- Ends at ~2000 ms
    
- Task2 _should have started_ at 1500 ms
    
- Executor begins Task2 immediately at ~2000 ms (as soon as execution thread becomes available)
    

### scheduleWithFixedDelay

Overruns insert themselves naturally:

```
actualStart(n+1) = previousEnd + delay
```

Initial delay has **no** effect on later drift.

---

# 4. Drift Behavior with Initial Delay

### scheduleAtFixedRate

- Initial delay defines the **first target start**, but from there forward timing is based on a fixed global timeline.
    
- If tasks overrun, execution drifts behind schedule but maintains the intended cadence.
    

### scheduleWithFixedDelay

- Initial delay only determines the first start.
    
- After that, each cycle is strictly:  
    `previousEnd + delay`
    

---

# 5. CPU Load & Backpressure Behavior 

Initial delay only affects the **first run**, not long-term load characteristics.

- **Fixed rate:** builds pressure if tasks are slow
    
- **Fixed delay:** naturally throttles
    

---

# 6. Parallel Execution and Threads 

### scheduleAtFixedRate + poolSize > 1:

If tasks overrun, multiple invocations can run concurrently.

### scheduleWithFixedDelay:

Never runs overlapping tasks, regardless of pool size.

Initial delay does not modify this behavior.

---

# 7. When To Use Which

(Updated with initial delay explanation)

## Use scheduleAtFixedRate when:

- You want a predictable global cadence.
    
- The first run should occur after a fixed initial delay (e.g., start polling after startup warmup).
    
- Tasks are short and predictable.
    

## Use scheduleWithFixedDelay when:

- You want spacing between task executions irrespective of task duration.
    
- The first run should wait for system stabilization (initial delay).
    
- Tasks vary in complexity or must not overlap.
    

---

# 8. Example With Initial Delay

```java
ScheduledExecutorService ses = Executors.newScheduledThreadPool(1);

ses.scheduleAtFixedRate(() -> {
    long start = System.currentTimeMillis();
    System.out.println("FixedRate  start=" + start);
    sleep(1500);
    long end = System.currentTimeMillis();
    System.out.println("FixedRate  end=" + end);
}, 500, 1000, TimeUnit.MILLISECONDS);

ses.scheduleWithFixedDelay(() -> {
    long start = System.currentTimeMillis();
    System.out.println("FixedDelay start=" + start);
    sleep(1500);
    long end = System.currentTimeMillis();
    System.out.println("FixedDelay end=" + end);
}, 500, 1000, TimeUnit.MILLISECONDS);
```

### Behavior:

- Both start 500 ms after scheduling.
    
- FixedRate attempts: `500, 1500, 2500, …`
    
- FixedDelay: each run executes after `(taskDuration + 1000)` ms
    

---

# 9. One-Sentence Rule 

- **scheduleAtFixedRate** → global time-based schedule beginning after the initial delay.
    
- **scheduleWithFixedDelay** → work-based schedule beginning after the initial delay.
    
