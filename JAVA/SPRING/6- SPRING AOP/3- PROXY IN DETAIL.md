# 1️⃣ What Is a Proxy? (FOUNDATION)

## Simple Definition (MEMORIZE)

> A **proxy** is an object that **stands in front of another object** and controls access to it.

### Visual Mental Model

```
You  ──▶  Proxy  ──▶  Real Object
        (Spring)      (Your class)
```

In Spring:

- You **never call your class directly**
    
- You call a **proxy**
    
- The proxy decides what extra logic to apply
    

---

## Real-World Analogy (VERY IMPORTANT)

**Security gate in a company**

```
Employee ──▶ Security Gate ──▶ Office
            (checks badge)
```

- Security gate = proxy
    
- Office = real object
    
- Badge check = AOP logic
    

---

# 2️⃣ Why Spring Uses Proxies

Spring needs proxies to apply **cross-cutting concerns** automatically.

### Features That Depend on Proxies

```
@Transactional
@Cacheable
@Async
@Secured
AOP Logging
```

### Diagram

```
Controller
   ↓
Service PROXY
   ↓   (transaction, logging, security)
Real Service
```

📌 **Key Rule**

> No proxy → no AOP → no @Transactional

---

# 3️⃣ High-Level Proxy Flow (VERY IMPORTANT)

### Normal Method Call (NO AOP)

```
Client ──▶ Service ──▶ Database
```

### Spring Proxy Call (WITH AOP)

```
Client
  ↓
Spring Proxy
  ↓  (AOP logic)
Target Object
```

📌 You **inject and call the proxy**, not the target.

---

# 4️⃣ Proxy Creation in Spring (HOW IT HAPPENS)

## When Proxies Are Created

- At **application startup**
    
- During bean creation
    

### Step-by-Step Flow

```
1️⃣ Spring scans beans
2️⃣ Finds @Transactional / @Aspect
3️⃣ Creates target object
4️⃣ Wraps it with proxy
5️⃣ Registers proxy in context
6️⃣ Injects proxy everywhere
```

### Diagram

```
Spring Context
 ├── OrderServiceProxy  ← injected
 │     ↓
 │  OrderService        ← real object
```

---

# 5️⃣ Types of Proxies in Spring (CRITICAL)

Spring uses **TWO mechanisms**.

---

## 5.1 JDK Dynamic Proxy (INTERFACE-BASED)

### When Used

- Class implements an interface
    
- Default behavior
    

### Diagram

```
OrderService (interface)
        ▲
        │
OrderServiceProxy  (JDK Proxy)
        │
OrderServiceImpl   (target)
```

### Example

```java
public interface OrderService {
    void placeOrder();
}

@Service
@Transactional
public class OrderServiceImpl implements OrderService {
    public void placeOrder() {
        System.out.println("Order placed");
    }
}
```

📌 Proxy implements the **interface**, not the class.

### Limitation ❌

- Methods not in interface are **not proxied**
    

---

## 5.2 CGLIB Proxy (CLASS-BASED)

### When Used

- No interface
    
- OR `proxyTargetClass = true`
    

```java
@EnableAspectJAutoProxy(proxyTargetClass = true)
```

### Diagram

```
PaymentService$$EnhancerBySpringCGLIB
           ▲
           │ extends
     PaymentService (target)
```

### Example

```java
@Service
@Transactional
public class PaymentService {
    public void pay() {
        System.out.println("Payment done");
    }
}
```

### Limitations ❌

- `final class` ❌
    
- `final method` ❌
    

---

## Interview Line (VERY IMPORTANT)

> Spring uses JDK proxies for interfaces and CGLIB proxies for concrete classes.

---

# 6️⃣ How Proxy Applies AOP Logic (DEEP BUT SIMPLE)

## Example: `@Transactional`

```java
@Transactional
public void transferMoney() {
    debit();
    credit();
}
```

### What You THINK Happens

```
transferMoney()
```

### What REALLY Happens

```
Client
 ↓
Transaction Proxy
   ├── start transaction
   ├── call transferMoney()
   ├── commit / rollback
 ↓
Client
```

📌 Transaction code is **outside** your method.

---

# 7️⃣ Method Interception (CORE MECHANISM)

Spring uses **interceptors** internally.

### Simplified Interceptor Code

```java
invoke() {
    before();
    call target method;
    after();
}
```

### Interceptor Chain Diagram

```
Client
 ↓
Logging Interceptor
 ↓
Transaction Interceptor
 ↓
Security Interceptor
 ↓
Target Method
```

📌 Order matters.

---

# 8️⃣ Multiple Proxies & Advice Chain

## Example

```java
@Transactional
@Cacheable
public Order findOrder() {}
```

### Execution Flow

```
Client
 ↓
Cache Proxy
 ↓
Transaction Proxy
 ↓
Target Method
```

Each annotation adds **one interceptor**.

---

# 9️⃣ Self-Invocation Problem (MOST COMMON BUG)

## Problem Code

```java
@Service
public class OrderService {

    @Transactional
    public void placeOrder() {
        save();   // ❌ NOT transactional
    }

    @Transactional
    public void save() {
        // DB logic
    }
}
```

### Why It Fails (DIAGRAM)

```
placeOrder()
   ↓ (same object)
save()   ❌ bypasses proxy
```

The call never goes through the proxy.

---

## Correct Flow (WHAT SHOULD HAPPEN)

```
Client
 ↓
Proxy
 ↓
save()
```

---

## How to Fix

### Fix 1: Move method to another bean

```
OrderService ──▶ OrderSaveService (proxy)
```

### Fix 2: Self-injection (advanced)

```java
@Autowired
private OrderService self;
```

---

## Interview Line (MEMORIZE)

> Self-invocation bypasses Spring proxies, so AOP is not applied.

---

# 🔟 Proxy vs Target Object

### Diagram

```
Injected Bean ──▶ Proxy ──▶ Target
```

|Feature|Proxy|Target|
|---|---|---|
|Contains AOP|✅|❌|
|Executes advice|✅|❌|
|Business logic|❌|✅|
|Called directly|✅|❌|

---

# 1️⃣1️⃣ Proxy Debugging Tips (REAL-WORLD)

### Print Proxy Class

```java
System.out.println(bean.getClass());
```

### Output

```
PaymentService$$EnhancerBySpringCGLIB
```

✔ Confirms proxy is active

---

# 1️⃣2️⃣ Proxy Performance

### Diagram

```
Method call
   ↓
Proxy (tiny overhead)
   ↓
Target
```

- Overhead is minimal
    
- Complexity is the real danger
    

---

# 1️⃣3️⃣ Advantages of Proxies

✔ Enable AOP  
✔ Clean separation  
✔ Declarative programming  
✔ Infrastructure power

---

# 1️⃣4️⃣ Disadvantages of Proxies

❌ Harder debugging  
❌ Hidden execution  
❌ Self-invocation  
❌ Final method limits

---

# 🧠 FINAL MENTAL MODEL (MOST IMPORTANT)

```
You NEVER call your class
You ALWAYS call the proxy
The proxy applies AOP
Then calls your class
```

---

# 🧠 FINAL THINGS TO MEMORIZE

- Spring AOP is proxy-based
    
- Proxies are created at startup
    
- JDK proxy → interfaces
    
- CGLIB proxy → classes
    
- @Transactional works via proxy
    
- Self-invocation breaks AOP
    
- Multiple interceptors form a chain
    
