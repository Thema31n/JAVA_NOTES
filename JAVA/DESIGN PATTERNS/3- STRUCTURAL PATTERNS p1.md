# 🟦 **1. PROXY PATTERN**

Proxy **stands between the client and the real object** and controls access.

A Proxy is used when you need **access control**, **lazy loading**, **caching**, **security**, **remote access**, etc.

---

## ✅ **Case 1 — Lazy Loading (Virtual Proxy)**

### **Real-World Scenario:**

Loading a 20MB image is expensive. Only load it when needed.

### Example:

```java
interface Image {
    void display();
}

class RealImage implements Image {
    private final String fileName;

    public RealImage(String fileName) {
        this.fileName = fileName;
        loadFromDisk();
    }

    private void loadFromDisk() {
        System.out.println("Loading " + fileName);
    }

    public void display() {
        System.out.println("Displaying " + fileName);
    }
}

class ImageProxy implements Image {
    private RealImage realImage;
    private final String fileName;

    public ImageProxy(String fileName) {
        this.fileName = fileName;
    }

    public void display() {
        if (realImage == null) {
            realImage = new RealImage(fileName); // lazy load
        }
        realImage.display();
    }
}
```

### Why Proxy is used:

- No need to load heavy file immediately
    
- Only load when user asks to display
    

---

## ✅ **Case 2 — Security / Access Control Proxy**

### Real-World Scenario:

Admins can delete files, normal users cannot.

```java
interface FileService {
    void deleteFile(String path);
}

class RealFileService implements FileService {
    public void deleteFile(String path) {
        System.out.println("Deleting " + path);
    }
}

class SecureFileProxy implements FileService {
    private final FileService real;
    private final boolean isAdmin;

    public SecureFileProxy(FileService service, boolean isAdmin) {
        this.real = service;
        this.isAdmin = isAdmin;
    }

    public void deleteFile(String path) {
        if (!isAdmin) {
            throw new SecurityException("Not allowed!");
        }
        real.deleteFile(path);
    }
}
```

---

## ✅ **Case 3 — Remote Proxy (Accessing remote resources)**

### Real-World Scenario:

A local object representing a remote REST API.

```java
interface OrderService {
    Order getOrder(int id);
}

class OrderServiceRemoteProxy implements OrderService {
    public Order getOrder(int id) {
        System.out.println("Calling remote /orders/" + id);
        return new Order(id, "Laptop");
    }
}
```

### Why Proxy:

- Treat remote API like a local method call
    
- Hides HTTP logic
    

---

## Proxy Summary

|Case|Why Proxy?|
|---|---|
|Lazy Loading|Only create expensive object when needed|
|Security|Prevent unauthorized access|
|Remote|Represent remote API locally|
|Logging / Caching|Added transparently|

---

# 🟩 **2. DECORATOR PATTERN**

Decorator **wraps** an existing object to **add behavior at runtime**.

Perfect for logging, caching, validation, compression, monitoring, etc.

---

## ✅ **Case 1 — Add Logging Dynamically**

### Real-World Scenario:

We want to log messages without modifying the EmailNotifier class.

```java
interface Notifier {
    void send(String msg);
}

class EmailNotifier implements Notifier {
    public void send(String msg) {
        System.out.println("Sending EMAIL: " + msg);
    }
}

class LoggingDecorator implements Notifier {
    private final Notifier notifier;

    public LoggingDecorator(Notifier notifier) {
        this.notifier = notifier;
    }

    public void send(String msg) {
        System.out.println("LOG: Sending message");
        notifier.send(msg);
    }
}
```

Usage:

```java
Notifier notifier = new LoggingDecorator(new EmailNotifier());
notifier.send("Hello World");
```

---

## ✅ **Case 2 — Add Encryption / Compression**

### Real-World Scenario:

Before sending data, compress or encrypt dynamically.

```java
class EncryptionDecorator implements Notifier {
    private final Notifier wrappee;

    public EncryptionDecorator(Notifier wrappee) {
        this.wrappee = wrappee;
    }

    public void send(String msg) {
        String encrypted = "[Encrypted]" + msg;
        wrappee.send(encrypted);
    }
}
```

Chain multiple decorators:

