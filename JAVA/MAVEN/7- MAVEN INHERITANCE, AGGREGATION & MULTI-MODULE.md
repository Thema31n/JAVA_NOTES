## 1. Maven Inheritance

### What Is Maven Inheritance?

**Inheritance** allows one Maven project (**child**) to **reuse configuration** from another (**parent**).

> Think of inheritance as **sharing rules**, not building multiple projects.

### What Can Be Inherited?

- Dependency versions
    
- Plugin versions & configuration
    
- Properties
    
- Build settings
    

---

### Real Example: Parent POM

**parent/pom.xml**

```xml
<project>
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.company</groupId>
  <artifactId>parent</artifactId>
  <version>1.0.0</version>
  <packaging>pom</packaging>

  <properties>
    <java.version>17</java.version>
    <spring.version>6.1.5</spring.version>
  </properties>

  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${spring.version}</version>
      </dependency>
    </dependencies>
  </dependencyManagement>
</project>
```

---

### Child Project Using Inheritance

```xml
<project>
  <parent>
    <groupId>com.company</groupId>
    <artifactId>parent</artifactId>
    <version>1.0.0</version>
  </parent>

  <artifactId>user-service</artifactId>
</project>
```

✅ No need to repeat versions  
✅ Central control  
✅ Cleaner POMs

---

## 2. Maven Project Aggregation

### What Is Aggregation?

**Aggregation** means:

> One Maven project builds **multiple modules together**.

Aggregation is about **building**, not sharing config.

---

### Aggregator POM Example

```xml
<project>
  <groupId>com.company</groupId>
  <artifactId>enterprise-app</artifactId>
  <version>1.0.0</version>
  <packaging>pom</packaging>

  <modules>
    <module>common</module>
    <module>user-service</module>
    <module>order-service</module>
  </modules>
</project>
```

Command:

```bash
mvn clean install
```

✔️ Builds all modules  
✔️ Single command  
✔️ Correct order

---

## 3. Project Inheritance vs Project Aggregation

|Aspect|Inheritance|Aggregation|
|---|---|---|
|Purpose|Share configuration|Build multiple projects|
|Uses `<parent>`|✅ Yes|❌ No|
|Uses `<modules>`|❌ No|✅ Yes|
|Triggers reactor|❌ No|✅ Yes|
|Packaging|Any|`pom`|

👉 **Most real projects use BOTH together**

---

## 4. Maven Reactor (VERY IMPORTANT)

### What Is the Maven Reactor?

The **Maven Reactor** is the **engine** that:

- Detects all modules
    
- Calculates build order
    
- Builds modules in memory
    
- Stops on failure
    

It is automatically activated when Maven sees:

```xml
<modules>
```

---

### Real Reactor Example

Structure:

```
enterprise-app/
 ├── common/
 ├── user-service/
 └── order-service/
```

Dependencies:

```
user-service → common
order-service → common
```

Run:

```bash
mvn clean install
```

Reactor order:

```
common
user-service
order-service
```

✔️ No manual sequencing  
✔️ No need to install first

---

## 5. Project Variables (Maven Properties)

### What Are Project Variables?

Reusable values defined in `<properties>`.

---

### Example

```xml
<properties>
  <java.version>17</java.version>
  <log4j.version>2.23.1</log4j.version>
</properties>
```

Usage:

```xml
<version>${log4j.version}</version>
```

---

### Built-in Maven Properties

|Property|Meaning|
|---|---|
|`${project.groupId}`|Group ID|
|`${project.version}`|Version|
|`${basedir}`|Project root|
|`${reactorProjects}`|All modules in build|

---

## 6. Dependencies Between Modules (REAL EXAMPLE)

### `common` Module

```xml
<artifactId>common</artifactId>
```

### `user-service` Depends on `common`

```xml
<dependency>
  <groupId>com.company</groupId>
  <artifactId>common</artifactId>
  <version>${project.version}</version>
</dependency>
```

### What Reactor Does

- Builds `common` first
    
- Shares compiled output
    
- No need to install `common` separately
    

---

## 7. Practical Exercise (Step-by-Step)

### Goal

Create a real multi-module Maven project.

---

### Step 1: Create Parent Project

```bash
mvn archetype:generate -DartifactId=enterprise-app
```

Edit POM:

```xml
<packaging>pom</packaging>
```

---

### Step 2: Create Modules

```bash
mvn archetype:generate -DartifactId=common
mvn archetype:generate -DartifactId=user-service
```

---

### Step 3: Register Modules

```xml
<modules>
  <module>common</module>
  <module>user-service</module>
</modules>
```

---

### Step 4: Add Parent Reference to Modules

```xml
<parent>
  <groupId>com.company</groupId>
  <artifactId>enterprise-app</artifactId>
  <version>1.0.0</version>
</parent>
```

---

### Step 5: Build Using Reactor

```bash
mvn clean install
```

✔️ All modules built  
✔️ Correct order  
✔️ Shared versions

---

## 8. Advantages of Maven Multi-Module Projects

### ✅ Advantages

- Centralized dependency management
    
- Consistent versions
    
- Automatic build order (reactor)
    
- Faster local development
    
- Scales well for enterprise systems
    

---

## 9. Disadvantages of Maven Multi-Module Projects

### ❌ Disadvantages

- Higher learning curve
    
- More complex structure
    
- Large builds can be slow
    
- Tight coupling if poorly designed
    

---

## 10. Import Maven Multi-Module Project into IntelliJ

### Correct Way (IMPORTANT)

1. Open **IntelliJ IDEA**
    
2. **File → Open**
    
3. Select **parent project folder**
    
4. IntelliJ detects `pom.xml`
    
5. Choose **Open as Maven Project**
    

---

### IntelliJ Result

- Parent shown as root
    
- Each module imported separately
    
- Reactor build supported
    
- Dependencies resolved automatically
    

---

### Common IntelliJ Fixes

- If modules missing → **Reimport Maven**
    
- If wrong JDK → Set in _Project Structure_
    
- If build fails → Check parent POM
    

---

## FINAL SUMMARY (CONSOLIDATED)

### Maven Inheritance

- Shares configuration
    
- Uses parent POM
    
- Reduces duplication
    

### Maven Aggregation

- Groups multiple modules
    
- Uses `<modules>`
    
- Enables multi-module builds
    

### Maven Reactor

- Build engine
    
- Determines order
    
- Shares module output
    

### Module Dependencies

- Declared normally
    
- Reactor resolves order
    
- No manual installs needed
    

### Multi-Module Projects

- Powerful & scalable
    
- More complex
    
- Best for large systems
    

### IntelliJ Integration

- Import via parent POM
    
- Full Maven & reactor support
    
- Industry-standard workflow
    
