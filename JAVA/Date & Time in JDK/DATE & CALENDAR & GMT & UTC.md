
# 🟦 **GMT & UTC (Time Standards)**

## **UTC — Coordinated Universal Time**

- Modern global time standard.
    
- Atomic-clock based.
    
- Includes leap seconds.
    
- Baseline for all servers.
    

## **GMT — Greenwich Mean Time**

- Older astronomical time system.
    
- Practically same as UTC for most applications.
    
- No leap seconds.
    

## Example — Print UTC & GMT

```java
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss z");

// UTC
sdf.setTimeZone(TimeZone.getTimeZone("UTC"));
System.out.println(sdf.format(new Date()));
// Output example: 2025-01-20 18:25:03 UTC

// GMT
sdf.setTimeZone(TimeZone.getTimeZone("GMT"));
System.out.println(sdf.format(new Date()));
// Output example: 2025-01-20 18:25:03 GMT
```

---

# 🟦 **java.util.Date**

Represents a **timestamp** — milliseconds since Unix Epoch.

## Constructors

```java
Date now = new Date();
System.out.println(now);
// Output: Mon Jan 20 18:25:03 UTC 2025

Date fromMillis = new Date(0);
System.out.println(fromMillis);
// Output: Thu Jan 01 00:00:00 UTC 1970
```

## Methods

```java
long ms = now.getTime();
System.out.println(ms);
// Output: epoch millis

now.setTime(2000);
System.out.println(now);
// Output: Thu Jan 01 00:00:02 UTC 1970

System.out.println(now.before(fromMillis));  // false
System.out.println(now.after(fromMillis));   // true
System.out.println(now.equals(fromMillis));  // false
```

---

# 🟦 **java.util.Calendar**

## Features

- Field-based API (YEAR, MONTH, DAY, etc.)
    
- Time-zone aware
    
- Mutable
    
- Complicated but historically used
    

## Creating calendars

```java
Calendar cal = Calendar.getInstance();
System.out.println(cal.getTime());
// Output: current date/time

Calendar utcCal = Calendar.getInstance(TimeZone.getTimeZone("UTC"));
System.out.println(utcCal.getTime());
// Output: current UTC time
```

---

# 🟦 **GregorianCalendar — FULL FIELD EXAMPLE**

```java
GregorianCalendar cal =
    new GregorianCalendar(2025, Calendar.JANUARY, 20, 14, 35, 50);
cal.set(Calendar.MILLISECOND, 123);

System.out.println("Date: " + cal.getTime());
// Output: Mon Jan 20 14:35:50 <TZ> 2025

System.out.println("YEAR = " + cal.get(Calendar.YEAR));
// 2025

System.out.println("MONTH = " + cal.get(Calendar.MONTH));
// 0 (January)

System.out.println("DAY_OF_MONTH = " + cal.get(Calendar.DAY_OF_MONTH));
// 20

System.out.println("DAY_OF_WEEK = " + cal.get(Calendar.DAY_OF_WEEK));
// 2 (Monday)

System.out.println("DAY_OF_YEAR = " + cal.get(Calendar.DAY_OF_YEAR));
// 20

System.out.println("WEEK_OF_MONTH = " + cal.get(Calendar.WEEK_OF_MONTH));
// 4

System.out.println("WEEK_OF_YEAR = " + cal.get(Calendar.WEEK_OF_YEAR));
// 4

System.out.println("HOUR (12h) = " + cal.get(Calendar.HOUR));
// 2

System.out.println("HOUR_OF_DAY (24h) = " + cal.get(Calendar.HOUR_OF_DAY));
// 14

System.out.println("MINUTE = " + cal.get(Calendar.MINUTE));
// 35

System.out.println("SECOND = " + cal.get(Calendar.SECOND));
// 50

System.out.println("MILLISECOND = " + cal.get(Calendar.MILLISECOND));
// 123

System.out.println("AM_PM = " + cal.get(Calendar.AM_PM));
// 1 (PM)

System.out.println("ZONE_OFFSET = " + cal.get(Calendar.ZONE_OFFSET));
// Output: 7200000 (example)

System.out.println("DST_OFFSET = " + cal.get(Calendar.DST_OFFSET));
// Output: 0 or 3600000 depending on DST

cal.add(Calendar.DAY_OF_MONTH, 5);
System.out.println("After add 5 days: " + cal.getTime());
// Output: Sat Jan 25 <time>

cal.roll(Calendar.HOUR_OF_DAY, 10);
System.out.println("After roll +10h: " + cal.getTime());
// Output: Sat Jan 25 00:35:50 <TZ> 2025
```

---

# 🟦 **GregorianCalendar Constructors**

```java
GregorianCalendar c1 = new GregorianCalendar();
System.out.println(c1.getTime());
// Output: current date

GregorianCalendar c2 = new GregorianCalendar(2025, Calendar.MARCH, 10);
System.out.println(c2.getTime());
// Output: Mon Mar 10 00:00:00 <TZ> 2025

GregorianCalendar c3 = new GregorianCalendar(2025, Calendar.MARCH, 10, 16, 45);
System.out.println(c3.getTime());
// Output: Mon Mar 10 16:45:00 <TZ> 2025

GregorianCalendar c4 = new GregorianCalendar(2025, Calendar.MARCH, 10, 16, 45, 30);
System.out.println(c4.getTime());
// Output: Mon Mar 10 16:45:30 <TZ> 2025
```

---

