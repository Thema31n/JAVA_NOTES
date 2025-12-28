# 📘 Java Notes (Obsidian → GitHub)

This repository contains my **personal Java knowledge base** organized by chatgpt 5.2, originally maintained in **Obsidian** and organized here for long‑term reference, revision, and interview preparation.

The repo is intentionally simple:

```
.
├── README.md
└── JAVA/
```

All notes live under the `JAVA` directory, grouped by **topic**, **concept**, and **framework**.

---

## 📂 Repository Index

### 🔹 JAVA

A complete Java ecosystem reference covering core language, JVM, collections, concurrency, frameworks, security, testing, and best practices.

---

## 🗂️ JAVA Folder Index

### 1️⃣ Collections

**Purpose:** Master Java Collections Framework, internals, performance, and comparisons.

**Topics:**

- Collection Interface & hierarchy
- List, Set, Map implementations
- Internal mechanics (HashMap, TreeMap, collisions)
- Iterators & immutability
- Sorting & comparators

**Files:**

- ArrayList vs Vector vs CopyOnWriteArrayList vs Stack
- Collection Interface
- Collections Diagram
- Collections vs Arrays
- Comparable vs Comparator
- HashMap & Sorting
- Internal Mechanics of Map, Set, HashMap, Search & Collisions
- Iterators
- JEP 431 – Sequenced Collections
- LinkedHashMap vs HashMap
- Map
- Marker Interface
- Queue vs Deque
- Set
- TreeMap Internals
- Unmodifiable Lists

---

### 2️⃣ Date & Time in JDK

**Purpose:** Understand legacy and modern Java date/time APIs.

**Files:**

- Date & Calendar & GMT & UTC
- Time Package (java.time)

---

### 3️⃣ Design Patterns

**Purpose:** Learn object‑oriented design patterns and real‑world usage.

#### Web App Design Patterns

- MVC
- Layered Architecture

#### Core Design Patterns

- Design Patterns Overview
- Creational Patterns
- Structural Patterns (Part 1 & 2)
- Behavioral Patterns (Part 1–3)
- Data Access Software (Connection Pooling)

---

### 4️⃣ Functional Programming & Streams

**Purpose:** Write modern, declarative Java using lambdas and streams.

**Files:**

- FP Concepts
- FP & Anonymous Functions
- Consumer & Predicate
- Function & BiFunction
- Stream API

---

### 5️⃣ IO Streams

**Purpose:** Handle file systems, byte/character streams, and serialization.

**Files:**

- Streams Diagram
- Byte Streams
- Character Streams
- Files, NIO, Path, Paths
- Read & Write Ways
- Serialization & Cloning
- Java Separators

---

### 6️⃣ JDBC & DAO

**Purpose:** Low‑level database access and transaction handling.

**Files:**

- JDBC Overview
- Statement vs PreparedStatement vs CallableStatement
- Transactions, Batch Updates & Metadata
- DAO Design Pattern

---

### 7️⃣ JSP & Servlets

**Purpose:** Java web fundamentals.

**Files:**

- JSP Overview
- Interview Tricks
- JSTL
- Custom Tags
- Intro to Servlets
- Servlet API
- web.xml (Deployment Descriptor)

---

### 8️⃣ Logging

**Purpose:** Application logging strategies and frameworks.

**Files:**

- Logging Theory, Levels & Frameworks
- Log4j2 Triggering Policies
- Log4j2 Rollover Strategy

---

### 9️⃣ Maven

**Purpose:** Build automation, dependency management, and multi‑module projects.

**Files:**

- Maven Overview
- Archetypes & Dependency Management
- IntelliJ IDEA & Maven
- Maven Settings
- App Build, JAR Structure & Dependency Control
- Maven Web Apps
- Inheritance, Aggregation & Multi‑Module

---

### 🔟 Multithreading & Concurrency

**Purpose:** Deep dive into Java concurrency and parallelism.

**Files:**

- Java Threading
- Scheduler, Race Conditions & More
- Synchronization Basics
- Memory, Volatile, ThreadLocal, Atomic
- Deadlock & Livelock
- Executor Services, Callable & Future
- Shutdown vs ShutdownNow
- Thread Pools & ThreadFactory
- Fixed Rate vs Fixed Delay Scheduling
- Fork/Join Framework
- CompletableFuture
- Java Lock API
- CyclicBarrier, CountDownLatch, Semaphore, Exchanger
- Virtual Threads

---

### 1️⃣1️⃣ OOP & Clean Code Design

