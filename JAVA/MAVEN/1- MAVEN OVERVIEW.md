## 1. Why Use Maven?

Apache Maven is a **build automation and project management tool** mainly used for **Java-based projects**.

### Problems Maven Solves

Before Maven:

- Developers manually managed JAR files
    
- Projects had inconsistent structures
    
- Builds were hard to reproduce
    
- Dependency conflicts were common (“JAR hell”)
    
- Build scripts were complex and hard to maintain
    

### Why Maven Is Used

- Automates **build, test, package, install, deploy**
    
- Manages dependencies automatically
    
- Enforces **standard project structure**
    
- Makes builds **repeatable and portable**
    
- Works well with **CI/CD pipelines**
    
- Ideal for **multi-module enterprise projects**
    

---

## 2. Main Maven Features

### 1. Dependency Management

- Automatically downloads dependencies
    
- Supports **transitive dependencies**
    
- Uses scopes (compile, test, provided, runtime)
    

### 2. Standard Project Structure

- Enforces conventions instead of custom layouts
    
- Makes projects easier to understand
    

### 3. Lifecycle Management

- Predefined build lifecycles
    
- Ensures consistent build behavior
    

### 4. Plugin-Based System

- Every task is performed by plugins
    
- Highly extensible
    

### 5. Repository Management

- Local repository
    
- Maven Central
    
- Remote/private repositories
    

### 6. IDE & Tool Integration

- IntelliJ IDEA, Eclipse, NetBeans
    
- Jenkins, GitHub Actions, GitLab CI
    

---

## 3. Maven Core Concepts (DETAILED)

### 1. Project Object Model (POM)

- Central configuration file (`pom.xml`)
    
- Describes **what the project is and how to build it**
    

### 2. Coordinates

Every Maven artifact is uniquely identified by:

- `groupId` – Organization or package name
    
- `artifactId` – Project name
    
- `version` – Project version
    
- `packaging` – jar, war, pom, etc.
    

### 3. Dependencies

- External libraries required by the project
    
- Maven resolves and downloads them automatically
    

#### Dependency Scopes

|Scope|Description|
|---|---|
|compile|Default, needed at compile and runtime|
|test|Used only for testing|
|provided|Provided by container (e.g., servlet API)|
|runtime|Needed at runtime only|
|system|Explicit local dependency (discouraged)|

---

### 4. Repositories

Repositories store Maven artifacts.

#### Types

- **Local Repository** (`~/.m2/repository`)
    
- **Central Repository** (default online repo)
    
- **Remote Repository** (company/private)
    

---

### 5. Plugins

- Plugins perform actual work
    
- Examples:
    
    - `maven-compiler-plugin`
        
    - `maven-surefire-plugin`
        
    - `maven-war-plugin`
        

---

### 6. Build Lifecycle

- Defines **when things happen**
    
- Includes phases and goals
    

---

## 4. Project Object Model (POM) – In Depth

### Basic Structure

```xml
<project>
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.company</groupId>
  <artifactId>app</artifactId>
  <version>1.0.0</version>
  <packaging>jar</packaging>

  <dependencies>
    ...
  </dependencies>

  <build>
    ...
  </build>
</project>
```

### Important Sections

- `<dependencies>` – Libraries
    
- `<build>` – Plugins & build config
    
- `<properties>` – Variables
    
- `<profiles>` – Environment-specific config
    
- `<modules>` – Multi-module projects
    

---

## 5. Maven Lifecycles

Maven has **3 built-in lifecycles**:

### 1. Default Lifecycle

Used to build and deploy the project

### 2. Clean Lifecycle

Cleans generated files

### 3. Site Lifecycle

Generates project documentation

---

## 6. Maven Phases

### Default Lifecycle Phases

| Phase    | Purpose                    |
| -------- | -------------------------- |
| validate | Validate project structure |
| compile  | Compile source code        |
| test     | Run unit tests             |
| package  | Create JAR/WAR             |
| verify   | Integration tests          |
| install  | Install to local repo      |
| deploy   | Deploy to remote repo      |

👉 Running a phase runs **all previous phases automatically**

---

## 7. Maven Goals

- A **goal** is a single task
    
- Goals are executed by plugins
    

### Examples

```bash
mvn compiler:compile
mvn surefire:test
```

### Hierarchy

```
Lifecycle → Phase → Goal
```

---

## 8. Convention Over Configuration (VERY IMPORTANT)

Maven avoids configuration by enforcing **standard conventions**.

---

## 9. COMPLETE MAVEN PROJECT CONVENTIONS

### 9.1 Standard Directory Structure

```
project-root/
 ├── pom.xml
 ├── src/
 │   ├── main/
 │   │   ├── java/
 │   │   ├── resources/
 │   │   └── webapp/        (for WAR projects)
 │   └── test/
 │       ├── java/
 │       └── resources/
 └── target/
```

---

### 9.2 Java Source Code

|Type|Location|
|---|---|
|Main Java code|`src/main/java`|
|Test Java code|`src/test/java`|

---

### 9.3 Resources

|Type|Location|
|---|---|
|Application configs|`src/main/resources`|
|Test configs|`src/test/resources`|

Automatically included in classpath.

---

### 9.4 Filters & Filtered Resources

#### Filters

- Used to replace placeholders in files
    

##### Filter File

```
src/main/filters/application.properties
```

##### Usage

```properties
app.name=${project.name}
```

Maven replaces placeholders at build time.

---

### 9.5 Web Applications (WAR)

|Item|Location|
|---|---|
|Web files|`src/main/webapp`|
|WEB-INF|`src/main/webapp/WEB-INF`|
|web.xml|`WEB-INF/web.xml`|

---

### 9.6 Tests Convention

#### Test Class Naming

Maven automatically runs:

- `*Test.java`
    
- `*Tests.java`
    
- `*TestCase.java`
    

#### Test Frameworks

- JUnit
    
- TestNG
    

Handled by `maven-surefire-plugin`.

---

### 9.7 Configuration Files

|Type|Location|
|---|---|
|application.properties|`src/main/resources`|
|logback.xml|`src/main/resources`|
|persistence.xml|`META-INF/`|

---

### 9.8 Generated Files

|Item|Location|
|---|---|
|Compiled classes|`target/classes`|
|Test classes|`target/test-classes`|
|JAR/WAR|`target/`|

---

### 9.9 Multi-Module Projects

```
parent-project/
 ├── pom.xml
 ├── module-a/
 ├── module-b/
```

Parent POM manages versions and plugins.

---

## 10. Installing Maven

### Prerequisites

- Java JDK installed
    
- `JAVA_HOME` set
    

### Steps

1. Download from Maven website
    
2. Extract archive
    
3. Set:
    
    - `MAVEN_HOME`
        
    - Add `bin` to `PATH`
        
4. Verify:
    

```bash
mvn -version
```

---

## 11. Final Summary

- Maven standardizes Java project builds
    
- Uses **POM** for configuration
    
- Follows **strict conventions**
    
- Automates dependency management
    
- Uses **lifecycles, phases, and goals**
    
- Minimizes configuration effort
    
- Essential for modern Java development
    
