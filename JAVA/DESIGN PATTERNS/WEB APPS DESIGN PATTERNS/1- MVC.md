# MVC Pattern (Model–View–Controller)
## 1. What is MVC?

**MVC (Model–View–Controller)** is a **design pattern** used to separate an application into three interconnected parts:

- **Model** → Handles data & business logic
    
- **View** → Displays data (UI)
    
- **Controller** → Handles user input and coordinates Model & View
    

👉 Goal: **separation of concerns**, easier maintenance, and scalability.

---

## 2. MVC Components Explained

### 2.1 Model

**Responsibility**

- Holds application data
    
- Contains business rules
    
- Independent of UI
    

**In Java**

- Plain Java classes (POJOs)
    
- Services, DAOs, Entities
    

**Example**

```java
public class Student {
    private String name;
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() { return name; }
    public int getAge() { return age; }
}
```

---

### 2.2 View

**Responsibility**

- Displays data
    
- No business logic
    
- Gets data from the controller
    

**In Java**

- Console output
    
- Swing / JavaFX
    
- JSP / HTML (web apps)
    

**Example (Console View)**

```java
public class StudentView {
    public void displayStudent(String name, int age) {
        System.out.println("Student Name: " + name);
        System.out.println("Student Age: " + age);
    }
}
```

---

### 2.3 Controller

**Responsibility**

- Receives user input
    
- Updates the Model
    
- Updates the View
    

**In Java**

- Servlets
    
- Controllers (Spring MVC)
    
- Plain Java controller classes
    

**Example**

```java
public class StudentController {
    private Student model;
    private StudentView view;

    public StudentController(Student model, StudentView view) {
        this.model = model;
        this.view = view;
    }

    public void updateView() {
        view.displayStudent(model.getName(), model.getAge());
    }
}
```

---

## 3. MVC Flow (How it Works)

1. User interacts with **View**
    
2. View sends input to **Controller**
    
3. Controller updates **Model**
    
4. Model changes data
    
5. Controller updates **View**
    

---

## 4. Complete Java MVC Example (Console App)

```java
public class MVCDemo {
    public static void main(String[] args) {
        Student model = new Student("Ahmed", 22);
        StudentView view = new StudentView();
        StudentController controller = new StudentController(model, view);

        controller.updateView();
    }
}
```

**Output**

```
Student Name: Ahmed
Student Age: 22
```

---

## 5. Real-World Java MVC Example (Web Application)

### Example: **Spring MVC**

#### Model

```java
public class User {
    private String username;
    // getters & setters
}
```

#### Controller

```java
@Controller
public class UserController {

    @GetMapping("/user")
    public String getUser(Model model) {
        model.addAttribute("username", "Ahmed");
        return "userView";
    }
}
```

#### View (userView.jsp / HTML)

```html
<h1>User: ${username}</h1>
```

---

## 6. Advantages of MVC

✅ **Separation of concerns**  
✅ **Easy maintenance**  
✅ **Scalable applications**  
✅ **Multiple views for same model**  
✅ **Better testability**

---

## 7. Disadvantages of MVC

❌ Increased complexity for small apps  
❌ More files and boilerplate  
❌ Learning curve for beginners  
❌ Controller can become too large (God Controller problem)

---

## 8. When to Use MVC?

✔ Large applications  
✔ Web applications  
✔ Applications needing multiple UI types  
✔ Team-based development

❌ Very small or simple programs

---

## 9. MVC vs Real Life Analogy

- **Model** → Kitchen (prepares food)
    
- **View** → Plate (presentation)
    
- **Controller** → Waiter (takes orders & delivers food)
    

---

## 10. Key Takeaways

- MVC improves **structure and maintainability**
    
- Java uses MVC heavily (Spring MVC, JavaFX, Swing)
    
- Always keep **business logic out of views**
    
- Controllers should be thin, models should be strong
    