# 🟦 **Unix Epoch / Unix Time**

```java
long millis = System.currentTimeMillis();
System.out.println(millis);
// Output: 1737395103000

long seconds = millis / 1000;
System.out.println(seconds);
// Output example: 1737395103

Date epoch = new Date(0);
System.out.println(epoch);
// Output: Thu Jan 01 00:00:00 UTC 1970
```

---

# 🟦 **Convert Date ↔ Calendar**

## Calendar → Date

```java
Calendar cal = Calendar.getInstance();
Date date = cal.getTime();
System.out.println(date);
// Output: current date
```

## Date → Calendar

```java
Date d = new Date(0);
Calendar cal = Calendar.getInstance();
cal.setTime(d);
System.out.println(cal.getTime());
// Output: Thu Jan 01 00:00:00 UTC 1970
```

---

# 🟦 **Parsing (String → Date)**

```java
SimpleDateFormat parser = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

Date parsed = parser.parse("2025-01-20 18:00:00");
System.out.println(parsed);
// Output: Mon Jan 20 18:00:00 <TZ> 2025
```

---

# 🟦 **Formatting (Date → String)**

```java
SimpleDateFormat fmt =
    new SimpleDateFormat("EEEE dd MMM yyyy HH:mm:ss Z");

String out = fmt.format(parsed);
System.out.println(out);
// Output: Monday 20 Jan 2025 18:00:00 +0200 (example)
```

---

# 🟦 **java.util.TimeZone (Old API)**

```java
TimeZone tz = TimeZone.getTimeZone("Africa/Cairo");
System.out.println(tz.getID());
// Output: Africa/Cairo

System.out.println(tz.getRawOffset());
// Output: 7200000 (example)
```

---

# 🟦 **ZoneId (Modern Time Zone API)**

`ZoneId` is the modern replacement for `TimeZone`.

## Example: Creating ZoneIds

```java
ZoneId z1 = ZoneId.of("UTC");
ZoneId z2 = ZoneId.of("Europe/London");
ZoneId z3 = ZoneId.systemDefault();

System.out.println(z1);   // UTC
System.out.println(z2);   // Europe/London
System.out.println(z3);   // Your system's zone
```

---

# 🟦 **Available Zone IDs**

```java
Set<String> zones = ZoneId.getAvailableZoneIds();
System.out.println(zones.size());
// Output example: 600+

System.out.println(zones.iterator().next());
// Output example: Africa/Abidjan
```

---

# 🟦 **ZoneOffset**

Represents a **fixed offset** from UTC, like `+02:00` or `-05:00`.

```java
ZoneOffset offset = ZoneOffset.ofHours(2);
System.out.println(offset);
// Output: +02:00

ZoneOffset offset2 = ZoneOffset.of("-05:00");
System.out.println(offset2);
// Output: -05:00
```

---

# 🟦 **ZoneRules**

Every ZoneId has DST and offset rules.

```java
ZoneId zone = ZoneId.of("Europe/Berlin");
ZoneRules rules = zone.getRules();

System.out.println(rules.getOffset(Instant.now()));
// Output example: +01:00 or +02:00 (depending on DST)
```

---

# 🟥 **Date/Calendar Drawbacks**

- ❌ Mutable
    
- ❌ Hard to reason about
    
- ❌ Zero-based months
    
- ❌ SimpleDateFormat not thread-safe
    
- ❌ TimeZone confusing
    
- ❌ DST bugs
    
- ❌ Many deprecated APIs
    

💡 Modern solution = **java.time** (`Instant`, `LocalDate`, `ZonedDateTime`, etc.)

---

# 🟩 **FULL UNIFIED EXAMPLE — EVERYTHING TOGETHER**

```java
import java.util.*;
import java.text.*;
import java.time.*;

public class FinalDemo {
    public static void main(String[] args) throws Exception {

        // ===== Date =====
        Date now = new Date();
        System.out.println("Now=" + now);
        // Output: current time

        // ===== Calendar =====
        Calendar cal = Calendar.getInstance();
        cal.set(2025, Calendar.JANUARY, 20, 15, 30, 10);
        System.out.println("Calendar=" + cal.getTime());
        // Output: Mon Jan 20 15:30:10 <TZ> 2025

        // ===== GregorianCalendar fields =====
        GregorianCalendar gc =
            new GregorianCalendar(2025, Calendar.JANUARY, 20, 14, 35, 50);

        System.out.println("YEAR=" + gc.get(Calendar.YEAR)); // 2025
        System.out.println("MONTH=" + gc.get(Calendar.MONTH)); // 0
        System.out.println("DAY=" + gc.get(Calendar.DAY_OF_MONTH)); // 20

        // ===== ZoneId =====
        ZoneId z1 = ZoneId.of("UTC");
        ZoneId z2 = ZoneId.of("Europe/Paris");
        System.out.println(z1); // UTC
        System.out.println(z2); // Europe/Paris

        // ===== Available zones =====
        System.out.println("Zone count=" + ZoneId.getAvailableZoneIds().size());
        // ~600

        // ===== ZoneOffset =====
        ZoneOffset off = ZoneOffset.ofHours(2);
        System.out.println(off); // +02:00

        // ===== ZoneRules =====
        ZoneRules r = ZoneId.of("Europe/Berlin").getRules();
        System.out.println("Offset now=" + r.getOffset(Instant.now()));
        // Output: +01:00 or +02:00 (DST)
    }
}
```
