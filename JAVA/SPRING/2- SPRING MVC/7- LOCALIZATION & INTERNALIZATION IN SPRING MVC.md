# INTERNATIONALIZATION (i18n) & LOCALIZATION (l10n) IN SPRING MVC

## COMPLETE REAL-WORLD GUIDE (WITH LocaleChangeInterceptor)

---

# 1. i18n vs l10n (FOUNDATION)

## 1.1 Internationalization (i18n) ✅

> **i18n is designing the application to support multiple languages without changing code.**

### Examples (i18n)

- Using message keys instead of text
    
- Using `MessageSource`
    
- Using `LocaleResolver`
    
- Using `LocaleChangeInterceptor`
    

✔ Language-agnostic  
✔ Done at development time

---

## 1.2 Localization (l10n) ✅

> **l10n is providing language-specific content for a particular locale.**

### Examples (l10n)

- `messages_en.properties`
    
- `messages_fr.properties`
    
- `messages_de.properties`
    

✔ Language-specific  
✔ Done per region/language

---

## Memorization Rule

> **i18n = framework & design**  
> **l10n = translated content**

---

# 2. MessageSource Bean (i18n)

## Purpose

> **Loads locale-specific messages from property files.**

---

## Configuration

```java
@Bean
public MessageSource messageSource() {
    ResourceBundleMessageSource ms =
            new ResourceBundleMessageSource();
    ms.setBasename("messages");
    ms.setDefaultEncoding("UTF-8");
    return ms;
}
```

📌 **Category:** ✅ i18n  
(Enables multilingual support)

---

## Localization Files (l10n)

```text
messages.properties
messages_en.properties
messages_fr.properties
messages_de.properties
```

### Example

```properties
# messages_en.properties
welcome.message=Welcome
```

```properties
# messages_fr.properties
welcome.message=Bienvenue
```

📌 **Category:** ✅ l10n  
(Actual translations)

---

# 3. Practical Demo (Controller + View)

## Controller

```java
@Controller
public class HomeController {

    @GetMapping("/home")
    public String home() {
        return "home";
    }
}
```

📌 **Category:** ✅ i18n  
(Controller is locale-agnostic)

---

## JSP / Thymeleaf View

```jsp
<spring:message code="welcome.message"/>
```

📌 **Category:**

- i18n → using message key
    
- l10n → resolved text per locale
    

---

# 4. LocaleResolver (i18n – CORE DECISION POINT)

## Definition

> **Decides which locale to use for the current request.**

📌 **Category:** ✅ i18n

⚠ Only **ONE** LocaleResolver per app.

---

# 5. AcceptHeaderLocaleResolver (l10n driven by browser)

## What It Does

> Uses browser’s `Accept-Language` header.

```java
@Bean
public LocaleResolver localeResolver() {
    return new AcceptHeaderLocaleResolver();
}
```

📌 **Category:**

- i18n → resolver mechanism
    
- l10n → browser language decides content
    

### Example

```
Accept-Language: fr-FR
```

Result → `messages_fr.properties`

---

# 6. FixedLocaleResolver (l10n fixed)

## What It Does

> Forces a single locale.

```java
@Bean
public LocaleResolver localeResolver() {
    FixedLocaleResolver resolver =
            new FixedLocaleResolver();
    resolver.setDefaultLocale(Locale.US);
    return resolver;
}
```

📌 **Category:**

- i18n → resolver setup
    
- l10n → fixed language
    

---

# 7. SessionLocaleResolver (l10n per session)

## What It Does

> Stores locale in HTTP session.

```java
@Bean
public LocaleResolver localeResolver() {
    SessionLocaleResolver resolver =
            new SessionLocaleResolver();
    resolver.setDefaultLocale(Locale.ENGLISH);
    return resolver;
}
```

📌 **Category:**

- i18n → session-based switching
    
- l10n → user-specific language
    

---

## Manual Change (Without Interceptor)

```java
request.getSession().setAttribute(
    SessionLocaleResolver.LOCALE_SESSION_ATTRIBUTE_NAME,
    new Locale("fr")
);
```

---

# 8. CookieLocaleResolver (l10n persistent)

## What It Does

> Stores locale in browser cookie.

```java
@Bean
public LocaleResolver localeResolver() {
    CookieLocaleResolver resolver =
            new CookieLocaleResolver();
    resolver.setDefaultLocale(Locale.ENGLISH);
    resolver.setCookieName("lang");
    resolver.setCookieMaxAge(3600);
    return resolver;
}
```

📌 **Category:**

- i18n → cookie-based resolution
    
- l10n → persistent language choice
    

---

# 9. LocaleChangeInterceptor (i18n – DYNAMIC SWITCHING)

## What is LocaleChangeInterceptor?

> **Allows changing locale dynamically using a request parameter.**

📌 **Category:** ✅ i18n  
(enables language switching)

---

## Real-World Scenario

User clicks:

```
/home?lang=fr
/home?lang=de
```

---

## Configuration

```java
@Bean
public LocaleChangeInterceptor localeChangeInterceptor() {
    LocaleChangeInterceptor interceptor =
            new LocaleChangeInterceptor();
    interceptor.setParamName("lang");
    return interceptor;
}
```

---

## Register Interceptor

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(
            InterceptorRegistry registry) {

        registry.addInterceptor(
            localeChangeInterceptor()
        );
    }
}
```

---

## How It Works Internally

1. Request arrives with `lang=fr`
    
2. Interceptor detects parameter
    
3. Updates LocaleResolver
    
4. Locale stored (session/cookie)
    
5. MessageSource resolves correct file
    

---

## Result

```
/home?lang=fr → Bienvenue
/home?lang=en → Welcome
```

📌 **i18n** → interceptor logic  
📌 **l10n** → resolved text

---

# 10. LocaleContextHolder (i18n – INTERNAL ACCESS)

## Purpose

> **Access current locale anywhere in the application.**

```java
Locale locale =
    LocaleContextHolder.getLocale();
```

📌 **Category:** ✅ i18n

---

## Real-World Use Case (Email)

```java
public void sendEmail() {
    Locale locale =
        LocaleContextHolder.getLocale();

    String subject =
        messageSource.getMessage(
            "email.subject", null, locale);
}
```

📌 **i18n** → locale resolution  
📌 **l10n** → email language

---

# 11. COMPLETE FLOW (MEMORIZE)

```
Request
 ↓
LocaleChangeInterceptor (optional)   → i18n
 ↓
LocaleResolver                        → i18n
 ↓
LocaleContextHolder                  → i18n
 ↓
MessageSource                        → i18n
 ↓
messages_xx.properties               → l10n
 ↓
View renders localized text
```

---

# 12. Common Interview Questions (WITH ANSWERS)

### Q: Is LocaleChangeInterceptor i18n or l10n?

✔ **i18n** (mechanism to support languages)

---

### Q: Are message files i18n or l10n?

✔ **l10n** (actual translations)

---

### Q: Can we have multiple LocaleResolvers?

❌ No — only one

---

# 13. FINAL MEMORIZATION CHEAT-SHEET

|Component|i18n / l10n|
|---|---|
|MessageSource|i18n|
|LocaleResolver|i18n|
|LocaleChangeInterceptor|i18n|
|LocaleContextHolder|i18n|
|messages_xx.properties|l10n|
|Translated UI text|l10n|
