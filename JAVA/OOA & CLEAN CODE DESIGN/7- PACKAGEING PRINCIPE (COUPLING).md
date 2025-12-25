# 🧩 **1. Coupling Principle**

**Coupling** describes how much two modules depend on each other.

> **Low coupling = good**  
> **High coupling = bad**

### ✔ What is a good coupling level?

- Modules depend only on what they truly need
    
- Modules can change independently
    
- Modules expose minimal surface area
    
- Interfaces abstract away implementation details
    

### ❌ What is bad coupling?

- A module knows too much about others' internals
    
- Changes ripple across many modules
    
- Hard to test because of many dependencies
    
- Must modify many classes to add one feature
    

---

## **Types of Coupling (from worst → best)**

1. **Content Coupling** – one module modifies contents of another
    
2. **Common Coupling** – shared global variables
    
3. **Control Coupling** – using flags to control other modules
    
4. **Stamp Coupling** – passing entire objects unnecessarily
    
5. **Data Coupling** – passing minimal data (best)
    

### Goal:

> Move your design closer to **data coupling**, avoid the rest.

---

# 🔄 **2. Acyclic Dependencies Principle (ADP)**

One of the most important architecture rules.

> **There should be NO cyclic dependencies between packages/modules.**

### ❌ Example of a cycle:

```
Order depends on Payment  
Payment depends on Invoice  
Invoice depends on Order  
```

This creates a **dependency cycle** → a nightmare:

- difficult builds
    
- confusing design
    
- unpredictable refactoring
    
- hard testing
    
- tight coupling
    

### ✔ ADP Solution:

Break the cycle using **interfaces**, **events**, or **dependency inversion**.

---

## Example Fix:

Instead of:

```
Order → Payment → Invoice → Order
```

Use an interface:

```
Invoice → IOrderInfo
Order implements IOrderInfo
```

Now the cycle is broken.

---

# 🔒 **3. Stable Dependencies Principle (SDP)**

Part of Uncle Bob’s component principles.

> **Modules should depend on modules more stable than themselves.**  
> **Instability should depend on stability, not the other way around.**

### ✔ Stable modules:

- change rarely
    
- have many dependents
    
- contain core logic
    
- should NOT depend on volatile modules
    

### ✔ Unstable modules:

- frequently changing
    
- high-level modules
    
- UI, controllers, frameworks
    
- may depend on stable modules
    

### ❌ Bad Example:

Stable "Domain" depends on unstable "UI".

### ✔ Good Example:

UI → Application → Domain (direction inwards)

---

# 🧠 **4. Stable Abstractions Principle (SAP)**

Another key principle.

> **The more stable a module is, the more abstract it should be.  
> Stable modules must not contain volatile concrete details.**

### Meaning:

- Highly stable modules = **pure abstractions**
    
- Unstable modules = concrete implementations
    

### Why?

If a stable module contained concrete logic:

- Every change would be painful
    
- Many modules would break
    
- Violates SDP
    

### Example:

✔ Core business rules → abstract interfaces  
✔ Infrastructure (DB, UI) → concrete classes

---

# 📦 **5. Package by Layer vs Package by Feature**

Two ways to organize Java projects.

---

## 📁 **A. Package By Layer (traditional)**

```
com.app.controller
com.app.service
com.app.repository
com.app.model
```

### ✔ Pros:

- Familiar to many developers
    
- Easy for small apps
    

### ❌ Cons:

- Scattered business logic
    
- High coupling
    
- Harder to work on a feature
    
- Poor modularity
    
- Violates CCP (Common Closure Principle)
    

---

## 📦 **B. Package By Feature (recommended)**

```
/orders
   OrderController
   OrderService
   OrderRepository
   OrderEntity

/payments
   PaymentController
   PaymentService
   PaymentRepository
```

### ✔ Pros:

- High cohesion
    
- Low coupling
    
- Clear module boundaries
    
- Easy feature development
    
- Easy team ownership
    
- Works well with Clean Architecture
    
- Better encapsulation
    

### ❌ Cons:

- Requires discipline
    
- More initial structure to set up
    

### Rule:

> **Structure code by business capability, not technical layer.**

---

