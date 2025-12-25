## **Definition**

> _High-level modules should not depend on low-level modules.  
> Both should depend on abstractions._

> _Abstractions should not depend on details.  
> Details should depend on abstractions._

---

# 🧠 **What does this really mean?**

### ✔ High-level code (business logic)

should **not** rely directly on

### ✔ Low-level code (implementation details, classes).

Instead, both should depend on:

### ✔ Interfaces or abstract classes

This makes your system:

- flexible
    
- easy to modify
    
- easy to test
    
- loosely coupled
    

---

# 🧩 **Example Without DIP (Bad Design)**

```java
class EmailService {
    void sendEmail(String message) {
        System.out.println("Sending email: " + message);
    }
}

class Notification {
    private EmailService emailService = new EmailService();

    void send(String msg) {
        emailService.sendEmail(msg);
    }
}
```

### ❌ **Violations**

1. **High-level Notification directly depends on EmailService**
    
2. If we want to send SMS or WhatsApp → we must change Notification → violates _Open/Closed_
    

---

# ⭐ **Apply DIP (Good Design)**

---

## **Step 1: Create an abstraction**

```java
interface MessageService {
    void sendMessage(String msg);
}
```

---

## **Step 2: Low-level modules depend on abstraction**

```java
class EmailService implements MessageService {
    public void sendMessage(String msg) {
        System.out.println("Sending email: " + msg);
    }
}

class SMSService implements MessageService {
    public void sendMessage(String msg) {
        System.out.println("Sending SMS: " + msg);
    }
}
```

---

## **Step 3: High-level module also depends on abstraction**

```java
class Notification {
    private MessageService service;

    public Notification(MessageService service) {
        this.service = service;
    }

    void send(String msg) {
        service.sendMessage(msg);
    }
}
```

---

## **Usage**

```java
Notification n = new Notification(new EmailService());
n.send("Hello!");

Notification n2 = new Notification(new SMSService());
n2.send("Hello!");
```

✔ Now Notification doesn’t care what service is used  
✔ Adding WhatsAppService doesn’t change Notification at all  
✔ We achieved flexibility

---

# ⭐ **Core Idea of DIP**

|Without DIP|With DIP|
|---|---|
|High-level → depends on low-level|High-level → depends on interface|
|Hard to change|Easy to change|
|Hard to test|Easy to mock interfaces|
|Tight coupling|Loose coupling|
|Violates OCP|Supports OCP|

---

# ⭐ **Why DIP is powerful?**

### ✔ Easy to swap implementations

(Email → SMS → WhatsApp → Push Notifications)

### ✔ High-level modules become stable

You rarely change the business logic.

### ✔ Promotes dependency injection

(constructor injection, setter injection, frameworks like Spring)

### ✔ Supports unit testing with mocks

No concrete classes needed.

---

# ⭐ **DIP in one sentence**

> **Make classes depend on interfaces, not concrete implementations.**
