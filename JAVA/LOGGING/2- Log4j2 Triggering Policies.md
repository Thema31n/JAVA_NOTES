## 1. What Is a Triggering Policy?

A **TriggeringPolicy** answers one question:

> **“Is it time to roll the log file?”**

Every time a log event is written, Log4j2 checks the configured triggering policy (or policies).  
If the condition is met → **rollover happens**.

---

## 2. Where Triggering Policies Are Used

```xml
<RollingFile name="RollingFile"
             fileName="logs/app.log"
             filePattern="logs/app-%d{yyyy-MM-dd}-%i.log.gz">

    <Policies>
        <!-- Triggering policies go here -->
    </Policies>

</RollingFile>
```

You can use:

- **One policy**
    
- **Multiple policies together** (logical OR)
    

---

## 3. SizeBasedTriggeringPolicy

### Purpose

Rolls the log file **when it reaches a certain size**.

### Configuration

```xml
<SizeBasedTriggeringPolicy size="10 MB"/>
```

### How It Works

- Tracks current log file size
    
- When size ≥ configured value → rollover
    
- Commonly used with `%i` (index) in `filePattern`
    

### Example

```xml
filePattern="logs/app-%i.log"
```

Result:

```
app.log      (current)
app-1.log
app-2.log
```

### Notes

- Size supports units: `KB`, `MB`, `GB`
    
- Very common in high-volume systems
    

---

## 4. TimeBasedTriggeringPolicy

### Purpose

Rolls logs **based on time intervals**.

### Configuration

```xml
<TimeBasedTriggeringPolicy interval="1" modulate="true"/>
```

### Key Attributes

|Attribute|Meaning|
|---|---|
|`interval`|How often to roll|
|`modulate`|Align rollovers to time boundaries|

### Common Time Patterns

```xml
%d{yyyy-MM-dd}        → daily
%d{yyyy-MM-dd-HH}     → hourly
%d{yyyy-MM-dd-HH-mm}  → per minute
```

### Example (Daily Rolling)

```xml
filePattern="logs/app-%d{yyyy-MM-dd}.log"
```

Result:

```
app-2025-12-16.log
app-2025-12-17.log
```

### modulate Explained

- `modulate="true"` → rolls exactly at midnight/hour
    
- `modulate="false"` → rolls based on JVM start time
    

---

## 5. CronTriggeringPolicy

### Purpose

Rolls logs using a **cron expression**.

### Configuration

```xml
<CronTriggeringPolicy schedule="0 0 0 * * ?"/>
```

### Example Schedules

|Cron|Meaning|
|---|---|
|`0 0 0 * * ?`|Every day at midnight|
|`0 0 */6 * * ?`|Every 6 hours|
|`0 0 9 ? * MON-FRI`|Weekdays at 9 AM|

### When to Use

- Business-hour rollovers
    
- Compliance-driven schedules
    
- Precise control beyond intervals
    

---

## 6. OnStartupTriggeringPolicy

### Purpose

Rolls the log file **when the application starts**.

### Configuration

```xml
<OnStartupTriggeringPolicy/>
```

### Behavior

- On JVM startup:
    
    - Existing log file is rolled
        
    - New log file starts clean
        

### Typical Use Case

- Avoid mixing logs between restarts
    
- Containers / microservices
    

---

## 7. CompositeTriggeringPolicy (Multiple Policies)

### Purpose

Use **multiple triggering conditions together**.

### Behavior

- Rollover occurs if **ANY** policy triggers (logical OR)
    

### Example: Size OR Daily

```xml
<Policies>
    <SizeBasedTriggeringPolicy size="100 MB"/>
    <TimeBasedTriggeringPolicy interval="1"/>
</Policies>
```

### Result

- Rolls daily
    
- Rolls earlier if size exceeds 100 MB
    

This is **very common in production**.

---

## 8. Triggering Policy vs Rollover Strategy

|Triggering Policy|Rollover Strategy|
|---|---|
|WHEN to roll|WHAT to do after roll|
|Time, size, cron|Delete, compress, retain|
|Decision logic|File management|

Example strategy:

```xml
<DefaultRolloverStrategy max="30"/>
```

---

## 9. Best-Practice Combinations

### High-Traffic Applications

```xml
<Policies>
    <SizeBasedTriggeringPolicy size="200 MB"/>
    <TimeBasedTriggeringPolicy interval="1"/>
</Policies>
```

### Microservices / Containers

```xml
<Policies>
    <OnStartupTriggeringPolicy/>
    <SizeBasedTriggeringPolicy size="50 MB"/>
</Policies>
```

### Compliance / Auditing

```xml
<Policies>
    <CronTriggeringPolicy schedule="0 0 0 * * ?"/>
</Policies>
```

---

## 10. Common Pitfalls

❌ Missing `%i` with size-based rolling  
❌ Using TimeBasedTriggeringPolicy without `%d`  
❌ Forgetting cleanup strategy → disk fills up  
❌ Assuming policies are ANDed (they are ORed)

---

## 11. Full Example (Production-Grade)

```xml
<RollingFile name="AppLog"
             fileName="logs/app.log"
             filePattern="logs/app-%d{yyyy-MM-dd}-%i.log.gz">

    <Policies>
        <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
        <SizeBasedTriggeringPolicy size="100 MB"/>
    </Policies>

    <DefaultRolloverStrategy max="14"/>

</RollingFile>
```

✔ Daily logs  
✔ Size protection  
✔ Automatic compression  
✔ Keeps last 14 files
