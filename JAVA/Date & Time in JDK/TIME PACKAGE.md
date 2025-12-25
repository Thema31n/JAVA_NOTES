# #️⃣ **1. java.time Overview**

Modern, immutable, thread-safe API introduced in Java 8 via **JSR-310**.

Replaces old classes:

- `Date` — mutable, timezone-attached, problematic
    
- `Calendar` — extremely complex, error-prone
    
- `SimpleDateFormat` — NOT thread-safe
    

---

# #️⃣ **2. JSR-310 Goals**

- **Immutable** objects (safe for multithreading)
    
- **Fluent API** (easy, chainable methods)
    
- **ISO-8601** correctness
    
- Clear **timezone separation**
    
- Distinguish **Instant (machine)** vs **local date/time (human)**
    
- **Better parsing/formatting** through `DateTimeFormatter`
    

---

# #️⃣ **3. java.time Class Categories**

## **A. Human-Based (Calendar timeline)**

Values meaningful to humans, independent of exact UTC time:

- `LocalDate` – Only date
    
- `LocalTime` – Only time
    
- `LocalDateTime` – Date + time (no timezone)
    
- `ZonedDateTime` – Date + time + region timezone
    
- `OffsetDateTime` – Date + time + UTC offset
    
- `OffsetTime` – Time + UTC offset
    

## **B. Machine-Based (Epoch timeline)**

Values meaningful for computers:

- `Instant` – UTC timestamp
    
- `Duration` – Time difference (hours/minutes/seconds)
    
- `Period` – Calendar difference (years/months/days)
    
- `Clock` – Time-source abstraction
    

## **C. Framework / Low-Level**

- `java.time.temporal.*` – advanced temporal framework
    
- `TemporalAdjuster` – custom date modifications
    
- `TemporalUnit` – time units
    
- `ChronoField` – date/time fields
    
- `ChronoUnit` – time units (DAYS, HOURS, etc.)
    

---

# #️⃣ **4. Class-by-Class Function Reference (With Descriptions)**

Below is a full **reference + description** for every method you listed.

---

# ⭐ **Instant — Key Methods (UTC Timestamp)**

### **Creation**

- `Instant.now()`  
    Returns the current moment in UTC.
    
- `Instant.ofEpochSecond(long)`  
    Creates an instant from seconds since 1970-01-01T00:00Z.
    
- `Instant.ofEpochSecond(long, long)`  
    Same as above but with additional nanoseconds.
    
- `Instant.ofEpochMilli(long)`  
    Creates from epoch milliseconds.
    
- `Instant.parse(String)`  
    Parses an ISO-8601 formatted timestamp.
    

---

### **Manipulation**

- `plusSeconds(long)`  
    Adds seconds to the instant.
    
- `plusMillis(long)`  
    Adds milliseconds.
    
- `plusNanos(long)`  
    Adds nanoseconds.
    
- `minusSeconds(long)`  
    Subtracts seconds.
    
- `minusMillis(long)`  
    Subtracts milliseconds.
    
- `minusNanos(long)`  
    Subtracts nanoseconds.
    

---

### **Comparison**

- `isBefore(Instant)`  
    Checks if this instant is earlier.
    
- `isAfter(Instant)`  
    Checks if this instant is later.
    
- `compareTo(Instant)`  
    Returns negative/zero/positive depending on ordering.
    

---

### **Conversions**

- `toEpochMilli()`  
    Converts to milliseconds since epoch.
    
- `getEpochSecond()`  
    Returns epoch seconds.
    
- `atZone(ZoneId)`  
    Converts instant → ZonedDateTime in given timezone.
    

---

---

# ⭐ **LocalDate — Key Methods (Date Only)**

### **Creation**

- `LocalDate.now()`  
    Gets today’s date.
    
- `LocalDate.of(y, m, d)`  
    Creates date with specific values.
    
- `LocalDate.parse(String)`  
    Parses ISO date (`yyyy-MM-dd`).
    

---

### **Getters**

- `getYear()` – returns the year
    
- `getMonth()` – returns the enum Month
    
- `getDayOfMonth()` – 1–31
    
- `getDayOfWeek()` – MONDAY–SUNDAY
    
- `lengthOfMonth()` – 28–31
    
- `isLeapYear()` – true if February has 29 days
    

---

### **Manipulation**

- `plusDays(long)` – add days
    
- `plusMonths(long)` – add months
    
- `plusYears(long)` – add years
    
- `minusDays(long)` – subtract days
    
