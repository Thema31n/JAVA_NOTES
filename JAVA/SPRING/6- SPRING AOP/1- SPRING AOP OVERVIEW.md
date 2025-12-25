# 1️⃣ AOP (Aspect-Oriented Programming) — Overview

## What is AOP?

**Aspect-Oriented Programming (AOP)** is a programming paradigm that allows you to **separate cross-cutting concerns** from business logic.

### Simple Definition (MEMORIZE)

> AOP allows you to apply behavior **across multiple classes** without modifying their code.

---

## The Problem AOP Solves

In real applications, some logic:

- Appears **everywhere**
    
- Is **not business logic**
    
- Is **hard to maintain**
    

Examples:

- Logging
    
- Transactions
    
- Security
    
- Auditing
    
- Metrics
    
- Caching
    

Without AOP → **code duplication + tight coupling**

---

# 2️⃣ Cross-Cutting Concern

## What Is a Cross-Cutting Concern?

A concern that:

- Affects multiple parts of the application
    
- Is orthogonal to core business logic
    

### Examples

|Concern|Appears in|
|---|---|
|Logging|Controllers, services, repositories|
|Transactions|Service methods|
|Security|API endpoints|
|Auditing|Entity changes|
|Monitoring|All layers|

---

## Without AOP (BAD)

```java
public void placeOrder() {
    log.info("start");
    validate();
    save();
    log.info("end");
}
```

Repeated everywhere ❌

---

## With AOP (GOOD)

```java
public void placeOrder() {
    validate();
    save();
}
```

Logging handled separately ✔

---

## Interview Line

> Cross-cutting concerns are behaviors that span multiple modules but are not part of business logic.

---

# 3️⃣ Why We Need AOP

### 1. Separation of Concerns

- Business logic stays clean
    
- Cross-cutting logic centralized
    

### 2. Less Duplication

- One place for logging, security, etc.
    

### 3. Easier Maintenance

- Change logic in one aspect
    

### 4. Declarative Programming

- Use annotations instead of code
    

---

## Real-World Example

`@Transactional`

- You never write transaction code
    
- AOP applies it automatically
    

---

# 4️⃣ Where AOP Is Used (REAL PROJECTS)

|Area|Example|
|---|---|
|Transactions|`@Transactional`|
|Security|Method-level authorization|
|Logging|Request/response logging|
|Performance|Execution time tracking|
|Validation|Pre-condition checks|
|Monitoring|Metrics collection|

---

## Important Rule (MEMORIZE)

> AOP is best for **infrastructure logic**, not business logic.

---

# 5️⃣ Aspect (Core Concept)

## What Is an Aspect?

> An **Aspect** is a module that encapsulates a cross-cutting concern.

### Spring Aspect

```java
@Aspect
@Component
public class LoggingAspect {
}
```

---

## Aspect Contains

- Pointcuts
    
- Advice
    

---

## Interview Line

> An aspect groups advice and pointcuts together.

---

# 6️⃣ Key AOP Concepts (VERY IMPORTANT)

## 1. Join Point

> A point during program execution.

Examples:

- Method execution
    
- Method call
    
- Exception thrown
    

📌 In Spring AOP → **method execution only**

---

## 2. Pointcut

### What Is a Pointcut?

> An expression that selects **which methods** should be intercepted.

### Example

```java
@Pointcut("execution(* com.app.service.*.*(..))")
public void serviceMethods() {}
```

✔ Matches all service methods

---

## 3. Advice

### What Is Advice?

> The action taken **at a join point**.

### Types (MEMORIZE)

|Advice|When it runs|
|---|---|
|@Before|Before method|
|@After|After method|
|@AfterReturning|After success|
|@AfterThrowing|After exception|
|@Around|Before & after|

---

## Advice Example

```java
@Before("serviceMethods()")
public void logBefore() {
    System.out.println("Before method");
}
```

---

## 4. Target Object

> The actual object being advised.

Example:

```java
OrderService
```

---

## 5. AOP Proxy

### What Is an AOP Proxy?

> A wrapper object created by Spring to apply advice.

📌 You interact with the proxy, not the target.

---

### Proxy Types (IMPORTANT)

|Proxy|Used when|
|---|---|
|JDK Dynamic Proxy|Interface-based|
|CGLIB Proxy|Class-based|

---

## Interview Trap

> Self-invocation does NOT trigger AOP

---

## 6. Weaving

### What Is Weaving?

> The process of linking aspects with target objects.

### Types

|Type|When|
|---|---|
|Compile-time|AspectJ|
|Load-time|AspectJ|
|Runtime|Spring AOP|

📌 Spring AOP uses **runtime weaving**

---

## 7. Interceptor

### What Is an Interceptor?

> A specific type of advice that intercepts method execution.

Used heavily internally by Spring:

- Transactions
    
- Security
    
- Caching
    

---

## 8️⃣ Introduction (Advanced Concept)

### What Is Introduction?

> Adding new behavior or methods to an existing class.

📌 Rarely used in Spring AOP  
📌 More common in full AspectJ

---

# 9️⃣ Practical AOP Examples (REAL-WORLD)

## Example 1: Logging Aspect

```java
@Aspect
@Component
public class LoggingAspect {

    @Around("execution(* com.app.service.*.*(..))")
    public Object logExecution(ProceedingJoinPoint pjp)
            throws Throwable {

        long start = System.currentTimeMillis();
        Object result = pjp.proceed();
        long end = System.currentTimeMillis();

        System.out.println(
            pjp.getSignature() + " took " + (end - start) + " ms"
        );

        return result;
    }
}
```

---

## Example 2: Security Check

```java
@Before("@annotation(RequiresAdmin)")
public void checkAdmin() {
    // security logic
}
```

---

## Example 3: Transaction (Conceptual)

```java
@Transactional
public void placeOrder() {
    save();
}
```

✔ Implemented using AOP  
✔ Uses proxy + interceptor

---

# 🔟 AOP Implementations

## 1. Spring AOP

- Proxy-based
    
- Runtime weaving
    
- Method-level only
    

## 2. AspectJ

- Full AOP support
    
- Compile-time or load-time weaving
    
- More powerful
    

---

## Interview Line

> Spring AOP is simpler and sufficient for most enterprise use cases.

---

# 1️⃣1️⃣ Advantages of AOP

✔ Clean code  
✔ Separation of concerns  
✔ Reusable cross-cutting logic  
✔ Declarative programming  
✔ Less duplication

---

# 1️⃣2️⃣ Disadvantages of AOP

❌ Harder debugging  
❌ Hidden behavior  
❌ Proxy limitations  
❌ Performance overhead (small but real)  
❌ Overuse leads to confusion

---

## Real-World Warning

> Overusing AOP can make the system unpredictable.

---

# 1️⃣3️⃣ Common Interview Questions

### Q: How does `@Transactional` work?

**Answer:**  
Using Spring AOP proxies and transaction interceptors.

---

### Q: Difference between filter, interceptor, and AOP?

**Answer:**

- Filter → Servlet layer
    
- Interceptor → Spring MVC
    
- AOP → Method level
    

---

### Q: Why doesn’t AOP work on private methods?

**Answer:**  
Because proxies intercept only public method calls.

---

# 🧠 Things to Memorize (FINAL)

- AOP separates cross-cutting concerns
    
- Aspect = advice + pointcuts
    
- Pointcut selects methods
    
- Advice defines behavior
    
- Proxy applies AOP logic
    
- Spring AOP uses runtime weaving
    
- `@Transactional` is AOP-based
    
- Self-invocation breaks AOP
    
- Overuse of AOP is dangerous
    