# 🏛 **6. Conway’s Law**

A famous socio-technical law:

> **“Any organization that designs a system will produce a design that mirrors the organization’s communication structure.” — Melvin Conway**

### Meaning:

- Modular teams → modular architecture
    
- Silos → siloed messy architecture
    
- If teams don't talk → components won't integrate well
    
- If teams collaborate → architecture will be decoupled
    

### Examples:

✔ If you have 3 teams → your system will have 3 major modules  
✔ Microservices often mirror team structure  
✔ Monolith often mirrors centralized teams

### Why it matters?

You must:

- Align team boundaries with architecture
    
- Organize teams around features/domains
    
- Use Domain-Driven Design (DDD) principles
    

Otherwise:

- Teams create accidental coupling
    
- Systems become tangled
    
- Architecture follows org dysfunction
    

---

# 🛠 **7. How Technical Factors Impact Code Structure**

Technical constraints shape architecture decisions:

---

## ✔ 1. Programming language

Java encourages:

- class-based design
    
- packages
    
- strong typing
    
- modularization
    

Dynamic languages like JavaScript allow flexibility → may cause messy structures if not controlled.

---

## ✔ 2. Frameworks

Spring Boot encourages layered architecture by default, but can adapt to:

- Hexagonal architecture
    
- Modular monolith
    
- Clean architecture
    

---

## ✔ 3. Build tools & module systems

Tools like Maven and Gradle influence:

- dependency direction
    
- module structure
    
- packaging
    
- versioning
    

---

## ✔ 4. Deployment environment

Microservices architecture is forced by:

- containerization
    
- distributed systems
    
- operational scaling needs
    

Monoliths work well when:

- team is small
    
- requirements are cohesive
    

---

## ✔ 5. Performance constraints

Sometimes structure is influenced by:

- low latency needs
    
- memory restrictions
    
- parallel execution
    
- batching
    

---

## ✔ 6. Legacy systems

Legacy restrictions shape:

- boundaries
    
- APIs
    
- data models
    
- refactoring limits
    

---

# 🔍 **8. Tools for Code Analysis**

These tools help detect:

- coupling
    
- cycles
    
- complexity
    
- architectural violations
    
- code smells
    
- dependency graphs
    

---

## 🛠 **Static Analysis Tools**

### ✔ SonarQube / SonarLint

Detect:

- code smells
    
- complexity
    
- duplication
    
- dependency cycles
    
- layering violations
    

### ✔ Checkstyle

Format and style enforcement.

### ✔ PMD

Finds:

- unused code
    
- bad patterns
    
- potential bugs
    

### ✔ SpotBugs / FindBugs

Detects:

- concurrency issues
    
- resource leaks
    
- logic defects
    

---

## 🧱 **Architecture Analysis Tools**

### ✔ Structure101

Visualizes module dependencies & cycles.

### ✔ ArchUnit (Java-specific)

Write architecture tests:

```java
classes()
  .that().resideInPackage("..controllers")
  .should().onlyBeAccessed().byAnyPackage("..config");
```

### ✔ SonarGraph

Detects architectural coupling & cycles.

---

## 📊 **Dependency Graph Tools**

- IntelliJ → “Analyze Dependencies”
    
- Gradle → dependency insight
    
- Maven → dependency tree
    
- Graphviz for visual graphs
    

---

# 🎯 **Final Summary**

### **Coupling Principle** → reduce dependencies, improve modularity

### **Acyclic Dependencies Principle (ADP)** → avoid cycles between modules

### **Stable Dependencies Principle (SDP)** → depend on more stable modules

### **Stable Abstractions Principle (SAP)** → stable modules must be abstract

### **Package-by-Feature** → best modularity for real-world systems

### **Package-by-Layer** → old style, leads to scattering & coupling

### **Conway’s Law** → architecture mirrors organizational structure

### **Technical Factors Affect Structure** → language, frameworks, deployment, performance

### **Code Analysis Tools** → SonarQube, ArchUnit, Structure101, PMD, etc.

These principles create:

✔ modular architecture  
✔ low coupling  
✔ high cohesion  
✔ maintainability  
✔ scalability  
✔ clean, understandable code