**Purpose:** Write maintainable, clean, and extensible code.

**Files:**

- Coupling & Cohesion
- Law of Demeter
- KISS Principle
- YAGNI Principle
- DRY Principle
- Packaging Principles (Cohesion & Coupling)

---

### 1️⃣2️⃣ OOP & SOLID

**Purpose:** Core object‑oriented principles.

**SOLID:**

- Single Responsibility Principle
- Open/Closed Principle
- Liskov Substitution Principle
- Interface Segregation Principle
- Dependency Inversion Principle

**Other Topics:**

- Interfaces
- Abstract vs Interface Contains
- Access Modifiers
- Anonymous Classes
- Association vs Aggregation vs Composition
- Equals & hashCode
- Native & Clone
- Nested & Inner Classes
- POJO Class
- static keyword
- Types of Classes & Inheritance

---

### 1️⃣3️⃣ ORM, JPA & Hibernate

**Purpose:** Persistence, ORM concepts, and performance tuning.

**Files:**

- JPA & ORM Overview
- Entity, ID, Generation Types & Composite Keys
- Hibernate ID Generation
- CRUD & Entity Lifecycle
- Optimistic & Pessimistic Locking
- Entity Relationships
- JPA Queries
- When to Use Each Query Type
- Caching in Hibernate
- Ehcache Demo
- N+1 Problem & Solution

---

### 1️⃣4️⃣ OWASP & Security

**Purpose:** Secure Java applications.

**Files:**

- OWASP Top 10 Overview (2021)
- Broken Access Control
- Cryptographic Failures
- Injection
- Insecure Design
- Security Misconfiguration

---

### 1️⃣5️⃣ REST Architecture

**Purpose:** RESTful API design with Java & Spring.

**Files:**

- REST Overview & Principles
- Naming Conventions
- REST with Spring MVC
- API Testing Tools & Postman

---

### 1️⃣6️⃣ Spring Framework

#### Spring Core

- Framework Overview
- Architecture, IoC, DI & Beans
- XML & Annotation Configurations
- Stereotype Annotations
- Dependency Injection (In‑Depth)

#### Spring MVC

- MVC Overview
- Model, RequestParam, PathVariable
- Forms & Bean Validation
- Exception Handling
- Static Resources
- Filters & Interceptors
- Localization & i18n
- Redirect vs Forward
- Model vs ModelMap vs FlashMap
- Cookies

#### Spring Security

- Overview
- Configurations
- DB Authentication & Authorization
- Remember‑Me & Method Security
- Spring Expression Language (SpEL)
- Auth Provider & Architecture

#### Spring Data & JDBC

- Spring Data JPA Overview
- JPA Configurations & Examples
- Spring JDBC Overview
- JDBC vs Spring JDBC vs Spring Data JDBC
- Spring JDBC Examples

#### Spring AOP

- AOP Overview
- AspectJ
- Proxy Internals

---

### 1️⃣7️⃣ Spring Boot

**Purpose:** Production‑ready Spring applications.

**Files:**

- Spring Boot Introduction
- Project & Initializer
- Starters
- Configurations & Application Properties
- Actuator (Monitoring Tools)
- Spring Boot Filters

---

### 1️⃣8️⃣ Testing

**Purpose:** Write reliable and maintainable tests.

**Files:**

- Testing Overview
- Unit Testing & JUnit
- JUnit 5 vs JUnit 4
- Advanced JUnit & Code Coverage
- Hamcrest
- Integration Testing & Mockito
- Mockito (Advanced)
- PowerMock
- TDD
- BDD & ATDD

---

### 1️⃣9️⃣ Miscellaneous Java Topics

- Annotations
- Arrays in Java
- BigDecimal
- Build Automation Tools
- Events Handling in Java Web Apps
- Exception Handling
- Integer Equality
- IntelliJ IDEA
- Java Generics
- Java Modules & Migration
- JVM, JRE & JDK
- Optional
- Localization & Internationalization
- Math in Java
- Primitive Variable Conversion
- Random
- Reactive Programming
- Reflection API
- Sessions & Cookies
- String
- StringBuilder vs StringBuffer
- Web Filters
- Mappers in java

---

## 🎯 Goal of This Repository

- 📚 Personal knowledge base
- 🧠 Interview preparation
- 🧩 Concept clarification
- 🚀 Long‑term Java mastery

---

## 📝 Notes

- Content is continuously updated
- Written primarily for **clarity and depth**, not brevity
- Examples focus on **real‑world usage**

---

**Author:** Ahmed Abdelaziz
