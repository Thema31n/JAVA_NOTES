### **1. Pure Functions**

A pure function:

- Depends only on its input arguments.
    
- Produces no side effects (no external state modification, no I/O).
    
- Always returns the same output for the same input.
    

This makes systems easier to reason about, test, and parallelize.

---

### **2. Immutability**

Data is never modified after creation. Instead of mutating values, new values are derived from existing ones.

Benefits include:

- Simplified reasoning about state.
    
- Thread-safe code without locks.
    
- Reduced unintended interactions between components.
    

---

### **3. First-Class and Higher-Order Functions**

- **First-class functions**: Functions are treated as values that can be passed, stored, or returned.
    
- **Higher-order functions**: Functions that take other functions as arguments or return functions.
    

This allows abstractions such as `map`, `filter`, `reduce`, and custom pipelines.

---

### **4. Function Composition**

The process of building complex behavior by combining simple functions.

Example: `f(g(x))`.

Composition supports modular, declarative designs.

---

### **5. Declarative Programming Style**

Functional code focuses on _what_ to compute, not _how_ to compute it.

The developer specifies transformations and relationships rather than control flow.

---

### **6. Expressions Over Statements**

Functional programs evaluate expressions (which return values) rather than statements (which perform actions).

This supports referential transparency and easier debugging.

---

### **7. Recursion as a Primary Control Structure**

Since mutable loops contradict immutability, recursion is used for iteration.

Tail-call optimization (TCO) is often critical in functional languages.

---

### **8. Referential Transparency**

An expression can be replaced by its value without affecting the program's behavior.

This property arises from purity and enables:

- Safe refactoring
    
- Aggressive compiler optimizations
    
- Simplified formal reasoning
    

---

### **9. Lazy Evaluation (in some FP languages)**

Computations are deferred until their results are needed.

Advantages include:

- Performance optimizations
    
- The ability to work with infinite data structures
    

---

### **10. Type Systems and Algebraic Data Types (ADTs)**

Strong, static type systems are common in functional languages.

Key constructs include:

- **Product types** (e.g., tuples, records)
    
- **Sum types** (e.g., enums, variants, `Either`, `Option/Maybe`)
    
- **Pattern matching** for decomposing ADTs declaratively
    

These features enhance correctness and eliminate entire classes of runtime errors.

---

### **11. Monads, Functors, and Applicatives**

Abstractions for handling effects while remaining pure:

- **Functors**: Apply a function over wrapped values.
    
- **Applicatives**: Apply wrapped functions to wrapped values.
    
- **Monads**: Sequence computations with context (e.g., state, errors, I/O) while retaining purity.
    

These structures encapsulate effects without mutating program state.
