## CompletableFuture basics

```java
CompletableFuture<Void> f = CompletableFuture.runAsync(() -> {
    // background task
});

CompletableFuture<String> f2 = CompletableFuture.supplyAsync(() -> {
    // compute result
    return "hello";
});
```

- **`CompletableFuture<T>`** = a future **with callbacks** and **chaining**.
    
- By default, async methods use `ForkJoinPool.commonPool()`, or you can pass your own `Executor`.
    

---

## Executing tasks

### `runAsync` (no result)

```java
CompletableFuture<Void> f = CompletableFuture.runAsync(() -> {
    doWork();
});

// with custom executor
CompletableFuture<Void> f2 = CompletableFuture.runAsync(() -> doWork(), executor);
```

- Use when you **don’t need a return value**.
    

---

### `supplyAsync` (with result)

```java
CompletableFuture<String> f = CompletableFuture.supplyAsync(() -> {
    return fetchData();
});

// with custom executor
CompletableFuture<String> f2 = CompletableFuture.supplyAsync(this::fetchData, executor);
```

- Use when you **do need a value**.
    

---

## Manually completing a future: `complete()`

```java
CompletableFuture<String> f = new CompletableFuture<>();

// complete from some event/callback:
f.complete("done!");
```

- `complete(value)` sets the result and wakes any waiters.
    
- If the future is already completed, `complete()` returns `false` and does nothing.
    
- There’s also `completeExceptionally(Throwable ex)` to fail it.
    

---

## What is a callback?

- A **callback** is a function that runs **after** the future completes.
    
- Instead of blocking:
    

```java
String result = f.get(); // blocking
```

you attach callbacks:

```java
f.thenAccept(result -> System.out.println("Result = " + result));
```

- Callbacks allow **non-blocking, chainable** processing.
    

---

## Transforming results

### `thenApply` vs `thenApplyAsync`

```java
CompletableFuture<Integer> f =
    CompletableFuture.supplyAsync(() -> 40)
        .thenApply(x -> x + 2); // synchronous callback
```

- **`thenApply`**:
    
    - Runs after the previous stage completes.
        
    - Executes in the **same thread** that completed the previous stage (unless completed async by pool).
        

```java
CompletableFuture<Integer> f =
    CompletableFuture.supplyAsync(() -> 40)
        .thenApplyAsync(x -> x + 2); // async callback
```

- **`thenApplyAsync`**:
    
    - Runs in the **ForkJoin common pool** or in the **executor** you pass.
        
    - Good when callback is expensive and you want it off the completion thread.
        

---

## Consuming results (no new value)

### `thenAccept` / `thenAcceptAsync`

```java
CompletableFuture<Void> f =
    CompletableFuture.supplyAsync(() -> "data")
        .thenAccept(s -> System.out.println("Got: " + s));
```

- **`thenAccept`**:
    
    - Callback gets the result.
        
    - Returns `CompletableFuture<Void>` (no value).
        

```java
CompletableFuture<Void> f =
    CompletableFuture.supplyAsync(() -> "data")
        .thenAcceptAsync(s -> log(s));
```

- **`thenAcceptAsync`**:
    
    - Same but callback runs in pool / given executor.
        

---

## Running side-effects (no input, no output)

### `thenRun` / `thenRunAsync`

```java
CompletableFuture<Void> f =
    CompletableFuture.supplyAsync(() -> "data")
        .thenRun(() -> System.out.println("Previous stage done"));
```

- **`thenRun`**:
    
    - Runs runnable **after** previous stage, but does **not** receive its result.
        

```java
CompletableFuture<Void> f =
    CompletableFuture.supplyAsync(() -> "data")
        .thenRunAsync(() -> cleanup());
```

- **`thenRunAsync`**:
    
    - Same but runs in pool / executor.
        

---

## Combining futures

### 1. Chaining dependent tasks: `thenCompose` / `thenComposeAsync`

Use when the **second task depends on the result** of the first and returns another `CompletableFuture`.

Without `thenCompose` (nested futures):

```java
CompletableFuture<CompletableFuture<User>> nested =
    CompletableFuture.supplyAsync(() -> userId)
        .thenApply(id -> findUserAsync(id));
```

With `thenCompose` (flat):

```java
CompletableFuture<User> userFuture =
    CompletableFuture.supplyAsync(() -> userId)
        .thenCompose(id -> findUserAsync(id)); // flattens
```

