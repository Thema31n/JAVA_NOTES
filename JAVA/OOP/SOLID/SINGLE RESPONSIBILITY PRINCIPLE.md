## **Definition**

> _A class should have only **one reason to change**._

Meaning:

- A class should **do one thing**
    
- And **do it well**
    

SRP is the **first** principle of **SOLID**.

---

## ⭐ **What “one reason to change” really means**

A **reason to change** = a **responsibility**.

A responsibility is:

- ❌ NOT a single method
    
- ❌ NOT a small function
    
- ✔ A **role or purpose** of the class
    

A class should have **one purpose**.  
If that purpose changes → the class changes.  
If something **unrelated** changes → the class should **not** change.

---

## ⭐ **Why SRP is Important**

### ✔ Easier Maintenance

Smaller, focused classes → fewer bugs when changing code.

### ✔ Reduced Coupling

Classes depend on fewer things → safer modifications.

### ✔ Easier Testing

One responsibility → simple unit tests.

### ✔ Better Reusability

Focused classes are reusable across projects.

### ✔ Prevents “God Classes”

Avoids large classes doing everything.

---

## ⭐ **Example: SRP Violation (Bad Design)**

```java
class Report {

    void generateReport() {
        // generate report data
    }

    void printReport() {
        // printing logic
    }

    void saveToDatabase() {
        // database logic
    }
}
```

### ❌ Problems

This class has **three responsibilities**:

1. Generating report data
    
2. Printing the report
    
3. Saving the report to the database
    

### ❌ Consequences

- Printing change → class changes
    
- Database change → class changes
    
- Report format change → class changes
    

👉 **Too many reasons to change → SRP violated**

---

## ⭐ **Correct Design Following SRP**

Split responsibilities into **separate classes**:

```java
class ReportGenerator {

    Report generate() {
        return new Report();
    }
}
```

```java
class ReportPrinter {

    void print(Report report) {
        // print logic
    }
}
```

```java
class ReportRepository {

    void save(Report report) {
        // database logic
    }
}
```

### ✔ Benefits

- Printing changes → only `ReportPrinter`
    
- Database changes → only `ReportRepository`
    
- Report logic changes → only `ReportGenerator`
    

Each class has **one responsibility**.

---

## ⭐ **Common SRP Misunderstandings**

### ❌ “A class should have only one method”

Wrong.

A class can have **many methods** as long as they serve **one purpose**.

---

### ❌ “Responsibility = function”

Wrong.

Responsibility is a **high-level reason to change**, not a method.

---

### ✔ Correct Example

```java
class UserValidator {

    boolean validateEmail(String email) {
        return email.contains("@");
    }

    boolean validatePassword(String password) {
        return password.length() >= 8;
    }

    boolean validateUser(User user) {
        return validateEmail(user.getEmail()) &&
               validatePassword(user.getPassword());
    }
}
```

✔ Multiple methods  
✔ One responsibility → **user validation**

---

## ⭐ **How to Detect SRP Violations**

Ask:

> ❓ **“Why would this class change?”**

If the answer has **more than one reason**, SRP is violated.

### Common Violations

- Business logic + UI
    
- Business logic + database access
    
- Saving data + sending notifications
    
- Validation + formatting
    

---

## ⭐ **Real-World Analogy**

A restaurant:

- 👨‍🍳 Chef → cooks
    
- 🧑‍💼 Waiter → serves
    
- 🧹 Cleaner → cleans
    
- 💰 Cashier → handles payments
    

If one person does all → chaos.

Each role = **one responsibility**  
Same rule applies to classes.

---

## ⭐ **SRP Summary Table**

|Concept|Meaning|
|---|---|
|Definition|A class has one reason to change|
|Responsibility|A high-level purpose|
|Goal|Focused, maintainable classes|
|Benefits|Low coupling, easy testing|
|Violation|One class does many unrelated tasks|
|Solution|Split responsibilities into separate classes|
