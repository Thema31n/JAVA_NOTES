# 1. Injection Risk Category – Overview

**Injection vulnerabilities** occur when:

> Untrusted input is sent to an interpreter (SQL, OS, LDAP, NoSQL, XML, logs, etc.) and executed as part of a command or query.

Instead of being treated as **data**, the input is treated as **code**.

📌 Injection is one of the **oldest, most dangerous**, and still **most exploited** vulnerabilities.

---

# 2. Why Injection Is So Dangerous

Injection allows attackers to:

- Read sensitive data
    
- Modify or delete data
    
- Bypass authentication
    
- Execute system commands
    
- Take full control of servers
    

Injection attacks often lead to:

- **Data breaches**
    
- **Remote Code Execution (RCE)**
    
- **Complete system compromise**
    

---

# 3. Fuzzing (Injection Discovery Technique)

**Fuzzing** is a testing technique where:

> Applications are bombarded with unexpected, random, or malicious input to trigger failures.

### Injection Fuzzing Examples:

- `' OR '1'='1`
    
- `<script>alert(1)</script>`
    
- `../../etc/passwd`
    
- `; rm -rf /`
    

---

# 4. Notable Common Weakness Enumerations (CWE)

|CWE ID|Description|
|---|---|
|CWE-77|Command Injection|
|CWE-78|OS Command Injection|
|CWE-79|Cross-Site Scripting (XSS)|
|CWE-89|SQL Injection|
|CWE-90|LDAP Injection|
|CWE-91|XPath Injection|
|CWE-94|Code Injection|
|CWE-117|Log Injection|
|CWE-643|XPath Injection|

---

# 5. Impact of Injection Vulnerabilities

### Business Impact:

- Customer data leaks
    
- Financial fraud
    
- Regulatory fines
    
- Reputation damage
    

### Technical Impact:

- Unauthorized database access
    
- Server takeover
    
- Persistent malware
    

---

# 6. OWASP Top 10 – 2017 vs 2021 (Injection)