- `minusMonths(long)` – subtract months
    
- `minusYears(long)` – subtract years
    
- `with(TemporalAdjuster)` – apply rules (e.g., next Monday)
    

---

### **Comparison**

- `isAfter(LocalDate)` – true if later
    
- `isBefore(LocalDate)` – true if earlier
    
- `isEqual(LocalDate)` – true if same date
    
- `compareTo(LocalDate)` – ordering comparison
    

---

### **Conversion**

- `atStartOfDay()` – convert to `LocalDateTime` at 00:00
    
- `atTime(LocalTime)` – attach time
    
- `toEpochDay()` – number of days since epoch
    

---

---

# ⭐ **LocalTime — Key Methods (Time Only)**

### **Creation**

- `LocalTime.now()` – current time
    
- `LocalTime.of(h, m)` – specific hour/minute
    
- `LocalTime.parse(String)` – parses `HH:mm:ss`
    

---

### **Getters**

- `getHour()` – hour 0–23
    
- `getMinute()` – minute 0–59
    
- `getSecond()` – second 0–59
    

---

### **Manipulation**

- `plusHours(long)`
    
- `plusMinutes(long)`
    
- `plusSeconds(long)`
    
- `minusHours(long)`  
    All modify the time appropriately.
    

---

### **Comparison**

- `isBefore(LocalTime)`
    
- `isAfter(LocalTime)`
    
- `compareTo(LocalTime)`
    

---

---

# ⭐ **LocalDateTime — Key Methods (Date + Time, No Zone)**

### **Creation**

- `LocalDateTime.now()` – current date+time
    
- `LocalDateTime.of(y, m, d, h, min)` – specify all components
    
- `LocalDateTime.parse(String)` – parses `yyyy-MM-ddTHH:mm:ss`
    

---

### **Getters**

- `getYear()`
    
- `getMonth()`
    
- `getDayOfMonth()`
    
- `getHour()`  
    Standard accessors.
    

---

### **Manipulation**

- `plusDays(long)`
    
- `plusHours(long)`
    
- `minusMinutes(long)`  
    Modify the date+time.
    

---

### **Comparison**

- `isAfter(LocalDateTime)`
    
- `isBefore(LocalDateTime)`
    
- `compareTo(LocalDateTime)`
    

---

### **Conversion**

- `atZone(ZoneId)` – attach timezone
    
- `toLocalDate()` – extract date
    
- `toLocalTime()` – extract time
    

---

---

# ⭐ **ZonedDateTime — Key Methods (Date + Time + Zone)**

### **Creation**

- `ZonedDateTime.now()` – date/time in system zone
    
- `ZonedDateTime.now(ZoneId)` – in specified zone
    
- `ZonedDateTime.of(LocalDateTime, ZoneId)` – convert LDT to zone
    

---

### **Timezone Operations**

- `withZoneSameInstant(ZoneId)`  
    Converts to a new timezone while preserving the _moment_.
    
- `withZoneSameLocal(ZoneId)`  
    Keeps the same date/time values, only swaps zone.
    

---

### **Manipulation**

- `plusHours(long)` – add hours
    
- `plusDays(long)` – add days
    
- `minusMonths(long)` – subtract months
    

---

### **Getters**

- `getZone()` – returns ZoneId
    
- `getOffset()` – returns +02:00, −05:00, etc.
    

---

---

# ⭐ **OffsetDateTime — Key Methods (Date + Time + Offset)**

### **Creation**

- `OffsetDateTime.now()` – system zone offset
    
- `OffsetDateTime.of(dateTime, ZoneOffset)` – manual offset
    
- `OffsetDateTime.parse(String)` – parse ISO string
    

---

### **Manipulation**

- `plusDays(long)` – add days
    
- `minusHours(long)` – subtract hours
    

---

### **Getters**

- `getOffset()` – +02:00, etc.
    
- `getYear()` – year component
    

---

---

# ⭐ **Clock — Key Methods (Time Source)**

### **Creation**

- `Clock.systemUTC()` – uses UTC
    
- `Clock.systemDefaultZone()` – system timezone
    
- `Clock.fixed(Instant, ZoneId)` – constant fixed time
    
- `Clock.offset(Clock, Duration)` – shifted time source
    

---

### **Usage**

- `instant()` – gets Instant from this clock
    
- `getZone()` – gets zone
    

---

---

# ⭐ **Duration — Key Methods (Time-Based Amount)**

### **Creation**

- `Duration.ofHours(long)` – hours
    
