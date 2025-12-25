# 🌍 Java Internationalization & Localization (Full Obsidian Summary)

---

# 1. Why We Need I18N & L10N

Applications today serve users worldwide. This requires:

- Correct **language**
    
- Correct **currency format**
    
- Correct **number symbols**
    
- Correct **date formats**
    
- Correct **sorting, messages, UI text**
    

### Real Life Example

|Country|Number|Currency|Date|
|---|---|---|---|
|US|12,345.67|$59.99|January 10, 2025|
|Germany|12.345,67|59,99 €|10. Januar 2025|
|Egypt (Arabic)|١٢٬٣٤٥٫٦٧|ج.م.‏ ٥٩٫٩٩|١٠ يناير ٢٠٢٥|

---

# 2. What Is I18N (Internationalization)?

Process of designing software that can support multiple languages **without modifying the source code**.

### Key Points

- Separate logic from text
    
- Use `Locale`, `ResourceBundle`
    
- Format numbers/dates dynamically
    
- Never hardcode text in UI
    

---

# 3. What Is L10N (Localization)?

Providing **actual translations and cultural formatting** for a specific region.

Examples:

- `Messages_en.properties`
    
- `Messages_fr.properties`
    
- `Messages_ar_EG.properties`
    

---

# 4. Difference Between I18N and L10N

|I18N|L10N|
|---|---|
|Make app ready for multiple languages|Provide translations|
|Developer task|Translator task|
|Includes locale-aware formatting|Language-specific strings|
|Done once|Done for every region|

---

# 5. `java.util.Locale`

A Locale represents a **language**, optional **country**, and optional **variant**.

---

# 6. Locale Constructors

### 6.1 Basic Constructors

```java
// 1. Language only
Locale en = new Locale("en");

// 2. Language + Country
Locale us = new Locale("en", "US");

// 3. Language + Country + Variant
Locale variant = new Locale("en", "US", "POSIX");
```

---

# 7. Locale.Builder (Preferred Modern Way)

```java
Locale locale = new Locale.Builder()
    .setLanguage("fr")
    .setRegion("CA")
    .setVariant("")
    .build();
```

More fields:

```java
Locale locale = new Locale.Builder()
    .setLanguage("zh")
    .setScript("Hant")   // Traditional Chinese
    .setRegion("TW")     // Taiwan
    .build();
```

---

# 8. Locale from Language Tag (IETF BCP 47)

Examples:

```java
Locale loc1 = Locale.forLanguageTag("en-US");
Locale loc2 = Locale.forLanguageTag("fr-CA");
Locale loc3 = Locale.forLanguageTag("ar-EG");
Locale loc4 = Locale.forLanguageTag("zh-Hans-CN"); // Simplified Chinese
```

Get language tag:

```java
String tag = Locale.US.toLanguageTag();   // "en-US"
```

---

# 9. Predefined Locale Constants

Java provides many built-ins:

```java
Locale.US
Locale.UK
Locale.CANADA
Locale.CANADA_FRENCH
Locale.FRANCE
Locale.GERMANY
Locale.ITALY
Locale.JAPAN
Locale.KOREA
Locale.CHINA
Locale.TAIWAN
```

---

# 10. Important Methods in `Locale`

### Get basic properties

```java
locale.getLanguage(); // "en"
locale.getCountry();  // "US"
locale.getScript();   // ""
locale.getVariant();  // ""
locale.toString();    // "en_US"
locale.toLanguageTag(); // "en-US"
```

### Display names (localized)

```java
Locale.FRANCE.getDisplayLanguage(Locale.ENGLISH); // "French"
Locale.FRANCE.getDisplayLanguage(Locale.FRENCH);  // "français"
Locale.FRANCE.getDisplayCountry(Locale.US);       // "France"
```

### List all available locales

```java
Locale[] all = Locale.getAvailableLocales();
```

---

# 11. Default Locale

### Get system default

```java
Locale def = Locale.getDefault();
```

### Set system default

```java
Locale.setDefault(Locale.GERMANY);
```

### Why important?

It controls:

- Number formatting
    
- Date formatting
    
- Default resource bundle selection
    

---

# 12. Formatting Numbers Using Locale

