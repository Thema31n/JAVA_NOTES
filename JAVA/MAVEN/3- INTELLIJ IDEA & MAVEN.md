## 1. Creating a Maven Project in IntelliJ IDEA

### Method 1: New Maven Project (Recommended)

#### Steps

1. Open **IntelliJ IDEA**
    
2. Click **New Project**
    
3. Select **Maven**
    
4. Choose **JDK**
    
5. Click **Next**
    

---

### Maven Coordinates Screen

Fill in:

- **GroupId** → `com.example`
    
- **ArtifactId** → `demo-app`
    
- **Version** → `1.0.0-SNAPSHOT`
    

Optional:

- Select an **Archetype** (e.g. quickstart)
    

Click **Create**

---

### What IntelliJ Generates

```
demo-app/
 ├── pom.xml
 └── src/
     ├── main/java
     └── test/java
```

✔️ Maven structure  
✔️ Maven project auto-imported  
✔️ Ready to build

---

## 2. Importing an Existing Maven Project into IntelliJ

### When to Use This

- Project already has a `pom.xml`
    
- You cloned a Maven project from Git
    

---

### Steps

1. Open IntelliJ
    
2. **File → Open**
    
3. Select the **project root folder**
    
4. IntelliJ detects `pom.xml`
    
5. Choose **Open as Maven Project**
    

---

### If IntelliJ Does NOT Auto-Detect

- Open **Maven Tool Window**
    
- Click **“+” (Add Maven Project)**
    
- Select `pom.xml`
    

---

## 3. Converting a Plain Java Project into a Maven Project

### Scenario

You already have:

```
src/
 └── com/example/Main.java
```

But **no Maven structure, no POM**

---

### Step-by-Step Conversion

#### 1️⃣ Create `pom.xml`

Right-click project → **New → File → pom.xml**

Basic POM:

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>legacy-app</artifactId>
  <version>1.0.0-SNAPSHOT</version>
</project>
```

---

#### 2️⃣ Reorganize Directory Structure

Move files to Maven convention:

```
src/main/java/
src/test/java/
src/main/resources/
```

---

#### 3️⃣ Reimport as Maven

- Right-click `pom.xml`
    
- **Add as Maven Project**
    

✔️ IntelliJ now treats it as Maven  
✔️ Dependencies auto-managed

---

## 4. Maven Directory Structure (Recap + IntelliJ View)

```
project/
 ├── pom.xml
 ├── src/
 │   ├── main/
 │   │   ├── java/
 │   │   ├── resources/
 │   │   └── webapp/
 │   └── test/
 │       ├── java/
 │       └── resources/
 └── target/
```

### IntelliJ Highlights

- `target/` marked as **Excluded**
    
- `src/main/java` marked as **Sources**
    
- `src/test/java` marked as **Test Sources**
    

---

## 5. IntelliJ & Maven Integration (How They Work Together)

### Maven Tool Window

View:

- Lifecycles
    
- Phases
    
- Plugins
    
- Dependencies
    

### Common Actions

- Double-click **package**
    
- Reimport project
    
- View dependency tree
    

---

### Maven Wrapper vs System Maven

In IntelliJ:

```
Settings → Build Tools → Maven
```

You can choose:

- Bundled Maven
    
- System Maven
    
- Maven Wrapper (`mvnw`)
    

---

## 6. Build Profiles (DETAILED)

### What is a Maven Profile?

A **profile** is a set of configuration values activated under certain conditions.

Used for:

- dev / test / prod
    
- OS-specific builds
    
- CI environments
    

---

### Define Profiles in `pom.xml`

```xml
<profiles>
  <profile>
    <id>dev</id>
    <properties>
      <env>dev</env>
    </properties>
  </profile>

  <profile>
    <id>prod</id>
    <properties>
      <env>prod</env>
    </properties>
  </profile>
</profiles>
```

---

### Activate Profiles

#### From Command Line

```bash
mvn clean package -Pdev
```

#### From IntelliJ

- Maven Tool Window
    
- Profiles → Check `dev` or `prod`
    

---

### Profile Activation Methods

|Method|Example|
|---|---|
|By ID|`-Pdev`|
|By property|`-Denv=prod`|
|By OS|`<os><family>Windows</family></os>`|
|Active by default|`<activeByDefault>true</activeByDefault>`|

---

## 7. `settings.xml` (VERY IMPORTANT)

### What is `settings.xml`?

A **user-specific Maven configuration file**

📍 Location:

```
~/.m2/settings.xml
```

(Not in the project!)

---

### What Goes in `settings.xml`?

- Credentials (usernames/passwords)
    
- Proxies
    
- Mirrors
    
- Local repository location
    
- Global profiles
    

---

### Example `settings.xml`

```xml
<settings>
  <localRepository>/custom/m2/repo</localRepository>

  <mirrors>
    <mirror>
      <id>central-mirror</id>
      <mirrorOf>central</mirrorOf>
      <url>https://repo.maven.apache.org/maven2</url>
    </mirror>
  </mirrors>

  <profiles>
    <profile>
      <id>company-repo</id>
      <repositories>
        <repository>
          <id>internal</id>
          <url>https://repo.company.com/maven</url>
        </repository>
      </repositories>
    </profile>
  </profiles>

  <activeProfiles>
    <activeProfile>company-repo</activeProfile>
  </activeProfiles>
</settings>
```

---

### Difference: `pom.xml` vs `settings.xml`

|pom.xml|settings.xml|
|---|---|
|Project config|User/system config|
|Versioned|NOT versioned|
|Shared|Personal|
|Dependencies|Credentials, mirrors|

---

## 8. IntelliJ Build vs Maven Build

### IntelliJ Build

- Faster
    
- IDE-specific
    
- Uses IntelliJ compiler
    

### Maven Build

- Standard
    
- CI-compatible
    
- Uses plugins
    

🔹 **Best Practice**: Use Maven build for production

---

## 9. Common IntelliJ + Maven Issues

### Dependencies not downloaded

- Click **Reimport Maven**
    
- Check `settings.xml`
    

### Wrong Java version

```xml
<maven-compiler-plugin>
  <configuration>
    <source>17</source>
    <target>17</target>
  </configuration>
</maven-compiler-plugin>
```

---

## Final Summary

- IntelliJ has **native Maven support**
    
- You can create, import, or convert projects
    
- Maven directory structure is enforced
    
- Profiles enable environment-specific builds
    
- `settings.xml` handles sensitive & global config
    
- IntelliJ + Maven is industry standard
    