```java
Notifier notifier =
    new EncryptionDecorator(
        new LoggingDecorator(
            new EmailNotifier()
        )
    );

notifier.send("Sensitive Data");
```

---

## Decorator Summary

|Case|Why Decorator?|
|---|---|
|Logging|Add logs without touching original class|
|Encryption|Transparent transformation|
|Compression|Improve performance dynamically|
|Multi-features|Compose decorators at runtime|

---

# 🟨 **3. ADAPTER PATTERN**

Adapter converts **one interface** into **another** expected by the client.

Use when integrating **incompatible systems** or **legacy code**.

---

## ✅ **Case 1 — Legacy System Adapter**

### Real-World Scenario:

Legacy system outputs XML.  
New system expects JSON.

### Example:

```java
interface JsonParser {
    void parseJson(String json);
}

class LegacyXmlParser {
    void parseXml(String xml) {
        System.out.println("Parsing XML: " + xml);
    }
}

class XmlToJsonAdapter implements JsonParser {
    private final LegacyXmlParser xmlParser = new LegacyXmlParser();

    public void parseJson(String json) {
        String xml = convertToXml(json); // JSON → XML
        xmlParser.parseXml(xml);
    }

    private String convertToXml(String json) {
        return "<xml>" + json + "</xml>";
    }
}
```

---

## ✅ **Case 2 — External Library Adapter**

### Real-World Scenario:

A 3rd-party library has a different interface from what your app expects.

```java
interface PaymentGateway {
    void pay(double amount);
}

// incompatible library
class StripeApi {
    void makePayment(double amt) { ... }
}

class StripeAdapter implements PaymentGateway {
    private final StripeApi stripe;

    public StripeAdapter(StripeApi stripe) {
        this.stripe = stripe;
    }

    public void pay(double amount) {
        stripe.makePayment(amount);
    }
}
```

### Why Adapter:

- Avoid rewriting entire system
    
- Keep external library isolated
    

---

## Adapter Summary

|Case|Why Adapter?|
|---|---|
|Legacy code|Make old API compatible with new|
|3rd-party libs|Integrate incompatible APIs|
|Multiple formats|Convert JSON/XML/CSV|
|Hardware adapters|Connect incompatible systems|

---

# 🟥 **4. FACADE PATTERN**

Facade **simplifies** a complex system by providing a **single unified API**.

---

## ✅ **Case 1 — Simplify complex system**

### Real-World Scenario:

Processing a payment requires:

- FraudService
    
- BankTransferService
    
- NotificationService
    
- Logging
    

Facade provides 1 method.

```java
class PaymentFacade {

    private FraudService fraud = new FraudService();
    private BankService bank = new BankService();
    private NotificationService notify = new NotificationService();

    public void process(double amount) {
        fraud.check(amount);
        bank.transfer(amount);
        notify.sendReceipt();
    }
}
```

Usage:

```java
new PaymentFacade().process(100);
```

---

## ✅ **Case 2 — Simplify a Library**

Java’s `Collections` class is a facade.

Example:

```java
List<Integer> list = new ArrayList<>();
Collections.sort(list);   // Simple facade for complex algorithm
```

---

## Facade Summary

|Case|Why Facade?|
|---|---|
|Complex subsystem|Provide simple API|
|Multiple services|Unify operations|
|Hide internal structure|Expose minimum surface|
|External clients|Cleaner interface|

---

# 🔥 **5. Comparative Summary (Real Difference)**

|Pattern|Purpose|Example Use Case|
|---|---|---|
|**Proxy**|Control access to object|Lazy load image, security checks, remote API|
|**Decorator**|Add features dynamically|Logging, encryption, compression|
|**Adapter**|Make interfaces compatible|Convert XML → JSON, wrap external library|
|**Facade**|Simplify complex systems|PaymentFacade, Library wrappers|

### One-line definitions:

- **Proxy** → “I control access.”
    
- **Decorator** → “I add extra features.”
    
- **Adapter** → “I convert the interface.”
    
- **Facade** → “I simplify everything.”
    

---

# 🎯 Final Summary

This expanded summary gives you:

- Real-world business scenarios
    
- Declarative reasoning
    
- Multiple examples per pattern
    
- Full code demonstrations
    
- Clear understanding of the differences
    