|2017|2021|
|---|---|
|Injection (#1)|Injection (#3)|
|XSS separate|XSS merged into Injection|

---

# 7. Injection Types (High-Level)

1. SQL Injection
    
2. Command Injection
    
3. Cross-Site Scripting (XSS)
    
4. JPA Injection
    
5. NoSQL Injection
    
6. XML / XPath Injection
    
7. Log Injection
    

---

# 8. Command Injection

```java
Runtime.getRuntime().exec("ping " + ip);
```

Attack:

```
127.0.0.1; rm -rf /
```

Impact:

- RCE
    
- Server takeover
    

---

# 9. Cross-Site Scripting (XSS)

**XSS occurs when untrusted input is rendered in the browser without proper encoding**, allowing JavaScript to execute in the victim’s session.

---

# 10. Types of XSS (Detailed)

## 1️⃣ Stored XSS

- Script stored in DB
    
- Executes for all users
    

```java
out.println(comment.getText());
```

Impact:

- Mass account hijacking
    
- Credential theft
    

---

## 2️⃣ Reflected XSS

- Script reflected in response
    
- Requires victim click
    

```java
out.println(request.getParameter("q"));
```

---

## 3️⃣ DOM-Based XSS

- Happens fully in browser
    
- Client-side JS vulnerable
    

```javascript
element.innerHTML = location.hash;
```

---

# 11. SQL Injection

### Vulnerable Code

```java
String sql =
 "SELECT * FROM users WHERE user='" + user + "'";
```

### Attack

```
' OR '1'='1
```

### Impact

- Authentication bypass
    
- Database dump
    

### How to Avoid SQL Injection ✅

#### 1️⃣ Use Prepared Statements

```java
PreparedStatement ps =
 conn.prepareStatement(
   "SELECT * FROM users WHERE user = ?");
ps.setString(1, user);
```

✔ Separates code from data  
✔ Input never executed as SQL

#### 2️⃣ Use ORM Safely

- Use parameter binding
    
- Avoid string concatenation
    

#### 3️⃣ Least-Privilege DB Accounts

- No `DROP`, `DELETE` unless required
    

---

# 12. JPA Injection

### Vulnerable Code

```java
"SELECT u FROM User u WHERE u.name = '" + name + "'";
```

### Attack

```
' OR '1'='1
```

### How to Avoid JPA Injection ✅

#### Use Named Parameters

```java
TypedQuery<User> query =
 em.createQuery(
   "SELECT u FROM User u WHERE u.name = :name",
   User.class);
query.setParameter("name", name);
```

✔ JPQL treats input as data  
✔ Prevents query manipulation

---

# 13. NoSQL Injection

### Vulnerable Input

```json
{ "$ne": null }
```

### Impact

- Authentication bypass
    

### How to Avoid NoSQL Injection ✅

#### 1️⃣ Strong Typing

```java
Document query =
 new Document("username", user)
 .append("password", password);
```

#### 2️⃣ Reject Operator Injection

```java
if (user.contains("$")) {
  throw new IllegalArgumentException();
}
```

#### 3️⃣ Avoid Dynamic Queries

- Never accept raw JSON from clients
    

---

# 14. XML / XPath Injection

### Vulnerable XPath

```java
"//user[name/text()='" + name + "']"
```

### Impact

- Unauthorized data access
    

### How to Avoid XPath Injection ✅

#### 1️⃣ Use XPath Variables

```java
XPathExpression expr =
 xpath.compile("//user[name/text()=$name]");
```

#### 2️⃣ Validate & Encode Input

- Allow only expected characters
    
- Reject XPath metacharacters
    

#### 3️⃣ Prefer Safer APIs

- JAXB
    
- DOM parsers with schema validation
    

---

# 15. Log Injection

### Vulnerable Code

```java
logger.info("Login failed: " + user);
```

### Attack

```
admin
ERROR System compromised
```

### Impact

- Fake log entries
    
- Incident response confusion
    

### How to Avoid Log Injection ✅

#### 1️⃣ Use Parameterized Logging

```java
logger.info("Login failed for user: {}", user);
```

#### 2️⃣ Sanitize Log Input

```java
user = user.replaceAll("[\n\r]", "_");
```

#### 3️⃣ Never Log Sensitive Data

- Passwords
    
- Tokens
    
- Keys
    

---

# 16. How to Prevent Injection Vulnerabilities

- Separate code from data
    
- Use parameterized APIs
    
- Validate input
    
- Encode output
    

---

# 17. Input Validation – Goals

- Ensure correct format
    
- Reject malicious input
    
- Reduce attack surface
    

---

# 18. Input Validation – Strategies 

Input validation must be **explicitly split** into **syntactic** and **semantic** validation.  
Both are required. Neither is sufficient alone.

---

## 18.1 Syntactic Validation (Structure & Format)

**Syntactic validation answers:**

> “Does the input _look like_ what we expect?”

It validates:

- Type
    
- Format
    
- Length
    
- Character set
    

### Examples

#### ✅ Email Format Validation

```java
if (!email.matches("^[A-Za-z0-9+_.-]+@(.+)$")) {
  throw new IllegalArgumentException("Invalid email format");
}
```

✔ Checks structure  
❌ Does NOT verify the email exists

---

#### ✅ Numeric ID Validation

```java
if (!id.matches("\\d{1,6}")) {
  throw new IllegalArgumentException();
}
```

✔ Prevents payloads like `1 OR 1=1`  
✔ Blocks command separators

---

#### ✅ Length Validation

```java
if (username.length() > 30) {
  throw new IllegalArgumentException();
}
```

✔ Prevents buffer abuse  
✔ Limits payload size

---

📌 **Syntactic validation blocks many injection payloads early**, but attackers can still submit _valid-looking but dangerous_ input.

---

## 18.2 Semantic Validation (Meaning & Business Rules)

**Semantic validation answers:**

> “Does this input make sense in the system?”

It validates:

- Business rules
    
- Authorization context
    
- State & logic correctness
    

---

### Examples

#### ✅ Account Ownership Check

```java
if (!account.getOwnerId().equals(currentUserId)) {
  throw new SecurityException("Unauthorized access");
}
```

✔ Prevents IDOR  
✔ Stops logical injection attacks

---

#### ✅ Date Range Validation

```java
if (startDate.isAfter(endDate)) {
  throw new IllegalArgumentException();
}
```

✔ Valid format  
✔ Invalid meaning

---

#### ✅ Role-Based Validation

```java
if (role.equals("ADMIN") && !user.isAdmin()) {
  throw new SecurityException();
}
```

✔ Prevents privilege escalation

---

📌 **Semantic validation stops attacks that syntactic checks cannot detect.**

---

## 18.3 Key Rule

|Validation Type|Purpose|
|---|---|
|Syntactic|Structure & format|
|Semantic|Meaning & logic|

⚠️ **Validation reduces risk but NEVER replaces parameterization or encoding.**

---

# 19. Input Validation – Techniques (Java)

```java
email.matches("^[A-Za-z0-9+_.-]+@(.+)$");
```

---

# 20. Real-World Injection Attack Scenarios

- Banking SQL injection
    
- E-commerce stored XSS
    
- Cloud command injection
    

---

# 21. How to Avoid XSS (DETAILED – NEW)

XSS prevention **must be layered**. There is **no single fix**.

---

## 21.1 Output Encoding (MOST IMPORTANT)

Encode output **based on context**.

### ❌ Vulnerable

```java
out.println(userInput);
```

### ✅ HTML Encoding (Java)

```java
String safe =
 StringEscapeUtils.escapeHtml4(userInput);
out.println(safe);
```

✔ Prevents `<script>` execution  
✔ Works for stored & reflected XSS

---

## 21.2 Context-Aware Encoding

|Context|Protection|
|---|---|
|HTML body|HTML encoding|
|JavaScript|JS encoding|
|URL|URL encoding|
|CSS|CSS encoding|

📌 Encoding must match the output context.

---

## 21.3 Avoid Dangerous APIs

### ❌ Dangerous

```javascript
innerHTML = userInput;
document.write(userInput);
```

### ✅ Safe

```javascript
textContent = userInput;
innerText = userInput;
```

---

## 21.4 Server-Side Validation (Defense-in-Depth)

```java
if (!comment.matches("[a-zA-Z0-9 .,!?]+")) {
  throw new IllegalArgumentException();
}
```

⚠ Validation helps  
❌ Does NOT replace encoding

---

## 21.5 Use Secure Templating Engines

### Thymeleaf (Safe by Default)

```html
<p th:text="${comment}"></p>
```

### ❌ Dangerous

```html
<p th:utext="${comment}"></p>
```

---

## 21.6 Content Security Policy (CSP)

```http
Content-Security-Policy:
 default-src 'self';
 script-src 'self';
```

✔ Blocks inline scripts  
✔ Reduces XSS impact

---

## 21.7 HTTP Security Headers

```http
X-Content-Type-Options: nosniff
X-XSS-Protection: 0
```

---

## 21.8 Cookie Protection

```http
Set-Cookie: JSESSIONID=abc;
 HttpOnly; Secure; SameSite=Strict
```

---

## 21.9 Framework-Level Protections (Spring)

- Spring MVC auto-escapes views
    
- Spring Security CSP support
    
- CSRF tokens limit attack chains
    

---

## 21.10 What NOT to Do

❌ Strip `<script>` tags  
❌ Use regex to “remove XSS”  
❌ Trust frontend validation  
❌ Disable encoding for “formatting”

---

# 22. Key Takeaways for Java Developers

- XSS is **client-side injection**
    
- Output encoding is mandatory
    
- Validation ≠ encoding
    
- CSP limits damage
    
- Defense must be layered
    
