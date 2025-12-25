# 🧠 **1. What Is the DRY Principle?**

**DRY = Don’t Repeat Yourself**

Formal definition from _The Pragmatic Programmer_:

> **Every piece of knowledge must have a single, unambiguous, authoritative representation in a system.**

In simple terms:

👉 **Don’t duplicate logic**  
👉 **Don’t duplicate data**  
👉 **Don’t duplicate business rules**  
👉 **Don’t duplicate structure**

DRY aims to eliminate:

- duplicated code
    
- duplicated ideas
    
- duplicated logic
    
- duplicated configuration
    

---

# 🎯 **2. Why Use DRY?**

Duplicated logic is **dangerous** because:

- If the logic changes → you must update it in many places
    
- You will forget one copy → bugs
    
- Code becomes harder to maintain
    
- Code becomes harder to understand
    

DRY prevents these problems by consolidating logic into **one place**.

---

# 🌟 **3. Benefits of DRY**

### ✔ 1. Lower maintenance cost

Fix code in **one place** instead of many.

### ✔ 2. Fewer bugs

No inconsistent logic.

### ✔ 3. Cleaner structure

More modular, organized design.

### ✔ 4. Better reusability

Common functionality becomes reusable.

### ✔ 5. Easier onboarding

Developers understand consistent code faster.

### ✔ 6. Predictable behavior

No hidden duplicates.

### ✔ 7. Scalability

Cleaner code evolves easier.

---

# 🔧 **4. How to Apply DRY (Practical Methods)**

### ✔ 1. Extract repeated logic into reusable functions

### ✔ 2. Use common abstractions (classes, helpers, services)

### ✔ 3. Use inheritance OR composition (prefer composition)

### ✔ 4. Centralize validation and business rules

### ✔ 5. Use configuration files to avoid repeating constants

### ✔ 6. Apply DRY to:

- SQL queries
    
- API routes
    
- Exception handling
    
- DTO mapping
    
- Logging
    
- Constants/Strings
    

### ✔ 7. Avoid repeating magic numbers / strings

Instead:

```java
private static final int MAX_RETRIES = 3;
```

### ✔ 8. Don’t write the same comment or documentation in multiple places

(Use self-documenting code.)

---

# 🧨 **5. Contrasting Concepts: WET & AHA**

To understand DRY better, you must know its opposite philosophies.

---

## ❌ **WET – “Write Everything Twice” / “We Enjoy Typing”**

This is the anti-pattern of DRY.

WET code occurs when:

- developers copy–paste code
    
- same logic appears in multiple classes
    
- small variations of the same function appear everywhere
    

WET leads to:

- bugs
    
- duplication
    
- high maintenance
    
- inconsistencies
    

---

## ⚡ **AHA – “Avoid Hasty Abstraction”**

AHA is a complement to DRY:

> **Avoid premature abstraction.  
> Duplication is better than wrong abstraction.**

Often developers try too early to apply DRY and create complex abstractions.

AHA says:

- Don’t abstract too early
    
- Let duplication happen first
    
- When patterns emerge → _then_ abstract
    

AHA protects you from:

- overengineering
    
- unnecessary inheritance
    
- complex hierarchies
    

---

### ✔ DRY + AHA Together

- **DRY**: eliminate duplication
    
- **AHA**: only abstract when it's safe & proven
    
- **KISS**: keep abstraction simple
    

Used together, they produce clean design.

---

# 📚 **6. Case Studies of DRY**

---

## 🧩 **Case Study 1 – Business Rule Duplication**

### ❌ Bad (Duplication)

```java
if (age < 18) throw new IllegalArgumentException();
```

This appears in:

- UserService
    
- RegistrationService
    
- AccountService
    

If rule changes → must update everywhere.

### ✔ DRY Version

```java
public class AgeValidator {
    public static void validate(int age) {
        if (age < 18) throw new IllegalArgumentException();
    }
}
```

Then reuse:

```java
AgeValidator.validate(user.getAge());
```

---

## 🧩 **Case Study 2 – Duplicate Tax Calculation**

### ❌ Bad

```java
double tax = price * 0.15;
```

Copied in multiple services.

### ✔ DRY Version

```java
public class TaxCalculator {
    public static final double RATE = 0.15;

    public static double calculate(double price) {
        return price * RATE;
    }
}
```

Now all modules use the same logic.

---

## 🧩 **Case Study 3 – Duplicate SQL Queries**

### ❌ Bad

```sql
SELECT id, name FROM users WHERE id = ?
```

Repeated in multiple DAOs.

### ✔ DRY Version

Create a single repository class or constant.

---

## 🧩 **Case Study 4 – Repeated Mapping Logic**

### ❌ Bad

```java
UserDto dto = new UserDto();
dto.setName(user.getName());
dto.setEmail(user.getEmail());
```

Appears in many controllers.

### ✔ DRY Version

```java
class UserMapper {
    static UserDto map(User user) { ... }
}
```

---

# 💻 **7. Java Code Examples Demonstrating DRY**

---

## ✔ Example 1 — Removing Duplicate Validation

### ❌ Bad

```java
public boolean isValid(User user) {
    return user.getName() != null && user.getName().length() > 3;
}

public boolean isValid(Employee emp) {
    return emp.getName() != null && emp.getName().length() > 3;
}
```

Same validation logic repeated.

### ✔ DRY Version

```java
public class NameValidator {
    public static boolean isValidName(String name) {
        return name != null && name.length() > 3;
    }
}
```

Usage:

```java
if (!NameValidator.isValidName(user.getName())) ...
```

---

## ✔ Example 2 — DRY Function Extraction

### ❌ Bad

```java
int area1 = width * height;
int area2 = w * h;
```

### ✔ DRY Version

```java
int area(int w, int h) {
    return w * h;
}
```

---

## ✔ Example 3 — Avoiding Repeated Error Messages

### ❌ Bad

```java
throw new RuntimeException("Invalid ID");
throw new RuntimeException("Invalid ID");
```

### ✔ DRY Version

```java
private static final String INVALID_ID = "Invalid ID";

throw new RuntimeException(INVALID_ID);
```

---

## ✔ Example 4 — Removing Duplicate Conditions

### ❌ Bad

```java
if (user.isActive() && user.hasPaid()) login();
...
if (user.isActive() && user.hasPaid()) showDashboard();
```

### ✔ DRY Version

```java
private boolean canAccess(User user) {
    return user.isActive() && user.hasPaid();
}
```

---

# 🧭 **Final Summary**

### 🧠 DRY Principle:

- **Don’t Repeat Yourself**
    
- One source of truth
    
- Avoid duplicate logic/data/config
    

### 🌟 Benefits:

- Less bugs
    
- Easier maintenance
    
- Cleaner structure
    
- Better reusability
    
- Faster development
    

### ⚡ How to apply:

- extract common logic
    
- use shared utilities
    
- follow AHA (wait before abstracting)
    
- avoid copy–paste
    
- centralize business rules
    

### 🔥 Related Concepts:

- **WET** → duplication anti-pattern
    
- **AHA** → avoid premature abstraction
    

Together with KISS and YAGNI, DRY creates **clean, maintainable, well-structured Java systems.**
