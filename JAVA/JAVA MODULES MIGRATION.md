
# **1. What Is Migration to JPMS?**

Migration is the process of moving an existing **classpath-based** Java application into a **module-aware** project that uses:

- `module-path`
    
- `module-info.java`
    
- Explicit dependencies
    
- Strong encapsulation
    

Migration can be done **incrementally**, starting from a single module.

---

# **2. Migration Algorithm (High-Level)**

This is the general algorithm used during modularization:

1. **Analyze dependencies**  
    → Detect module boundaries & cyclic dependencies
    
2. **Identify module candidates**  
    → Group packages & libraries
    
3. **Resolve split packages**  
    → Required by JPMS (no duplicate pkg names in two modules)
    
4. **Check for illegal reflection**  
    → Frameworks (Jackson/Hibernate) require `opens`
    
5. **Create module-info.java gradually**  
    → Start from simplest modules
    
6. **Fix migration issues**  
    → Missing exports, deep reflection issues, patching modules
    
7. **Add service usage if needed**  
    → Replace ServiceLoader setups
    
8. **Move to module-path from classpath**  
    → Test app progressively
    
9. **Refactor for modular boundaries**  
    → Remove cyclic deps, internal APIs, unneeded exports
    
10. **Finalize with jlink / jmod** (optional)  
    → Build custom runtime images
    

---

# **3. Detailed Migration Steps**

Below is the industry-standard step-by-step modular migration workflow.

### **Step 1 — Identify your dependencies**

- List all internal modules
    
- List external libraries
    
- Check JAR version compatibility
    

### **Step 2 — Run dependency analysis (`jdeps`)**

Use `jdeps` to map existing relationships.

### **Step 3 — Detect illegal reflection**

Check which classes are accessed reflectively by:

- Jackson
    
- Hibernate
    
- CDI
    
- Frameworks using reflection
    

### **Step 4 — Fix “split packages”**

JPMS forbids packages appearing in more than one module.

### **Step 5 — Start with bottom-most modules**

(Utility libraries, core modules)

### **Step 6 — Add module-info.java incrementally**

Do NOT modularize everything at once.

### **Step 7 — Use command-line fixes (`--add-opens`, `--add-exports`)**

To keep backward compatibility.

### **Step 8 — Migrate top modules last**

Application entry, UI, REST layer.

### **Step 9 — Switch app to module-path**

Update IDE and scripts.

### **Step 10 — Run integration tests**

Ensure all reflective access issues are fixed.

---

# **4. Java Class Dependency Analyzer — `jdeps` Tool**

`jdeps` analyzes JAR/class dependencies.

### **Common usages**

### ✔ Analyze JAR dependencies

```
jdeps myapp.jar
```

### ✔ Analyze module dependencies

```
jdeps --module-path mods --module my.module
```

### ✔ Generate module-info.java suggestions

```
jdeps --generate-module-info out myapp.jar
```

### ✔ Print package-level dependencies

```
jdeps -s myapp.jar
```

### ✔ Find JDK internal API usage

```
jdeps --jdk-internals myapp.jar
```

### `jdeps` helps with:

- Detecting cyclic dependencies
    
- Discovering missing module boundaries
    
- Finding illegal internal API usages
    
- Automatically generating module descriptors
    

It’s the **primary tool** for migration.

---

# **5. Java Deprecated API Scanner — `jdeprscan` Tool**

`jdeprscan` scans for deprecated and **removed** Java APIs.

### Basic usage:

```
jdeprscan myapp.jar
```

### Scan classpath:

```
jdeprscan --class-path libs myapp.jar
```

### Scan source code:

```
jdeprscan --for-removal myapp.jar
```

### What it detects:

- APIs marked **@Deprecated(forRemoval = true)**
    
- Removed APIs in future Java versions
    
- Deprecated JDK modules
    
- Unsafe APIs
    

`jdeprscan` helps ensure your code won’t break in future Java versions.

---

# **6. Migration Strategies**

There are two primary modular migration strategies:

---

# **6.1 Bottom-Up Migration (Recommended)**

Migrate **low-level** modules first.

### How it works:

- Start with smallest modules (utilities, shared libs)
    
- Convert them to named modules
    
- Fix their exports & dependencies
    
- Slowly move upward toward application-level modules
    

### Pros:

✔ Simpler  
✔ Breaks migration into small chunks  
✔ Lower risk  
✔ Avoids large refactoring early

### When to use:

- Large legacy systems
    
- Multi-module projects
    
- Systems with many dependencies
    

This is the **most common** approach.

---

# **6.2 Top-Down Migration**

Start from the **entry module** (main application).

### How it works:

- Modularize the main application
    
- Push encapsulation downward
    
- Gradually modularize dependent libraries
    

### Pros:

✔ Immediate modular entry point  
✔ Useful for mostly monolithic apps

### Cons:

✘ Harder with many dependencies  
✘ Often requires many temporary command-line hacks

### When to use:

- Smaller applications
    
- Systems with minimal external dependencies
    

---

# **7. Combining Both Strategies (Hybrid Approach)**

Often teams use a hybrid model:

- Bottom-up for internal libraries
    
- Top-down for main app modules
    

This ensures:

- Low-level APIs are stable
    
- High-level modules adopt modules gradually
    

---

# **8. Summary **

### ✔ Migration Algorithm

Analyze deps → fix reflection issues → remove split pkgs → add module-info → test → refine.

### ✔ Tools

- `jdeps` → dependency analyzer
    
- `jdeprscan` → deprecated/removed API scanner
    

### ✔ Migration Strategies

- **Bottom-Up** → start with small, low-level modules
    
- **Top-Down** → start from main application
    
- **Hybrid** → combine both
    

### ✔ Recommended migration workflow

1. Run `jdeps`
    
2. Fix split packages
    
3. Modularize utilities
    
4. Add module-info gradually
    
5. Use `--add-opens`, `--add-exports` temporarily
    
6. Migrate main app last
    
7. Test with `--module-path`
    
