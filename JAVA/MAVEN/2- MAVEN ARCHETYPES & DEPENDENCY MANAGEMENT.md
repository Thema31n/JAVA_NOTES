## 1. Maven Archetypes

### What is a Maven Archetype?

A **Maven archetype** is a **project template**.

Think of it as:

> “A predefined project structure + sample files”

Archetypes help you:

- Create projects quickly
    
- Follow Maven conventions
    
- Avoid manual setup
    

---

### Common Maven Archetypes

|Archetype|Use Case|
|---|---|
|`maven-archetype-quickstart`|Simple Java app|
|`maven-archetype-webapp`|Web application (WAR)|
|`maven-archetype-site`|Maven documentation|
|`spring-boot` archetypes|Spring Boot apps|

---

## 2. Create Your First Maven Project (Using Archetype)

### Command (Quickstart Java App)

```bash
mvn archetype:generate \
  -DgroupId=com.example.demo \
  -DartifactId=my-first-app \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DinteractiveMode=false
```

---

### Project Structure Created

```
my-first-app/
 ├── pom.xml
 └── src/
     ├── main/java/com/example/demo/App.java
     └── test/java/com/example/demo/AppTest.java
```

✔️ Standard Maven structure  
✔️ Ready to build immediately

---

## 3. What is `groupId`?

### Definition

`groupId` identifies **who owns or produces the project**.

It is usually:

- A **reverse domain name**
    
- Same as Java package naming
    

### Examples

```text
com.company.project
org.springframework.boot
io.github.username
```

### Best Practices

- Use lowercase
    
- Use reverse domain if available
    
- Match base Java package
    

---

## 4. What is `artifactId`?

### Definition

`artifactId` is the **name of the project** (the thing being built).

It becomes:

- The project folder name
    
- The JAR/WAR name
    

### Examples

```text
user-service
order-api
payment-gateway
```

### Best Practices

- Lowercase
    
- Use hyphens (`-`)
    
- Be descriptive
    

---

## 5. Naming Conventions (IMPORTANT)

### groupId

```
com.company.department.project
```

❌ Bad:

```
MyCompany.Project
```

✅ Good:

```
com.mycompany.project
```

---

### artifactId

```
project-name
```

❌ Bad:

```
MyProject
```

✅ Good:

```
my-project
```

---

### version

```
<major>.<minor>.<patch>
```

Examples:

```
1.0.0
2.1.3
```

Special suffixes:

- `-SNAPSHOT` → development version
    

```text
1.0.0-SNAPSHOT
```

---

## 6. Validate a Maven Project

### What Does `validate` Do?

- Checks project structure
    
- Ensures `pom.xml` is correct
    
- Verifies required information exists
    

### Command

```bash
mvn validate
```

If successful:  
✔️ Project structure is valid  
✔️ POM is readable  
✔️ No dependency resolution issues

---

## 7. `pom.xml` – Detailed Structure Overview

### Basic Structure

```xml
<project>
  <modelVersion>4.0.0</modelVersion>

  <!-- Project coordinates -->
  <groupId>com.example</groupId>
  <artifactId>demo-app</artifactId>
  <version>1.0.0</version>
  <packaging>jar</packaging>

  <name>Demo Application</name>
  <description>Sample Maven Project</description>

  <!-- Properties -->
  <properties>
    <java.version>17</java.version>
  </properties>

  <!-- Dependencies -->
  <dependencies>
    ...
  </dependencies>

  <!-- Build configuration -->
  <build>
    <plugins>
      ...
    </plugins>
  </build>
</project>
```

---

### Key Sections Explained

#### `<modelVersion>`

- Maven POM version (almost always `4.0.0`)
    

#### `<packaging>`

- Output type:
    
    - `jar`
        
    - `war`
        
    - `pom`
        

---

## 8. Managing Dependencies with Maven

### Why Maven Dependency Management is Powerful

- Automatic downloads
    
- Transitive dependency resolution
    
- Version conflict handling
    

---

### Adding a Dependency

```xml
<dependencies>
  <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.13</version>
  </dependency>
</dependencies>
```

---

### Transitive Dependencies

If:

- A depends on B
    
- B depends on C
    

➡ Maven automatically downloads **C**

---

## 9. Dependency Scopes (VERY IMPORTANT)

### What is Scope?

Scope defines:

- When a dependency is available
    
- Where it is included
    

---

### All Maven Dependency Scopes

|Scope|Compile|Test|Runtime|Example|
|---|---|---|---|---|
|compile (default)|✔|✔|✔|Spring Core|
|test|❌|✔|❌|JUnit|
|provided|✔|✔|❌|Servlet API|
|runtime|❌|❌|✔|JDBC Driver|
|system|✔|✔|✔|Local JAR (avoid)|
|import|—|—|—|BOM files|

---

### Examples

#### Test Scope

```xml
<dependency>
  <groupId>org.junit.jupiter</groupId>
  <artifactId>junit-jupiter</artifactId>
  <version>5.10.2</version>
  <scope>test</scope>
</dependency>
```

---

#### Provided Scope (Web Apps)

```xml
<dependency>
  <groupId>jakarta.servlet</groupId>
  <artifactId>jakarta.servlet-api</artifactId>
  <version>6.0.0</version>
  <scope>provided</scope>
</dependency>
```

---

## 10. Build & Run Your First Maven Project

### Compile

```bash
mvn compile
```

### Test

```bash
mvn test
```

### Package

```bash
mvn package
```

Output:

```
target/my-first-app-1.0.0.jar
```

---

## Final Summary

- Archetypes = project templates
    
- `groupId` = owner / organization
    
- `artifactId` = project name
    
- `version` = release identifier
    
- `validate` checks correctness
    
- `pom.xml` controls everything
    
- Maven manages dependencies automatically
    
- Dependency scopes control visibility
    
