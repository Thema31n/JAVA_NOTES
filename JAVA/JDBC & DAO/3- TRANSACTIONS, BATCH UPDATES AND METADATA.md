# 🧩 **Transactions**

A **transaction** is a group of one or more SQL operations that are treated as a **single unit of work**.  
Either **all operations succeed**, or **none of them do**.

### 🔍 Real-world analogy:

Think of **online banking**:

- You withdraw money from account A
    
- You deposit money into account B
    

Both actions must succeed together.  
If the second fails, the first should be undone.

---

# 🧬 **ACID Properties**

A good database transaction must follow **ACID**:

## 1️⃣ **Atomicity**

All steps succeed or none of them.  
**Example:**  
If transferring money, debit and credit must both happen or neither.

## 2️⃣ **Consistency**

After the transaction, the database must remain in a valid state.  
**Example:**  
Total money in the system remains unchanged.

## 3️⃣ **Isolation**

Concurrent transactions do not affect each other.  
**Example:**  
Two users buying the last movie ticket should not both succeed.

## 4️⃣ **Durability**

Once committed, data survives power failure/crash.  
**Example:**  
Your bank transfer is still valid after the system restarts.

---

# 🔄 **Commit**

`commit()` permanently saves all operations in the current transaction.

### Example:

```java
conn.commit(); // permanently save changes
```

Real-world example:  
After editing a Google Doc, pressing **Save** makes changes permanent.

---

# 🔁 **Rollback**

`rollback()` cancels the current transaction and restores the previous state.

### Example:

```java
conn.rollback(); // undo all changes
```

Real-world example:  
Undoing changes in Photoshop before saving.

---

# 🧷 **Savepoint**

A **Savepoint** marks a checkpoint inside a transaction that you can roll back to.

### Example:

```java
Savepoint sp = conn.setSavepoint("BeforeUpdate");

try {
    stmt.executeUpdate("UPDATE accounts SET balance = balance - 100 WHERE id=1");
    stmt.executeUpdate("UPDATE accounts SET balance = balance + 100 WHERE id=999"); // invalid
} catch (SQLException e) {
    conn.rollback(sp); // roll back only second update
}
```

Real-world analogy:  
A video game checkpoint—you don't restart from the beginning.

---

# 🔌 **Transactions in JDBC**

In JDBC, transactions do **not** automatically start.  
By default, JDBC uses **auto-commit mode** (each SQL statement commits immediately).

## To manually control transactions:

### 1. Disable auto-commit:

```java
conn.setAutoCommit(false);
```

### 2. Execute SQL commands:

```java
stmt.executeUpdate("UPDATE accounts SET balance = balance - 100 WHERE id = 1");
stmt.executeUpdate("UPDATE accounts SET balance = balance + 100 WHERE id = 2");
```

### 3. Commit or rollback:

```java
conn.commit(); // success
// conn.rollback(); // failure
```

---

# 📦 **Batch Requests to Database**

Batching groups multiple SQL operations and sends them to the database in one request.

## Benefits:

- Faster (reduces network round trips)
    
- Efficient for bulk inserts or updates
    

### Example: inserting 1000 employees

```java
PreparedStatement ps = conn.prepareStatement(
    "INSERT INTO employees(name, salary) VALUES(?, ?)"
);

for (int i = 1; i <= 1000; i++) {
    ps.setString(1, "Employee_" + i);
    ps.setDouble(2, 3000 + i);
    ps.addBatch();   // add to batch
}

int[] results = ps.executeBatch();  // send all at once
```

### Real-world example:

Instead of visiting the post office 1000 times to mail letters,  
you bring **all 1000 letters in one batch**. Much faster.

---

# 🗃 **DatabaseMetaData**

`DatabaseMetaData` provides **information about the database and its capabilities**.

### How to obtain:

```java
DatabaseMetaData meta = conn.getMetaData();
```

### What you can retrieve:

- Database product name & version
    
- Driver name & version
    
- Supported SQL features
    
- List of tables
    
- List of columns
    
- Supported transactions
    

### Example:

```java
String product = meta.getDatabaseProductName();
System.out.println(product);
```

### Real-world usage:

- Auto-generating ORM classes
    
- Checking database compatibility
    
- Building database admin tools
    

---

# 📊 **ResultSetMetaData**

Provides information about the **columns in a ResultSet**.

### How to obtain:

```java
ResultSet rs = stmt.executeQuery("SELECT id, name, salary FROM employees");
ResultSetMetaData meta = rs.getMetaData();
```

### Example: printing column info

```java
int columnCount = meta.getColumnCount();

for (int i = 1; i <= columnCount; i++) {
    System.out.println(meta.getColumnName(i) + " - " + meta.getColumnTypeName(i));
}
```

### Real-world usage:

- Building dynamic UI tables
    
- Creating export-to-Excel or CSV tools
    
- Debugging unknown result sets
    
- Generic DAO implementations
    

---

# 🧠 **Real World Example: Salary Transfer with Transaction + Savepoint**

```java
try {
    conn.setAutoCommit(false);

    // Step 1: withdraw
    stmt.executeUpdate("UPDATE accounts SET balance = balance - 500 WHERE id = 10");

    Savepoint mid = conn.setSavepoint("AfterWithdraw");

    // Step 2: deposit (possibly fails)
    stmt.executeUpdate("UPDATE accounts SET balance = balance + 500 WHERE id = 999"); // nonexistent

    conn.commit(); // if everything succeeds

} catch (SQLException e) {

    // rollback only deposit
    conn.rollback(mid);
    conn.commit();

} finally {
    conn.setAutoCommit(true);
}
```

### Outcome:

- Withdrawal happens
    
- Deposit fails
    
- Database returns to the point after withdrawal
    

---

# 🎯 **Quick Summary**

|Topic|Description|
|---|---|
|**Transaction**|A group of SQL operations treated as one unit|
|**ACID**|Atomicity, Consistency, Isolation, Durability|
|**commit()**|Saves changes permanently|
|**rollback()**|Undoes all transaction changes|
|**Savepoint**|Marks checkpoints inside transactions|
|**Transaction in JDBC**|Requires disabling auto-commit|
|**Batch Requests**|Send multiple SQL operations together for efficiency|
|**DatabaseMetaData**|Information about DB structure & capabilities|
|**ResultSetMetaData**|Information about columns in a query result|
