# 1. Spring IoC Containers (Big Picture)

## Definition

> **Spring IoC Container creates, configures, wires, manages, and destroys Spring beans.**

---

## Types of IoC Containers

1. **BeanFactory**
    
2. **ApplicationContext**
    

👉 **ApplicationContext is built on top of BeanFactory**

---

# 2. Low-Level BeanFactory Initialization (CORE INTERNALS)

## Key Classes (MEMORIZE)

- **DefaultListableBeanFactory** → core container
    
- **XmlBeanDefinitionReader** → reads XML
    
- **ClassPathResource** → loads resource
    
- **loadBeanDefinitions()** → registers beans
    

---

## Full Low-Level Example (VERY IMPORTANT)

```java
DefaultListableBeanFactory beanFactory =
        new DefaultListableBeanFactory();

XmlBeanDefinitionReader reader =
        new XmlBeanDefinitionReader(beanFactory);

Resource resource =
        new ClassPathResource("beans.xml");

reader.loadBeanDefinitions(resource);
```

---

## Internal Flow

1. Container created
    
2. XML read
    
3. BeanDefinitions created
    
4. Beans registered (not instantiated yet)
    

👉 **Beans are created lazily**

---

## Getting Bean

```java
UserService service =
    beanFactory.getBean("userService", UserService.class);
```

---

# 3. ApplicationContext Initialization

## XML-Based

```java
ApplicationContext context =
    new ClassPathXmlApplicationContext("beans.xml");
```

---

## Java-Based

```java
ApplicationContext context =
    new AnnotationConfigApplicationContext(AppConfig.class);
```

---

## Spring Boot

```java
SpringApplication.run(MyApp.class, args);
```

---

# 4. BeanFactory vs ApplicationContext

|Feature|BeanFactory|ApplicationContext|
|---|---|---|
|Init|Lazy|Eager|
|AOP|❌|✅|
|Events|❌|✅|
|Enterprise|❌|✅|

---

# 5. Annotation-Based Bean Configuration (FULL FLOW)

## Step 1: Configuration Class

```java
@Configuration
@ComponentScan("com.app")
public class AppConfig {
}
```

---

## Step 2: Business Interface

```java
public interface PaymentService {
    void pay();
}
```

---

## Step 3: Implementation Beans

```java
@Service
public class CreditCardPaymentService
        implements PaymentService {

    public void pay() {
        System.out.println("Paying via Credit Card");
    }
}
```

```java
@Service
public class PaypalPaymentService
        implements PaymentService {

    public void pay() {
        System.out.println("Paying via PayPal");
    }
}
```

---

## Step 4: Bootstrap Context

```java
ApplicationContext context =
    new AnnotationConfigApplicationContext(AppConfig.class);
```

---

## Step 5: Use Bean

```java
PaymentService service =
    context.getBean(PaymentService.class);
service.pay();
```

---

# 6. Class-Level Bean Annotations (FULL LAYERED EXAMPLE)

---

## Controller Layer

```java
@RestController
@RequestMapping("/orders")
public class OrderController {

    private final OrderService orderService;

    public OrderController(OrderService orderService) {
        this.orderService = orderService;
    }
}
```

---

## Service Layer

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

## Repository Layer

```java
@Repository
public class OrderRepository {

    public void save() {
        System.out.println("Order saved");
    }
}
```

---

## Why These Annotations Matter

- `@Service` → business logic clarity
    
- `@Repository` → exception translation
    
- `@Controller` → web layer
    
- All are **@Component**
    

---

# 7. @Qualifier Annotation (FULL REAL-WORLD EXAMPLE)

## Problem: Multiple Beans of Same Interface

```java
public interface NotificationService {
    void send(String msg);
}
```

---

## Two Implementations

```java
@Service("emailService")
public class EmailNotificationService
        implements NotificationService {

    public void send(String msg) {
        System.out.println("Email sent: " + msg);
    }
}
```

```java
@Service("smsService")
public class SmsNotificationService
        implements NotificationService {

    public void send(String msg) {
        System.out.println("SMS sent: " + msg);
    }
}
```

---

## Injection WITHOUT @Qualifier ❌

```java
@Autowired
private NotificationService service;
```

❌ **NoUniqueBeanDefinitionException**

---

## Injection WITH @Qualifier ✅ (Constructor Injection)

```java
@Service
public class AlertService {

    private final NotificationService service;

    public AlertService(
        @Qualifier("emailService")
        NotificationService service) {

        this.service = service;
    }

    public void alert() {
        service.send("System alert");
    }
}
```

---

## Alternative: Field Injection (NOT Recommended)

```java
@Autowired
@Qualifier("smsService")
private NotificationService service;
```

---

## Memorization Rule

> **@Qualifier selects which bean to inject when multiple beans exist**

---

# 8. Bean ID vs Bean Name

## Annotation Example

```java
@Service("emailService")
public class EmailNotificationService {}
```

- `emailService` → bean name
    

---

## Default Naming Rule

```text
EmailNotificationService → emailNotificationService
```

---

# 9. XML-Based Bean Configuration (Legacy)

```xml
<bean id="orderService"
      class="com.app.OrderService">
    <property name="paymentService"
              ref="paymentService"/>
</bean>
```

---

# 10. @Bean (Method-Level Bean Annotation)

```java
@Configuration
public class AppConfig {

    @Bean
    public ObjectMapper objectMapper() {
        return new ObjectMapper();
    }
}
```

---

## When to Use

- Third-party libraries
    
- External beans
    

---

# 11. Scopes of Spring Beans

| Scope       | Meaning                               |
| ----------- | ------------------------------------- |
| singleton   | One instance (default)                |
| prototype   | New instance                          |
| request     | Per HTTP request (not in BeanFactory) |
| session     | Per HTTP session (not in BeanFactory) |
| application | Per web app (not in BeanFactory)      |

---

```java
@Scope("prototype")
@Component
class TempBean {}
```

---

# 12. XML vs Annotation Configuration

## XML

### Pros

- Externalized
    
- No recompilation
    

### Cons

- Verbose
    
- Error-prone
    

---

## Annotation

### Pros

- Clean
    
- Modern
    
- Compile-time safety
    

### Cons

- Config mixed with code
    

---

# 13. FINAL INTERVIEW REVISION CHEAT-SHEET

- **DefaultListableBeanFactory** → core container
    
- **XmlBeanDefinitionReader** → reads XML
    
- **loadBeanDefinitions()** → registers beans
    
- **ApplicationContext** → enterprise container
    
- **@Component / @Service / @Repository / @Controller**
    
- **@Bean** → method-level bean
    
- **@Qualifier** → resolve ambiguity
    
- **Singleton** → default scope
    
- **XML** → legacy
    
- **Annotations** → modern
    
