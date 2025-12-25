# 1️⃣ AspectJ + Spring AOP — Big Picture

## What Does “AOP with AspectJ” Mean in Spring?

It means:

- Using **AspectJ annotations & pointcut expressions**
    
- Still running on **Spring AOP (proxy-based)**
    
- NOT full compile-time AspectJ weaving (unless explicitly configured)
    

### Key Clarification (MEMORIZE)

> Spring AOP uses **AspectJ syntax**, but weaving is still **runtime proxy-based**.

---

# 2️⃣ Required Dependencies (REVIEW)

## Spring Boot (Most Common)

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

### What This Brings

- Spring AOP
    
- AspectJ annotations (`@Aspect`)
    
- AspectJ Weaver (runtime)
    

📌 **You do NOT need full AspectJ compiler for Spring AOP**

---

## Non-Boot (Manual)

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
</dependency>

<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
</dependency>
```

---

## Interview Line

> Spring AOP relies on AspectJ annotations and the AspectJ weaver at runtime.

---

# 3️⃣ Core Configuration

## 3.1 Enable AspectJ Support

```java
@Configuration
@EnableAspectJAutoProxy
public class AopConfig {
}
```

---

## What `@EnableAspectJAutoProxy` Does

### Responsibilities

- Enables processing of `@Aspect`
    
- Creates **AOP proxies**
    
- Applies advice at runtime
    

### Optional Attributes

```java
@EnableAspectJAutoProxy(proxyTargetClass = true)
```

|Option|Meaning|
|---|---|
|false (default)|JDK dynamic proxy|
|true|CGLIB class proxy|

---

## Interview Trap

> Without `@EnableAspectJAutoProxy`, aspects are ignored.

---

# 4️⃣ Aspect Configuration

## 4.1 Defining an Aspect

```java
@Aspect
@Component
public class LoggingAspect {
}
```

### What Happens

- Spring detects it as a bean
    
- Registers it with AOP infrastructure
    

---

## Interview Line

> An aspect is a Spring bean annotated with `@Aspect`.

---

# 5️⃣ Pointcuts (MOST IMPORTANT PART)

## What Is a Pointcut?

> A predicate that selects **join points (methods)**.

---

## 5.1 Declaring a Pointcut

```java
@Pointcut("execution(* com.app.service.*.*(..))")
public void serviceLayer() {}
```

✔ Reusable  
✔ Readable

---

## 5.2 Using the Pointcut

```java
@Before("serviceLayer()")
public void beforeServiceCall() {
    System.out.println("Service method called");
}
```

---

# 6️⃣ Pointcut Designators (VERY IMPORTANT)

These define **WHAT** to intercept.

---

## 6.1 `execution` (MOST USED)

```java
execution(
  return-type
  declaring-type
  method-name
  arguments
)
```

### Example

```java
execution(public * *(..))
```

✔ Matches all public methods

---

## 6.2 `within`

```java
within(com.app.service..*)
```

✔ All methods inside a package

---

## 6.3 `@annotation`

```java
@annotation(LogExecution)
```

✔ Matches methods annotated with `@LogExecution`

---

## 6.4 `args`

```java
args(String, ..)
```

✔ Matches methods based on **runtime argument types**

---

## 6.5 `this` vs `target` (INTERVIEW FAVORITE)

```java
this(com.app.service.OrderService)
target(com.app.service.OrderService)
```

|Keyword|Matches|
|---|---|
|this|Proxy type|
|target|Actual object|

---

## 6.6 `@target` ✅ (ADDED)

```java
@target(AdminOnly)
```

✔ Matches methods where the **target class** is annotated  
✔ Checked at **runtime**

### Example

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface AdminOnly {}
```

```java
@AdminOnly
@Service
public class AdminService {}
```

---

## 6.7 `@within` ✅ (ADDED)

```java
@within(AdminOnly)
```

✔ Matches **all methods inside classes** annotated with `@AdminOnly`  
✔ Similar to `within`, but annotation-based

---

## 6.8 `@args` ✅ (ADDED)

```java
@args(Validated)
```

✔ Matches methods whose **runtime arguments’ classes** are annotated

### Example

```java
public class OrderRequest {
    @Validated
    private String product;
}
```

---

## 6.9 `@annotation` (REVIEW – IMPORTANT)

```java
@annotation(Secured)
```

