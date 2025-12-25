## 1️⃣ JDBC (Plain JDBC)

**What it is**

- The **standard Java API** for interacting with relational databases.
    

**Key characteristics**

- You write **SQL manually**
    
- You manage:
    
    - Connections
        
    - Statements
        
    - ResultSets
        
    - Exception handling
        
- Very **low-level**, very **flexible**
    

**Example**

```java
Connection conn = DriverManager.getConnection(url, user, pass);
PreparedStatement ps = conn.prepareStatement(
    "SELECT * FROM users WHERE id = ?");
ps.setInt(1, 1);
ResultSet rs = ps.executeQuery();
```

**Pros**

- Full control
    
- No framework dependency
    

**Cons**

- Verbose
    
- Boilerplate-heavy
    
- Easy to make mistakes (resource leaks)
    

**Best when**

- Small utilities
    
- Performance-critical or highly customized SQL
    
- Framework-free environments
    

---

## 2️⃣ Spring JDBC

**What it is**

- A **thin abstraction** on top of JDBC provided by Spring.
    

**Key characteristics**

- Still **SQL-first**
    
- Eliminates boilerplate
    
- Handles:
    
    - Connection management
        
    - Exception translation
        
    - Resource cleanup
        

**Core class**

- `JdbcTemplate`
    

**Example**

```java
String sql = "SELECT * FROM users WHERE id = ?";
User user = jdbcTemplate.queryForObject(
    sql,
    new BeanPropertyRowMapper<>(User.class),
    1
);
```

**Pros**

- Much cleaner than plain JDBC
    
- Better error handling
    
- Still full SQL control
    

**Cons**

- You still write SQL
    
- Manual mapping (unless simple cases)
    

**Best when**

- You like SQL
    
- You want control without boilerplate
    
- Legacy schemas or complex queries
    

---

## 3️⃣ Spring Data JDBC

**What it is**

- A **repository-based abstraction** built on top of Spring JDBC.
    
- **NOT an ORM** like JPA/Hibernate.
    

**Key characteristics**

- Focused on **aggregates**
    
- Minimal magic
    
- No lazy loading, no caching, no proxies
    
- Simple CRUD via repositories
    

**Example**

```java
@Table("users")
public class User {
    @Id
    Long id;
    String name;
}

public interface UserRepository
        extends CrudRepository<User, Long> {
}
```

Usage:

```java
userRepository.findById(1L);
```

**Pros**

- Very simple
    
- Less magic than JPA
    
- Easy CRUD
    
- SQL remains predictable
    

**Cons**

- Limited relationships
    
- No advanced ORM features
    
- Complex queries need custom SQL
    

**Best when**

- Simple domain model
    
- Microservices
    
- You want repositories without JPA complexity
    

---

## 🔥 Side-by-Side Comparison

|Feature|JDBC|Spring JDBC|Spring Data JDBC|
|---|---|---|---|
|Abstraction level|Low|Medium|High|
|Boilerplate|Very high|Low|Very low|
|SQL required|Yes|Yes|Mostly no|
|ORM features|❌|❌|❌|
|Repository support|❌|❌|✅|
|Lazy loading|❌|❌|❌|
|Best for|Full control|SQL-heavy apps|Simple CRUD|

---

## ⚠️ Spring Data JDBC vs Spring Data JPA (Important)

- **Spring Data JDBC ≠ JPA**
    
- No:
    
    - Lazy loading
        
    - Caching
        
    - Complex relationships
        
- Think of it as:
    
    > “Repositories + SQL, not an ORM”
    

---

## 🧠 How to Choose

- **Use JDBC** → if you need absolute control
    
- **Use Spring JDBC** → if you love SQL but hate boilerplate
    
- **Use Spring Data JDBC** → if you want simple repositories without ORM magic
    