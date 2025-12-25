# 🧠 **Behavioral Patterns — Overview**

Behavioral patterns define **how objects interact** and **share responsibilities**.  
They help avoid:  
❌ Big conditional blocks  
❌ Hard-coded logic  
❌ Rigid flows  
❌ Tight coupling

These patterns make behavior **pluggable**, **extensible**, and **dynamic**.

---

# 🟦 **1. Strategy Pattern**

### **Declarative Definition**

> Define a family of algorithms and make them interchangeable at runtime.

### **Why use it**

- Want to replace **IF/ELSE** with interchangeable behaviors
    
- Select algorithm dynamically (discounts, sorting, payment methods)
    
- Avoid hardcoding choices
    

### **Real-World Scenario**

Payment Gateway:

- Pay via **PayPal**
    
- Pay via **Credit Card**
    
- Pay via **Cryptocurrency**
    
- Pay via **Bank Transfer**
    

Without Strategy → huge `if-else` tree.  
With Strategy → pluggable strategy objects.

---

## ▶️ Strategy — Java Example

### Strategy interface

```java
interface PaymentStrategy {
    void pay(double amount);
}
```

### Concrete Strategies

```java
class PaypalStrategy implements PaymentStrategy {
    public void pay(double amount) { System.out.println("PayPal: " + amount); }
}

class CreditCardStrategy implements PaymentStrategy {
    public void pay(double amount) { System.out.println("Card: " + amount); }
}
```

### Context

```java
class Checkout {
    private PaymentStrategy strategy;

    public void setStrategy(PaymentStrategy strategy) {
        this.strategy = strategy;
    }

    public void process(double amount) {
        strategy.pay(amount);
    }
}
```

### Usage

```java
Checkout c = new Checkout();
c.setStrategy(new PaypalStrategy());
c.process(100);
```

---

# 🟥 **2. Command Pattern**

### **Declarative Definition**

> Encapsulate a **request** as an object.

### **Why use it**

- Undo/redo functionality
    
- Queue, log, or schedule operations
    
- Decouple invoker from receiver
    
- GUI button actions
    

### **Real-World Scenario**

A Remote Control:

- Turn TV on/off
    
- Change channel
    
- Volume up/down
    

Each button click → **Command object**.

---

## ▶️ Command — Java Example

### Command interface

```java
interface Command {
    void execute();
}
```

### Receiver (the one that actually does work)

```java
class Light {
    void on() { System.out.println("Light ON"); }
    void off() { System.out.println("Light OFF"); }
}
```

### Concrete commands

```java
class LightOnCommand implements Command {
    private Light light;
    public LightOnCommand(Light light) { this.light = light; }
    public void execute() { light.on(); }
}

class LightOffCommand implements Command {
    private Light light;
    public LightOffCommand(Light light) { this.light = light; }
    public void execute() { light.off(); }
}
```

### Invoker

```java
class RemoteControl {
    private Command slot;
    public void setCommand(Command cmd) { slot = cmd; }
    public void pressButton() { slot.execute(); }
}
```

### Usage

```java
RemoteControl remote = new RemoteControl();
remote.setCommand(new LightOnCommand(new Light()));
remote.pressButton();
```

---

# 🔥 **3. Strategy vs Command (Very Important)**

|Feature|Strategy|Command|
|---|---|---|
|Purpose|Choose algorithm|Encapsulate request|
|Object Represents|Behavior|Action|
|When to use|Algorithm variation|Execution control (undo, queue)|
|Trigger|Called by code logic|Called by invoker (button)|
|Parameters|Behavior change|Request + receiver|
|Analogy|“How do you want to sort?”|“Push button to do action”|

### Simple Summary:

**Strategy = interchangeable behavior**  
**Command = executable action**

---

# 🟩 **4. Template Method Pattern**

### **Declarative Definition**

> Define the **skeleton of an algorithm** and let subclasses fill specific steps.

### **Why use it**

- Algorithm is same across multiple implementations
    
- Only some steps change
    
- Common in frameworks like Spring, Servlet API
    

