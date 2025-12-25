	 # 1. What is Redirect in Spring MVC?

## Definition

> **Redirect tells the browser to make a NEW HTTP request to a different URL.**

---

## What Happens Internally

```
Client → Server
Server → 302 Redirect
Client → NEW Request → Server
```

✔ URL changes  
✔ New request created  
❌ Request-scoped data lost

---

## Why Redirect Is Used (Real World)

### 1️⃣ Prevent duplicate form submission (PRG pattern)

### 2️⃣ Navigate between controllers

### 3️⃣ Clean URLs

---

## Interview Line

> **Redirect creates a new request and changes the browser URL.**

---

# 2. Redirect with `redirect:` Prefix

---

## Absolute Redirect (RECOMMENDED)

```java
@PostMapping("/login")
public String login() {
    return "redirect:/home";
}
```

✔ Clean  
✔ Predictable

---

## Relative Redirect (NOT RECOMMENDED)

```java
return "redirect:home";
```

❌ Confusing  
❌ Depends on current URL

---

# 3. Redirect with ModelAndView

## Example

```java
@PostMapping("/save")
public ModelAndView save() {

    ModelAndView mv = new ModelAndView();
    mv.setViewName("redirect:/success");

    return mv;
}
```

---

## Important

❌ Any model data added here is **lost**  
✔ Because redirect = new request

---

# 4. RedirectView (Low-Level API)

```java
@GetMapping("/go")
public RedirectView redirect() {
    return new RedirectView("/home");
}
```

✔ Explicit redirect  
✔ Low-level control

---

# 5. ModelMap

---

# 5.1 What is ModelMap?

## Definition

> **ModelMap is a Spring MVC data holder used to pass data from Controller to View within the SAME request.**

- Extends `LinkedHashMap`
    
- Request-scoped
    
- View-oriented
    

---

## KEY RULE (MEMORIZE)

> ❌ ModelMap does NOT survive redirect  
> ✅ ModelMap works with forward & normal view rendering

---

# 5.2 ModelMap – Normal View Rendering (WORKS)

```java
@GetMapping("/profile")
public String profile(ModelMap model) {

    model.addAttribute("username", "Ahmed");
    model.addAttribute("role", "ADMIN");

    return "profile";
}
```

### View

```jsp
Username: ${username}
Role: ${role}
```

---

# 6. ModelMap with ModelAndView (VERY IMPORTANT)

## Example

```java
@GetMapping("/dashboard")
public ModelAndView dashboard() {

    ModelAndView mv = new ModelAndView("dashboard");

    ModelMap model = mv.getModelMap();
    model.addAttribute("totalUsers", 120);
    model.addAttribute("activeUsers", 85);

    return mv;
}
```

---

## How It Works

- `ModelAndView` internally uses **ModelMap**
    
- Data is available to the view
    
- Same request → data preserved
    

---

## Interview Line

> **ModelAndView internally stores model data using ModelMap.**

---

# 7. ModelMap with Forward (WORKS)

## Definition of Forward

> **Forward transfers control internally without creating a new request.**

---

## Example

```java
@PostMapping("/process")
public String process(ModelMap model) {

    model.addAttribute("status", "processed");

    return "forward:/result";
}
```

---

## Target Controller

```java
@GetMapping("/result")
public String result(ModelMap model) {
    return "result";
}
```

---

## View

```jsp
Status: ${status}
```

✔ Same request  
✔ ModelMap preserved

---

# 8. ModelMap with Redirect (DOES NOT WORK)

## WRONG Example

```java
@PostMapping("/save")
public String save(ModelMap model) {

    model.addAttribute("msg", "Saved successfully");

    return "redirect:/success";
}
```

---

## Result

❌ `msg` is LOST

---

## Why?

- Redirect = new request
    
- ModelMap = request-scoped
    

---

## Correct Solution

Use **Flash Attributes**, not ModelMap.

---

# 9. Flash Attributes (Correct for Redirect)

```java
@PostMapping("/save")
public String save(RedirectAttributes attrs) {

    attrs.addFlashAttribute("msg", "Saved successfully");

    return "redirect:/success";
}
```

---

## Access After Redirect

```jsp
${msg}
```

✔ Not in URL  
✔ Available for ONE request

---

# 10. Forward vs Redirect (INTERVIEW FAVORITE)

|Feature|Forward|Redirect|
|---|---|---|
|New Request|❌|✅|
|URL Change|❌|✅|
|ModelMap|✅|❌|
|Flash Attributes|❌|✅|
|Client Involved|❌|✅|

---

# 11. When to Use ModelMap (DECISION GUIDE)

|Scenario|Use ModelMap|
|---|---|
|Normal view rendering|✅|
|ModelAndView|✅|
|Forward|✅|
|Redirect|❌|
|Passing temp UI data|✅|

---

# 12. FINAL MEMORIZATION CHEAT-SHEET

- **ModelMap** → request-scoped data holder
    
- **ModelAndView** → View + ModelMap
    
- **Redirect** → new request (ModelMap lost)
    
- **Forward** → same request (ModelMap kept)
    
- **Flash Attributes** → redirect-safe data
    
- **Absolute redirect paths** → best practice
    

---

## ONE-LINE INTERVIEW ANSWER 🧠

> **ModelMap is a request-scoped model used to pass data to views and is internally used by ModelAndView; it does not survive redirects.**

---

If you want next, best continuations are:  
🔥 `Model vs ModelMap vs ModelAndView` (deep comparison)  
🔥 FlashAttributes vs Session  
🔥 PRG pattern in real systems

Just tell me 🚀