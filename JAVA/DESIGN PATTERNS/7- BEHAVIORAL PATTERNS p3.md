# 🧠 **1. Interpreter Pattern**

## ✔ Declarative Definition

> Define a **mini-language** grammar and create an interpreter that evaluates expressions in that language.

Interpreter is used to process **structured text**, **commands**, or **expressions** by building a syntax tree and evaluating it.

---

# 🎯 **Why Use Interpreter?**

Use it when:

- You have a **simple grammar** (NOT a huge programming language)
    
- You want to parse & evaluate expressions
    
- You want to represent rules in a **tree-like structure**
    
- You frequently need to extend grammar
    
- Behavior varies depending on rule types
    

---

# 🧰 **Real-World Scenarios**

### 1. Filtering logs:

```
level == "ERROR" AND date > "2023-01-01"
```

### 2. Rule engines:

```
customer.age > 18 AND customer.country == "US"
```

### 3. Mathematical parsers:

```
3 + 5 * 2
```

### 4. Simple scripting in games:

```
if player.health < 20 → spawn health_pack
```

---

# ▶️ **Interpreter Pattern — Java Example (Simple Math Expression)**

We build a grammar:

```
Expression → Number | AddExpression | SubExpression
```

### Expression interface

```java
interface Expression {
    int interpret();
}
```

### Terminal expression: Number

```java
class Number implements Expression {
    private int value;

    public Number(int value) {
        this.value = value;
    }

    public int interpret() {
        return value;
    }
}
```

### Non-terminal: Add

```java
class Add implements Expression {
    private Expression left, right;

    public Add(Expression left, Expression right) {
        this.left = left;
        this.right = right;
    }

    public int interpret() {
        return left.interpret() + right.interpret();
    }
}
```

### Non-terminal: Subtract

```java
class Sub implements Expression {
    private Expression left, right;

    public Sub(Expression left, Expression right) {
        this.left = left;
        this.right = right;
    }

    public int interpret() {
        return left.interpret() - right.interpret();
    }
}
```

### Usage

```java
Expression expr = new Add(
    new Number(10),
    new Sub(new Number(5), new Number(2))
);

System.out.println(expr.interpret()); // 10 + (5 - 2) = 13
```

---

# 👍 Pros

- Easy to extend grammar (add new Expression classes)
    
- Clear OO representation of grammar
    
- Great for rule engines or DSLs
    

# 👎 Cons

- Slow for large grammars
    
- Hard to maintain for big languages
    
- Requires many small classes
    

---

# 🟦 **2. Mediator Pattern**

## ✔ Declarative Definition

> Encapsulate object communication in a **central mediator** so objects don’t communicate directly.

### Mediator = **traffic controller**

All components talk **only to the mediator**, not to each other.

---

# 🎯 **Why Use Mediator?**

Use it when:

- Many objects interact in complex ways
    
- Want to eliminate **tight coupling** among components
    
- Want to avoid a spider web of object references
    
- Need event-driven communication
    

---

# 🧰 **Real-World Scenarios**

### 1. Chat Room

Users don’t message each other directly → messages go through a **ChatMediator**.

### 2. Air Traffic Control

Planes do not talk to each other → only via the **control tower**.

### 3. UI frameworks (buttons, dialogs, panels)

UI components communicate via controller/mediator.

### 4. Event systems

Modules publish events to mediator → mediator notifies listeners.

---

# ▶️ **Mediator — Java Example (Chat Room)**

### Mediator Interface

```java
interface ChatMediator {
    void sendMessage(String message, User user);
    void addUser(User user);
}
```

### Concrete Mediator

```java
class ChatRoom implements ChatMediator {
    private List<User> users = new ArrayList<>();

    public void addUser(User user) { users.add(user); }

    public void sendMessage(String message, User fromUser) {
        for (User u : users) {
            if (u != fromUser) {
                u.receive(message);
            }
        }
    }
}
```

### User Class

```java
class User {
    private ChatMediator mediator;
    private String name;

    public User(ChatMediator mediator, String name) {
        this.mediator = mediator;
        this.name = name;
    }

    public void send(String message) {
        System.out.println(name + " sends: " + message);
        mediator.sendMessage(message, this);
    }

    public void receive(String message) {
        System.out.println(name + " received: " + message);
    }
}
```

### Usage

```java
ChatMediator mediator = new ChatRoom();
User u1 = new User(mediator, "Ahmed");
User u2 = new User(mediator, "Ali");
User u3 = new User(mediator, "Sara");

mediator.addUser(u1);
mediator.addUser(u2);
mediator.addUser(u3);

u1.send("Hello everyone!");
```

---

# 👍 Pros

- Reduces tight coupling
    
- Improves maintainability
    
- Centralized control
    
- Easy to modify interactions
    
- Perfect for UI events, networking, chat apps
    

# 👎 Cons

- Mediator can become **God Object** if bloated
    
- Too many responsibilities in mediator
    

---

# 🔥 **INTERPRETER vs MEDIATOR — When to Use Which?**

|Pattern|Purpose|Real-World|
|---|---|---|
|**Interpreter**|Parse and evaluate expressions|Math interpreters, rules engines, DSLs|
|**Mediator**|Manage communication between objects|UI systems, chat rooms, air traffic control|

### Simple Summary:

- **Interpreter = understand commands**
    
- **Mediator = coordinate components**
    

---

# 🎯 FINAL SUMMARY

### ✔ Interpreter

- Defines a grammar
    
- Evaluates expressions
    
- Great for rules, filters, expressions, DSLs
    
- Uses syntax trees
    

### ✔ Mediator

- Central communication hub
    
- Reduces coupling
    
- Used in UI, networking, chat, event-driven systems
    
- Each object communicates through the mediator
    
