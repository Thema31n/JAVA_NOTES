# 1. Dependency Injection (DI)

## Definition

> **Dependency Injection is when Spring supplies required objects to a class instead of the class creating them.**

---

## Why DI?

- Loose coupling
    
- Easy testing
    
- Flexible design
    

---

## Injection Types (MEMORIZE)

> **Constructor → Setter → Field**

---

# 2. Constructor Injection (BEST PRACTICE)

---

## 2.1 Annotation-Based Example

```java
@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

---

## How It Works

1. Spring finds constructor
    
2. Resolves parameter type
    
3. Injects matching bean
    
4. Creates fully initialized object
    

---

## 2.2 XML Configuration (Constructor Injection)

```xml
<bean id="paymentService"
      class="com.app.PaymentService"/>

<bean id="orderService"
      class="com.app.OrderService">
    <constructor-arg ref="paymentService"/>
</bean>
```

---

## When to Use

✔ Mandatory dependencies  
✔ Best practice  
✔ Immutable objects

---

# 3. Setter Injection (OPTIONAL DEPENDENCIES)

---

## 3.1 Annotation-Based Example

```java
@Service
public class OrderService {

    private PaymentService paymentService;

    @Autowired
    public void setPaymentService(PaymentService ps) {
        this.paymentService = ps;
    }
}
```

---

## How It Works

1. Spring creates object
    
2. Calls setter
    
3. Injects dependency after creation
    

---

## 3.2 XML Configuration (Setter Injection)

```xml
<bean id="orderService"
      class="com.app.OrderService">
    <property name="paymentService"
              ref="paymentService"/>
</bean>
```

---

## Drawback

❌ Object can be partially initialized

---

# 4. Field Injection (AVOID)

---

## Annotation-Based Example

```java
@Service
public class OrderService {

    @Autowired
    private PaymentService paymentService;
}
```

---

## How It Works

- Reflection-based injection
    
- No setter or constructor involved
    

❌ Hard to test  
❌ Hidden dependency

---

## XML Support

❌ **Not supported in XML**

---

# 5. `@Autowired`

## Definition

> **Automatically injects a matching bean.**

---

## Resolution Order

1. By type
    
2. By name
    
3. By qualifier
    

---

# 6. `@Value` – Injecting Values

```java
@Value("${server.port}")
private int port;
```

---

```xml
<property name="port" value="8080"/>
```

---

# 7. XML AUTOWIRING MODES (IMPORTANT SECTION)

---

## 7.1 Autowire byType

### Rule

> **Spring matches dependency by type**

---

### Java Class

```java
public class OrderService {

    private PaymentService paymentService;

    public void setPaymentService(PaymentService ps) {
        this.paymentService = ps;
    }
}
```

---

### XML Configuration

```xml
<bean id="paymentService"
      class="com.app.PaymentService"/>

<bean class="com.app.OrderService"
      autowire="byType"/>
```

---

### How It Works

- Spring looks for a bean of type `PaymentService`
    
- Injects it automatically
    

---

### Failure Case

❌ If **multiple PaymentService beans exist** → exception

---

### When to Use

✔ Only one bean of that type exists

---

## 7.2 Autowire byName

### Rule

> **Spring matches dependency by property name = bean id**

---

### Java Class

```java
public class OrderService {

    private PaymentService paymentService;

    public void setPaymentService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

---

### XML Configuration

```xml
<bean id="paymentService"
      class="com.app.PaymentService"/>

<bean class="com.app.OrderService"
      autowire="byName"/>
```

---

### How It Works

- Property name: `paymentService`
    
- Bean id: `paymentService`
    
- Names match → injected
    

---

### Failure Case

❌ If names do not match exactly

---

### When to Use

✔ Strict naming conventions

---

## 7.3 Autowire byConstructor

### Rule

> **Spring injects dependencies using constructor arguments**

---

### Java Class

```java
public class OrderService {

    private PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

---

### XML Configuration

```xml
<bean class="com.app.OrderService"
      autowire="constructor"/>

<bean id="paymentService"
      class="com.app.PaymentService"/>
```

---

### How It Works

- Spring finds constructor
    
- Resolves constructor parameter type
    
- Injects matching bean
    

---

### Failure Case

❌ If constructor parameters cannot be resolved

---

### When to Use

✔ Mandatory dependencies  
✔ Clean constructor design

---

## XML Autowire Memorization Rule

> **byType → match by class**  
> **byName → match by property name**  
> **constructor → match constructor args**

---

# 8. NoUniqueBeanDefinitionException

## Cause

> **Multiple beans of same type exist**

---

```java
@Autowired
private NotificationService service;
```

❌ Exception thrown

---

# 9. Resolving Multiple Beans

---

## 9.1 `@Qualifier`

```java
public OrderService(
    @Qualifier("emailService")
    NotificationService service) {
    this.service = service;
}
```

---

## 9.2 `@Primary`

```java
@Primary
@Service
class EmailService implements NotificationService {}
```

---

## Rule

> **@Qualifier overrides @Primary**

---

# 10. Lazy Initialization

---

```java
@Lazy
@Service
class HeavyService {}
```

---

```xml
<bean class="com.app.HeavyService"
      lazy-init="true"/>
```

---

# 11. Injecting Collections

---

## List

```java
@Autowired
private List<PaymentService> services;
```

---

## XML

```xml
<list>
    <ref bean="emailService"/>
    <ref bean="smsService"/>
</list>
```

---

## Map

```java
@Autowired
private Map<String, PaymentService> serviceMap;
```

---

# 12. `@Order`

## Real-World Scenario

### **Payment Processing Pipeline**

Before charging a customer, an order must go through **multiple steps in a strict order**:

1. Validate request
    
2. Check fraud
    
3. Apply discount
    
4. Charge payment
    

Each step is implemented as a **separate bean**, and Spring injects them as a **list**.

---

## Step 1: Common Interface

```java
public interface OrderProcessor {
    void process(Order order);
}
```

---

## Step 2: Multiple Implementations with Order

```java
@Order(1)
@Service
public class ValidationProcessor implements OrderProcessor {
    public void process(Order order) {
        System.out.println("Validating order");
    }
}
```

```java
@Order(2)
@Service
public class FraudCheckProcessor implements OrderProcessor {
    public void process(Order order) {
        System.out.println("Checking fraud");
    }
}
```

```java
@Order(3)
@Service
public class PaymentProcessor implements OrderProcessor {
    public void process(Order order) {
        System.out.println("Processing payment");
    }
}
```

---

## Step 3: Inject as a List

```java
@Service
public class OrderProcessingService {

