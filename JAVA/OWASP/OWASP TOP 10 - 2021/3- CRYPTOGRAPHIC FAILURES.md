# 1. Cryptographic Failures – Overview

**Cryptographic failures** occur when:

> An application fails to correctly protect data using cryptography.

This includes:

- No encryption
    
- Weak algorithms
    
- Incorrect use of crypto APIs
    
- Broken TLS configuration
    
- Insecure password storage
    

📌 In **OWASP Top 10 2021**, this category replaced **Sensitive Data Exposure** to focus on **why data is exposed**, not just that it was.

---

# 2. Most Common Root Causes

1. Data sent over **HTTP instead of HTTPS**
    
2. Weak hashing (MD5, SHA-1)
    
3. Hardcoded secrets or keys
    
4. Missing encryption at rest
    
5. Misconfigured TLS
    
6. Skipped certificate validation
    
7. Developer-written crypto
    
8. Excessive storage of sensitive data
    

---

# 3. OWASP Top 10 – 2017 vs 2021 (Comparative Analysis)

|2017|2021|
|---|---|
|Sensitive Data Exposure|Cryptographic Failures|
|Focus on leaked data|Focus on crypto misuse|
|Outcome-based|Root-cause–based|

**Key idea**

> Data exposure is the result — cryptographic failure is the cause.

---

# 4. Notable CWE (Common Weakness Enumerations)

|CWE|Description|
|---|---|
|CWE-319|Cleartext transmission|
|CWE-326|Weak encryption|
|CWE-327|Broken crypto algorithm|
|CWE-328|Reversible hash|
|CWE-522|Weak password hashing|
|CWE-295|Improper cert validation|
|CWE-311|Missing encryption|

---

# 5. Types of Cryptographic Failures (CIA Triad)

## 1️⃣ Confidentiality Breach

Unauthorized disclosure of data.

### Example (Java – HTTP login)

```java
POST /login
username=admin&password=123456
```

### Attack

- Attacker sniffs traffic on public Wi-Fi
    
- Credentials stolen
    

---

## 2️⃣ Integrity Failure

Data is modified without detection.

### Example (JWT without signature validation)

```json
{
  "user": "john",
  "role": "ADMIN"
}
```

---

## 3️⃣ Availability Failure

Loss of access to encrypted data or services.

### Example

- Encryption keys lost
    
- TLS certificate expired
    

---

# 6. Personal Data vs Sensitive Data

## Personal Data

Identifies a person:

- Name
    
- Email
    
- Phone
    
- IP address
    

## Sensitive Data

Causes serious harm if exposed:

- Passwords
    
- Credit cards
    
- Medical data
    
- Government IDs
    
- Tokens & keys
    

📌 Sensitive data **must always be cryptographically protected**

---

# 7. Types of Sensitive Data

- Authentication data
    
- Financial data
    
- Health records
    
- Biometrics
    
- Cryptographic keys
    
- Session tokens
    

---

# 8. Cryptographic Failure vs Data Breach

|Cryptographic Failure|Data Breach|
|---|---|
|Developer error|Attacker outcome|
|Preventable|Often detected late|

---

# 9. What Leads to Cryptographic Failures?

- Poor security knowledge
    
- Legacy systems
    
- Performance shortcuts
    
- Copy-paste crypto code
    
- No security reviews
    

---

# 10. SQL Injection (What It Is + Java Example)

## What is SQL Injection?

An attack where **untrusted input is executed as SQL**.

## Vulnerable Java Code

```java
String sql =
  "SELECT * FROM users WHERE username='" + user +
  "' AND password='" + pass + "'";
```

## Attack Input

```
' OR '1'='1
```

---

# 11. TLS & SSL (What They Are)

- **SSL** → obsolete
    
- **TLS** → secure transport encryption
    

---

# 12. HTTPS (What It Does)

HTTPS = HTTP + TLS

Provides:

- Confidentiality
    
