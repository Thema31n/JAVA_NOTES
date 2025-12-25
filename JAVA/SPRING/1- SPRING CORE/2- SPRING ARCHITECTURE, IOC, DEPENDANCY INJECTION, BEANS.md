# 1. Spring Framework Architecture

### Definition

Spring is a **layered, modular framework** where each layer builds on top of the **Core Container**.

### Core Rule (memorize)

> **Everything in Spring depends on the Core Container**

---

## Main Layers (in order of importance)

1. **Core Container**
    
2. **AOP**
    
3. **Data Access / Integration**
    
4. **Web**
    
5. **Security**
    
6. **Test**
    

---

## Core Container Modules (VERY IMPORTANT)

- `spring-core` → utilities, IoC basics
    
- `spring-beans` → bean creation & wiring
    
- `spring-context` → ApplicationContext
    
- `spring-expression` → SpEL
    

👉 **If you understand Core Container, you understand Spring**

---

# 2. Inversion of Control (IoC)

## Definition (Interview-ready)

> **IoC means the control of object creation and dependency management is transferred from the developer to the Spring container.**

---

## Without IoC (Traditional Java)

```java
class Car {
    Engine engine = new Engine();
}
```

❌ Tight coupling  
❌ Hard to change  
❌ Hard to test

---

## With IoC (Spring)

```java
@Component
class Car {
    private final Engine engine;

    public Car(Engine engine) {
        this.engine = engine;
    }
}
```

✔ Loose coupling  
✔ Easy to test  
✔ Easy to replace implementations

---

## Memorization Analogy

- **Without IoC** → You cook everything yourself
    
- **With IoC** → Restaurant serves you ready food
    

---

# 3. Dependency Injection (DI)

## Definition

> **Dependency Injection is the mechanism used by Spring to implement IoC by injecting required objects into a class.**

---

## Rule to Memorize

> **IoC = concept**  
> **DI = implementation**

---

## Types of DI (Order Matters)

### 1️⃣ Constructor Injection (BEST)

```java
@Service
class OrderService {
    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

✔ Mandatory dependencies  
✔ Immutable  
✔ Recommended by Spring

---

### 2️⃣ Setter Injection

```java
@Service
class OrderService {
    private PaymentService paymentService;

    @Autowired
    public void setPaymentService(PaymentService ps) {
        this.paymentService = ps;
    }
}
```

✔ Optional dependencies

---

### 3️⃣ Field Injection (AVOID)

```java
@Autowired
private PaymentService paymentService;
```

❌ Hard to test  
❌ Hidden dependency

---

# 4. Spring IoC Container

## Definition

> **Spring IoC Container is responsible for creating, configuring, managing, and destroying Spring beans.**

---

## What the Container Does

1. Reads configuration
    
2. Creates objects (beans)
    
3. Injects dependencies
    
4. Manages lifecycle
    

---

## Types of IoC Containers

### 1️⃣ BeanFactory

- Basic container
    
- Lazy initialization
    
- Rarely used directly
    

### 2️⃣ ApplicationContext (IMPORTANT)

- Extends BeanFactory
    
- Used in real applications
    

---

## Memorization Rule

> **BeanFactory = basic**  
> **ApplicationContext = enterprise-level**

---

# 5. ApplicationContext

## Definition

> **ApplicationContext is an advanced IoC container that provides additional enterprise features.**

---

## Extra Features Over BeanFactory

- Event publishing
    
- Internationalization (i18n)
    
- AOP support
    
- Environment & profiles
    

---

## Common ApplicationContext Types

### 1️⃣ AnnotationConfigApplicationContext

Used in **standalone Java apps**

```java
ApplicationContext ctx =
    new AnnotationConfigApplicationContext(AppConfig.class);
```

---

### 2️⃣ ClassPathXmlApplicationContext (Legacy)

```java
ApplicationContext ctx =
    new ClassPathXmlApplicationContext("beans.xml");
```

---

### 3️⃣ WebApplicationContext

Used in **web applications**

- Integrated with Spring MVC
    

---

## Memorization Tip

> **Standalone → AnnotationConfig**  
> **Legacy → XML**  
> **Web → WebApplicationContext**

---

# 6. Spring Context (What People Mean)

## Important Clarification

When developers say **“Spring Context”**, they usually mean:

> **ApplicationContext + all managed beans**

---

## Context Responsibilities

- Holds beans
    
- Manages lifecycle
    
- Provides dependency injection
    
- Publishes events
    

---

# 7. Spring Beans

## Definition

> **A Spring Bean is an object that is created, configured, and managed by the Spring IoC container.**

---

## How a Class Becomes a Bean

### Using Annotations

```java
@Component
class UserService {}
```

Specialized forms:

- `@Service`
    
- `@Repository`
    
- `@Controller`
    

---

### Using Configuration Class

```java
@Configuration
class AppConfig {

    @Bean
    public UserService userService() {
        return new UserService();
    }
}
```

---

## Bean Lifecycle (Simplified)

1. Bean instantiated
    
2. Dependencies injected
    
3. Initialization callbacks
    
4. Ready to use
    
5. Destroyed on context shutdown
    

---

# 8. Scopes of Spring Beans

## Definition

> **Bean scope defines the lifecycle and visibility of a bean within the Spring container.**

---

## Common Bean Scopes 

### 1️⃣ Singleton (DEFAULT)

```java
@Component
class UserService {}
```

- One instance per container
    
- Shared across application
    

✔ Most common

---

### 2️⃣ Prototype

```java
@Scope("prototype")
@Component
class TempObject {}
```

- New instance every request
    
- Spring does NOT manage destruction
    

---

### 3️⃣ Request (Web)

- One bean per HTTP request
    

---

### 4️⃣ Session (Web)

- One bean per HTTP session
    

---

### 5️⃣ Application (Web)

- One bean per ServletContext
    

---

## Memorization Rule

> **Singleton → One per container**  
> **Prototype → New every time**  
> **Request/Session → Web only**

---

# 9. Spring Expression Language (SpEL)

## Definition

> **SpEL is a powerful expression language used to query and manipulate objects at runtime in Spring.**

---

## Where SpEL Is Used

- `@Value`
    
- Security rules
    
- Conditional beans
    

---

## Basic Example

```java
@Value("#{2 + 3}")
private int result;
```

---

## Access Bean Properties

```java
@Value("#{user.name}")
private String username;
```

---

## Read from Properties

```java
@Value("${server.port}")
private int port;
```

---

## Conditional Bean Creation

```java
@ConditionalOnExpression("#{environment['env'] == 'prod'}")
```

---

## Memorization Tip

> **SpEL = dynamic values at runtime**

---

# 10. Ultra-Short Revision Sheet (Interview Gold)

- **IoC** → Spring controls object creation
    
- **DI** → Dependencies injected by Spring
    
- **IoC Container** → Creates & manages beans
    
- **ApplicationContext** → Advanced container
    
- **Spring Context** → Container + beans
    
- **Spring Bean** → Managed object
    
- **Bean Scope** → Bean lifecycle rule
    
- **SpEL** → Runtime expressions
    
