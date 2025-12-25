## 1. Maven Archetypes for Web Applications

### What Is a Web Archetype?

A **web archetype** generates a **ready-to-use Java web project** with:

- WAR packaging
    
- Web directory structure
    
- `web.xml`
    
- JSP sample files
    

---

### Common Maven Web Archetypes

|Archetype|Use Case|
|---|---|
|`maven-archetype-webapp`|Classic Servlet/JSP web app|
|`org.apache.maven.archetypes:webapp-javaee7`|Java EE app|
|Spring Boot web archetypes|Modern web apps|

---

## 2. Creating a Maven Web Project (REAL COMMAND)

### Using Web Archetype

```bash
mvn archetype:generate \
  -DgroupId=com.example.web \
  -DartifactId=my-web-app \
  -DarchetypeArtifactId=maven-archetype-webapp \
  -DinteractiveMode=false
```

---

### Resulting Project Structure

```
my-web-app/
 ├── pom.xml
 └── src/
     └── main/
         └── webapp/
             ├── index.jsp
             └── WEB-INF/
                 └── web.xml
```

✔️ WAR-based web app  
✔️ Servlet container ready

---

## 3. Structure of a Maven Web Project (VERY IMPORTANT)

```
project/
 ├── pom.xml
 ├── src/
 │   └── main/
 │       ├── java/
 │       │   └── com/example/web/
 │       │       └── HelloServlet.java
 │       ├── resources/
 │       └── webapp/
 │           ├── index.jsp
 │           ├── css/
 │           ├── js/
 │           ├── images/
 │           └── WEB-INF/
 │               ├── web.xml
 │               └── classes/
 └── target/
```

---

### Key Folders Explained

|Folder|Purpose|
|---|---|
|`src/main/java`|Servlets, controllers, filters|
|`src/main/resources`|Config files|
|`src/main/webapp`|Web content|
|`WEB-INF`|Secure server-side resources|
|`target`|Build output|

---

## 4. Create a Simple Servlet (REAL EXAMPLE)

### Servlet Code

```java
package com.example.web;

import jakarta.servlet.*;
import jakarta.servlet.http.*;
import java.io.IOException;

public class HelloServlet extends HttpServlet {

    protected void doGet(HttpServletRequest req, HttpServletResponse res)
            throws IOException {
        res.getWriter().println("Hello from Maven Web App");
    }
}
```

---

### web.xml Mapping

```xml
<web-app>
  <servlet>
    <servlet-name>hello</servlet-name>
    <servlet-class>com.example.web.HelloServlet</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>
</web-app>
```

---

## 5. WEB-INF Folder (CRITICAL)

### What Is `WEB-INF`?

A **protected folder**:

- ❌ Not accessible directly from browser
    
- ✅ Used by the server internally
    

---

### Typical `WEB-INF` Contents

```
WEB-INF/
 ├── web.xml
 ├── classes/
 └── lib/
```

|Item|Purpose|
|---|---|
|`web.xml`|Deployment descriptor|
|`classes`|Compiled classes|
|`lib`|Dependency JARs|

---

### Why `WEB-INF` Exists

- Security
    
- Prevent direct access to:
    
    - Servlets
        
    - Configuration
        
    - Internal classes
        

---

## 6. META-INF Folder (Web Context)

### What Is `META-INF`?

Used for **application metadata**.

---

### Common Contents

```
META-INF/
 ├── MANIFEST.MF
 └── context.xml
```

---

### `context.xml` Example (Tomcat)

```xml
<Context>
  <Resource name="jdbc/MyDB"
            type="javax.sql.DataSource"
            url="jdbc:mysql://localhost:3306/appdb"/>
</Context>
```

---

## 7. Dynamic Web Module Project (Concept Explained)

### What Is a Dynamic Web Project?

In IDE terms:

- A **Java Web project**
    
- Runs on a **Servlet container**
    
- Supports:
    
    - Servlets
        
    - JSP
        
    - Filters
        
    - Listeners
        

---

### Maven vs Dynamic Web Project

|Dynamic Web Project|Maven Web Project|
|---|---|
|IDE-managed|Maven-managed|
|Manual libraries|Dependency-managed|
|Non-standard structure|Standard structure|

👉 **Maven web project = better dynamic web project**

---

## 8. WAR Packaging (IMPORTANT)

### WAR = Web ARchive

Used to deploy apps to:

- Tomcat
    
- Jetty
    
- WildFly
    

---

### WAR Packaging in POM

```xml
<packaging>war</packaging>
```

---

### Build WAR

```bash
mvn clean package
```

Output:

```
target/my-web-app.war
```

---

## 9. WAR File Structure (INSIDE WAR)

Inspect:

```bash
jar tf my-web-app.war
```

### Structure

```
META-INF/
WEB-INF/
 ├── web.xml
 ├── classes/
 │   └── com/example/web/HelloServlet.class
 ├── lib/
 │   └── servlet-api.jar
index.jsp
css/
js/
```

---

### Mapping Source → WAR

|Source Folder|WAR Location|
|---|---|
|`src/main/java`|`WEB-INF/classes`|
|`src/main/resources`|`WEB-INF/classes`|
|Dependencies|`WEB-INF/lib`|
|`src/main/webapp`|Root of WAR|

---

## 10. Running the Web App on a Server

---

### Option 1: Deploy to External Tomcat

1. Copy WAR to:
    

```
tomcat/webapps/
```

2. Start Tomcat
    
3. Access:
    

```
http://localhost:8080/my-web-app
```

---

### Option 2: Run via Maven (Tomcat Plugin)

```xml
<plugin>
  <groupId>org.apache.tomcat.maven</groupId>
  <artifactId>tomcat7-maven-plugin</artifactId>
  <configuration>
    <port>8080</port>
    <path>/</path>
  </configuration>
</plugin>
```

Run:

```bash
mvn tomcat7:run
```

---

## 11. Servlet API Dependency (Provided Scope)

```xml
<dependency>
  <groupId>jakarta.servlet</groupId>
  <artifactId>jakarta.servlet-api</artifactId>
  <version>6.0.0</version>
  <scope>provided</scope>
</dependency>
```

Why `provided`?

- Server already supplies it
    
- Avoids duplication
    

---

# 🔁 FULL SUMMARY (FINAL & DETAILED)

---

## Maven Web Archetypes

- Templates for web apps
    
- Generate WAR structure
    
- Save setup time
    

---

## Maven Web Project Structure

- Java code → `src/main/java`
    
- Web files → `src/main/webapp`
    
- Secure resources → `WEB-INF`
    

---

## WEB-INF

- Protected server-only directory
    
- Contains servlets, configs, libs
    

---

## META-INF

- Metadata and server configuration
    
- Contains manifest and context config
    

---

## WAR File

- Deployable web archive
    
- Contains web resources + compiled classes
    
- Standard format for servlet containers
    

---

## Running Web Apps

- External server deployment
    
- Embedded server via Maven plugin
    
- IDE integration (IntelliJ)
    

---

## Best Practices

- Use `provided` for servlet APIs
    
- Keep configs in `WEB-INF`
    
- Use Maven to manage dependencies
    
- Avoid manual JAR management
    
