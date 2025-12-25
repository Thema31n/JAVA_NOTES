# 1️⃣ Spring JDBC Package Overview

Spring JDBC lives mainly under:

```
org.springframework.jdbc
```

## Key Sub-Packages (MEMORIZE)

### 1. `org.springframework.jdbc.core`

Core JDBC abstraction:

- `JdbcTemplate`
    
- `NamedParameterJdbcTemplate`
    
- `RowMapper`
    
- `ResultSetExtractor`
    
- `PreparedStatementSetter`
    

📌 **Most-used package**

---

### 2. `org.springframework.jdbc.datasource`

DataSource utilities:

- Transaction-aware DataSource
    
- Connection management
    
- `DataSourceTransactionManager`
    

---

### 3. `org.springframework.jdbc.support`

Supporting classes:

- Exception translation
    
- SQL type helpers
    
- Generated key handling
    

---

### Interview Line

> Spring JDBC builds on JDBC by providing templates, exception translation, and transaction integration.

---

# 2️⃣ Dependency Configuration (Spring Boot & Non-Boot)

## Spring Boot (Recommended)

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

Includes:

- Spring JDBC
    
- HikariCP (connection pool)
    
- Transaction support
    

---

## Non-Boot (Manual)

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>6.1.2</version>
</dependency>
```

You must configure:

- DataSource
    
- TransactionManager
    

---

# 3️⃣ Configuration of Required Beans (Core Setup)

## 3.1 DataSource (Production-Style)

```java
@Configuration
@EnableTransactionManagement
public class JdbcConfig {

    @Bean
    public DataSource dataSource() {
        HikariDataSource ds = new HikariDataSource();
        ds.setJdbcUrl("jdbc:postgresql://localhost:5432/ecommerce");
        ds.setUsername("ecommerce_user");
        ds.setPassword("secret");
        ds.setMaximumPoolSize(10);
        return ds;
    }

    @Bean
    public PlatformTransactionManager transactionManager(
            DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }
}
```

📌 Spring JDBC uses **DataSourceTransactionManager**, not JPA’s one.

---

## 3.2 JdbcTemplate Bean

```java
@Bean
public JdbcTemplate jdbcTemplate(DataSource dataSource) {
    return new JdbcTemplate(dataSource);
}
```

---

## 3.3 NamedParameterJdbcTemplate Bean

```java
@Bean
public NamedParameterJdbcTemplate namedParameterJdbcTemplate(
        DataSource dataSource) {
    return new NamedParameterJdbcTemplate(dataSource);
}
```

---

# 4️⃣ JdbcTemplate Examples (ALL COMMON QUERIES)

### 4.1 INSERT

```java
jdbcTemplate.update(
    "INSERT INTO users(email, status) VALUES (?, ?)",
    "a@b.com",
    "ACTIVE"
);
```

---

### 4.2 UPDATE

```java
jdbcTemplate.update(
    "UPDATE users SET status = ? WHERE id = ?",
    "BLOCKED",
    10L
);
```

---

### 4.3 DELETE

```java
jdbcTemplate.update(
    "DELETE FROM users WHERE id = ?",
    5L
);
```

---

### 4.4 SELECT – Single Value

```java
int count = jdbcTemplate.queryForObject(
    "SELECT COUNT(*) FROM users",
    Integer.class
);
```

---

### 4.5 SELECT – Single Row

```java
User user = jdbcTemplate.queryForObject(
    "SELECT * FROM users WHERE id = ?",
    new UserRowMapper(),
    1L
);
```

---

### 4.6 SELECT – Multiple Rows

```java
List<User> users = jdbcTemplate.query(
    "SELECT * FROM users WHERE status = ?",
    new UserRowMapper(),
    "ACTIVE"
);
```

---

# 5️⃣ RowMapper (VERY IMPORTANT)

## What Is RowMapper?

Maps **one row** of `ResultSet` → Java object.

---

## Real-World RowMapper

```java
public class UserRowMapper implements RowMapper<User> {

    @Override
    public User mapRow(ResultSet rs, int rowNum)
            throws SQLException {

        User user = new User();
        user.setId(rs.getLong("id"));
        user.setEmail(rs.getString("email"));
        user.setStatus(rs.getString("status"));
        return user;
    }
}
```

📌 Preferred over inline lambdas in large projects.

---

## Interview Line

> RowMapper maps a single row; JdbcTemplate handles iteration.

---

# 6️⃣ NamedParameterJdbcTemplate Examples

## 6.1 Why Named Parameters Matter

```sql
WHERE status = :status AND role = :role
```

✔ Readable  
✔ Order-safe

---

## 6.2 Example

```java
String sql = """
    SELECT * FROM orders
    WHERE status = :status
    AND total_amount > :minAmount
""";

