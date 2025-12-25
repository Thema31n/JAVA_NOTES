## 1. `Model` vs `ModelMap`

### A. `Model` Example (Recommended)

```java
@GetMapping("/user")
public String user(Model model) {
    model.addAttribute("name", "Ahmed");
    return "user";   // user.html
}
```

**View (Thymeleaf)**

```html
<p th:text="${name}"></p>
```

---

### B. `ModelMap` Example

```java
@GetMapping("/user")
public String user(ModelMap modelMap) {
    modelMap.addAttribute("name", "Ahmed");
    modelMap.put("age", 30);
    return "user";
}
```

➡️ Same behavior as `Model`, just Map-style access.

---

## 2. `Model` with Forward vs Redirect

---

### A. `forward:` ✅ Model Works

```java
@GetMapping("/step1")
public String step1(Model model) {
    model.addAttribute("msg", "Forward works");
    return "forward:/step2";
}

@GetMapping("/step2")
public String step2() {
    return "result";
}
```

**Why it works:** same HTTP request → request attributes preserved.

---

### B. `redirect:` ❌ Model Fails

```java
@GetMapping("/step1")
public String step1(Model model) {
    model.addAttribute("msg", "This will be lost");
    return "redirect:/step2";
}

@GetMapping("/step2")
public String step2() {
    return "result";  // msg NOT available
}
```

**Why it fails:** redirect = new request.

---

## 3. `RedirectAttributes` (For Redirects) ✅

### Example: Flash Attribute

```java
@PostMapping("/save")
public String save(RedirectAttributes ra) {
    ra.addFlashAttribute("msg", "Saved successfully");
    return "redirect:/result";
}
```

```java
@GetMapping("/result")
public String result() {
    return "result";
}
```

**View**

```html
<p th:if="${msg}" th:text="${msg}"></p>
```

➡️ Survives redirect  
➡️ Removed automatically after access

---

### Example: URL Parameter (NOT Flash)

```java
@PostMapping("/save")
public String save(RedirectAttributes ra) {
    ra.addAttribute("id", 10);
    return "redirect:/result";
}
```

Resulting URL:

```
/result?id=10
```

---

## 4. `FlashMap` (Underlying Mechanism) 🔑

### A. Indirect Usage (Recommended)

```java
@PostMapping("/save")
public String save(RedirectAttributes ra) {
    ra.addFlashAttribute("status", "OK");
    return "redirect:/result";
}
```

➡️ Spring internally creates a `FlashMap`

---

### B. Direct FlashMap Usage (Advanced / Rare)

```java
@PostMapping("/save")
public String save(HttpServletRequest request) {

    FlashMap flashMap = new FlashMap();
    flashMap.put("msg", "Saved via FlashMap");

    FlashMapManager manager =
        RequestContextUtils.getFlashMapManager(request);

    manager.saveOutputFlashMap(
        flashMap, request, null
    );

    return "redirect:/result";
}
```

⚠️ **Not recommended** unless you know what you’re doing  
➡️ `RedirectAttributes` is cleaner and safer

---

## 5. Flash Attributes vs Session (Code)

---

### Flash Attribute (Auto cleanup)

```java
@PostMapping("/save")
public String save(RedirectAttributes ra) {
    ra.addFlashAttribute("success", "One-time message");
    return "redirect:/result";
}
```

---

### Session Attribute (Manual cleanup)

```java
@GetMapping("/login")
public String login(HttpSession session) {
    session.setAttribute("user", "Ahmed");
    return "home";
}
```

```java
@GetMapping("/logout")
public String logout(HttpSession session) {
    session.invalidate();
    return "redirect:/login";
}
```

---

## 6. Complete Comparison Table (with Code Reference)

|Scenario|Code to Use|
|---|---|
|Normal view|`Model` / `ModelMap`|
|forward|`Model`|
|redirect|`RedirectAttributes.addFlashAttribute()`|
|URL params|`RedirectAttributes.addAttribute()`|
|Internal mechanism|`FlashMap`|
|User state|`HttpSession`|

---

## 7. PRG Pattern (Post-Redirect-Get)

### ❌ Wrong (Duplicate submit risk)

```java
@PostMapping("/save")
public String save(Model model) {
    model.addAttribute("msg", "Saved");
    return "result";
}
```

---

### ✅ Correct (PRG)

```java
@PostMapping("/save")
public String save(RedirectAttributes ra) {
    ra.addFlashAttribute("msg", "Saved");
    return "redirect:/result";
}

@GetMapping("/result")
public String result() {
    return "result";
}
```

---

## 8. Golden Interview Rules 🏆 (with Code Meaning)

1. **Model / ModelMap → request scope**
    
2. **Forward → same request**
    
3. **Redirect → new request**
    
4. **RedirectAttributes → FlashMap**
    
5. **FlashMap → one request only**
    
6. **Session → long-lived**
    
7. **PRG → RedirectAttributes**
    

---

## 9. One-Line Summary

> **Use `Model` for views, `RedirectAttributes` for redirects, and remember that `FlashMap` is the internal engine that makes redirect data possible.**
