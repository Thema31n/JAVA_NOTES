# 1️⃣ application.properties – Overview

### 🔹 What is `application.properties`?

> **`application.properties` is the central configuration file used to customize Spring Boot behavior without changing code.**

It controls:

- Server settings
    
- Database configs
    
- Security
    
- Logging
    
- Custom application settings
    

📌 Location:

```
src/main/resources/application.properties
```

---

### 🔹 Why it exists

- Externalize configuration
    
- Environment-specific configs
    
- Easy changes without recompiling
    

### 🧠 Interview Tip

> ❓ _“Why use application.properties instead of hardcoding values?”_  
> ✔ Separation of configuration from code

---

# 2️⃣ Precedence Order of Properties (VERY IMPORTANT)

Spring Boot follows a **strict priority order** when loading properties.

### 🔹 Highest → Lowest Priority (MEMORIZE — ADVANCED / INTERVIEW LEVEL)

1. **Command-line arguments**
    
2. **`SPRING_APPLICATION_JSON`**
    
3. **Java System Properties**
    
4. **`./config` directory of the current directory**
    
5. **Classpath root (`src/main/resources`)**
    
6. **`SPRING_CONFIG_NAME` environment variable**
    
7. **Default Spring Boot properties**
    

📌 **Higher level overrides lower level**

---

## 1️⃣ Command-line Arguments (HIGHEST PRIORITY)

```bash
java -jar app.jar --server.port=9090
```

✔ App runs on **9090**  
📌 Used heavily in **CI/CD, Docker, Kubernetes**

---

## 2️⃣ `SPRING_APPLICATION_JSON`

```bash
export SPRING_APPLICATION_JSON='{"server.port":8085}'
```

✔ App runs on **8085**  
📌 Used in **cloud & container environments**

---

## 3️⃣ Java System Properties

```bash
java -Dserver.port=7070 -jar app.jar
```

✔ App runs on **7070**  
📌 JVM-level configuration

---

## 4️⃣ `./config` Directory (External Configuration)

```
./config/application.properties
```

```properties
server.port=6060
```

✔ App runs on **6060**  
📌 Used in **production servers** to avoid rebuilding jars

---

## 5️⃣ Classpath Root (`src/main/resources`)

```
src/main/resources/application.properties
```

```properties
server.port=5050
```

✔ App runs on **5050** (only if higher sources are absent)  
📌 Used mainly for **local development**

---

## 6️⃣ `SPRING_CONFIG_NAME` Environment Variable

```bash
export SPRING_CONFIG_NAME=myapp
```

Spring Boot will load:

```
myapp.properties
myapp.yml
```

📌 Used when running **multiple applications with different config names**

---

## 7️⃣ Default Spring Boot Properties (LOWEST PRIORITY)

|Property|Default|
|---|---|
|`server.port`|8080|
|Logging level|INFO|
|Context path|`/`|

📌 Used only when **nothing else overrides them**

---

### 🧠 Interview Tip

> ❓ _“If the same property is defined in multiple places, which one is used?”_  
> ✔ The one with **higher precedence**

---

# 3️⃣ Overriding Properties

### 🔹 Example: Overriding Server Port

**application.properties**

```properties
server.port=8081
```

**Command line override**

```bash
java -jar app.jar --server.port=9090
```

➡ Server runs on **9090**, not 8081.

---

### 📌 Real-world Example

- Dev → port 8080
    
- QA → port 8081
    
- Prod → port 80
    

---

# 4️⃣ Default Spring Boot Properties

### 🔹 Examples of Defaults (MEMORIZE)

|Property|Default|
|---|---|
|`server.port`|8080|
|`server.servlet.context-path`|/|
|Logging level|INFO|
|Tomcat server|Enabled|

Spring Boot works **out-of-the-box** because of these defaults.

---

# 5️⃣ Common Spring Boot Properties (INTERVIEW LIST)

### 🔹 Server

```properties
server.port
server.servlet.context-path
```

### 🔹 Database

```properties
spring.datasource.url
spring.datasource.username
spring.datasource.password
spring.jpa.hibernate.ddl-auto
```

### 🔹 JPA

```properties
spring.jpa.show-sql
spring.jpa.properties.hibernate.format_sql
```

### 🔹 Logging

```properties
logging.level.root
logging.level.org.springframework
```

### 🔹 Security

