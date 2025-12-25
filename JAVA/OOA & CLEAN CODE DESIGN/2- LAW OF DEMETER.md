# 🧩 **1. What Is the Law of Demeter?**

**Law of Demeter (LoD)** is also known as:

- **“Principle of Least Knowledge”**
    
- **“Don’t Talk to Strangers” rule**
    
- **“Only talk to your immediate friends”**
    

**Definition:**

> A module/class/method should only communicate with objects it directly knows.  
> You should not call methods on objects returned by other methods.

Meaning:

❌ **Don’t chain too many dots**  
❌ **Don’t navigate deep into object graphs**  
❌ **Don’t break encapsulation by digging into internal structures**

In simple words:

👉 **A class should only know about:**

1. itself
    
2. its own fields
    
3. its own method parameters
    
4. objects it creates
    
5. objects injected into it (dependencies)
    

Everything else is a “stranger.”

---

# 🛑 **2. What Violates the Law of Demeter?**

The classic violation looks like this:

```java
customer.getOrder().getAddress().getStreet().length();
```

This is called **train wreck code**:

```
a.getB().getC().getD()
```

It becomes fragile and tightly coupled.

---

# 🎯 **3. Goals of the Law of Demeter**

The primary goal of LoD is **reduce coupling** and **increase encapsulation**.

### ✔ 1. Reduce knowledge about internal structures

Classes shouldn’t break encapsulation by traversing other objects.

### ✔ 2. Increase maintainability

Changes to internal models won’t break unrelated classes.

### ✔ 3. Improve readability

Short, clean, behavioral methods.

### ✔ 4. Encourage delegation

Objects should ask others to do things, not manipulate their data.

### ✔ 5. Grow behavior in domain objects

Avoid anemic domain models.

---

# 🧠 **4. Acceptable Objects You Can Call Methods On**

According to LoD, a method `m` in a class `C` may only call methods on:

### ✔ 1. `this`

The object itself.

### ✔ 2. Method parameters

```java
void pay(Order order) {
    order.process();
}
```

### ✔ 3. Fields of the class

(objects owned by this class)

### ✔ 4. Objects created inside the method

```java
var tmp = new Calculator();
tmp.add(5);
```

### ✔ 5. Global singleton (rare exception, but allowed technically)

---

# 🚫 **5. What You Should NOT Do**

❌ Calling methods on returned objects of other methods:

```java
a.getB().getC().calculate();
```

❌ Accessing internal data structures:

```java
user.getProfile().getAddress().getCity();
```

❌ Navigating object maps/lists deeply.

---

# 🧊 **6. Exceptions to the Law of Demeter**

LoD is a **guideline**, not an absolute rule.

### ✔ 1. Value Objects (immutable, simple objects)

Chaining on value objects is OK:

```java
LocalDate.now().plusDays(5).getDayOfWeek();
```

Because:

- They hold no domain behavior
    
- You don’t violate encapsulation
    

---

### ✔ 2. Fluent APIs

Chaining in builder/fluent APIs is designed that way:

```java
User user = UserBuilder.create()
        .name("Ahmed")
        .email("a@test.com")
        .build();
```

This is _not_ an LoD violation because:

- The builder is designed to chain
    
- All methods return the SAME semantic object
    

---

### ✔ 3. Data Transfer Objects (DTOs)

DTOs are **data structures**, not real objects.

Sometimes short chains are acceptable:

```java
req.getUser().getId();
```

BUT avoid long chains.

---

### ✔ 4. Aggregates (DDD)

Inside a domain aggregate, entities can reference each other.  
This is allowed because the aggregate is a consistency boundary.

---

### ✔ 5. Framework-owned objects

Example:  
Spring Security, ORM entities, JSON, Map, List, etc.

Navigating them is often fine.

---

# ⚠️ **7. Pros & Cons of the Law of Demeter**

---

## 🌟 **Pros**

### ✔ 1. Lower coupling

Changes stay contained.  
Less ripple effect.

### ✔ 2. Higher encapsulation

Classes control access to their data.

### ✔ 3. Easier to understand code

Behavior is exposed clearly.

### ✔ 4. Better domain modeling

More behavior lives in domain classes instead of services.

### ✔ 5. Encourage clear boundaries

Classes communicate through well-defined methods.

---

## ⚠️ **Cons / Misunderstandings**

### ❌ 1. Over-applying can create too many wrapper methods

You might end up writing:

```java
getOrderStreetName()
getOrderCity()
getOrderCountry()
```

Too many pass-through methods.

---

### ❌ 2. Can conflict with OO simplicity

Sometimes chaining **is simpler** than extra methods.

---

### ❌ 3. May encourage micromanagement

Developers may fear small violations and complicate code unnecessarily.

---

### ❌ 4. Not always appropriate in simple CRUD applications

For CRUD, deep chains may be acceptable.

---

### ❌ 5. Over-encapsulation can hide necessary data

Sometimes you _want_ to traverse structures (DTOs, responses).

---

# 🧑‍💻 **8. Code Examples**

---

## ❌ **Bad Example (LoD Violation – Train Wreck Code)**

```java
String city = order.getCustomer().getAddress().getCity();
```

Too many dots.

---

## ✔ Good Example (Ask, Don’t Get)

```java
String city = order.getCustomerCity();
```

OR better:

```java
order.sendToCity();
```

---

## ❌ Another Violation

```java
cart.getItems().get(0).getProduct().getPrice();
```

---

## ✔ Fix via delegation

```java
cart.getItemPrice(0);
```

OR domain behavior:

```java
cart.totalPrice();
```

---

## ❌ Violation example with parameters

```java
void process(User u) {
    log(u.getProfile().getAddress().getCountry());
}
```

---

## ✔ Fix: Add behavior to User

```java
void process(User u) {
    log(u.getCountry());
}
```

User class:

```java
public String getCountry() {
    return profile.getAddress().getCountry();
}
```

---

## ✔ Exception Example — Fluent API

```java
builder.setName("Ahmed").setAge(20).build();
```

This is **allowed**.

---

## ✔ Exception Example — Value Object Chains

```java
LocalDate.now().plusDays(3).getYear();
```

Safe because LocalDate is immutable.

---

# 🎯 **Final Summary**

### The Law of Demeter states:

> **Only interact with your direct collaborators.  
> Avoid deep navigation through objects.**

It promotes:

- low coupling
    
- high encapsulation
    
- clean domain logic
    
- maintainable code
    

Exceptions exist (DTOs, builders, value objects).

Over-enforcing LoD can cause too much boilerplate, but respecting it in core logic leads to **cleaner, safer, more robust architecture**.