```java
double number = 12345.678;

NumberFormat nfUs = NumberFormat.getNumberInstance(Locale.US);
System.out.println(nfUs.format(number));
// 12,345.678

NumberFormat nfDe = NumberFormat.getNumberInstance(Locale.GERMANY);
System.out.println(nfDe.format(number));
// 12.345,678
```

---

# 13. Formatting Currency Using Locale

```java
double amount = 59.99;

System.out.println(NumberFormat.getCurrencyInstance(Locale.US).format(amount));
// $59.99

System.out.println(NumberFormat.getCurrencyInstance(Locale.JAPAN).format(amount));
// ￥60

System.out.println(NumberFormat.getCurrencyInstance(new Locale("ar", "EG")).format(amount));
// ج.م.‏ ٥٩٫٩٩
```

---

# 14. Formatting Dates Using Locale

```java
Date now = new Date();

DateFormat dfUs = DateFormat.getDateInstance(DateFormat.FULL, Locale.US);
System.out.println(dfUs.format(now));
// Friday, January 10, 2025

DateFormat dfFr = DateFormat.getDateInstance(DateFormat.FULL, Locale.FRANCE);
System.out.println(dfFr.format(now));
// vendredi 10 janvier 2025
```

---

# 15. Formatting Time Using Locale

```java
DateFormat tf = DateFormat.getTimeInstance(DateFormat.LONG, Locale.UK);
System.out.println(tf.format(now));
// 22:15:34 GMT
```

---

# 16. Formatting Using `DateTimeFormatter` (Modern API)

```java
LocalDate date = LocalDate.now();

DateTimeFormatter fmt = DateTimeFormatter
        .ofLocalizedDate(FormatStyle.FULL)
        .withLocale(Locale.GERMANY);

System.out.println(date.format(fmt));
// Freitag, 10. Januar 2025
```

---

# 17. Resource Bundles

Used to load language-specific messages.

---

# 18. Properties-Based Resource Bundles (Most Common)

### Files:

```
Messages.properties
Messages_en.properties
Messages_fr.properties
Messages_ar.properties
```

### Example: `Messages_en.properties`

```
greeting=Hello
farewell=Goodbye
```

### Example: `Messages_fr.properties`

```
greeting=Bonjour
farewell=Au revoir
```

### Usage:

```java
Locale locale = Locale.FRANCE;
ResourceBundle rb = ResourceBundle.getBundle("Messages", locale);

System.out.println(rb.getString("greeting"));  
// Bonjour
```

---

# 19. Class-Based Resource Bundles (`ListResourceBundle`)

```java
public class Messages_fr extends ListResourceBundle {
    @Override
    protected Object[][] getContents() {
        return new Object[][] {
            {"greeting", "Bonjour"},
            {"farewell", "Au revoir"},
            {"taxRate", 0.20} // can store objects
        };
    }
}
```

---

# 20. How Java Selects the Best Resource Bundle (Priority)

If we request:

```java
Locale locale = new Locale("fr", "CA");
```

Java looks in this order:

1. `Messages_fr_CA.properties`
    
2. `Messages_fr.properties`
    
3. `Messages.properties` (base)
    
4. **If none found → MissingResourceException**
    

---

# 21. Full Real-Life Example: Checkout Page

```java
Locale locale = new Locale("fr", "FR");
ResourceBundle rb = ResourceBundle.getBundle("Messages", locale);

double price = 1500.75;

System.out.println(rb.getString("order.success"));
System.out.println(NumberFormat.getCurrencyInstance(locale).format(price));
System.out.println(DateFormat.getDateInstance(DateFormat.FULL, locale).format(new Date()));
```

Output:

```
Votre commande a été confirmée
1 500,75 €
vendredi 10 janvier 2025
```

---

# ✔ Final Revision Checklist (For Exams & Interviews)

- ✔ Know all Locale constructors
    
- ✔ Know Locale.Builder
    
- ✔ Understand language tags (IETF BCP 47)
    
- ✔ Understand default locale behavior
    
- ✔ Know how numbers/dates/currencies are formatted
    
- ✔ Know resource bundles (properties + class-based)
    
- ✔ Know bundle fallback order
    
- ✔ Understand difference between I18N and L10N
    