```properties
spring.security.user.name
spring.security.user.password
```

📌 **You don’t need to memorize all — know categories.**

---

# 6️⃣ Practical Example – Changing Port Number

### 🔹 Requirement

Run app on port **9090** instead of default 8080.

```properties
server.port=9090
```

📌 Real-world:

- Avoid conflict with other services
    
- Run multiple apps on same server
    

---

# 7️⃣ SSL / TLS Configuration in Spring Boot (HTTPS)

### 🔹 Why SSL/TLS?

- Secure data in transit
    
- Mandatory for production
    
- Required for OAuth, payments, login systems
    

---

### 🔹 Enable HTTPS in Spring Boot

```properties
server.port=8443
server.ssl.enabled=true
server.ssl.key-store=classpath:keystore.p12
server.ssl.key-store-password=password
server.ssl.key-store-type=PKCS12
server.ssl.key-alias=myapp
```

---

### 📌 Real-world Example

- Banking application
    
- Payment gateways
    
- Authentication services
    

---

# 8️⃣ Generate Self-Signed TLS Certificate

### 🔹 Use `keytool` (JDK tool)

```bash
keytool -genkeypair -alias myapp \
  -keyalg RSA \
  -keysize 2048 \
  -storetype PKCS12 \
  -keystore keystore.p12 \
  -validity 365
```

Move `keystore.p12` to:

```
src/main/resources
```

---

### 🧠 Interview Tip

> ❓ _“How do you enable HTTPS locally?”_  
> ✔ Using self-signed certificate + SSL config

---

# 9️⃣ Changing Context Path of Application

### 🔹 Default

```text
http://localhost:8080/
```

### 🔹 Custom Context Path

```properties
server.servlet.context-path=/api
```

### 🔹 Result

```text
http://localhost:8080/api/users
```

---

### 📌 Real-world Example

- Multiple applications on same domain
    
- Versioned APIs
    

---

# 🔟 Logging Configuration

### 🔹 Default Logging

- Framework: Logback
    
- Level: INFO
    

---

### 🔹 Change Logging Level

```properties
logging.level.root=INFO
logging.level.com.example=DEBUG
logging.level.org.springframework.web=ERROR
```

---

### 📌 Real-world Use

- DEBUG in dev
    
- INFO/WARN in production
    

---

### 🧠 Interview Tip

> ❓ _“How do you reduce log noise in production?”_  
> ✔ Increase logging level to WARN or ERROR

---

# 1️⃣1️⃣ @ConfigurationProperties Annotation (VERY IMPORTANT)

### 🔹 What is it?

> Used to **bind external configuration properties to a Java class**.

---

### 🔹 Example Use Case

Configuration for payment gateway.

#### application.properties

```properties
payment.url=https://pay.example.com
payment.timeout=5000
payment.currency=USD
```

---

#### Configuration Class

```java
@Component
@ConfigurationProperties(prefix = "payment")
public class PaymentProperties {

    private String url;
    private int timeout;
    private String currency;

    // getters and setters
}
```

---

### 🔹 Why Use It?

- Type-safe configuration
    
- Cleaner code
    
- Centralized configs
    

---

### 🧠 Interview Tip

> ❓ _“Difference between @Value and @ConfigurationProperties?”_  
> ✔ `@Value` → single value  
> ✔ `@ConfigurationProperties` → group of related properties

---

# 1️⃣2️⃣ Full Real-World Practical Scenario

## 🎯 Scenario

You’re building a **production-ready REST API** with:

- Custom port
    
- HTTPS
    
- Logging control
    
- Externalized configs
    

---

### 🔹 application.properties

```properties
server.port=8443
server.servlet.context-path=/api

logging.level.root=INFO

spring.datasource.url=jdbc:mysql://localhost:3306/appdb
spring.datasource.username=appuser
spring.datasource.password=secret
```

---

### 🔹 Result

- Secure HTTPS app
    
- Clean logs
    
- Environment-based configuration
    

---

# 1️⃣3️⃣ Things You MUST Memorize (INTERVIEW CORE)

- `application.properties` = external config
    
- Property precedence order
    
- Default port = 8080
    
- SSL via keystore
    
- Context path configuration
    
- Logging control via properties
    
- `@ConfigurationProperties` for grouped configs
    

---

# 🧠 Final Mental Summary

> **Properties → Behavior → Environment-specific configuration**
