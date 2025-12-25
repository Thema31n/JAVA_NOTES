# 1️⃣ JDBC Overview

## What is JDBC?

**JDBC (Java Database Connectivity)** is the **low-level Java API** used to interact with relational databases.

### What JDBC Gives You

- Open DB connection
    
- Execute SQL
    
- Read `ResultSet`
    
- Manage transactions manually
    

### Typical JDBC Flow

```text
Get Connection
 → Prepare Statement
 → Execute SQL
 → Process ResultSet
 → Close Resources
```

---

## Classic JDBC Example (Painful but Real)

```java
Connection conn = null;
PreparedStatement ps = null;
ResultSet rs = null;

try {
    conn = dataSource.getConnection();
    ps = conn.prepareStatement(
        "SELECT id, email FROM users WHERE status = ?");
    ps.setString(1, "ACTIVE");

    rs = ps.executeQuery();
    while (rs.next()) {
        System.out.println(rs.getString("email"));
    }
} finally {
    rs.close();
    ps.close();
    conn.close();
}
```

🚨 **Problems already visible**

- Boilerplate
    
- Resource leaks
    
- Hard to read
    
- Hard to maintain
    

---

# 2️⃣ Problems of JDBC API (WHY SPRING JDBC EXISTS)

## 1. Boilerplate Code

- Repeated try/catch/finally
    
- Manual closing
    

## 2. Error-Prone Resource Management

- Memory leaks
    
- Connection leaks
    

## 3. Poor Exception Handling

- Checked `SQLException`
    
- No meaningful error info
    

## 4. No Integration with Spring

- Manual transactions
    
- Manual connection handling
    

---

## Interview Line

> JDBC is powerful but too low-level and error-prone for enterprise applications.

---

# 3️⃣ Why We Need Spring JDBC

## What Spring JDBC Does

Spring JDBC **wraps JDBC** and:

- Eliminates boilerplate
    
- Handles resource management
    
- Translates SQL exceptions
    
- Integrates with Spring transactions
    

### Key Idea (MEMORIZE)

> Spring JDBC = JDBC + Spring infrastructure

---

## What Spring JDBC Does NOT Do

❌ No ORM  
❌ No entity lifecycle  
❌ No lazy loading

You write SQL. Spring executes it safely.

---

# 4️⃣ Spring JDBC vs Spring Data JDBC (VERY IMPORTANT)

|Feature|Spring JDBC|Spring Data JDBC|
|---|---|---|
|Abstraction level|Low|Medium|
|ORM|❌ No|❌ No|
|SQL writing|Manual|Mostly manual|
|Repository support|❌|✅|
|Lazy loading|❌|❌|
|Use case|Fine-grained control|Simple domain models|

---

## When to Choose Each

### Use Spring JDBC When

- Performance-critical queries
    
- Reporting systems
    
- Complex SQL
    
- Legacy schemas
    

### Use Spring Data JDBC When

- Simple aggregates
    
- Clean domain models
    
- No complex joins
    

---

## Interview Trap

> Spring Data JDBC is **NOT** JPA

---

# 5️⃣ JdbcTemplate (CORE CLASS)

## What is JdbcTemplate?

`JdbcTemplate` is the **central class** in Spring JDBC.

### What It Handles

- Connection management
    
- Statement creation
    
- ResultSet iteration
    
- Exception translation
    

---

## Real-World Example: Fetch Users

```java
@Repository
public class UserJdbcRepository {

    private final JdbcTemplate jdbcTemplate;

    public UserJdbcRepository(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    public List<User> findActiveUsers() {
        return jdbcTemplate.query(
            "SELECT id, email FROM users WHERE status = ?",
            (rs, rowNum) -> new User(
                rs.getLong("id"),
                rs.getString("email")
            ),
            "ACTIVE"
        );
    }
}
```

✔ Clean  
✔ No resource handling  
✔ Readable

---

## Common JdbcTemplate Methods (MEMORIZE)

|Method|Usage|
|---|---|
|query|SELECT multiple rows|
|queryForObject|SELECT single row|
|update|INSERT / UPDATE / DELETE|
|execute|DDL / stored procedures|

---

# 6️⃣ NamedParameterJdbcTemplate

## Why It Exists

Problem with `JdbcTemplate`:

```sql
WHERE status = ? AND role = ?
```

