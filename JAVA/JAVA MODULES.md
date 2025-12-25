# **1. What Are Java Modules?**

A **module** is a strongly-named, self-describing unit of code introduced in **Java 9** as part of:

➡ **JSR 376 — JPMS (Java Platform Module System)**

A module contains:

- Java packages
    
- Resources
    
- A descriptor (`module-info.java`)
    

Modules improve encapsulation, maintainability, and runtime performance.

---

# **2. JSR 376 — Java Platform Module System**

JSR 376 defines:

- How modules are declared
    
- Rules for dependencies
    
- Encapsulation model (exports/opens)
    
- Service provider mechanisms
    
- Module graph verification at compile/run time
    

It modularized the entire JDK.

---

# **3. Goals of Java Modules**

1. **Strong encapsulation**
    
2. **Reliable configuration** (no cyclic dependencies)
    
3. **Improved security**
    
4. **Smaller runtime** (via jlink)
    
5. **Modularized JDK**
    
6. **Better performance**
    
7. **Service-based architecture support**
    

---

# **4. Benefits of Java Modules**

|Benefit|Explanation|
|---|---|
|Strong encapsulation|Internal packages hidden|
|Clear dependency graph|Fewer runtime surprises|
|Performance|Faster startup + reduced footprint|
|Better organization|Explicit APIs|
|Security|Illegal reflection blocked|
|Service loading|Built-in provider mechanism|

---

# **5. Types of Java Modules**

### ✔ **Named modules**

Have a `module-info.java`.

### ✔ **Automatic modules**

JARs on module-path without descriptor.  
Java assigns a module name from the JAR filename.

### ✔ **Unnamed module**

Everything on the **classpath** becomes part of one large fallback module.

---

# **6. Module Directives (module-info.java)**

|Directive|Meaning|
|---|---|
|`module X {}`|Declares a module|
|`requires M;`|Depend on module M|
|`requires transitive M;`|Expose dependency to consumers|
|`requires static M;`|Optional compile-time dependency|
|`exports pkg;`|Make package visible to other modules|
|`opens pkg;`|Open package for reflection (runtime only)|
|`opens pkg to M;`|Open package only to specific module|
|`provides S with Impl;`|Service provider|
|`uses S;`|Service consumer|

---

# **7. JPMS Command-Line Options (Updated Section)**

Below are the **official** and **correct** module-related CLI options.

---

## **7.1 `--module-path`**

Specifies location of modules.

```
java --module-path mods --module my.module/pkg.Main
```

Acts like the classpath but for modules.

---

## **7.2 `--add-reads`**

Force a module to **read** another module even if not declared in `module-info.java`.

```
--add-reads my.module=other.module
```

Useful when:

- Adapting older libraries
    
- Fixing missing `requires` without modifying code
    

---

## **7.3 `--add-exports`**

Export a package **at runtime** even if not exported.

```
--add-exports my.module/com.example.pkg=other.module
```

Used when:

- You need reflective access
    
- A library uses internal packages
    

---

## **7.4 `--add-opens`**

Similar to `--add-exports`, **but enables deep reflection** (e.g., by Gson, Jackson, Hibernate).

```
--add-opens my.module/com.example.pkg=other.module
```

This is often needed for frameworks that rely on reflection.

---

## **7.5 `--add-modules`**

Explicitly add modules to the root module set.

```
--add-modules my.module
```

Also useful for:

- Enabling standard JDK modules
    
- Running automatic modules
    

Example:

```
--add-modules java.sql
```

---

## **7.6 `--list-modules`**

List all observable modules available in the module-path or JDK.

```
java --list-modules
```

Useful for debugging classpath/module-path issues.

---

## **7.7 `--patch-module`**

Allows replacing or augmenting classes in a module.

```
--patch-module my.module=patches/
```

Use cases:

- Hot fixes
    
- Testing
    
- Splitting modules across directories
    
- Adding resources/classes without rebuilding module
    

---

## **7.8 `--illegal-access={permit|warn|deny}`**

Controls access to internal JDK APIs.

|Option|Meaning|
|---|---|
|`permit`|Allow illegal reflective access (Java 9 default)|
|`warn`|Allow but warn|
|`deny`|Completely block access|

Example:

```
--illegal-access=warn
```

Use this during migration.

---

# **8. Creating Custom Modules**

Example project structure:

```
src/
  my.module/
    module-info.java
    mypkg/Main.java
```

### module-info.java

```java
module my.module {
    exports mypkg;
}
```

### Main.java

```java
package mypkg;

public class Main {
    public static void main(String[] args) {
        System.out.println("Hello from my.module!");
    }
}
```

Compile:

```
javac -d out --module-path out $(find src -name "*.java")
```

Run:

```
java --module-path out --module my.module/mypkg.Main
```

---

# **9. Modular Hello World (Two Modules)**

### moduleB/module-info.java

```java
module moduleB {
    exports b;
}
```

### moduleA/module-info.java

```java
module moduleA {
    requires moduleB;
}
```

---

# **10. IntelliJ Setup for Modular Apps**

1. Use **JDK 9+**
    
2. Create folders:  
    `src/moduleName/...`
    
3. Add `module-info.java`
    
4. Ensure build system recognizes module structure
    
5. Run configuration → VM options:
    

```
--module-path out --module moduleA/a.MainA
```

IntelliJ will help with module-path automatically.

---

# **11. Consuming Services in Modules**

### Service interface (in "service" module)

```java
package service;
public interface Greeting {
    String greet();
}
```

### Provider module

```java
module provider {
    provides service.Greeting with providerpkg.GreetingImpl;
    exports providerpkg;
}
```

### Consumer module

```java
module consumer {
    requires provider;
    uses service.Greeting;
}
```

### Code to load service

```java
ServiceLoader<Greeting> loader = ServiceLoader.load(Greeting.class);
for (Greeting g : loader) System.out.println(g.greet());
```

---

# **12. Full Summary**

### ✔ What modules are

Strongly encapsulated units introduced in Java 9.

### ✔ JSR 376 / JPMS

Defines module metadata, rules, dependency resolution.

### ✔ Goals & Benefits

Encapsulation, maintainability, performance, modular JDK, reliable config.

### ✔ Module types

Named, automatic, unnamed.

### ✔ Directives

`requires`, `exports`, `opens`, `uses`, `provides`.

### ✔ Command-line options (updated)

- `--module-path`
    
- `--add-reads`
    
- `--add-exports`
    
- `--add-opens`
    
- `--add-modules`
    
- `--list-modules`
    
- `--patch-module`
    
- `--illegal-access={permit|warn|deny}`
    

### ✔ Custom module creation

Using module-info.java.

### ✔ Modular Hello World

Two-module example.

### ✔ IntelliJ configuration

Module-path setup + run options.

### ✔ Services in modules

Using `uses` / `provides` with ServiceLoader.
