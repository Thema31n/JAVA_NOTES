# 1. Insecure Design – Overview

**Insecure design** refers to security weaknesses introduced during the **architecture, requirements, or business-logic phase** of a system. These flaws exist **even if the code is written securely**, because the system allows unsafe behavior by design.

### Why Insecure Design Is Dangerous

- Affects the entire system
    
- Often invisible to SAST tools
    
- Hard and expensive to fix late
    

### Real-World Example

An authentication system:

- Returns different error messages for _“user not found”_ and _“wrong password”_
    
- Enables attackers to enumerate valid users
    

This is **CWE-209** and a **design flaw**, not a coding bug.

---

# 2. Insecure Design vs Insecure Implementation

|Aspect|Insecure Design|Insecure Implementation|
|---|---|---|
|Phase|Architecture / Requirements|Coding|
|Scope|System-wide|Local|
|Fix effort|High|Low|
|Example|Trusting client roles|SQL injection|

### Java Example

**Insecure Design**

> “Frontend will decide if a user is admin”

**Insecure Implementation**

```java
String sql = "SELECT * FROM users WHERE id=" + id;
```

---

# 3. Shift Left Security Approach

**Shift Left Security** means moving security activities **earlier in the SDLC**, especially into **design and requirements**.

### Why Shift Left Matters

- Fixing design flaws in production can cost **10–100x more**
    
- Prevents insecure architecture decisions
    

### Java Example

Before implementing login in Spring Boot:

- Decide password hashing
    
- Decide error handling strategy
    
- Decide rate-limiting approach
    

---

# 4. Notable Common Weakness Enumerations (CWEs)

## CWE-209 – Information Exposure Through Error Messages

### Description

Application reveals sensitive internal details via errors.

### Java Example

```java
catch (Exception e) {
    return e.getMessage();
}
```

### Real-World Impact

- Database schema exposure
    
- Framework version leaks
    
- Easier exploitation
    

### Prevention

- Generic error messages
    
- Detailed server-side logging
    

---

## CWE-256 – Plaintext Storage of Passwords

### Description

Passwords stored without hashing.

### Java Example

```java
user.setPassword(password);
```

### Impact

- Database breach = full credential exposure
    
- Password reuse causes lateral breaches
    

### Prevention

```java
BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();
user.setPassword(encoder.encode(password));
```

---

## CWE-501 – Trust Boundary Violation

### Description

Trusting untrusted input across system boundaries.

### Java Example

```java
if ("true".equals(req.getParameter("isAdmin"))) {
    user.setRole("ADMIN");
}
```

### Impact

- Privilege escalation
    
- Full system compromise
    

### Prevention

- Server-side authorization only
    

---

## CWE-522 – Insufficiently Protected Credentials

### Description

Credentials transmitted or handled insecurely.

### Example

Login over HTTP or insecure cookies.

### Prevention

- TLS everywhere
    
- Secure cookies
    
- Token expiration
    

---

# 5. What Is Secure Design?

**Secure design** ensures security is **embedded into system architecture**.

### Secure Design Principles

- Least privilege
    
- Explicit trust boundaries
    
- Secure defaults
    
- Fail securely
    
- Defense in depth
    

### Example

A password reset system:

- One-time tokens
    
- Expiry
    
- Rate limiting
    
- No user enumeration
    

---

# 6. Threat Modeling

**Threat modeling** is a structured approach to:

- Identify threats
    
- Analyze attack paths
    
- Design mitigations
    

### Common Framework

**STRIDE**

---

# 7. Goal of Threat Modeling

- Prevent insecure design
    
- Reduce attack surface
    
- Guide security controls
    
- Save cost and time
    

### Java Example

Threat-modeling a file upload feature:

- Malware upload
    
- Path traversal
    
- ZIP bombs
    

---

# 8. Threat Modeling Manifesto – Overview

The manifesto emphasizes **thinking over tools**.

> Threat modeling is a mindset, not a checklist.

---

# 9. Threat Modeling Manifesto – Values

- Collaboration over silos
    
- Pragmatism over perfection
    
- Continuous improvement
    
- Shared responsibility
    

---

# 10. Threat Modeling Manifesto – Principles

- Start early
    
- Iterate often
    
- Focus on real risks
    
- Keep it lightweight
    

---

# 11. Building a Secure Design Process (Updated & Detailed)

Below are the **updated steps**, fully explained:

### 1. Security Requirements Gathering

Identify security needs based on:

- Data sensitivity
    
- User roles
    
- External integrations
    

### 2. Compliance Requirements Definition

Map legal and regulatory requirements:

- GDPR
    
- PCI-DSS
    
- ISO 27001
    

### 3. Business Impact Analysis (BIA)

Assess impact of compromise:

- Financial loss
    
- Legal penalties
    
- Reputational damage
    

### 4. Convert Security Requirements into Tickets

Track security work as first-class SDLC items.

### 5. Architecture Definition

Design application, feature, or component architecture with trust boundaries.

### 6. Refinement & Estimation

Refine security requirements and estimate effort.

### 7. Threat Modeling Activities

Identify threats using STRIDE or attack trees.

### 8. Threat Register Creation

Document threats, risk, mitigations, and ownership.

### 9. Security Controls Identification

Define:

- Preventive
    
- Detective
    
- Corrective controls
    

### 10. Gap Analysis Execution

Identify gaps between current design and required controls.

### 11. Security Design Document Creation

Document architecture, threats, assumptions, and controls.

---

# 12. Business Impact Analysis (BIA)

BIA connects **technical threats to business risk**.

|Asset|Impact|
|---|---|
|Credentials|Account takeover|
|PII|Regulatory fines|
|Admin access|Full breach|

---

# 13. Working with a Threat Register

A threat register tracks:

- Threat ID
    
- CWE
    
- Risk
    
- Mitigation
    
- Owner
    
- Status
    

---

# 14. Security Controls

|Type|Example|
|---|---|
|Preventive|RBAC, TLS|
|Detective|Logging|
|Corrective|Lockout|

---

# 15. Security Design Document (SDD)

Documents:

- Architecture diagrams
    
- Threat model
    
- Controls
    
- Known risks
    

---

# 16. Secure Design Process Metrics

- Threats found before coding
    
- % mitigated early
    
- Design issues found in production
    

---

# 17. Example Attacks & Prevention

- **User enumeration** → Generic errors
    
- **Password theft** → Hashing
    
- **Privilege escalation** → Server-side checks
    
- **Credential sniffing** → TLS everywhere
    

---

## Final Takeaway for Java Developers

- Insecure design is **more dangerous than insecure code**
    
- CWEs 209, 256, 501, 522 are common design failures
    
- Threat modeling + Shift Left prevents most of them
    
- Secure design starts **before writing code**
    
