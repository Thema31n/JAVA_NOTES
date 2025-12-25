## 1. What Is Logging and What Are Logs?

### Logging

Logging is the process of recording application events during runtime without stopping the application.

### Logs

Logs are structured records that describe:

- What happened
    
- When it happened
    
- Where it happened (class/package)
    
- Severity of the event
    

### Example

```text
2025-01-10 12:40:15 INFO  UserService - User logged in: Ahmed
2025-01-10 12:41:02 ERROR OrderService - Database error
```

---

## 2. Goals of Logging

- Debugging application behavior
    
- Monitoring production systems
    
- Auditing and security tracking
    
- Error analysis and troubleshooting
    
- Performance monitoring
    
- Replacing `System.out.println`
    

---

## 3. Overview of Logging Libraries in Java

|Library|Description|
|---|---|
|java.util.logging (JUL)|Built-in JDK logging|
|Log4j (1.x)|Deprecated|
|Log4j2|Fast, modern, secure|
|Logback|Log4j successor|
|SLF4J|Logging facade|

---

## 4. Key Elements of Any Logging Framework

- **Logger** – Entry point for logging
    
- **Handler / Appender** – Where logs go
    
- **Formatter / Layout** – How logs look
    
- **Level** – Severity of logs
    
- **Filter** – Accept or reject logs
    
- **Configuration** – XML / properties / code
    

---

## 5. Logging Levels (General Concept)

|Level|Meaning|
|---|---|
|TRACE|Very detailed|
|DEBUG|Debug information|
|INFO|Normal operations|
|WARN|Potential problems|
|ERROR|Serious issues|
|FATAL|Application crash|

---

## 6. Java Logging Framework – `java.util.logging` (JUL)

### Overview

- Built into Java
    
- No external dependencies
    
- Suitable for small or legacy systems
    

---

## 7. `java.util.logging` Package Overview

Important classes:

- `Logger`
    
- `Handler`
    
- `ConsoleHandler`
    
- `FileHandler`
    
- `Formatter`
    
- `SimpleFormatter`
    
- `HTMLFormatter`
    
- `Filter`
    
- `Level`
    

---

## 8. Logging Levels in `java.util.logging`

```text
SEVERE
WARNING
INFO
CONFIG
FINE
FINER
FINEST
```

---

## 9. GLOBAL LOGGING IN `java.util.logging`

### What Is Global Logging?

Global logging means:

- Logging configured once
    
- All classes automatically use the same configuration
    
- Same levels, handlers, and formatters
    

Uses:

```java
Logger.getLogger("")
```

---

## 10. GLOBAL LOGGING CONFIGURATION

### (Console + Text File + HTML File + Custom HTML File)

### Features Demonstrated

✔ Root logger  
✔ Console output  
✔ Plain text log file  
✔ HTML log file  
✔ Custom HTML log file  
✔ All levels enabled  
✔ Logger per class  
✔ Exception logging

---

### `GlobalLoggingConfig.java`

```java
package com.example.logging;

import java.io.IOException;
import java.util.logging.*;

public class GlobalLoggingConfig {

    public static void setup() {
        try {
            Logger rootLogger = Logger.getLogger("");

            for (Handler handler : rootLogger.getHandlers()) {
                rootLogger.removeHandler(handler);
            }

            rootLogger.setLevel(Level.ALL);

            ConsoleHandler consoleHandler = new ConsoleHandler();
            consoleHandler.setLevel(Level.ALL);
            consoleHandler.setFormatter(new SimpleFormatter());

            FileHandler textFileHandler =
                    new FileHandler("application.log", true);
            textFileHandler.setLevel(Level.ALL);
            textFileHandler.setFormatter(new SimpleFormatter());

            FileHandler htmlFileHandler =
                    new FileHandler("application.html", true);
            htmlFileHandler.setLevel(Level.ALL);
            htmlFileHandler.setFormatter(new HTMLFormatter());

            FileHandler customHtmlHandler =
                    new FileHandler("application-custom.html", true);
            customHtmlHandler.setLevel(Level.ALL);
            customHtmlHandler.setFormatter(new CustomHtmlFormatter());

            rootLogger.addHandler(consoleHandler);
            rootLogger.addHandler(textFileHandler);
            rootLogger.addHandler(htmlFileHandler);
            rootLogger.addHandler(customHtmlHandler);

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

### `CustomHtmlFormatter.java`

```java
package com.example.logging;

import java.util.logging.Formatter;
import java.util.logging.LogRecord;
import java.util.Date;

public class CustomHtmlFormatter extends Formatter {

    @Override
    public String format(LogRecord record) {
        return "<tr>"
                + "<td>" + new Date(record.getMillis()) + "</td>"
                + "<td>" + record.getLevel() + "</td>"
                + "<td>" + record.getLoggerName() + "</td>"
                + "<td>" + record.getMessage() + "</td>"
                + "</tr>\n";
    }