    private final List<OrderProcessor> processors;

    public OrderProcessingService(List<OrderProcessor> processors) {
        this.processors = processors;
    }

    public void process(Order order) {
        processors.forEach(p -> p.process(order));
    }
}
```

---

## Output (Guaranteed Order)

```text
Validating order
Checking fraud
Processing payment
```

---

## Why Use `@Order`?

✔ Controls execution order  
✔ Avoids manual sorting  
✔ Clean, extensible pipeline

---

## How `@Order` Works Internally

1. Spring finds all beans of type `OrderProcessor`
    
2. Reads `@Order` values
    
3. Sorts beans before injection
    
4. Injects sorted list
    

---

## Interview One-Liner

> **`@Order` defines the execution priority when Spring injects multiple beans as a collection.**

---

# 13. `@Lookup`

## Real-World Scenario

### **Generating Multiple PDF Reports in One Request**

- `ReportService` is **singleton**
    
- Each report must be **new instance**
    
- `Report` is **prototype-scoped**
    

### Problem

A singleton **cannot directly inject a prototype** — Spring would inject only one instance.

---

## Step 1: Prototype Bean

```java
@Component
@Scope("prototype")
public class Report {

    public Report() {
        System.out.println("New Report created");
    }

    public void generate() {
        System.out.println("Generating report");
    }
}
```

---

## Step 2: Singleton Service Using `@Lookup`

```java
@Service
public abstract class ReportService {

    public void generateReports() {
        Report r1 = createReport();
        Report r2 = createReport();

        r1.generate();
        r2.generate();
    }

    @Lookup
    protected abstract Report createReport();
}
```

---

## Output

```text
New Report created
Generating report
New Report created
Generating report
```

✔ New object each time  
✔ No manual context access

---

## Why Use `@Lookup`?

✔ Get new prototype instances  
✔ Avoid `ApplicationContext` dependency  
✔ Clean design

---

## How `@Lookup` Works Internally

1. Spring creates a **runtime subclass** (CGLIB proxy)
    
2. Overrides `createReport()` method
    
3. Each call:
    
    - Fetches a new bean from container
        
    - Returns fresh instance
        

---

## Interview One-Liner

> **`@Lookup` allows a singleton bean to dynamically obtain new prototype instances at runtime.**

---

# 🔑 Final Memorization Table

|Annotation|Problem Solved|Real-World Use|
|---|---|---|
|`@Order`|Multiple beans execution order|Pipelines, filters|
|`@Lookup`|Prototype in singleton|Reports, tasks|

---

# 🔥 When Interviewers Ask “Why Not Just Inject Normally?”

### `@Order`

❌ Without it → unpredictable order  
✔ With it → deterministic pipeline

### `@Lookup`

❌ Normal injection → same instance reused  
✔ `@Lookup` → new instance every call

---
```xml
<lookup-method name="createReport"
               bean="report"/>
```

---

# 14. FINAL MEMORIZATION CHEAT SHEET

- Constructor injection → BEST
    
- Setter injection → Optional
    
- Field injection → Avoid
    
- XML autowire:
    
    - byType → type match
        
    - byName → property = bean id
        
    - constructor → constructor args
        
- `@Qualifier` → explicit bean
    
- `@Primary` → default bean
    
- `@Lazy` → delayed creation
    
- Collections → List / Map
    
- `@Lookup` → prototype in singleton
    
