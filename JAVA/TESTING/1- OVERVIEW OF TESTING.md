# 🧪 **1. What Is Testing?**

**Testing** is the process of evaluating software to find defects, verify that it behaves correctly, and ensure it meets business/technical requirements.

In simple terms:

> Testing = **confirm the software works as expected and does not break.**

Testing is **not only finding bugs**, but also improving confidence, stability, and quality.

---

# ⭐ **2. What Is Quality?**

**Quality** means that software:

- Works correctly
    
- Meets customer expectations
    
- Is maintainable and reliable
    
- Performs well under expected loads
    
- Is secure and stable
    
- Is easy to use and understand
    

High-quality software is:

- Correct
    
- Efficient
    
- Robust
    
- Secure
    
- Scalable
    
- Maintainable
    

Quality is **not an accident**; it is engineered.

---

# 🎯 **3. Why Do We Need to Write Tests?**

We write tests to:

### ✔ Prevent regressions (old features breaking)

### ✔ Validate new features

### ✔ Catch bugs early (cheap stage)

### ✔ Ensure stable releases

### ✔ Increase confidence in changes

### ✔ Document how the code is supposed to behave

### ✔ Improve design (testable code = better architecture)

Without tests, the cost of change becomes very high.

---

# ⚙️ **4. Function of Testing (Primary Purpose)**

Testing ensures:

### ✔ Verification

Confirm software meets its specifications.

### ✔ Validation

Confirm it meets user needs (value).

### ✔ Detection

Find faults, defects, inconsistencies.

### ✔ Prevention

Avoid defects from entering production.

### ✔ Measurement

Assess quality, performance, usability, etc.

### ✔ Confidence

Gives stakeholders trust in the system.

---

# 🧭 **5. Types of Testing**

Testing is broad. The main categories include:

### ## **A. Based on scope**

- **Unit testing**
    
- **Integration testing**
    
- **System testing**
    
- **End-to-end testing**
    

### ## **B. Based on purpose**

- **Functional testing**
    
- **Non-functional testing**
    

### ## **C. Based on execution**

- **Manual testing**
    
- **Automated testing**
    

### ## **D. Other common types**

- **Regression testing**
    
- **Smoke testing**
    
- **Sanity testing**
    
- **Acceptance testing**
    
- **Load/Stress testing**
    
- **Security testing**
    
- **Compatibility testing**
    

---

# 🏗 **6. Testing Pyramid (Very Important)**

The Testing Pyramid is a guideline for **test distribution**.

```
        UI / E2E Tests  (slow, expensive)
        -------------------------------
        Integration Tests
        -------------------------------
        Unit Tests        (fast, cheap)
```

### ✔ Bottom (Unit Tests)

- Fast, cheap
    
- Written by developers
    
- Should be the majority (50–70%)
    

### ✔ Middle (Integration Tests)

- Verify how modules work together
    
- Medium speed and cost
    

### ✔ Top (UI/E2E Tests)

- Test the entire system through the user interface
    
- Slow, brittle, expensive
    
- Should be limited
    

The pyramid reflects:

- Cost vs. benefit
    
- Speed vs. stability
    

---

# 💸 **7. Defect Cost Inference**

The later a defect is found, the more expensive it is.

|Stage|Relative Cost|
|---|---|
|Code/Unit level|$1|
|Integration testing|$10|
|System testing|$100|
|Production|$1,000+|

Finding bugs early saves huge amounts of money.

This is why automated tests are essential—they catch problems **before** integration and release.

---

# 📦 **8. Functional vs Non-Functional Requirements**

### **Functional Requirements (WHAT system does)**

- Business logic
    
- Features
    
- API behavior
    
- User interactions
    
- Data processing
    

Example:  
“User can reset their password.”

---

### **Non-Functional Requirements (HOW system behaves)**

- Performance
    
- Security
    
- Scalability
    
- Reliability
    
- Usability
    
- Maintainability
    
- Compliance
    

Example:  
“System must handle 500 requests per second.”

---

# 🔍 **9. Functional vs Non-Functional Testing**

### ✔ **Functional Testing**

Validates _what_ the system does.

Types:

- Unit tests
    
- Integration tests
    
- Acceptance tests
    
- API tests
    
- End-to-end tests
    

Tests:

- Inputs/Outputs
    
- User flows
    
- Business rules
    

---

### ✔ **Non-Functional Testing**

Validates _how_ the system behaves.

Types:

- Load testing
    
- Stress testing
    
- Performance
    
- Security
    
- Reliability
    
- Compatibility
    

Tests:

- Speed
    
- Failover
    
- Responsiveness
    
- Resource usage
    

---

# 👨‍💻 **10. What Kind of Tests Do Developers Usually Write?**

Developers commonly write:

### ✔ Unit Tests (MOST COMMON)

Using:

- JUnit
    
- Mockito
    
- AssertJ
    

Purpose:

- Test small pieces of logic
    
- Fast feedback
    

---

### ✔ Integration Tests

Examples:

- Testing database + repository
    
- Testing controllers + services
    
- Testing API endpoints
    

Tools:

- Spring Boot Test
    
- Testcontainers
    
- WireMock
    

---

### ✔ Component/Service Tests

Testing each service or module separately.

---

### ✔ API Contract Tests

Using:

- REST Assured
    
- MockMvc
    

---

### ✔ Regression Tests

Added when fixing bugs.

---

### ✔ Sometimes: End-to-End Tests

But developers write fewer of these because they are:

- Slow
    
- Fragile
    
- Hard to maintain
    

Usually done with QA/automation teams.

---

# 📌 **11. Summary 

### ✔ What is testing?

Evaluating software to ensure correctness, quality, and reliability.

### ✔ What is quality?

Software that meets expectations, works reliably, and is maintainable.

### ✔ Why write tests?

Fewer bugs, faster development, safer changes, better design.

### ✔ Functions of testing

Verification, validation, detection, prevention, confidence.

### ✔ Types of testing

Unit, integration, system, E2E, functional, non-functional.

### ✔ Testing pyramid

Unit tests → majority  
Integration → balanced  
E2E → minimal

### ✔ Defect cost inference

The later a bug is found, the more expensive it becomes.

### ✔ Functional vs Non-functional requirements

WHAT system does vs HOW it behaves.

### ✔ Functional vs Non-functional testing

Business logic vs behavior qualities (performance/security/etc).

### ✔ What do developers write?

Unit tests, integration tests, regression tests, API tests.
