# 1️⃣ Tools for API Testing – Overview

### Why API testing tools matter

APIs are:

- Consumed by many clients
    
- Often the backbone of systems
    
- Hard to debug without visibility
    

API testing tools help with:

- Functional testing
    
- Contract validation
    
- Debugging requests & responses
    
- Performance & security checks
    

---

### Common categories of API tools

|Category|Purpose|Examples|
|---|---|---|
|API Clients|Send requests & inspect responses|Postman, Insomnia|
|Network Debuggers|Inspect HTTP traffic|Fiddler, Wireshark|
|SOAP Tools|SOAP-specific testing|SoapUI|
|Automation|CI/CD testing|Newman, JMeter|

---

### Interview tip

Say:

> “Different tools serve different layers: API clients for functionality, sniffers for network visibility.”

---

### Things to memorize

- No single tool fits all needs
    
- Tool choice depends on debugging depth
    

---

# 2️⃣ SOAP (Context for API Testing)

### What is SOAP?

**SOAP (Simple Object Access Protocol)** is a **protocol** for exchanging structured information using **XML**.

---

### SOAP characteristics

- Strong contract (WSDL)
    
- XML-only
    
- Strict standards
    
- Built-in error handling
    

---

### Example SOAP request (simplified)

```xml
<soap:Envelope>
  <soap:Body>
    <getUser>
      <id>42</id>
    </getUser>
  </soap:Body>
</soap:Envelope>
```

---

### Why SOAP matters here

Many enterprise systems:

- Still use SOAP
    
- Require specialized testing tools
    

---

### SOAP testing tools

- SoapUI
    
- Postman (basic support)
    
- Fiddler (transport-level)
    

---

### Interview tip

Say:

> “SOAP is heavier and contract-driven compared to REST.”

---

### Things to memorize

- SOAP = protocol
    
- REST = architectural style
    
- SOAP uses WSDL
    

---

# 3️⃣ Comparative Analysis: Postman vs Fiddler vs Wireshark

### High-level comparison

|Tool|Level|Primary Use|
|---|---|---|
|Postman|Application|API testing|
|Fiddler|HTTP proxy|Debug HTTP traffic|
|Wireshark|Network|Packet analysis|

---

## 3.1 Postman

### What it is

- API development & testing tool
    
- Focused on HTTP APIs
    

### Strengths

- Easy to use
    
- Supports REST & SOAP
    
- Automation & scripting
    
- Environment management
    

### Limitations

- Cannot inspect raw TCP packets
    
- Limited network-level debugging
    

---

## 3.2 Fiddler

### What it is

- HTTP debugging proxy
    
- Sits between client and server
    

### Strengths

- Inspect HTTP/HTTPS traffic
    
- Modify requests & responses
    
- Debug real application traffic
    

### Limitations

- HTTP/S only
    
- Not designed for API test automation
    

---

## 3.3 Wireshark

### What it is

- Network protocol analyzer
    
- Packet-level inspection
    

### Strengths

- Sees everything on the wire
    
- Protocol-agnostic
    
- Deep troubleshooting
    

### Limitations

- Steep learning curve
    
- Not API-friendly
    
- No business-level view
    

---

### Interview comparison answer (strong)

> “Postman tests APIs, Fiddler inspects HTTP traffic, and Wireshark analyzes raw network packets.”

---

### Things to memorize

- Postman → functional testing
    
- Fiddler → HTTP debugging
    
- Wireshark → network debugging
    

---

# 4️⃣ Postman (Deep Dive)

### What is Postman?

Postman is an **API development platform** used to:

- Send HTTP requests
    
- Validate responses
    
- Automate tests
    
- Document APIs
    

---

### Supported protocols

- REST (primary)
    
- SOAP (basic)
    
- GraphQL
    
- WebSockets (limited)
    

---

### Real-world usage

- Manual API testing
    
- Smoke tests
    
- Contract verification
    
- Sharing collections with teams
    

---

### Interview tip

Say:

> “Postman is often the first tool used when developing or consuming APIs.”

---

### Things to memorize

- Postman is not just a REST client
    
- It supports scripting and automation
    

---

# 5️⃣ Key Components of Postman

---

## 5.1 Workspace

### Purpose

- Organize APIs by project or team
    
- Collaboration
    

---

## 5.2 Collections

### What it is

- Group of related API requests
    

### Example

```
User APIs
 ├── GET /users
 ├── POST /users
 └── GET /users/{id}
```

---

## 5.3 Requests

Each request includes:

- Method
    
- URL
    
- Headers
    
- Body
    
- Scripts
    

---

## 5.4 Environments

### Purpose

- Manage variables
    

### Example

```json
{
  "baseUrl": "https://api.dev.company.com"
}
```

Use in request:

```
{{baseUrl}}/users
```

---

## 5.5 Variables

Types:

- Global
    
- Environment
    
- Collection
    
- Local
    

---

### Interview tip

Say:

> “Environments enable the same collection to run across dev, test, and prod.”

---

## 5.6 Tests (Scripts)

### Example

```javascript
pm.test("Status is 200", function () {
  pm.response.to.have.status(200);
});
```

---

### Why important

- Automated validation
    
- CI/CD integration
    

---

## 5.7 Pre-request Scripts

### Purpose

- Prepare request dynamically
    

Example:

```javascript
pm.environment.set("token", "abc123");
```

---

### Things to memorize (Postman components)

- Collection
    
- Environment
    
- Variables
    
- Tests
    
- Pre-request scripts
    

---

# 6️⃣ Postman Desktop vs Web Version

---

## Postman Desktop Version

### Characteristics

- Native application
    
- Direct network access
    
- Better performance
    

### Pros

- Works behind corporate proxies
    
- Full feature set
    
- Can intercept localhost
    

### Cons

- Requires installation
    

---

## Postman Web Version

### Characteristics

- Runs in browser
    
- Requires Postman agent
    

### Pros

- No installation
    
- Easy access anywhere
    

### Cons

- Limited local network access
    
- Depends on agent for full features
    

---

### Interview comparison answer

> “Desktop is preferred for full functionality; web is convenient but limited.”

---

### Things to memorize

- Web version needs an agent
    
- Desktop is more powerful
    

---

## ✅ Final Interview Memorization Summary

- Postman = API testing & automation
    
- Fiddler = HTTP proxy debugger
    
- Wireshark = packet analyzer
    
- SOAP requires specialized tools
    
- Collections organize APIs
    
- Environments enable multi-stage testing
    
- Desktop Postman > Web for debugging
    