- `Duration.ofMinutes(long)` – minutes
    
- `Duration.between(time1, time2)` – compute difference
    

---

### **Getters**

- `toHours()` – convert duration to hours
    
- `toMinutes()` – convert to minutes
    
- `getSeconds()` – raw seconds
    

---

### **Manipulation**

- `plusHours(long)` – add hours
    
- `minusMinutes(long)` – subtract minutes
    

---

---

# ⭐ **Period — Key Methods (Date-Based Amount)**

### **Creation**

- `Period.ofYears(int)`
    
- `Period.ofMonths(int)`
    
- `Period.ofDays(int)`
    
- `Period.between(date1, date2)` – calendar difference
    

---

### **Getters**

- `getYears()`
    
- `getMonths()`
    
- `getDays()`
    

---

### **Manipulation**

- `plus(Period)` – add periods
    
- `minus(Period)` – subtract periods
    

---

---

# ⭐ **java.time.temporal Package — Key Components**

### **ChronoUnit**

Units such as:

- `DAYS`, `HOURS`, `MONTHS`, `YEARS`  
    Used for measuring (`between`) or adding/subtracting.
    

### **ChronoField**

Field constants:

- `YEAR`, `MONTH_OF_YEAR`, `DAY_OF_MONTH`, `HOUR_OF_DAY`  
    Used to fetch or modify specific components.
    

### **TemporalAdjusters**

Useful rules:

- `next(DayOfWeek)` – next Monday, etc.
    
- `previous(DayOfWeek)` – previous Sunday
    
- `firstDayOfMonth()`
    
- `lastDayOfMonth()`
    

### **Temporal Interfaces**

- `TemporalAccessor` – read-only accessor
    
- `TemporalAdjuster` – rule to adjust date/time
    
- `TemporalAmount` – represents amount (Period/Duration)
    
- `TemporalUnit` – represents units (ChronoUnit)
    

---

# #️⃣ **5. Comparing Dates Summary**

### Works on:

`LocalDate`, `LocalDateTime`, `Instant`, `ZonedDateTime`

- `isBefore()` – earlier
    
- `isAfter()` – later
    
- `isEqual()` – same date/time
    
- `compareTo()` – ordering integer
    

---