- Integrity
    
- Server authentication
    

---

# 13. Enabling HTTPS on Apache Tomcat

```bash
keytool -genkeypair -alias tomcat -keyalg RSA -keystore keystore.jks
```

```xml
<Connector
  port="8443"
  SSLEnabled="true"
  keystoreFile="keystore.jks"
  keystorePass="changeit" />
```

---

# 14. Password Encryption (Wrong Approach)

```java
MessageDigest.getInstance("MD5"); // ❌ insecure
```

---

# 15. Password Hashing (Correct Approach)

Passwords must be:

- **Hashed**
    
- **Salted**
    
- **Slow**
    

---

# 16. Hashing Algorithms Comparison

|Algorithm|Status|
|---|---|
|MD5|❌ Broken|
|SHA-1|❌ Broken|
|SHA-256|⚠ Too fast|
|PBKDF2|✅ Secure|
|BCrypt|✅ Recommended|
|SCrypt|✅ Memory-hard|

---

# 17. Password Hashing & Validation – Java Code Examples (NEW)

## ❌ MD5 (Broken – Do NOT Use)

```java
// Fast and broken – vulnerable to rainbow tables
MessageDigest md = MessageDigest.getInstance("MD5");
byte[] hash = md.digest(password.getBytes());
```

---

## ❌ SHA-256 (Still NOT Recommended for Passwords)

```java
// Cryptographically strong, but too fast for passwords
MessageDigest md = MessageDigest.getInstance("SHA-256");
byte[] hash = md.digest(password.getBytes());
```

---

## ✅ PBKDF2 (Recommended)

```java
// Uses iterations to slow down brute-force attacks
PBEKeySpec spec = new PBEKeySpec(
    password.toCharArray(),
    salt,           // random salt
    65536,          // iterations
    256             // key length
);

SecretKeyFactory skf =
    SecretKeyFactory.getInstance("PBKDF2WithHmacSHA256");

byte[] hash = skf.generateSecret(spec).getEncoded();
```

✔ Salted  
✔ Iterative  
✔ Secure

---

## ✅ BCrypt (Best Practical Choice)

```java
// Automatically handles salt and cost factor
BCryptPasswordEncoder encoder =
    new BCryptPasswordEncoder();

String hash = encoder.encode(password);
```

### Password Validation

```java
// Compares raw password with stored hash
boolean matches =
    encoder.matches(password, storedHash);
```

✔ Auto-salted  
✔ Slow  
✔ GPU-resistant

---

## ✅ SCrypt (High-Security Systems)

```java
// Memory-hard hashing – very resistant to GPU attacks
SCryptPasswordEncoder encoder =
    new SCryptPasswordEncoder();

String hash = encoder.encode(password);
boolean valid = encoder.matches(password, hash);
```

✔ Strong against ASIC/GPU  
✔ Higher resource cost

---

# 18. Example Real-World Attack Scenarios

### 🏦 Banking App

- HTTP login
    
- Credentials sniffed
    
- Account takeover
    

### 🛒 E-commerce

- SQL injection
    
- MD5 hashes stolen
    
- Password cracking
    

### ☁ Cloud API

- TLS cert validation disabled
    
- MITM attack
    
- Token theft
    

---

# 19. How to Prevent Cryptographic Failures

## Design

- Classify data
    
- Minimize sensitive data
    
- Secure by default
    

## Implementation

- HTTPS everywhere
    
- Strong password hashing
    
- Never write crypto yourself
    
- Secure key storage
    

## Operations

- Rotate keys
    
- Patch dependencies
    
- Monitor CVEs
    
- Security testing
    

---

# 20. Key Takeaways for Java Developers

- Cryptographic failures break **confidentiality, integrity, availability**
    
- SQL injection + weak crypto = disaster
    
- Passwords are **hashed, not encrypted**
    
- BCrypt / PBKDF2 / SCrypt only
    
- HTTPS is mandatory
    
