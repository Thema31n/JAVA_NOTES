# 1. Spring Forms (What & Why)

## What are Spring Forms?

> **Spring Form tags are JSP/Thymeleaf helpers that bind HTML form fields directly to a Java object (Model).**

They eliminate:

- Manual request parameter parsing
    
- Boilerplate HTML name matching
    

---

## Real-World Scenario

👉 User Registration Form

- Name
    
- Email
    
- Password
    

We want:

- Automatic binding
    
- Validation
    
- Error messages
    

---

# 2. `@ModelAttribute` Annotation

## Purpose

> **Binds form data to a Java object and adds it to the Model.**

---

## Example: Form Backing Object

```java
public class User {

    private String name;
    private String email;
    private String password;

    // getters & setters
}
```

---

## Controller – Show Form

```java
@GetMapping("/register")
public String showForm(Model model) {
    model.addAttribute("user", new User());
    return "register";
}
```

---

## Controller – Handle Submit

```java
@PostMapping("/register")
public String submitForm(
    @ModelAttribute("user") User user) {

    System.out.println(user.getName());
    return "success";
}
```

---

## How `@ModelAttribute` Works Internally

1. Spring creates object
    
2. Reads request parameters
    
3. Matches field names
    
4. Populates object
    
5. Adds it to Model
    

---

## Interview Line

> **@ModelAttribute binds request data to a Java object and exposes it to the view.**

---

# 3. Spring Form Tags (JSP Example)

```jsp
<form:form modelAttribute="user" method="post">

    Name:
    <form:input path="name"/>

    Email:
    <form:input path="email"/>

    Password:
    <form:password path="password"/>

    <input type="submit"/>

</form:form>
```

---

## Why Use Spring Forms?

✔ Automatic binding  
✔ Validation support  
✔ Error display support

---

# 4. Bean Validation API (JSR-303 / JSR-380)

## What is JSR-303?

> **JSR-303 is a Java specification for validating object fields using annotations.**

Implemented by:

- Hibernate Validator (most common)
    

---

## Why Use Bean Validation?

- Centralized validation rules
    
- Reusable
    
- Declarative
    

---

# 5. Setting Constraints on Object Fields

## Example: User Validation Rules

```java
import javax.validation.constraints.*;

public class User {

    @NotBlank(message = "Name is required")
    private String name;

    @Email(message = "Invalid email")
    @NotBlank
    private String email;

    @Size(min = 6, message = "Password must be at least 6 chars")
    private String password;

    // getters & setters
}
```

---

## Common Constraint Annotations (MEMORIZE)

|Annotation|Purpose|
|---|---|
|`@NotNull`|Not null|
|`@NotBlank`|Not empty string|
|`@Size`|Length|
|`@Min` / `@Max`|Numeric range|
|`@Email`|Email format|
|`@Pattern`|Regex|

---

# 6. `@Valid` Annotation

## Purpose

> **Triggers validation of the annotated object.**

---

## Controller Example

```java
@PostMapping("/register")
public String submitForm(
    @Valid @ModelAttribute("user") User user,
    BindingResult result) {

    if (result.hasErrors()) {
        return "register";
    }

    return "success";
}
```

---

## VERY IMPORTANT RULE (INTERVIEW FAVORITE)

> **`BindingResult` MUST come immediately after `@Valid` parameter.**

---

## How `@Valid` Works

1. Spring sees `@Valid`
    
2. Runs JSR-303 validation
    
3. Collects violations
    
4. Stores them in `BindingResult`
    

---

# 7. BindingResult

## What is BindingResult?

> **Holds validation errors and data binding errors.**

---

## Check Errors

```java
if (result.hasErrors()) {
    return "register";
}
```

---

## Access Field Errors

```java
result.getFieldError("email");
```

---

## Interview Line

> **BindingResult captures validation and binding errors for form objects.**

---

# 8. Displaying Validation Errors (JSP)

```jsp
<form:form modelAttribute="user">

    Name:
    <form:input path="name"/>
    <form:errors path="name"/>

    Email:
    <form:input path="email"/>
    <form:errors path="email"/>

    Password:
    <form:password path="password"/>
    <form:errors path="password"/>

</form:form>
```

---

## Result

- Errors shown near fields
    
- User-friendly UI
    

---

# 9. Validating a Model (End-to-End Flow)

### Full Validation Flow

1. User submits form
    
2. Spring binds data to object
    
3. `@Valid` triggers validation
    
4. Errors collected in `BindingResult`
    
5. If errors → show form again
    
6. If no errors → proceed
    

---

# 10. Data Binding Errors (Type Mismatch)

## Example Problem

```java
@Min(18)
private int age;
```

User enters:

```text
age = abc
```

---

## What Happens

- Binding error occurs
    
- Stored in `BindingResult`
    

---

## Handling Binding Errors

```java
if (result.hasErrors()) {
    return "register";
}
```

---

## Show Error Message

```jsp
<form:errors path="age"/>
```

---

# 11. Custom Validation Message

## `messages.properties`

```properties
NotBlank.user.name=Name cannot be empty
Size.user.password=Password too short
```

---

## Automatic Resolution

Spring resolves messages using:

- Field name
    
- Constraint type
    

---

# 12. Real-World Registration Example (SUMMARY)

### User submits form

✔ Data bound  
✔ Validated  
✔ Errors handled  
✔ Clean controller logic

---

# 13. Advantages of Spring Validation

### ✅ Clean separation of concerns

### ✅ Reusable rules

### ✅ Declarative

### ✅ Works with REST & MVC

---

# 14. Common Interview Mistakes (IMPORTANT)

❌ Forgetting `BindingResult`  
❌ Wrong parameter order  
❌ Using manual validation logic  
❌ Mixing validation inside service

---

# 15. FINAL MEMORIZATION CHEAT-SHEET

- **Spring Forms** → bind UI to object
    
- **@ModelAttribute** → bind request to model
    
- **JSR-303** → validation standard
    
- **Constraints** → annotations on fields
    
- **@Valid** → trigger validation
    
- **BindingResult** → holds errors
    
- **form:errors** → display errors
    
- **Data binding errors** → type mismatch
    
