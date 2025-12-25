# 1. What Is Access Control?

**Access control** is the mechanism that decides:

> **WHO** can access **WHAT** resource and **WHAT ACTIONS** they can perform.

It answers questions like:

- Can this user read this data?
    
- Can they modify it?
    
- Can they access this endpoint?
    

### In Java Web Apps

Access control is usually enforced:

- At **API endpoints**
    
- On **business logic**
    
- At **data access level**
    

Example:

```java
User A → read own profile ❌ read admin data
```

---

# 2. Authentication vs Authorization

|Authentication|Authorization|
|---|---|
|Who are you?|What are you allowed to do?|
|Login process|Permission checking|
|Username/password|Roles, scopes, privileges|
|Happens first|Happens after authentication|

### Example:

- Authentication: “I am Ahmed”
    
- Authorization: “Ahmed can access `/orders` but not `/admin`”
    

📌 **Broken access control is an authorization failure, not authentication**

---

# 3. Types of Access Control

## 1️⃣ Vertical Access Control

Controls **access between different privilege levels**.

### Example:

- USER → view products
    
- ADMIN → delete users
    

### Vulnerability:

A normal user accesses admin functionality.

```http
POST /admin/deleteUser
```

### Real-world:

- User manually accesses admin endpoints via Postman
    

---

## 2️⃣ Horizontal Access Control

Controls **access between users of the same role**.

### Example:

- User A should not access User B’s data
    

```http
GET /orders/1001
```

User changes ID:

```http
GET /orders/1002
```

✔ Same role  
❌ Different owner

This is the most common form of **IDOR**.

---

## 3️⃣ Context-Dependent Access Control

Access depends on **state, time, location, or ownership**.

### Examples:

- User can edit order **only before shipment**
    
- Employee can access system **only during work hours**
    
- Bank transfer allowed **only if balance is sufficient**
    

### Real-world:

- Refund API callable even after refund deadline → fraud
    

---

# 4. OAuth Overview (Authorization Delegation)

**OAuth 2.0** allows:

> An app to access a resource **on behalf of a user** without sharing credentials.

### Real-world example:

- Login with Google
    
- GitHub accessing your repos
    

### OAuth Roles:

- **Resource Owner** → user
    
- **Client** → app
    
- **Authorization Server** → Google
    
- **Resource Server** → API
    

📌 OAuth is about:  
✔ Authorization  
❌ Not authentication (by itself)

---

# 5. JWT Overview (Token-Based Security)

**JWT (JSON Web Token)** is a **self-contained access token**.

### Structure:

```
HEADER.PAYLOAD.SIGNATURE
```

### Payload Example:

```json
{
  "sub": "123",
  "role": "ADMIN",
  "exp": 1700000000
}
```

### Used for:

- Stateless authentication
    
- API authorization
    

⚠ Risks:

- Trusting token claims blindly
    
- Long expiration times
    
- Missing signature validation
    

---

# 6. What Is Broken Access Control?

**Broken Access Control** happens when:

> Users can access data or perform actions **outside their intended permissions**

It is **OWASP Top 10 #1 (2021)**.

---

# 7. Impact of Broken Access Control

### Business Impact:

- Data breaches
    
- Financial fraud
    
- Regulatory fines
    
- Loss of customer trust
    

### Technical Impact:

- Account takeover
    
- Privilege escalation
    
- Full system compromise
    

---

# 8. Insecure ID Vulnerability (IDOR)

**IDOR (Insecure Direct Object Reference)** occurs when:

> Object IDs are exposed without authorization checks.

### Vulnerable Example:

```http
GET /users/123/profile
```

Change ID:

```http
GET /users/124/profile
```

### Real-world:

- Banking apps exposing account details
    
- Healthcare apps leaking patient records
    

### Fix:

```java
if (!resource.getOwnerId().equals(currentUserId)) {
    throw new AccessDeniedException();
}
```

---

# 9. Path Traversal Vulnerability

Occurs when user input controls file paths.

### Vulnerable Code:

```java
new File("/uploads/" + filename);
```

### Attack:

```
../../etc/passwd
```

### Impact:

- Read sensitive system files
    
- Credential leakage
    

### Prevention:

- Normalize paths
    
- Restrict directories
    
- Use safelisting
    

---

# 10. Poison Null Bytes Attack

Used to **truncate strings** in older systems.

### Example:

```
image.jpg%00.exe
```

### Effect:

- Validation passes
    
- Execution uses full filename
    

### Impact:

- Malicious file uploads
    
- Filter bypass
    

📌 Mostly mitigated today, but legacy systems still vulnerable.

---

# 11. Safelisting (Allowlisting)

Safelisting means:

> Allow only explicitly trusted values.

### Weak (Blocklisting):

```java
if (!input.contains("../"))
```

### Strong (Safelisting):

```java
if (!allowedFiles.contains(input)) {
    deny();
}
```

✔ Predictable  
✔ Secure  
✔ Recommended by OWASP

---

# 12. Client-Side Caching Vulnerability

Sensitive data cached in:

- Browser
    
- Shared machines
    

### Example:

- Logout → press Back → sensitive page visible
    

### Fix:

```http
Cache-Control: no-store, no-cache
```

---

# 13. Violation of the Principle of Least Privilege

**Least Privilege** means:

> Give the **minimum permissions necessary**.

### Violations:

- All users are admins
    
- DB user has full privileges
    

### Impact:

- Small bug → massive breach
    

---

# 14. Elevation of Privilege

Occurs when:

> User gains higher permissions than intended.

### Examples:

- JWT role tampering
    
- Accessing undocumented admin APIs
    

### Real-world:

- Normal user becomes admin
    
- Complete system takeover
    

---

# 15. Review Roles Management Approach

### Bad Practices:

- UI-only checks
    
- Hardcoded roles
    
- No ownership validation
    

### Good Practices:

- Centralized authorization
    
- Backend enforcement
    
- Policy-based checks
    
- Regular reviews
    

---

# 16. How to Prevent Broken Access Control

## Design-Level

- Secure-by-default
    
- Deny by default
    
- Threat modeling
    
- Clear access rules
    

## Implementation

- Server-side enforcement
    
- Ownership checks
    
- Use frameworks (Spring Security)
    
- Log authorization failures
    

## Testing

- Manual authorization testing
    
- Automated security tests
    
- Penetration testing
    

---

# 17. Real-World Attack Scenarios

### 🏦 Banking IDOR

User changes account ID → views another customer’s balance

### 🛒 E-commerce Admin Bypass

Hidden admin endpoint callable by normal users

### ☁ Cloud Privilege Escalation

Weak access control + SSRF → admin credentials exposed

---

# 18. Key Takeaways for Java Developers

- Authentication ≠ Authorization
    
- Never trust client-side checks
    
- Enforce ownership
    
- Apply least privilege
    
- Broken Access Control is **architectural**, not accidental
    