```java
import java.time.*;
import java.time.chrono.IsoChronology;
import java.time.format.DateTimeFormatter;
import java.time.temporal.*;

public class FullJavaTimeDemo {

    public static void main(String[] args) {

        System.out.println("\n========================");
        System.out.println("      INSTANT API");
        System.out.println("========================");

        // Instant creation
        Instant instant1 = Instant.now();
        Instant instant2 = Instant.ofEpochSecond(1000000000);
        Instant instant3 = Instant.ofEpochMilli(123456789);
        Instant instant4 = Instant.parse("2020-01-01T10:15:30.00Z");

        System.out.println("instant1 = " + instant1);                // Current UTC timestamp
        System.out.println("instant2 = " + instant2);                // Timestamp at epoch second 1,000,000,000
        System.out.println("instant3 = " + instant3);                // Timestamp from epoch milliseconds
        System.out.println("instant4 = " + instant4);                // Parsed fixed Instant

        // Manipulation
        System.out.println("instant1 + 10s = " + instant1.plusSeconds(10));    // 10 seconds after instant1
        System.out.println("instant1 + 500ms = " + instant1.plusMillis(500));  // 500 ms after instant1
        System.out.println("instant1 + 200ns = " + instant1.plusNanos(200));   // 200 nanoseconds after instant1
        System.out.println("instant1 - 10s = " + instant1.minusSeconds(10));    // 10 seconds before instant1

        // Comparison
        System.out.println("instant1 > instant4 ? " + instant1.isAfter(instant4));     // true if instant1 occurs later
        System.out.println("instant1 < instant4 ? " + instant1.isBefore(instant4));    // true if instant1 occurs earlier
        System.out.println("instant1 compareTo instant4 = " + instant1.compareTo(instant4)); // positive/negative difference

        // Conversions
        System.out.println("instant1.toEpochMilli() = " + instant1.toEpochMilli()); // Milliseconds since epoch
        System.out.println("instant1.getEpochSecond() = " + instant1.getEpochSecond()); // Seconds since epoch
        System.out.println("instant1 at UTC zone = " + instant1.atZone(ZoneId.of("UTC"))); // Convert to ZonedDateTime

        // Fields
        System.out.println("instant1.getNano() = " + instant1.getNano()); // Nanoseconds part of the instant



        System.out.println("\n========================");
        System.out.println("     LOCALDATE API");
        System.out.println("========================");

        // Creation
        LocalDate ld1 = LocalDate.now();
        LocalDate ld2 = LocalDate.of(2025, 5, 12);
        LocalDate ld3 = LocalDate.parse("2025-05-12");

        System.out.println("ld1 = " + ld1);     // Today's date
        System.out.println("ld2 = " + ld2);     // Date created with of()
        System.out.println("ld3 = " + ld3);     // Parsed date

        // Getters
        System.out.println("ld1 year = " + ld1.getYear());            // Extract year
        System.out.println("ld1 month = " + ld1.getMonth());          // Extract month enum
        System.out.println("ld1 day = " + ld1.getDayOfMonth());       // Day of month
        System.out.println("ld1 dayOfWeek = " + ld1.getDayOfWeek());  // Day of week enum
        System.out.println("ld1 lengthOfMonth = " + ld1.lengthOfMonth()); // Number of days in month
        System.out.println("ld1 lengthOfYear = " + ld1.lengthOfYear());   // 365 or 366
        System.out.println("ld1 isLeapYear = " + ld1.isLeapYear());       // Whether the year is leap

        // Manipulation
        System.out.println("ld1 + 5 days = " + ld1.plusDays(5));        // Add days
        System.out.println("ld1 + 1 month = " + ld1.plusMonths(1));     // Add months
        System.out.println("ld1 + 1 year = " + ld1.plusYears(1));       // Add years
        System.out.println("ld1 - 3 days = " + ld1.minusDays(3));       // Subtract days

        // With/TemporalAdjusters
        System.out.println("ld1 with day 1 = " + ld1.withDayOfMonth(1));                       // First day of this month
        System.out.println("ld1 first day of month = " + ld1.with(TemporalAdjusters.firstDayOfMonth())); // First day adjuster
        System.out.println("ld1 last day of month = " + ld1.with(TemporalAdjusters.lastDayOfMonth()));   // Last day adjuster
        System.out.println("ld1 next Monday = " + ld1.with(TemporalAdjusters.next(DayOfWeek.MONDAY)));   // Next Monday

        // Comparison
        System.out.println("ld2 > ld1 ? " + ld2.isAfter(ld1));      // Compare dates
        System.out.println("ld2 < ld1 ? " + ld2.isBefore(ld1));
        System.out.println("ld2 == ld3 ? " + ld2.isEqual(ld3));     // True because same date
        System.out.println("ld2 compareTo ld3 = " + ld2.compareTo(ld3)); // Should be 0

        // Conversion
        System.out.println("ld1 start of day = " + ld1.atStartOfDay());        // LocalDateTime at midnight
        System.out.println("ld1 at time 10:30 = " + ld1.atTime(10, 30));       // Combine date+time
        System.out.println("ld1 epoch day = " + ld1.toEpochDay());            // Days since epoch



        System.out.println("\n========================");
        System.out.println("     LOCALTIME API");
        System.out.println("========================");

        // Creation
        LocalTime lt1 = LocalTime.now();
        LocalTime lt2 = LocalTime.of(10, 30);
        LocalTime lt3 = LocalTime.parse("22:15:30");

        System.out.println("lt1 = " + lt1);     // Current time
        System.out.println("lt2 = " + lt2);     // Fixed time 10:30
        System.out.println("lt3 = " + lt3);     // Parsed time 22:15:30

        // Getters
        System.out.println("lt1 hour = " + lt1.getHour());       // Hour part
        System.out.println("lt1 minute = " + lt1.getMinute());   // Minute part
        System.out.println("lt1 second = " + lt1.getSecond());   // Seconds
        System.out.println("lt1 nano = " + lt1.getNano());       // Nanoseconds

        // Manipulation
        System.out.println("lt1 + 1h = " + lt1.plusHours(1));       // Add hour
        System.out.println("lt1 + 30m = " + lt1.plusMinutes(30));   // Add minutes
        System.out.println("lt1 + 20s = " + lt1.plusSeconds(20));   // Add seconds
        System.out.println("lt1 - 2h = " + lt1.minusHours(2));      // Subtract hours

        // Comparison
        System.out.println("lt2 > lt3 ? " + lt2.isAfter(lt3));      // Compare times
        System.out.println("lt2 < lt3 ? " + lt2.isBefore(lt3));
        System.out.println("lt2 compareTo lt3 = " + lt2.compareTo(lt3)); // difference in order

        // Conversion
        System.out.println("lt2 with today's date = " + lt2.atDate(LocalDate.now())); // LocalDateTime



        System.out.println("\n========================");
        System.out.println("   LOCALDATETIME API");
        System.out.println("========================");

        // Creation
        LocalDateTime ldt1 = LocalDateTime.now();
        LocalDateTime ldt2 = LocalDateTime.of(2025, 5, 12, 10, 30);
        LocalDateTime ldt3 = LocalDateTime.parse("2025-05-12T10:30:15");

        System.out.println("ldt1 = " + ldt1);   // Current date and time
        System.out.println("ldt2 = " + ldt2);   // Hard-coded LocalDateTime
        System.out.println("ldt3 = " + ldt3);   // Parsed LocalDateTime

        // Getters
        System.out.println("ldt1 year = " + ldt1.getYear());     // Extract year
        System.out.println("ldt1 month = " + ldt1.getMonth());   // Month enum
        System.out.println("ldt1 day = " + ldt1.getDayOfMonth());// Day of month
        System.out.println("ldt1 hour = " + ldt1.getHour());     // Hour
        System.out.println("ldt1 minute = " + ldt1.getMinute()); // Minute

        // Manipulation
        System.out.println("ldt1 + 1 day = " + ldt1.plusDays(1));  // Add day
        System.out.println("ldt1 + 3 hours = " + ldt1.plusHours(3)); // Add hours
        System.out.println("ldt1 - 2 months = " + ldt1.minusMonths(2)); // Subtract months

        // Comparison
        System.out.println("ldt2 > ldt3 ? " + ldt2.isAfter(ldt3));  // Compare LocalDateTimes
        System.out.println("ldt2 < ldt3 ? " + ldt2.isBefore(ldt3));
        System.out.println("ldt2 compareTo ldt3 = " + ldt2.compareTo(ldt3)); // ordering

        // Conversion
        System.out.println("ldt1.toLocalDate() = " + ldt1.toLocalDate()); // Extract date part
        System.out.println("ldt1.toLocalTime() = " + ldt1.toLocalTime()); // Extract time part
        System.out.println("ldt1 at UTC = " + ldt1.atZone(ZoneId.of("UTC"))); // Convert to ZonedDateTime



        System.out.println("\n========================");
        System.out.println("   ZONEDDATETIME API");
        System.out.println("========================");

        // Creation
        ZonedDateTime zdt1 = ZonedDateTime.now();
        ZonedDateTime zdt2 = ZonedDateTime.now(ZoneId.of("America/New_York"));
        ZonedDateTime zdt3 = ZonedDateTime.of(ldt2, ZoneId.of("Asia/Tokyo"));

        System.out.println("zdt1 = " + zdt1);  // Current date/time with system zone
        System.out.println("zdt2 = " + zdt2);  // Current date/time NY
        System.out.println("zdt3 = " + zdt3);  // ldt2 in Tokyo zone

        // Getters
        System.out.println("zdt2 zone = " + zdt2.getZone());     // ZoneId of New York
        System.out.println("zdt2 offset = " + zdt2.getOffset()); // UTC offset

        // Manipulation
        System.out.println("zdt1 + 3h = " + zdt1.plusHours(3));  // Add hours
        System.out.println("zdt1 - 1d = " + zdt1.minusDays(1));  // Subtract day

        // Zone conversions
        System.out.println("NY → Paris same instant = " + zdt2.withZoneSameInstant(ZoneId.of("Europe/Paris"))); // preserve moment
        System.out.println("NY → Paris same LocalDateTime = " + zdt2.withZoneSameLocal(ZoneId.of("Europe/Paris"))); // preserve fields



        System.out.println("\n========================");
        System.out.println("   OFFSETDATETIME API");
        System.out.println("========================");

        // Creation
        OffsetDateTime odt1 = OffsetDateTime.now();
        OffsetDateTime odt2 = OffsetDateTime.of(ldt2, ZoneOffset.of("+02:00"));
        OffsetDateTime odt3 = OffsetDateTime.parse("2024-01-01T10:15:30+03:00");

        System.out.println("odt1 = " + odt1);  // Current date/time with offset
        System.out.println("odt2 = " + odt2);  // ldt2 with +02:00 offset
        System.out.println("odt3 = " + odt3);  // Parsed OffsetDateTime

        // Getters
        System.out.println("odt1 offset = " + odt1.getOffset()); // UTC offset
        System.out.println("odt1 year = " + odt1.getYear());     // Extract year

        // Manipulation
        System.out.println("odt1 + 2d = " + odt1.plusDays(2));    // Add days
        System.out.println("odt1 - 5h = " + odt1.minusHours(5));  // Subtract hours



        System.out.println("\n========================");
        System.out.println("        CLOCK API");
        System.out.println("========================");

        // Creation
        Clock c1 = Clock.systemUTC();
        Clock c2 = Clock.systemDefaultZone();
        Clock c3 = Clock.fixed(Instant.now(), ZoneId.of("UTC"));
        Clock c4 = Clock.offset(c1, Duration.ofHours(2));

        System.out.println("c1 instant = " + c1.instant()); // Current UTC time
        System.out.println("c2 instant = " + c2.instant()); // System default time
        System.out.println("c3 instant = " + c3.instant()); // Fixed instant
        System.out.println("c4 instant = " + c4.instant()); // UTC clock + 2 hours



        System.out.println("\n========================");
        System.out.println("      DURATION API");
        System.out.println("========================");

        // Creation
        Duration dur1 = Duration.ofHours(5);
        Duration dur2 = Duration.ofMinutes(90);
        Duration dur3 = Duration.between(LocalTime.of(10, 0), LocalTime.of(15, 30));

        System.out.println("dur1 = " + dur1); // 5 hours duration
        System.out.println("dur2 = " + dur2); // 90 minutes
        System.out.println("dur3 = " + dur3); // Time difference 10:00 → 15:30

        // Getters
        System.out.println("dur3 hours = " + dur3.toHours());        // Total hours
        System.out.println("dur3 minutes = " + dur3.toMinutes());    // Total minutes
        System.out.println("dur3 seconds = " + dur3.getSeconds());   // Total seconds

        // Manipulation
        System.out.println("dur1 + 30m = " + dur1.plusMinutes(30));  // Add minutes
        System.out.println("dur2 - 30m = " + dur2.minusMinutes(30)); // Subtract minutes



        System.out.println("\n========================");
        System.out.println("       PERIOD API");
        System.out.println("========================");

        // Creation
        Period p1 = Period.ofYears(2);
        Period p2 = Period.ofMonths(3);
        Period p3 = Period.ofDays(10);
        Period p4 = Period.between(LocalDate.of(2020, 1, 1), LocalDate.now());

        System.out.println("p1 = " + p1); // Period of 2 years
        System.out.println("p2 = " + p2); // Period of 3 months
        System.out.println("p3 = " + p3); // Period of 10 days
        System.out.println("p4 = " + p4); // Difference between 2020-01-01 and today

        // Getters
        System.out.println("p4 years = " + p4.getYears());   // Years in period
        System.out.println("p4 months = " + p4.getMonths()); // Months in period
        System.out.println("p4 days = " + p4.getDays());     // Days in period

        // Manipulation
        System.out.println("p1 + p2 = " + p1.plus(p2)); // Add periods
        System.out.println("p1 - 1 month = " + p1.minus(Period.ofMonths(1))); // Subtract month



        System.out.println("\n=========================================");
        System.out.println("   TEMPORAL ADJUSTERS / CHRONO API");
        System.out.println("=========================================");

        LocalDate today = LocalDate.now();

        System.out.println("first day of month = " + TemporalAdjusters.firstDayOfMonth().adjustInto(today)); // First day
        System.out.println("last day of month = " + TemporalAdjusters.lastDayOfMonth().adjustInto(today));   // Last day
        System.out.println("next Sunday = " + TemporalAdjusters.next(DayOfWeek.SUNDAY).adjustInto(today));   // Next Sunday

        // ChronoUnit
        System.out.println("Days between today and +10 = " +
            ChronoUnit.DAYS.between(LocalDate.now(), LocalDate.now().plusDays(10))); // 10 days

        System.out.println("Months between 2020-01-01 and today = " +
            ChronoUnit.MONTHS.between(LocalDate.of(2020, 1, 1), LocalDate.now())); // Month difference

        // ChronoField
        System.out.println("today dayOfMonth = " + today.get(ChronoField.DAY_OF_MONTH)); // Day number
        System.out.println("today year = " + today.get(ChronoField.YEAR));             // Year number

        // TemporalAccessor example
        TemporalAccessor ta = ldt1;
        System.out.println("ta supports hour? " + ta.isSupported(ChronoField.HOUR_OF_DAY)); // Does LocalDateTime support hour field?
        System.out.println("ta hour = " + ta.get(ChronoField.HOUR_OF_DAY));                // Extract hour

    }
}
```
