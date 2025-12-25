# **JDBC Overview**

JDBC (Java Database Connectivity) is a Java API that allows Java applications to interact with relational databases.  
It provides a standard interface for executing SQL statements, retrieving results, and managing database connections.

**Main goals of JDBC:**

- Independent of any specific database.
    
- Provide a consistent API for connecting, querying, and updating.
    
- Allow vendors to supply drivers for their databases.
    

---

# **JDBC Driver Types**

JDBC drivers are implementations that enable Java to communicate with specific databases. Traditionally, four driver types are defined:

## **Type 1: JDBC–ODBC Bridge Driver**

- Uses ODBC drivers under the hood.
    
- Requires ODBC installation on the system.
    
- Slow performance; platform-dependent.
    
- Removed from Java 8 onward.
    

## **Type 2: Native API Driver**

- Converts JDBC calls into native database client API calls.
    
- Requires native DB libraries installed on the machine.
    
- Faster than Type 1 but not portable across systems.
    

## **Type 3: Network Protocol Driver**

- Uses a middle-tier server that translates JDBC calls into database-specific protocol.
    
- Portable and does not require database libraries on client.
    
- Suitable for enterprise applications.
    

## **Type 4: Thin Driver (Pure Java Driver)**

- Written entirely in Java.
    
- Converts JDBC calls directly into the database network protocol.
    
- Most commonly used (e.g., MySQL Connector/J, PostgreSQL JDBC, Oracle Thin Driver).
    
- Platform-independent and fast.
    

---

# **What is ODBC?**

ODBC (Open Database Connectivity) is a C-based API developed by Microsoft to allow applications to access relational databases using a common interface.

**Differences from JDBC:**

- ODBC is language-independent, but not Java-friendly.
    
- JDBC is designed specifically for Java.
    
- JDBC handles Java objects more naturally and avoids the need for native libraries.
    

---

# **How JDBC Works (Architecture)**

JDBC is built around a standard workflow:

1. **Load JDBC Driver**  
    The driver registers itself with the `DriverManager`.
    
2. **Establish Connection**  
    Use `DriverManager.getConnection(url, user, pass)`.
    
3. **Create Statement Object**  
    This can be:
    
    - `Statement`
        
    - `PreparedStatement`
        
    - `CallableStatement`
        
4. **Execute SQL Query**
    
    - `executeQuery()` for SELECT
        
    - `executeUpdate()` for INSERT/UPDATE/DELETE
        
5. **Process Results**  
    Using a `ResultSet`.
    
6. **Close Connection and Resources**  
    Prevents memory leaks.
    

---

# **How to Add JDBC Driver to Classpath**

### **1. Using IDE (e.g., IntelliJ, Eclipse)**

- Add the JDBC `.jar` file as a library/dependency.
    

### **2. Using Command Line**

```
javac -cp .;path/to/jdbc-driver.jar MyApp.java
java -cp .;path/to/jdbc-driver.jar MyApp
```

### **3. Using Maven**

Add dependency to `pom.xml`:

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <version>8.2.0</version>
</dependency>
```

---

# **Connecting to a Database via JDBC**

### **Example (MySQL):**

```java
import java.sql.*;

public class Main {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydb";
        String user = "root";
        String pass = "password";

        try (Connection conn = DriverManager.getConnection(url, user, pass)) {
            System.out.println("Connected!");

            String sql = "SELECT * FROM employees";
            PreparedStatement stmt = conn.prepareStatement(sql);
            ResultSet rs = stmt.executeQuery();

            while (rs.next()) {
                System.out.println(rs.getString("name"));
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

---

# **SQLException**

`SQLException` is the main exception class for JDBC errors.

**Common causes:**

- Invalid SQL syntax
    
- Failed connection
    
- Database unavailable
    
- Driver not found
    
- Permission issues
    

**Useful methods:**

- `getMessage()` – error message
    
- `getErrorCode()` – vendor-specific code
    
- `getSQLState()` – standard 5-character SQL state
    
- `getNextException()` – chained exceptions
    

---

# **SQLTimeoutException**

A subclass of `SQLException`.

**Thrown when:**

- A database operation takes too long.
    
- Connection timeout occurs.
    
- Query execution timeout is reached.
    

### Example:

```java
try {
    Statement stmt = conn.createStatement();
    stmt.setQueryTimeout(10); // seconds
    stmt.executeQuery("SELECT * FROM large_table");
} catch (SQLTimeoutException e) {
    System.out.println("Query timed out!");
}
```

---

# **Quick Summary**

- **JDBC** allows Java to interact with databases.
    
- Drivers come in 4 types; Type 4 is most used.
    
- **ODBC** is older and not Java-native.
    
- JDBC workflow: load driver → connect → execute → process → close.
    
- Add drivers via classpath or Maven.
    
- Use `DriverManager` for connections.
    
- `SQLException` handles all database errors.
    
- `SQLTimeoutException` is thrown when an operation exceeds the allowed time.
    
