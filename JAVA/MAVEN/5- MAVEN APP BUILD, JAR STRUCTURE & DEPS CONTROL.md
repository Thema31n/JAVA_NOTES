## 1. Creating an Application Build (REAL EXAMPLES)

### Example 1: Simple Console App

**Java class**

```java
package com.company.app;

public class MainApp {
    public static void main(String[] args) {
        System.out.println("Application started");
    }
}
```

### Build Command

```bash
mvn clean package
```

### Result

```
target/app-1.0.0.jar
```

---

### Example 2: CI-Style Build

```bash
mvn clean verify
```

Runs:

- Compile
    
- Unit tests
    
- Integration checks
    

Used in:

- Jenkins
    
- GitHub Actions
    
- GitLab CI
    

---

## 2. JAR File Structure (INSIDE A REAL JAR)

Inspect:

```bash
jar tf target/app-1.0.0.jar
```

### Output

```
META-INF/
META-INF/MANIFEST.MF
META-INF/maven/com.company/app/pom.xml
META-INF/maven/com.company/app/pom.properties
com/company/app/MainApp.class
```

---

### Why Maven Adds `META-INF/maven`

- Traceability
    
- Identify exact version used at runtime
    
- Used by tools and scanners
    

---

## 3. Manifest (REAL USE CASES)

### Use Case 1: Runnable JAR

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-jar-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>com.company.app.MainApp</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
```

Run:

```bash
java -jar app-1.0.0.jar
```

---

### Use Case 2: Classpath in Manifest (Legacy)

```xml
<manifestEntries>
  <Class-Path>lib/log4j.jar</Class-Path>
</manifestEntries>
```

⚠️ Mostly replaced by **fat JARs**

---

## 4. Surefire Plugin (REAL PROJECT SETUP)

### Typical Enterprise Test Setup

```
src/test/java/
 └── com/company/app/
     ├── UserServiceTest.java
     └── OrderServiceTest.java
```

Surefire auto-runs them.

---

### Disable Tests for Fast Builds

```bash
mvn package -DskipTests
```

### Skip Compilation + Execution

```bash
mvn package -Dmaven.test.skip=true
```

---

## 5. HTML Test Reports (REAL WORKFLOW)

### Step 1: Add Report Plugin

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-surefire-report-plugin</artifactId>
</plugin>
```

### Step 2: Generate Reports

```bash
mvn test site
```

### Output

```
target/site/surefire-report.html
```

Used by:

- QA teams
    
- Managers
    
- CI artifacts
    

---

## 6. Optional Dependencies (REAL SCENARIOS)

### Scenario: Logging Abstraction

Your library supports **multiple loggers**:

- log4j
    
- logback
    
- slf4j-simple
    

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <optional>true</optional>
</dependency>
```

Consumer decides which logger to use.

---

### When NOT to Use Optional

❌ Application projects  
❌ Required runtime libs

✔️ Library projects only

---

## 7. Dependency Exclusion (REAL PROBLEMS)

### Problem 1: Duplicate Logging

```
spring-core → commons-logging
your-app → logback
```

### Fix

```xml
<exclusions>
  <exclusion>
    <groupId>commons-logging</groupId>
    <artifactId>commons-logging</artifactId>
  </exclusion>
</exclusions>
```

---

### Problem 2: Vulnerable Dependency

Security scan flags:

```
log4j-core 2.14
```

Exclude & replace with safe version.

---

## 8. Dependency Tree (REAL DEBUGGING)

```bash
mvn dependency:tree -Dverbose
```

Used to:

- Find conflicts
    
- Verify exclusions
    
- Analyze fat JAR content
    

---

# 🔁 FULL SUMMARY (Expanded & Final)

---

## Application Build

- Build = compile + test + package
    
- `mvn clean package` creates distributable artifact
    
- Output stored in `target/`
    

---

## JAR Structure

- JAR is a ZIP archive
    
- Contains:
    
    - Compiled classes
        
    - `META-INF` metadata
        
    - Maven POM info
        

---

## Manifest

- Located at `META-INF/MANIFEST.MF`
    
- Defines:
    
    - Main class
        
    - Runtime metadata
        
- Required for runnable JARs
    

---

## Surefire Plugin

- Runs unit tests
    
- Executes during `test` phase
    
- Detects test classes automatically
    
- Supports JUnit & TestNG
    

---

## HTML Test Reports

- Raw reports in `target/surefire-reports`
    
- HTML reports via `mvn site`
    
- Used in CI & QA workflows
    

---

## Optional Dependencies

- Used only by current project
    
- NOT inherited transitively
    
- Best for library flexibility
    

---

## Dependency Exclusions

- Remove unwanted transitive dependencies
    
- Used to:
    
    - Avoid conflicts
        
    - Fix vulnerabilities
        
    - Reduce classpath size
        

---

## Best Practices

- Always inspect dependency tree
    
- Exclude aggressively but carefully
    
- Separate unit vs integration tests
    
- Generate reports in CI
    
