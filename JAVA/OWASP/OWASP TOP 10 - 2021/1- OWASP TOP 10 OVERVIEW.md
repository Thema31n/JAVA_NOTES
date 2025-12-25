# 1. What is OWASP?

**OWASP (Open Worldwide Application Security Project)** is a **non-profit organization** that focuses on improving **software security**, especially **web applications**.

### OWASP provides:

- Free security tools
    
- Documentation and standards
    
- Training and awareness
    
- The famous **OWASP Top 10** list
    

### Why OWASP matters for Java developers

Java is widely used in:

- Banking systems
    
- Enterprise web apps (Spring, Jakarta EE)
    
- APIs and microservices
    

This makes Java apps a **prime target** for attackers.

---

# 2. What is OWASP Top 10?

**OWASP Top 10** is a list of the **10 most critical web application security risks**.

### Purpose:

- Help developers understand common security flaws
    
- Guide secure coding practices
    
- Serve as a security baseline for organizations
    

👉 It is **updated every few years** based on real attack data.

---

# 3. Why is OWASP Important?

### Business impact:

- Prevents **data breaches**
    
- Avoids **financial loss**
    
- Protects **user trust**
    
- Helps with **compliance** (PCI-DSS, ISO, GDPR)
    

### Technical importance:

- Identifies **real-world attack patterns**
    
- Helps developers write **secure Java code**
    
- Used in **penetration testing & audits**
    

📌 Many real breaches happened due to OWASP Top 10 issues.

---

# 4. OWASP Top 10 – 2021 (Detailed)

## A01: Broken Access Control

**Problem:** Users can access resources they shouldn’t.

### Java Example:

```java
// Missing role check
@GetMapping("/admin")
public String adminPanel() {
    return "admin data";
}
```

### Real-world example:

- A user changes `/user/123` to `/user/124` and views another user’s data.
    

### Fix:

```java
@PreAuthorize("hasRole('ADMIN')")
```

---

## A02: Cryptographic Failures

**Problem:** Weak or missing encryption.

### Java Example:

```java
MessageDigest md = MessageDigest.getInstance("MD5");
```

❌ MD5 is broken.

### Real-world example:

- Stolen database passwords cracked in minutes.
    

### Fix:

- Use `BCrypt`, `PBKDF2`, `Argon2`
    
- Use HTTPS everywhere
    

---

## A03: Injection

**Problem:** Untrusted input executed as code.

### Java SQL Injection:

```java
String query = "SELECT * FROM users WHERE name = '" + name + "'";
```

### Attack:

```sql
' OR '1'='1
```

### Fix:

```java
PreparedStatement ps = conn.prepareStatement(
  "SELECT * FROM users WHERE name = ?");
ps.setString(1, name);
```

---

## A04: Insecure Design

**Problem:** Application logic itself is insecure.

### Example:

- Password reset without expiry
    
- No rate limiting on login
    

### Real-world:

- Unlimited OTP attempts → account takeover
    

---

## A05: Security Misconfiguration

**Problem:** Unsafe default settings.

### Java Example:

- Stack traces shown in production
    
- Default admin/admin credentials
    

### Fix:

- Disable debug mode
    
- Secure headers
    
- Harden server configs
    

---

## A06: Vulnerable and Outdated Components

**Problem:** Using libraries with known vulnerabilities.

### Java Example:

- Old Log4j → **Log4Shell (CVE-2021-44228)**
    

### Real-world:

- Remote code execution in thousands of systems
    

### Fix:

- Dependency scanning (OWASP Dependency-Check)
    
- Regular updates
    

---

## A07: Identification and Authentication Failures

**Problem:** Weak authentication mechanisms.

### Example:

- No account lockout
    
- Weak passwords
    

### Fix:

- MFA
    
- Secure session management
    
- Strong password policies
    

---

## A08: Software and Data Integrity Failures

**Problem:** Untrusted updates or deserialization.

### Java Example:

```java
ObjectInputStream ois = new ObjectInputStream(inputStream);
Object obj = ois.readObject(); // Dangerous
```

### Real-world:

- Malicious serialized objects → RCE
    

---

## A09: Security Logging and Monitoring Failures

**Problem:** Attacks go undetected.

### Example:

- No logging for failed logins
    

### Fix:

- Centralized logging
    
- Alerts and monitoring
    

---

## A10: Server-Side Request Forgery (SSRF)

**Problem:** Server makes unintended internal requests.

### Example:

```java
URL url = new URL(userInput);
```

### Attack:

- Access internal services (AWS metadata)
    

---

# 5. What is CWE (Common Weakness Enumeration)?

**CWE** is a **list of software weakness types**, not specific vulnerabilities.

### Examples:

|CWE ID|Description|
|---|---|
|CWE-79|Cross-Site Scripting|
|CWE-89|SQL Injection|
|CWE-287|Improper Authentication|

📌 OWASP Top 10 items are **mapped to CWE categories**.

---

# 6. What is CVE (Common Vulnerabilities and Exposures)?

**CVE** identifies **specific, real vulnerabilities** in software.

### Example:

- **CVE-2021-44228** → Log4Shell
    

### Difference:

|CWE|CVE|
|---|---|
|General weakness|Specific vulnerability|
|Design issue|Actual bug|
|Reusable|One-time|

---

# 7. What is CVSS (Common Vulnerability Scoring System)?

**CVSS** measures **severity** of a vulnerability.

### Score Range:

- **0.0 – 3.9** → Low
    
- **4.0 – 6.9** → Medium
    
- **7.0 – 8.9** → High
    
- **9.0 – 10.0** → Critical
    

### Example:

- Log4Shell → **CVSS 10.0 (Critical)**
    

---

# 8. OWASP Top 10: 2017 vs 2021 Comparison

|2017|2021|
|---|---|
|Injection (#1)|Broken Access Control (#1)|
|Broken Auth|Identification & Auth Failures|
|Sensitive Data Exposure|Cryptographic Failures|
|XXE|Removed|
|Insecure Deserialization|Merged into A08|
|Security Misconfiguration|Security Misconfiguration|
|XSS|Part of Injection|
|Using Vulnerable Components|Vulnerable & Outdated Components|
|Insufficient Logging|Logging & Monitoring Failures|
|—|SSRF (New)|

### Key Changes:

- Focus shifted from **input attacks** to **access control & design**
    
- More emphasis on **architecture & supply chain security**
    

---

# 9. Real-World Java Security Breach Examples

### 🔥 Log4Shell (Java)

- A06: Vulnerable Components
    
- RCE via logging
    
- Affected Minecraft, AWS, enterprise apps
    

### 💳 Banking App IDOR

- A01: Broken Access Control
    
- Users accessed other accounts by changing IDs
    

### 🛒 E-commerce SQL Injection

- A03: Injection
    
- Full database dumped
    

---

# 10. Why This Matters for Java Developers

As a Java developer, OWASP helps you:

- Write **secure Spring Boot apps**
    
- Protect APIs
    
- Pass **security audits**
    
- Avoid **career-ending bugs**
    