- **`thenCompose`**: flatMaps `T -> CompletableFuture<U>` to `CompletableFuture<U>`.
    
- **`thenComposeAsync`**: same, but callback runs in pool/executor.
    

---

### 2. Combining independent results: `thenCombine` / `thenCombineAsync`

```java
CompletableFuture<Integer> f1 = CompletableFuture.supplyAsync(() -> price());
CompletableFuture<Integer> f2 = CompletableFuture.supplyAsync(() -> tax());

CompletableFuture<Integer> total =
    f1.thenCombine(f2, (p, t) -> p + t);
```

- **`thenCombine`**:
    
    - Waits for **both** futures.
        
    - Combines their results into a new value.
        

```java
CompletableFuture<Integer> total =
    f1.thenCombineAsync(f2, (p, t) -> p + t);
```

- **`thenCombineAsync`**: combine function runs in pool/executor.
    

---

### 3. Consuming two results: `thenAcceptBoth` / `thenAcceptBothAsync`

```java
CompletableFuture<Void> f =
    f1.thenAcceptBoth(f2, (p, t) -> {
        System.out.println("Price: " + p + ", Tax: " + t);
    });
```

- **`thenAcceptBoth`**:
    
    - Waits for both futures and **consumes** the two results.
        
    - Returns `CompletableFuture<Void>`.
        

```java
CompletableFuture<Void> f =
    f1.thenAcceptBothAsync(f2, (p, t) -> log(p, t));
```

- **`thenAcceptBothAsync`**: callback runs in pool/executor.
    

---

## Waiting for multiple futures: `allOf` and `anyOf`

### `allOf`

```java
CompletableFuture<Void> all =
    CompletableFuture.allOf(f1, f2, f3);

all.thenRun(() -> {
    // all completed
    try {
        String r1 = f1.get();
        String r2 = f2.get();
        String r3 = f3.get();
    } catch (Exception e) { /* handle */ }
});
```

- Completes when **all** given futures complete.
    
- **Does not** give combined result; you must get each individually.
    

---

### `anyOf`

```java
CompletableFuture<Object> any =
    CompletableFuture.anyOf(f1, f2, f3);

any.thenAccept(result -> {
    System.out.println("First result: " + result);
});
```

- Completes when **first** future completes (success or failure).
    
- Result type is `Object` → cast as needed.
    

---

## Exception handling with CompletableFuture

### `exceptionally`

```java
CompletableFuture<String> f =
    CompletableFuture.supplyAsync(() -> {
        if (true) throw new RuntimeException("error");
        return "ok";
    }).exceptionally(ex -> {
        System.out.println("Failed: " + ex.getMessage());
        return "fallback";
    });
```

- Catches exceptions and provides a **fallback value**.
    
- Only handles exceptions; normal completion bypasses this.
    

---

### `handle`

```java
CompletableFuture<String> f =
    CompletableFuture.supplyAsync(() -> riskyCall())
        .handle((result, ex) -> {
            if (ex != null) {
                logError(ex);
                return "fallback";
            }
            return result.toUpperCase();
        });
```

- Always runs (success or failure).
    
- Receives both `result` and `exception`.
    
- Good for **combined success + error logic**.
    

---

### `whenComplete`

```java
CompletableFuture<String> f =
    CompletableFuture.supplyAsync(() -> riskyCall())
        .whenComplete((result, ex) -> {
            if (ex != null) {
                logError(ex);
            } else {
                logResult(result);
            }
        });
```

- Like `handle`, but **returns the original result** unchanged.
    
- Good for logging/side effects without altering the pipeline.
    

---

### Manually failing a future

```java
CompletableFuture<String> f = new CompletableFuture<>();
f.completeExceptionally(new RuntimeException("boom"));
```

- Any `.get()` or dependent stage sees this exception.
    

---

## Quick mental model

- **Start work**: `runAsync`, `supplyAsync`, or `new CompletableFuture<>() + complete()`.
    
- **Transform result**: `thenApply*`.
    
- **Consume result**: `thenAccept*`.
    
- **Side effects only**: `thenRun*`.
    
- **Chain futures (dependent work)**: `thenCompose*`.
    
- **Combine independent futures**: `thenCombine*`, `thenAcceptBoth*`.
    
- **Wait for many**: `allOf`, `anyOf`.
    
- **Handle errors**: `exceptionally`, `handle`, `whenComplete`, `completeExceptionally`.