MapSqlParameterSource params =
        new MapSqlParameterSource()
            .addValue("status", "PAID")
            .addValue("minAmount", BigDecimal.valueOf(100));

List<Order> orders =
    namedJdbcTemplate.query(sql, params, new OrderRowMapper());
```

---

# 6️⃣➕ BeanPropertySqlParameterSource (NEW – ADDED)

## What It Is

`BeanPropertySqlParameterSource` maps **Java bean properties**  
➡️ **directly to named SQL parameters**

### Key Rule (MEMORIZE)

> Java field names must match SQL named parameters.

---

## Real-World Example

### Domain Object

```java
public class User {
    private String email;
    private String status;

    // getters & setters
}
```

---

### SQL

```sql
INSERT INTO users(email, status)
VALUES (:email, :status)
```

---

### Spring Code

```java
User user = new User();
user.setEmail("a@a.com");
user.setStatus("ACTIVE");

SqlParameterSource params =
        new BeanPropertySqlParameterSource(user);

namedJdbcTemplate.update(
    "INSERT INTO users(email, status) VALUES (:email, :status)",
    params
);
```

✔ No Map  
✔ Cleaner code  
✔ Less error-prone

---

## When to Use

- Insert/update using domain objects
    
- DTO-driven persistence
    
- Clean repository methods
    

---

## Interview Line

> BeanPropertySqlParameterSource binds SQL parameters directly from object properties.

---

# 7️⃣ SimpleJdbcInsert (Insert without SQL)

## Example

```java
SimpleJdbcInsert insert =
    new SimpleJdbcInsert(dataSource)
        .withTableName("orders")
        .usingGeneratedKeyColumns("id");

Map<String, Object> values = Map.of(
    "status", "NEW",
    "total_amount", 200
);

Number id = insert.executeAndReturnKey(values);
```

---

## When to Use

- Tables with many columns
    
- Clean insert logic
    

---

# 8️⃣ SimpleJdbcCall (Stored Procedure)

## Example

```java
SimpleJdbcCall call =
    new SimpleJdbcCall(dataSource)
        .withProcedureName("calculate_discount");

Map<String, Object> result =
    call.execute(Map.of("order_id", 10));

BigDecimal discount =
    (BigDecimal) result.get("discount");
```

---

# 9️⃣ Batch Update with SqlParameterSourceUtils

## Real-World Use Case

- Bulk inserts
    
- Data migration
    
- Batch jobs
    

---

## Example

```java
List<User> users = List.of(
    new User("a@a.com"),
    new User("b@b.com")
);

SqlParameterSource[] batch =
    SqlParameterSourceUtils.createBatch(users);

namedJdbcTemplate.batchUpdate(
    "INSERT INTO users(email) VALUES (:email)",
    batch
);
```

✔ Fast  
✔ Scalable  
✔ Transactional

---

# 🔟 Transactions with Spring JDBC

```java
@Transactional
public void bulkUpdate(...) {
    jdbcTemplate.update(...);
    jdbcTemplate.update(...);
}
```

✔ Same transaction  
✔ Rollback on failure

---

# 🔥 Interview Questions You WILL Get

### Q: JdbcTemplate vs JPA?

**Answer:**  
JdbcTemplate gives full SQL control; JPA abstracts SQL.

---

### Q: RowMapper vs ResultSetExtractor?

**Answer:**  
RowMapper maps one row; ResultSetExtractor maps entire ResultSet.

---

### Q: When to use batchUpdate?

**Answer:**  
High-volume insert/update operations.

---

# 🧠 Things to Memorize (FINAL)

- `JdbcTemplate` is the core class
    
- NamedParameterJdbcTemplate improves readability
    
- RowMapper maps one row
    
- BeanPropertySqlParameterSource binds object → SQL
    
- SimpleJdbcInsert avoids SQL
    
- SimpleJdbcCall handles stored procedures
    
- Batch updates are for performance
    
- Spring JDBC integrates with @Transactional
    
- Spring JDBC ≠ ORM
    
