## 1. What Is a Rollover Strategy?

A **RolloverStrategy** controls:

- How rolled files are **named**
    
- How many files are **kept**
    
- Whether old logs are **deleted**
    
- Whether logs are **compressed**
    
- How file indexes (`%i`) are handled
    

It works **only with `RollingFileAppender`**.

---

## 2. Types of Rollover Strategies

Log4j2 mainly provides **two strategies**:

|Strategy|Use Case|
|---|---|
|**DefaultRolloverStrategy**|Most common, index-based|
|**DirectWriteRolloverStrategy**|Time-based, no active file rename|

---

## 3. DefaultRolloverStrategy (Most Used)

### Purpose

- Renames the current log file
    
- Applies indexing (`%i`)
    
- Enforces retention (`max`)
    
- Supports cleanup actions
    

### Basic Configuration

```xml
<DefaultRolloverStrategy max="10"/>
```

### How It Works

1. Triggering policy fires
    
2. Current log file is renamed using `filePattern`
    
3. New active file starts
    
4. Old files beyond `max` are deleted
    

---

### Index Handling (`%i`)

```xml
filePattern="logs/app-%i.log"
```

If `max="3"`:

```
app.log      (current)
app-1.log
app-2.log
app-3.log
```

When rolling again:

```
app-3.log → deleted
app-2.log → app-3.log
app-1.log → app-2.log
app.log   → app-1.log
```

---

### Key Attributes

|Attribute|Meaning|
|---|---|
|`max`|Max number of rolled files|
|`min`|Starting index|
|`fileIndex`|`min`, `max`, or `nomax`|

Example:

```xml
<DefaultRolloverStrategy max="30" fileIndex="min"/>
```

---

## 4. Compression in Rollover Strategy

Compression is driven by **filePattern**, not the strategy itself.

### Example

```xml
filePattern="logs/app-%d{yyyy-MM-dd}-%i.log.gz"
```

Result:

```
app-2025-12-16-1.log.gz
app-2025-12-16-2.log.gz
```

Supported formats:

- `.gz`
    
- `.zip`
    
- `.bz2`
    
- `.xz`
    

---

## 5. Delete Actions (Advanced Cleanup)

Instead of simple `max`, you can define **delete rules**.

### Example: Delete Files Older Than 14 Days

```xml
<DefaultRolloverStrategy>
    <Delete basePath="logs">
        <IfLastModified age="14d"/>
    </Delete>
</DefaultRolloverStrategy>
```

### Conditional Deletion Options

|Condition|Purpose|
|---|---|
|`IfLastModified`|Delete by age|
|`IfFileName`|Match patterns|
|`IfAccumulatedFileSize`|Cap disk usage|
|`IfAny` / `IfAll`|Combine rules|

---

## 6. Disk-Quota-Based Cleanup (Very Useful)

```xml
<DefaultRolloverStrategy>
    <Delete basePath="logs">
        <IfAccumulatedFileSize exceeds="5 GB"/>
    </Delete>
</DefaultRolloverStrategy>
```

✔ Keeps logs under disk limits  
✔ Deletes oldest first

---

## 7. DirectWriteRolloverStrategy

### Purpose

- Writes logs **directly to the rolled file**
    
- No renaming of active file
    
- Best for **pure time-based logging**
    

### Example

```xml
<RollingFile name="RollingFile"
             filePattern="logs/app-%d{yyyy-MM-dd}.log">
    <DirectWriteRolloverStrategy/>
    <Policies>
        <TimeBasedTriggeringPolicy/>
    </Policies>
</RollingFile>
```

### Differences vs Default Strategy

|Feature|Default|DirectWrite|
|---|---|---|
|Active file|`app.log`|No active file|
|Rename on roll|Yes|No|
|Index support|Yes|No `%i`|
|Performance|Good|Slightly better|

---

## 8. Strategy + Policy Relationship

Example:

```xml
<Policies>
    <SizeBasedTriggeringPolicy size="100 MB"/>
</Policies>

<DefaultRolloverStrategy max="10"/>
```

Flow:

1. Size reaches 100 MB
    
2. Policy triggers
    
3. Strategy renames and deletes old logs
    

---

## 9. Common Production Patterns

### Time + Size + Retention

```xml
<Policies>
    <TimeBasedTriggeringPolicy interval="1"/>
    <SizeBasedTriggeringPolicy size="200 MB"/>
</Policies>

<DefaultRolloverStrategy max="14"/>
```

### Compliance (Age-Based Retention)

```xml
<DefaultRolloverStrategy>
    <Delete basePath="logs">
        <IfLastModified age="90d"/>
    </Delete>
</DefaultRolloverStrategy>
```

### Container-Friendly

```xml
<DirectWriteRolloverStrategy/>
```

---

## 10. Common Mistakes

❌ Assuming `max` deletes by date (it deletes by count)  
❌ Using `%i` with `DirectWriteRolloverStrategy`  
❌ Forgetting delete rules for long-running apps  
❌ Not compressing rolled files

---

## 11. Full Example (Recommended)

```xml
<RollingFile name="AppLog"
             fileName="logs/app.log"
             filePattern="logs/app-%d{yyyy-MM-dd}-%i.log.gz">

    <Policies>
        <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
        <SizeBasedTriggeringPolicy size="100 MB"/>
    </Policies>

    <DefaultRolloverStrategy max="14">
        <Delete basePath="logs">
            <IfLastModified age="30d"/>
        </Delete>
    </DefaultRolloverStrategy>

</RollingFile>
```

✔ Time + size rollover  
✔ Compression  
✔ Count-based + age-based cleanup  
✔ Production-safe