❌ Hard to read  
❌ Error-prone ordering

---

## NamedParameterJdbcTemplate Example

```java
@Repository
public class OrderJdbcRepository {

    private final NamedParameterJdbcTemplate jdbcTemplate;

    public OrderJdbcRepository(NamedParameterJdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    public List<Order> findOrders(
            OrderStatus status, BigDecimal minAmount) {

        String sql = """
            SELECT * FROM orders
            WHERE status = :status
            AND total_amount >= :amount
        """;

        Map<String, Object> params = Map.of(
            "status", status.name(),
            "amount", minAmount
        );

        return jdbcTemplate.query(
            sql,
            params,
            new BeanPropertyRowMapper<>(Order.class)
        );
    }
}
```

✔ Readable SQL  
✔ Safer  
✔ Preferred in real projects

---

## Interview Line

> NamedParameterJdbcTemplate improves readability and safety of SQL queries.

---

# 7️⃣ SimpleJdbcInsert (INSERT without SQL)

## What It Does

- Generates INSERT statements automatically
    
- Uses table metadata
    

---

## Real-World Example

```java
@Repository
public class UserInsertRepository {

    private final SimpleJdbcInsert jdbcInsert;

    public UserInsertRepository(DataSource dataSource) {
        this.jdbcInsert = new SimpleJdbcInsert(dataSource)
                .withTableName("users")
                .usingGeneratedKeyColumns("id");
    }

    public Long saveUser(User user) {
        Map<String, Object> params = Map.of(
            "email", user.getEmail(),
            "name", user.getName(),
            "status", user.getStatus().name()
        );

        return jdbcInsert.executeAndReturnKey(params).longValue();
    }
}
```

✔ No SQL string  
✔ Clean insert logic

---

## When to Use

- Simple inserts
    
- Tables with many columns
    

---

# 8️⃣ SimpleJdbcCall (Stored Procedures)

## What It Does

- Calls stored procedures/functions
    
- Handles input/output parameters
    

---

## Real-World Example: Stored Procedure

### DB Procedure

```sql
CREATE PROCEDURE count_orders_by_status(
    IN p_status VARCHAR,
    OUT p_count INT
)
```

### Spring Code

```java
@Repository
public class OrderProcedureRepository {

    private final SimpleJdbcCall jdbcCall;

    public OrderProcedureRepository(DataSource dataSource) {
        this.jdbcCall = new SimpleJdbcCall(dataSource)
                .withProcedureName("count_orders_by_status");
    }

    public int countOrders(String status) {
        Map<String, Object> result =
            jdbcCall.execute(Map.of("p_status", status));

        return (Integer) result.get("p_count");
    }
}
```

✔ Clean stored procedure calls  
✔ No low-level JDBC code

---

# 9️⃣ Transactions in Spring JDBC

Spring JDBC integrates with:

```java
@Transactional
```

```java
@Transactional
public void transferMoney(...) {
    jdbcTemplate.update(...);
    jdbcTemplate.update(...);
}
```

✔ Same transaction  
✔ Automatic rollback

---

# 🔟 Spring JDBC vs JPA (Quick Interview Comparison)

|Aspect|Spring JDBC|JPA|
|---|---|---|
|SQL control|Full|Limited|
|Performance|Predictable|Can surprise|
|Learning curve|Lower|Higher|
|Boilerplate|Low|Very low|
|ORM|❌|✅|

---

# 🔥 Interview Questions You WILL Get

### Q: Why use Spring JDBC instead of JPA?

**Answer:**  
When SQL control, performance, or legacy schemas are critical.

---

### Q: JdbcTemplate vs NamedParameterJdbcTemplate?

**Answer:**  
NamedParameterJdbcTemplate improves readability and reduces parameter order bugs.

---

### Q: Does Spring JDBC manage transactions?

**Answer:**  
Yes, via Spring’s transaction abstraction.

---

# 🧠 Things to Memorize (FINAL)

- JDBC is low-level and verbose
    
- Spring JDBC removes boilerplate
    
- JdbcTemplate is the core class
    
- NamedParameterJdbcTemplate is preferred
    
- SimpleJdbcInsert avoids SQL for inserts
    
- SimpleJdbcCall handles stored procedures
    
- Spring JDBC ≠ ORM
    
- Spring JDBC vs Spring Data JDBC are different
    
