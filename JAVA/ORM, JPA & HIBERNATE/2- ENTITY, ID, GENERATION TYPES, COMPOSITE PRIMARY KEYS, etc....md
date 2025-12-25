# 1️⃣ What is a JPA Project (Real World View)

## What “JPA Project” Means in Practice

A **JPA project** is any Java application where:

- Data is stored in a relational DB
    
- Java objects are persisted using **JPA APIs**
    
- A JPA provider (usually Hibernate) does the ORM work
    

### Common Real-World JPA Projects

- Spring Boot REST APIs
    
- Monolith enterprise apps
    
- Microservices with relational DBs
    
- Legacy Java EE applications
    

---

## Typical JPA Project Structure

```
src/main/java
 └── com.company.app
     ├── entity
     │   └── User.java
     ├── repository
     │   └── UserRepository.java
     ├── service
     │   └── UserService.java
     └── Application.java

src/main/resources
 └── META-INF
     └── persistence.xml   (or application.yml in Spring Boot)
```

---

# 2️⃣ Entities in JPA

## What Is an Entity?

An **entity** is:

- A **POJO**
    
- Mapped to a database table
    
- Managed by JPA
    

### Real-World Meaning

If your database has:

- users
    
- orders
    
- payments
    

➡ Each is an entity class.

---

## Example

```java
@Entity
public class User {
    @Id
    private Long id;
}
```

---

# 3️⃣ `@Entity` Annotation

## Purpose

Marks a class as **persistent**.

### Rules

- Must be placed on class
    
- Class must have a **no-args constructor**
    

### Example

```java
@Entity
public class Product {
    @Id
    private Long id;
}
```

Without `@Entity` → JPA ignores the class completely.

---

# 4️⃣ `@Table` Annotation

## Purpose

Maps entity to a specific table.

### Real-World Use Cases

- Table name differs from class name
    
- Schema is specified
    
- Unique constraints
    

### Example

```java
@Entity
@Table(
    name = "users",
    uniqueConstraints = @UniqueConstraint(columnNames = "email")
)
public class User {
}
```

---

# 5️⃣ `@Column` Annotation

## Purpose

Customizes column mapping.

### When You Need It

- Column name differs
    
- Length constraints
    
- Nullable / unique rules
    

### Example

```java
@Column(name = "email", nullable = false, unique = true, length = 100)
private String email;
```

---

# 6️⃣ `@Id` Annotation

## Purpose

Marks **primary key**.

### Rules

- Every entity MUST have exactly one identifier
    
- Can be simple or composite
    

### Example

```java
@Id
private Long id;
```

---

# 7️⃣ `@GeneratedValue` Annotation

## Purpose

Tells JPA how primary key is generated.

```java
@Id
@GeneratedValue
private Long id;
```

---

## Generation Strategies (VERY IMPORTANT)

---

# 8️⃣ `GenerationType.TABLE`

## How It Works

- Uses a **separate table** to generate IDs
    

### Real-World Usage

- Rare
    
- Used when DB does not support sequences
    

### Example

```java
@GeneratedValue(strategy = GenerationType.TABLE)
```

### Drawbacks

- Slower
    
- Extra table access
    

---

# 9️⃣ `GenerationType.SEQUENCE`

## How It Works

- Uses DB sequence
    

### Best For

- Oracle
    
- PostgreSQL
    

### Example

```java
@SequenceGenerator(
    name = "user_seq",
    sequenceName = "user_sequence",
    allocationSize = 1
)
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "user_seq")
private Long id;
```

### Real-World

✅ Best performance  
❌ Not supported by MySQL (older versions)

---

# 🔟 `GenerationType.IDENTITY`

## How It Works

- DB auto-increment column
    

### Best For

- MySQL
    
- SQL Server
    

### Example

```java
@GeneratedValue(strategy = GenerationType.IDENTITY)
```

### Real-World Drawback

- No batch inserts
    
- ID generated only after insert
    

---

# 1️⃣1️⃣ `GenerationType.UUID` (JPA 3 / Hibernate)

## How It Works

- Generates UUID as primary key
    

### Example

```java
@Id
@GeneratedValue(strategy = GenerationType.UUID)
private UUID id;
```

### Real-World Usage

- Distributed systems
    
- Microservices
    
- Avoids DB coordination
    

---

# 1️⃣2️⃣ `GenerationType.AUTO`

## How It Works

- JPA chooses best strategy based on DB
    

### Example

```java
@GeneratedValue(strategy = GenerationType.AUTO)
```

### Reality

- Common default
    
- Can be unpredictable across DBs
    

---

# 1️⃣3️⃣ Requirements for an Entity Class

### MUST Have

✅ `@Entity`  
✅ No-args constructor  
✅ Primary key  
✅ Non-final class

### SHOULD Have

✔ Serializable (optional)  
✔ Equals & hashCode (careful!)

---

# 1️⃣4️⃣ Composite Primary Keys

## Real-World Example

Order item identified by:

- order_id
    
- product_id
    

---

## Option 1: `@EmbeddedId`

```java
@Embeddable
public class OrderItemId {
    Long orderId;
    Long productId;
}
```

```java
@Entity
public class OrderItem {

    @EmbeddedId
    private OrderItemId id;

    private int quantity;
}
```