### **Real-World Scenario**

Order processing always:

1. Validate
    
2. Process payment
    
3. Generate receipt
    
4. Notify user
    

But different order types may vary step 2.

---

## ▶️ Template Method — Java Example

### Template (abstract class)

```java
abstract class OrderProcessor {
    public final void processOrder() {
        validate();
        pay();
        generateReceipt();
        notifyUser();
    }

    protected abstract void pay();

    protected void validate() {
        System.out.println("Validating...");
    }

    protected void generateReceipt() {
        System.out.println("Receipt generated.");
    }

    protected void notifyUser() {
        System.out.println("User notified.");
    }
}
```

### Concrete classes

```java
class CreditCardOrder extends OrderProcessor {
    protected void pay() { System.out.println("Paid by Card"); }
}

class PaypalOrder extends OrderProcessor {
    protected void pay() { System.out.println("Paid by PayPal"); }
}
```

### Usage

```java
OrderProcessor o = new PaypalOrder();
o.processOrder();
```

---

# 🟨 **5. Iterator Pattern**

### **Declarative Definition**

> Provide a way to traverse a collection without exposing its internal layout.

### **Why use it**

- Uniform iteration interface
    
- Hide internal storage (array, tree, linked list)
    
- Enable custom traversal logic
    

### **Real-World Scenario**

Iterating over:

- Database results
    
- Custom tree structures
    
- Custom collections (graph nodes, filesystem objects)
    

---

## ▶️ Iterator — Java Example

Java’s built-in iterator supports this:

```java
List<String> names = List.of("Ali", "Omar", "Sara");

Iterator<String> iterator = names.iterator();

while (iterator.hasNext()) {
    System.out.println(iterator.next());
}
```

---

## Custom Iterator Example

```java
class MyCollection implements Iterable<String> {
    private List<String> data = List.of("a", "b", "c");

    public Iterator<String> iterator() {
        return data.iterator();
    }
}
```

---

# 🟫 **6. Chain of Responsibility Pattern**

### **Declarative Definition**

> A request passes through a **chain of handlers**, each deciding whether to process it or pass it on.

### **Why use it**

- Avoid massive `if-else`/`switch` chains
    
- Dynamic handler stacking
    
- Useful for pipelines, middleware, validation
    

### **Real-World Scenario**

Web server request pipeline:

- Authentication
    
- Authorization
    
- Validation
    
- Business logic
    

If any step fails → chain breaks.

---

## ▶️ Chain of Responsibility — Java Example

### Handler interface

```java
abstract class Handler {
    private Handler next;

    public Handler setNext(Handler next) {
        this.next = next;
        return next;
    }

    public void handle(String req) {
        if (process(req) && next != null) {
            next.handle(req);
        }
    }

    protected abstract boolean process(String req);
}
```

### Concrete handlers

```java
class AuthHandler extends Handler {
    protected boolean process(String req) {
        System.out.println("Auth check");
        return req.contains("token");
    }
}

class ValidationHandler extends Handler {
    protected boolean process(String req) {
        System.out.println("Validating data");
        return req.contains("data");
    }
}

class BusinessHandler extends Handler {
    protected boolean process(String req) {
        System.out.println("Business logic executed");
        return true;
    }
}
```

### Usage

```java
Handler chain = new AuthHandler();
chain.setNext(new ValidationHandler())
     .setNext(new BusinessHandler());

chain.handle("token-data");
```

---

# 🎯 **Final Summary**

### ✔ **Strategy**

- Behavior interchangeable
    
- Replace IF/ELSE
    
- Example: payment method selection
    

### ✔ **Command**

- Encapsulate executable action
    
- Undo/redo, scheduler, remote control
    

### ✔ **Strategy vs Command**

- Strategy = behavior
    
- Command = request execution
    

### ✔ **Template Method**

- Algorithm skeleton in a superclass
    
- Subclasses override specific steps
    

### ✔ **Iterator**

- Uniform traversal
    
- Hides data structure
    

### ✔ **Chain of Responsibility**

- Processing pipeline
    
- Handlers chain request
    
