# **Statement**

`Statement` is used to execute static SQL strings (i.e., SQL queries that do not need parameters).  
Created from a `Connection` object:

```java
Statement stmt = conn.createStatement();
```

**Characteristics:**

- Sends SQL directly to the database every time.
    
- Not safe against SQL injection.
    
- Best for simple, one-time queries.
    

---

# **execute(String sqlQuery)**

General-purpose method that can run any SQL command.

```java
boolean hasResult = stmt.execute("SELECT * FROM users");
```

**Returns:**

- `true` → the result is a `ResultSet`
    
- `false` → the result is an update count (e.g., rows affected)
    

Used for complex/unknown SQL instructions, such as stored procedures returning different result types.

---

# **executeQuery(String sqlQuery)**

Executes **SELECT queries only**.

```java
ResultSet rs = stmt.executeQuery("SELECT * FROM employees");
```

**Returns:**

- A `ResultSet` containing the data.
    

Throws `SQLException` if used with non-SELECT statements.

---

# **executeUpdate(String sqlQuery)**

Executes SQL statements that **modify data**:

- `INSERT`
    
- `UPDATE`
    
- `DELETE`
    
- DDL commands (`CREATE`, `ALTER`, `DROP`)
    

```java
int rows = stmt.executeUpdate("UPDATE employees SET salary = 4000");
```

**Returns:** number of affected rows.

---

# **ResultSet**

Represents the table returned from a `SELECT` query.

```java
ResultSet rs = stmt.executeQuery("SELECT name, age FROM users");
while (rs.next()) {
    String name = rs.getString("name");
    int age = rs.getInt("age");
}
```

## ResultSet Cursor

- Starts before the first row.
    
- `rs.next()` moves to next row.
    
- Methods:
    
    - `getString()`
        
    - `getInt()`
        
    - `getDouble()`
        
    - `getDate()`
        
    - or by column index: `rs.getString(1)`
        

## ResultSet Types

- `TYPE_FORWARD_ONLY` — default
    
- `TYPE_SCROLL_INSENSITIVE`
    
- `TYPE_SCROLL_SENSITIVE`
    

## Concurrency Modes

- `CONCUR_READ_ONLY`
    
- `CONCUR_UPDATABLE`
    

---

# **SQL → Java Types Mapping**

|SQL Type|Java Type|Getter Method|
|---|---|---|
|`VARCHAR`|`String`|`getString()`|
|`CHAR`|`String`|`getString()`|
|`INT` / `INTEGER`|`int` / `Integer`|`getInt()`|
|`BIGINT`|`long` / `Long`|`getLong()`|
|`FLOAT`|`float`|`getFloat()`|
|`DOUBLE`|`double`|`getDouble()`|
|`DECIMAL`/`NUMERIC`|`BigDecimal`|`getBigDecimal()`|
|`DATE`|`java.sql.Date`|`getDate()`|
|`TIME`|`java.sql.Time`|`getTime()`|
|`TIMESTAMP`|`java.sql.Timestamp`|`getTimestamp()`|
|`BOOLEAN`|`boolean`|`getBoolean()`|
|`BLOB`|`byte[]`|`getBytes()`|

---

# **SQL Injection**

SQL injection occurs when untrusted input is inserted directly into an SQL query.

### Vulnerable Example (bad):

```java
String name = userInput;
String sql = "SELECT * FROM users WHERE name = '" + name + "'";
ResultSet rs = stmt.executeQuery(sql);
```

Attack example:

```
' OR '1'='1
```

### Protection:

1. Use **PreparedStatement**
    
2. Validate user input
    
3. Use least-privilege SQL accounts
    

---

# **PreparedStatement**

A safer and more efficient alternative to `Statement`.

```java
PreparedStatement ps = conn.prepareStatement(
    "SELECT * FROM users WHERE name = ? AND age = ?"
);
ps.setString(1, "ahmed");
ps.setInt(2, 25);

ResultSet rs = ps.executeQuery();
```

## Advantages:

### **1. Prevents SQL Injection**

Database treats the SQL and the parameters separately.

### **2. Precompiled SQL**

Faster for repeated execution.

### **3. Easier to set parameters**

`setString()`, `setInt()`, `setDate()`, etc.

### **4. Supports batching**

```java
ps.addBatch();
ps.executeBatch();
```

---

# **CallableStatement**

Used to call **stored procedures** in the database.

```java
CallableStatement cs = conn.prepareCall("{call increment_salary(?, ?)}");
cs.setInt(1, employeeId);
cs.setDouble(2, bonus);
cs.execute();
```

### For stored procedures with OUT parameters:

```java
CallableStatement cs = conn.prepareCall("{ call get_salary(?, ?) }");

cs.setInt(1, 10);           // input
cs.registerOutParameter(2, Types.DOUBLE); // output

cs.execute();
double salary = cs.getDouble(2);
```

## When to use?

- To run business logic on the database side.
    
- To improve performance for repeated operations.
    
- To enforce rules inside the DB.
    

---

# **Quick Summary**

- **Statement**: executes static SQL; vulnerable to SQL injection.
    
- **execute()**: returns `true/false` depending on result type.
    
- **executeQuery()**: SELECT only → returns ResultSet.
    
- **executeUpdate()**: INSERT/UPDATE/DELETE → returns affected rows.
    
- **ResultSet**: holds returned rows; accessed via cursor.
    
- **SQL–Java type mapping**: ensures correct data retrieval.
    
- **SQL Injection**: prevented using PreparedStatement.
    
- **PreparedStatement**: safer, precompiled SQL with parameters.
    
- **CallableStatement**: runs stored procedures and handles IN/OUT params.
    