---

## Option 2: `@IdClass`

Used less often, more verbose.

### Real-World Legacy Example

OrderItem table:

- order_id
    
- product_id
    
- quantity
    

### ID Class

```java
public class OrderItemId implements Serializable {

    private Long orderId;
    private Long productId;

    public OrderItemId() {}

    @Override
    public boolean equals(Object o) { /* required */ }

    @Override
    public int hashCode() { /* required */ }
}
```

### Entity

```java
@Entity
@IdClass(OrderItemId.class)
public class OrderItem {

    @Id
    private Long orderId;

    @Id
    private Long productId;

    private int quantity;
}
```

**Why it’s used:** legacy schemas, full column control  
**Why it’s avoided:** duplication, verbosity

---

# 1️⃣5️⃣ Temporal Annotations

## Problem

Java has:

- `Date`
    
- `LocalDate`
    
- `LocalDateTime`
    

DB has:

- DATE
    
- TIME
    
- TIMESTAMP
    

---

## `@Temporal`

```java
@Temporal(TemporalType.DATE)
private Date birthDate;
```

### Types

|TemporalType|DB Column|
|---|---|
|DATE|Date only|
|TIME|Time only|
|TIMESTAMP|Date + time|

---

# 1️⃣6️⃣ Non-Persisted Fields (`@Transient`)

## Purpose

Exclude field from persistence.

### Real-World Use

- Calculated fields
    
- Temporary values
    
- DTO helpers
    

### Example

```java
@Transient
private int age;
```

Not stored in DB.

---

# 1️⃣7️⃣ Persistent Enum Types (`@Enumerated`)

## Problem

How to store enums?

---

## `EnumType.ORDINAL` (DANGEROUS)

```java
@Enumerated(EnumType.ORDINAL)
private Status status;
```

❌ Breaks if enum order changes

---

## `EnumType.STRING` (RECOMMENDED)

```java
@Enumerated(EnumType.STRING)
private Status status;
```

✔ Safe  
✔ Readable DB values

---

# 1️⃣8️⃣ EntityManagerFactory

## Purpose

- Heavy object
    
- Created once per application
    

### Example

```java
EntityManagerFactory emf =
    Persistence.createEntityManagerFactory("myPU");
```

### Real-World

- Created at startup
    
- Closed at shutdown
    

---

# 1️⃣9️⃣ EntityManager

## Purpose

- Manages entities
    
- Represents persistence context
    

### Example

```java
EntityManager em = emf.createEntityManager();
```

### Lifecycle

- Short-lived
    
- One per transaction/request
    

---

# 2️⃣0️⃣ `persistence.xml`

## Purpose

- Defines persistence unit
    
- DB connection
    
- JPA provider
    

### Example

```xml
<persistence-unit name="myPU">
    <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>

    <properties>
        <property name="jakarta.persistence.jdbc.url"
                  value="jdbc:mysql://localhost:3306/app"/>
        <property name="jakarta.persistence.jdbc.user" value="root"/>
        <property name="hibernate.hbm2ddl.auto" value="update"/>
    </properties>
</persistence-unit>
```

---

# 2️⃣1️⃣ Transactions in JPA

## Why Transactions Matter

- ACID guarantees
    
- Data consistency
    
- Rollback on failure
    

---

## Manual Transaction

```java
EntityTransaction tx = em.getTransaction();
tx.begin();

em.persist(user);

tx.commit();
```

---

# 2️⃣2️⃣ Transaction Types in JPA

|Type|Used In|
|---|---|
|RESOURCE_LOCAL|Java SE, Spring Boot|
|JTA|Java EE, Jakarta EE|

---

# 2️⃣3️⃣ JTA vs RESOURCE_LOCAL (HOW THEY WORK)

## RESOURCE_LOCAL (How It Works)

- Application controls transaction boundaries
    
- One JDBC connection
    
- Auto-commit disabled manually
    
- Commit / rollback handled explicitly
    

```java
em.getTransaction().begin();
```

Fast, simple, single DB.

---

## JTA (How It Works)

- Container manages transactions
    
- Global transaction context
    
- Supports multiple resources
    
- Uses **Two-Phase Commit (2PC)**
    

```java
@Transactional
public void processOrder() { }
```

Slower, safer, distributed.

---

## Real-World Rule

|Scenario|Use|
|---|---|
|Spring Boot|RESOURCE_LOCAL|
|App Server (WildFly)|JTA|
|Microservices|RESOURCE_LOCAL|
|XA transactions|JTA|

---

# 2️⃣4️⃣ Practical Real-World Exercise

## Scenario

Build a **User Management System**

### Requirements

- Store users
    
- Auto-generated ID
    
- Enum status
    
- Non-persisted computed field
    
- Transactions
    

---

## Entity

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Enumerated(EnumType.STRING)
    private Status status;

    @Temporal(TemporalType.TIMESTAMP)
    private Date createdAt;

    @Transient
    private int sessionCount;
}
```

---

## Persisting User

```java
em.getTransaction().begin();

User user = new User();
user.setName("Ahmed");
user.setStatus(Status.ACTIVE);
user.setCreatedAt(new Date());

em.persist(user);

em.getTransaction().commit();
```
