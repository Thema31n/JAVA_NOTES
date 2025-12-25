# 🎴 **GoF Design Patterns – Overview**

The **Gang of Four (GoF)** patterns come from the 1994 book _“Design Patterns: Elements of Reusable Object-Oriented Software.”_  
They introduced **23 classic design patterns** grouped into **3 categories**:

---

# 🧱 **1. Creational Patterns**

> **Purpose:** How to create objects in a flexible, reusable way.

Creational patterns help you **abstract object creation**, avoid direct constructors, and support different creation mechanisms.

### ✔ **1. Singleton**

Ensures only _one instance_ of a class exists.

### ✔ **2. Factory Method**

Subclasses decide which object to create.

### ✔ **3. Abstract Factory**

Creates _families_ of related objects without specifying concrete classes.

### ✔ **4. Builder**

Constructs complex objects _step-by-step_.

### ✔ **5. Prototype**

Creates new objects by _cloning existing ones_.

---

# 🧠 **2. Structural Patterns**

> **Purpose:** How to compose objects and classes to form larger structures.

Structural patterns help organize components to work together while keeping systems flexible.

### ✔ **1. Adapter**

Converts one interface into another that clients expect.

### ✔ **2. Bridge**

Separates abstraction from implementation so they can evolve independently.

### ✔ **3. Composite**

Treats individual objects and groups of objects _uniformly_.

### ✔ **4. Decorator**

Adds behavior dynamically without modifying the original class.

### ✔ **5. Facade**

Provides a simplified interface to a complex subsystem.

### ✔ **6. Flyweight**

Shares objects efficiently to reduce memory cost.

### ✔ **7. Proxy**

Controls access to an object; can add lazy loading, caching, etc.

---

# 🔄 **3. Behavioral Patterns**

> **Purpose:** How objects communicate and share responsibilities.

Behavioral patterns define how objects **interact**, **delegate**, and **divide work**.

### ✔ **1. Chain of Responsibility**

Passes a request through a chain until someone handles it.

### ✔ **2. Command**

Encapsulates actions as objects (undo/redo, queues).

### ✔ **3. Interpreter**

Defines grammar and interprets sentences (rare).

### ✔ **4. Iterator**

Provides sequential access to elements without exposing underlying structure.

### ✔ **5. Mediator**

Central controller to manage object interactions.

### ✔ **6. Memento**

Save/restore an object's state (undo functionality).

### ✔ **7. Observer**

Notifies multiple objects automatically when a subject changes.

### ✔ **8. State**

Changes behavior based on internal state.

### ✔ **9. Strategy**

Select algorithm/behavior at runtime.

### ✔ **10. Template Method**

Defines the algorithm structure; subclasses fill the steps.

### ✔ **11. Visitor**

Adds new operations without modifying existing classes.

---

# 🎯 **Final Summary**

### **Creational:**

How to create objects (Singleton, Factory, Builder…)

### **Structural:**

How to compose/object structures (Adapter, Composite, Decorator, Facade…)

### **Behavioral:**

How objects communicate (Observer, Strategy, Command, Chain of Responsibility…)

These patterns provide:

✔ reusable solutions  
✔ common vocabulary  
✔ improved flexibility  
✔ better maintainability