    @Override
    public String getHead(java.util.logging.Handler h) {
        return "<html><body><table border='1'>"
                + "<tr><th>Time</th><th>Level</th><th>Logger</th><th>Message</th></tr>";
    }

    @Override
    public String getTail(java.util.logging.Handler h) {
        return "</table></body></html>";
    }
}
```

---

## 11. Logger Per Class (Best Practice)

### `UserService.java`

```java
package com.example.logging;

import java.util.logging.Level;
import java.util.logging.Logger;

public class UserService {

    private static final Logger logger =
            Logger.getLogger(UserService.class.getName());

    public void login(String username) {

        logger.severe("SEVERE: critical error");
        logger.warning("WARNING: password attempts high");
        logger.info("INFO: user logged in -> " + username);
        logger.config("CONFIG: login config loaded");
        logger.fine("FINE: entering login()");
        logger.finer("FINER: validating credentials");
        logger.finest("FINEST: username length=" + username.length());

        try {
            int x = 10 / 0;
        } catch (Exception e) {
            logger.log(Level.SEVERE, "Exception occurred", e);
        }
    }
}
```

---

## 12. Log4j (Version 1.x)

### Features

- Multiple logging levels
    
- File and console appenders
    
- XML / properties configuration
    
- Hierarchical loggers
    

❌ **Deprecated and insecure**

### Levels

```text
ALL, TRACE, DEBUG, INFO, WARN, ERROR, FATAL, OFF
```

---

## 13. Log4j2 (Modern Logging Framework)

### Features

- Very high performance (async logging)
    
- Secure (Log4Shell fixed)
    
- XML / JSON / YAML / properties configuration
    
- Advanced filters
    
- Rolling and archiving support
    
- Plugin architecture
    

---

### Log4j2 Logging Levels

```text
TRACE
DEBUG
INFO
WARN
ERROR
FATAL
```

---

### Structure Elements of Log4j2

- Logger
    
- Appender
    
- Layout
    
- Filter
    
- Triggering policies
    
- Rollover strategies
    

---

### Triggering Policies (Log4j2)

```xml
<Policies>
    <TimeBasedTriggeringPolicy/>
    <SizeBasedTriggeringPolicy size="10MB"/>
</Policies>
```

---

### Rollover Strategies (Log4j2)

```xml
<DefaultRolloverStrategy max="7"/>
```

---

### BIG Log4j2 Practical Example

#### `log4j2.xml`

```xml
<Configuration status="WARN">

    <Appenders>
        <Console name="ConsoleAppender">
            <PatternLayout pattern="%d %-5level %logger - %msg%n"/>
        </Console>

        <RollingFile name="FileAppender"
                     fileName="logs/app.log"
                     filePattern="logs/app-%d{yyyy-MM-dd}-%i.log.gz">
            <PatternLayout pattern="%d %-5level %logger - %msg%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="10MB"/>
            </Policies>
            <DefaultRolloverStrategy max="7"/>
        </RollingFile>
    </Appenders>

    <Loggers>
        <Root level="INFO">
            <AppenderRef ref="ConsoleAppender"/>
            <AppenderRef ref="FileAppender"/>
        </Root>
    </Loggers>

</Configuration>
```

#### Java Code

```java
private static final Logger logger =
        LogManager.getLogger(MyClass.class);

logger.info("Processing data");
logger.error("Processing failed");
```

---

## 14. Logback

### Features

- Default in Spring Boot
    
- Fast and reliable
    
- Native SLF4J support
    
- XML configuration
    

### Levels

```text
TRACE, DEBUG, INFO, WARN, ERROR
```

---

## 15. Logback Practical Example

```java
private static final Logger logger =
        LoggerFactory.getLogger(PaymentService.class);

logger.info("Payment successful");
```

---

## 16. SLF4J

### What Is SLF4J?

- Logging facade
    
- Decouples code from implementation
    

```text
Your Code → SLF4J → Logback / Log4j2
```

---

## 17. SLF4J + Log4j2

- High-performance systems
    
- Enterprise applications
    

---

## 18. SLF4J + Logback

- Most common setup
    
- Spring Boot default
    
- Recommended choice
    

---

## 19. Real-World Recommendations

|Scenario|Choice|
|---|---|
|Learning|java.util.logging|
|Small apps|JUL|
|Spring Boot|SLF4J + Logback|
|High performance|SLF4J + Log4j2|

---

## 20. Key Interview Notes

✔ One logger per class  
✔ Use global configuration  
✔ Enable levels on logger & handler  
✔ Prefer SLF4J in real projects  
✔ Log4j2 ≠ Log4j
