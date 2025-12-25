# 🧱 **1. Singleton (Realistic Example)**

### **Real-world scenario:**

A system has **one global configuration**, loaded once.

### **Declarative goal:**

“Only ONE instance of this object exists, and everyone shares it.”

### **Realistic Example: ApplicationConfig**

```java
public class ApplicationConfig {

    private static volatile ApplicationConfig INSTANCE;

    private final Properties props;

    private ApplicationConfig() {
        props = new Properties();
        props.load(ApplicationConfig.class.getResourceAsStream("/app.properties"));
    }

    public static ApplicationConfig getInstance() {
        if (INSTANCE == null) {
            synchronized (ApplicationConfig.class) {
                if (INSTANCE == null) {
                    INSTANCE = new ApplicationConfig();
                }
            }
        }
        return INSTANCE;
    }

    public String get(String key) {
        return props.getProperty(key);
    }
}
```

### Used in real applications for:

- Configuration
    
- Caching
    
- Logging system
    
- Connection pools
    

---

# 🟦 **2. Prototype (Realistic Example)**

### **Real-world scenario:**

Creating many similar objects is expensive (e.g., complex UI components, game entities).

### **Declarative goal:**

“Clone an existing object instead of rebuilding it.”

### **Example: Cloning a document template**

```java
public class Document implements Cloneable {

    private String title;
    private String body;
    private List<String> tags;

    public Document(String title, String body, List<String> tags) {
        this.title = title;
        this.body = body;
        this.tags = tags;
    }

    @Override
    public Document clone() {
        return new Document(title, body, new ArrayList<>(tags)); // deep copy
    }
}
```

### Usage:

```java
Document invoiceTemplate = new Document("Invoice", "...", List.of("Finance", "Invoice"));
Document invoiceCopy = invoiceTemplate.clone();
invoiceCopy.setTitle("Invoice #5453");
```

---

# 🟪 **3. Factory Method (Realistic Example)**

### **Real-world scenario:**

The system should decide the correct payment processor at runtime.

### **Declarative goal:**

“Subclass decides the concrete object to create.”

### **Example: Payment Dialog**

```java
abstract class PaymentDialog {
    public void processPayment() {
        PaymentProcessor processor = createProcessor();
        processor.pay();
    }
    protected abstract PaymentProcessor createProcessor();
}

class PaypalDialog extends PaymentDialog {
    @Override
    protected PaymentProcessor createProcessor() {
        return new PaypalProcessor();
    }
}

class VisaDialog extends PaymentDialog {
    @Override
    protected PaymentProcessor createProcessor() {
        return new VisaProcessor();
    }
}
```

---

# 🟫 **4. Abstract Factory (Realistic Example)**

### **Real-world scenario:**

Build a UI theme system: Light Theme and Dark Theme.

### **Declarative goal:**

“Return a whole FAMILY of related objects.”

### **Example: UI Theme Factory**

```java
interface UIThemeFactory {
    Button createButton();
    Menu createMenu();
}

class DarkThemeFactory implements UIThemeFactory {
    public Button createButton() { return new DarkButton(); }
    public Menu createMenu() { return new DarkMenu(); }
}

class LightThemeFactory implements UIThemeFactory {
    public Button createButton() { return new LightButton(); }
    public Menu createMenu() { return new LightMenu(); }
}
```

### Usage:

```java
UIThemeFactory factory = new DarkThemeFactory();
Button btn = factory.createButton();
Menu menu = factory.createMenu();
```

---

# 🟩 **5. Builder Pattern — REALISTIC + 2 WAYS**

The Builder pattern solves the **“too many constructor parameters”** problem and improves readability.

There are **2 main ways to implement Builder in Java**:

---

## ✅ **Way #1: Classic Mutable Builder (Lombok-like)**

### **Declarative goal:**

“Build an object step-by-step using chained methods.”

### **Example: Building an HTTP Request**

```java
public class HttpRequest {
    private final String method;
    private final String url;
    private final Map<String, String> headers;

    private HttpRequest(Builder b) {
        this.method = b.method;
        this.url = b.url;
        this.headers = b.headers;
    }

    public static class Builder {
        private String method;
        private String url;
        private Map<String, String> headers = new HashMap<>();

        public Builder method(String method) {
            this.method = method;
            return this;
        }

        public Builder url(String url) {
            this.url = url;
            return this;
        }

        public Builder header(String key, String value) {
            this.headers.put(key, value);
            return this;
        }

        public HttpRequest build() {
            return new HttpRequest(this);
        }
    }
}
```

### Usage:

```java
HttpRequest request = new HttpRequest.Builder()
    .method("GET")
    .url("https://api.example.com")
    .header("Auth", "TOKEN")
    .build();
```

---

## ✅ **Way #2: Immutable Step Builder (fluent enforced ordering)**

Better when certain fields must be set **in a certain order**.

### **Declarative goal:**

“Force the builder user to follow steps in order.”

### Example:

```java
public class User {

    private final String name;
    private final int age;
    private final String email;

    private User(Builder b) {
        this.name = b.name;
        this.age = b.age;
        this.email = b.email;
    }

    public static NameStep builder() {
        return new Builder();
    }

    interface NameStep {
        AgeStep name(String name);
    }

    interface AgeStep {
        EmailStep age(int age);
    }

    interface EmailStep {
        BuildStep email(String email);
    }

    interface BuildStep {
        User build();
    }

    private static class Builder implements NameStep, AgeStep, EmailStep, BuildStep {
        private String name;
        private int age;
        private String email;

        public AgeStep name(String name) { this.name = name; return this; }
        public EmailStep age(int age) { this.age = age; return this; }
        public BuildStep email(String email) { this.email = email; return this; }

        public User build() { return new User(this); }
    }
}
```

### Usage:

```java
User u = User.builder()
    .name("Ahmed")
    .age(26)
    .email("a@test.com")
    .build();
```

This guarantees:

- name must come first
    
- age must come second
    
- email must come last
    

Perfect for APIs with mandatory fields.

---

# 🟥 **Factory Method vs Abstract Factory: Declarative Differences**

|Concept|Factory Method|Abstract Factory|
|---|---|---|
|What is created?|**One product**|**A family/group of products**|
|How?|Subclass overrides a method|Inject a factory object|
|Real-world analogy|“Tell me what pizza type you want.”|“This restaurant produces Chicago-style pizza, drinks, and desserts.”|
|Pattern type|Class-level decision|Object-level decision|
|Use when|You need one product chosen by subclasses|You need a _set_ of related products|

### Example summary:

- **Factory Method** → “which product?”
    
- **Abstract Factory** → “which family?”
    

---

# 🎯 **Final Summary**

### ✔ Singleton

One shared instance across application (e.g., Config, Logger)

### ✔ Prototype

Clone existing objects instead of creating new ones

### ✔ Factory Method

Subclass decides **which concrete product** to create

### ✔ Abstract Factory

Create **related product families** without specifying their classes

### ✔ Builder

Build complex objects step-by-step  
Two types:

- **Classic Mutable Builder**
    
- **Immutable Step Builder** (ordered construction)
    