✔ Matches methods **directly annotated**  
✔ Very common in security & logging

---

## Interview Line (VERY IMPORTANT)

> `@target` and `@within` match class-level annotations,  
> `@annotation` matches method-level annotations,  
> `@args` matches runtime argument annotations.

---

# 7️⃣ Advice Configuration

## Advice Types (REVIEW)

|Advice|Purpose|
|---|---|
|@Before|Pre-processing|
|@After|Always runs|
|@AfterReturning|Success only|
|@AfterThrowing|Exception only|
|@Around|Full control|

---

## 7.1 `@Before` Advice

```java
@Before("serviceLayer()")
public void logBefore() {
    System.out.println("Before service method");
}
```

---

## 7.2 `@AfterReturning`

```java
@AfterReturning(
    pointcut = "serviceLayer()",
    returning = "result"
)
public void logReturn(Object result) {
    System.out.println("Returned: " + result);
}
```

---

## 7.3 `@AfterThrowing`

```java
@AfterThrowing(
    pointcut = "serviceLayer()",
    throwing = "ex"
)
public void logException(Exception ex) {
    System.out.println("Exception: " + ex.getMessage());
}
```

---

## 7.4 `@Around` (MOST POWERFUL)

```java
@Around("serviceLayer()")
public Object measureTime(ProceedingJoinPoint pjp)
        throws Throwable {

    long start = System.currentTimeMillis();
    Object result = pjp.proceed();
    long end = System.currentTimeMillis();

    System.out.println(
        pjp.getSignature() + " took " + (end - start) + " ms"
    );

    return result;
}
```

---

## Interview Line

> `@Around` advice can control method execution completely.

---

# 8️⃣ Real-World Coding Demo

## 8.1 Business Service

```java
@Service
public class OrderService {

    public void placeOrder() {
        System.out.println("Placing order");
    }
}
```

---

## 8.2 Logging Aspect

```java
@Aspect
@Component
public class LoggingAspect {

    @Pointcut("execution(* com.app.service.*.*(..))")
    public void serviceMethods() {}

    @Around("serviceMethods()")
    public Object log(ProceedingJoinPoint pjp)
            throws Throwable {

        System.out.println("Before: " + pjp.getSignature());
        Object result = pjp.proceed();
        System.out.println("After: " + pjp.getSignature());

        return result;
    }
}
```

---

## 8.3 Execution Flow (MEMORIZE)

```
Client
 → AOP Proxy
 → @Around (before)
 → Target Method
 → @Around (after)
 → Client
```

---

# 9️⃣ Common Real-World Use Cases

✔ Logging  
✔ Transactions (`@Transactional`)  
✔ Security checks  
✔ Metrics & monitoring  
✔ Caching (`@Cacheable`)

---

# 🔟 Spring AOP vs Full AspectJ

|Feature|Spring AOP|AspectJ|
|---|---|---|
|Weaving|Runtime|Compile / Load|
|Join points|Method only|Method, field, constructor|
|Complexity|Low|High|
|Usage|Enterprise apps|Framework-level|

---

## Interview Line

> Spring AOP is sufficient for most applications; full AspectJ is used when deeper weaving is required.

---

# 1️⃣1️⃣ Common Pitfalls (VERY IMPORTANT)

❌ Self-invocation doesn’t trigger AOP  
❌ Private methods not advised  
❌ Final classes block CGLIB  
❌ Overusing aspects  
❌ Hard-to-debug flows

---

# 1️⃣2️⃣ Advantages of AspectJ-Style AOP

✔ Clean separation  
✔ Reusable logic  
✔ Declarative programming  
✔ Powerful pointcuts

---

# 1️⃣3️⃣ Disadvantages

❌ Hidden execution flow  
❌ Debugging complexity  
❌ Proxy limitations  
❌ Performance overhead

---

# 🧠 Things to Memorize (FINAL)

- `@EnableAspectJAutoProxy` enables AOP
    
- `@Aspect` defines cross-cutting logic
    
- Pointcuts select methods
    
- Advice defines behavior
    
- `execution` is the most used designator
    
- `@Around` is most powerful
    
- `@target`, `@within`, `@annotation`, `@args` are annotation-based selectors
    
- Spring AOP uses proxies, not bytecode weaving
    
- Self-invocation breaks AOP
    