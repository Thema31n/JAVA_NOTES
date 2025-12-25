# #️⃣ **1. What Is Java Reflection?**

Java Reflection is a runtime API that lets you inspect and modify:

- Classes
    
- Fields (public and private)
    
- Methods (public and private)
    
- Constructors
    
- Interfaces
    
- Superclasses
    
- Modifiers
    
- Generic types
    
- Annotations
    

It powers all modern Java frameworks (Spring, Hibernate, Jackson, JUnit, etc.)

---

# #️⃣ **2. Goals of Reflection**

|Goal|Explanation|
|---|---|
|**Runtime inspection**|See class structure while running|
|**Dynamic invocation**|Call methods dynamically|
|**Dynamic instantiation**|Create objects without knowing class|
|**Private access**|Read/write private fields & methods|
|**Framework features**|Serialization, dependency injection|
|**Metadata processing**|Annotations + generic types|

---

# #️⃣ **3. The `java.lang.reflect` Package**

|Component|Purpose|
|---|---|
|`Field`|Inspect & modify fields|
|`Method`|Inspect & invoke methods|
|`Constructor`|Create instances dynamically|
|`Modifier`|Utility for checking access modifiers|
|`Parameter`|Represents method parameters|
|`Type / ParameterizedType`|Represents generic types|
|`Proxy`|Create dynamic proxy objects|
|`Array`|Array reflection utilities|

---

# #️⃣ **4. Reflection Functions — FULL DESCRIPTIONS**

Below is the full list of reflection functions grouped by category, each with a clear explanation.

---

# 🟩 **A. CLASS Functions (java.lang.Class)**

### ✔ 1. Getting Class objects

|Function|Description|
|---|---|
|`Class.forName("pkg.ClassName")`|Loads class by its name at runtime.|
|`obj.getClass()`|Gets class of an object instance.|
|`MyClass.class`|Gets class at compile-time.|

---

### ✔ 2. Class Metadata

|Function|Description|
|---|---|
|`getName()`|Fully qualified class name.|
|`getSimpleName()`|Class name only.|
|`getPackage()`|Returns package object.|
|`getModifiers()`|Returns modifier integer for the class.|

---

### ✔ 3. Class Members

|Function|Description|
|---|---|
|`getDeclaredFields()`|All fields (private included).|
|`getFields()`|Public fields only (including inherited).|
|`getDeclaredMethods()`|All methods (private included).|
|`getMethods()`|Public methods only (including inherited).|
|`getDeclaredConstructors()`|All constructors (public/private).|
|`getConstructors()`|Public constructors only.|

---

### ✔ 4. Class Type Hierarchy

|Function|Description|
|---|---|
|`getInterfaces()`|Interfaces implemented by the class.|
|`getSuperclass()`|Direct parent class.|
|`getGenericSuperclass()`|Parent class with generics included.|

---

# 🟦 **B. FIELD Functions (java.lang.reflect.Field)**

### ✔ 1. Obtaining fields

|Function|Description|
|---|---|
|`getDeclaredField(name)`|Returns a field by name (private allowed).|
|`getDeclaredFields()`|Returns all fields.|
|`getFields()`|Returns only public fields.|

---

### ✔ 2. Reading/writing field values

|Function|Description|
|---|---|
|`get(obj)`|Reads the field’s value.|
|`set(obj, value)`|Writes a new value to the field.|
|`setAccessible(true)`|Allows private access.|

---

### ✔ 3. Field Metadata

|Function|Description|
|---|---|
|`getName()`|Field name.|
|`getType()`|Field data type.|
|`getGenericType()`|Returns generic type (List, etc.)|
|`getModifiers()`|Returns modifier integer.|

---

# 🟫 **C. METHOD Functions (java.lang.reflect.Method)**

### ✔ 1. Obtaining methods

|Function|Description|
|---|---|
|`getDeclaredMethod(name, params...)`|Gets method (private allowed).|
|`getDeclaredMethods()`|All declared methods.|
|`getMethod(name, params...)`|Gets public method only.|
|`getMethods()`|All public methods.|

---

### ✔ 2. Invoking methods

|Function|Description|
|---|---|
|`invoke(obj, args...)`|Calls the method on the given object.|
|`setAccessible(true)`|Enables private method access.|

---

### ✔ 3. Method Metadata

|Function|Description|
|---|---|
|`getName()`|Method name.|
|`getReturnType()`|Method return type.|
|`getParameterTypes()`|Parameter type list.|
|`getModifiers()`|Access modifiers.|

---

# 🟧 **D. CONSTRUCTOR Functions (java.lang.reflect.Constructor)**

### ✔ Obtaining constructors

|Function|Description|
|---|---|
|`getDeclaredConstructors()`|All constructors (private included).|
|`getDeclaredConstructor(params...)`|Specific constructor.|
|`getConstructors()`|Only public constructors.|

---

### ✔ Instantiating objects

|Function|Description|
|---|---|
|`newInstance(args...)`|Creates an object.|
|`setAccessible(true)`|Allows private constructor usage.|

---

### ✔ Constructor Metadata

|Function|Description|
|---|---|
|`getParameterTypes()`|Parameter list.|
|`getModifiers()`|Modifiers.|

---

# 🟦 **E. MODIFIER Functions (java.lang.reflect.Modifier)**

Modifier contains utility methods for checking access modifiers:

|Function|Description|
|---|---|
|`toString(int)`|Converts modifiers to a readable string.|
|`isPublic(int)`|True if public.|
|`isPrivate(int)`|True if private.|
|`isProtected(int)`|True if protected.|
|`isStatic(int)`|True if static.|
|`isFinal(int)`|True if final.|
|`isAbstract(int)`|True if abstract.|

---

# 🟨 **F. GENERIC TYPE Functions**

Used for inspecting `List<T>`, `Map<K,V>`, etc.

|Function|Description|
|---|---|
|`getGenericType()`|Returns field’s generic type.|
|`ParameterizedType`|Represents the generic type.|
|`getActualTypeArguments()`|Returns actual type parameters.|

Example:  
`List<String>` → actual type argument = `String`

---

# #️⃣ **5. Real-World Domain Model**

We use this class for the full demo:

```java
public class User implements Comparable<User> {

    private long id;
    private String name;
    private String email;

    private List<String> roles;
    private Map<String, Object> metadata;

    public User() {
        this.roles = new ArrayList<>();
        this.metadata = new HashMap<>();
    }

    private User(long id) {
        this();
        this.id = id;
    }

    public User(long id, String name, String email) {
        this();
        this.id = id;
        this.name = name;
        this.email = email;
    }

    public void addRole(String role) { roles.add(role); }

    public void addMetadata(String key, Object value) { metadata.put(key, value); }

    public String greet(String msg) { return name + ": " + msg; }

    private String privateToken() { return "TOKEN-SECRET"; }

    @Override
    public int compareTo(User o) { return Long.compare(this.id, o.id); }
}
```

---

# #️⃣ **6. FULL REFLECTION DEMO — USING ALL FUNCTIONS**

### (Every print includes expected output)

---

```java
import java.lang.reflect.*;
import java.util.*;

public class ReflectionMasterDemo {

    public static void main(String[] args) throws Exception {

        Class<?> cls = User.class;

        System.out.println("Class = " + cls.getName());
        // Class = User

        System.out.println("Simple Name = " + cls.getSimpleName());
        // Simple Name = User

        System.out.println("Superclass = " + cls.getSuperclass().getName());
        // Superclass = java.lang.Object

        System.out.println("Interfaces = " + Arrays.toString(cls.getInterfaces()));
        // Interfaces = [interface java.lang.Comparable]


        // ----------------------------------------------------------
        // MODIFIERS
        // ----------------------------------------------------------
        int classModifiers = cls.getModifiers();
        System.out.println("Class modifiers = " + Modifier.toString(classModifiers));
        // Class modifiers = public


        // ----------------------------------------------------------
        // FIELDS + MODIFIERS + GENERIC TYPES
        // ----------------------------------------------------------
        for (Field f : cls.getDeclaredFields()) {
            f.setAccessible(true);

            System.out.println("\nField = " + f.getName());
            // e.g. Field = id

            System.out.println("Type = " + f.getType());
            // e.g. Type = long

            System.out.println("Generic = " + f.getGenericType());
            // e.g. Generic = java.util.List<java.lang.String>

            System.out.println("Modifiers = " + Modifier.toString(f.getModifiers()));
            // e.g. private
        }


        // ----------------------------------------------------------
        // GETTING GENERIC TYPE ARGUMENTS
        // ----------------------------------------------------------
        Field rolesField = cls.getDeclaredField("roles");
        ParameterizedType pt = (ParameterizedType) rolesField.getGenericType();

        System.out.println("\nRoles generic argument = " + pt.getActualTypeArguments()[0]);
        // Roles generic argument = class java.lang.String

        Field metaField = cls.getDeclaredField("metadata");
        ParameterizedType mapType = (ParameterizedType) metaField.getGenericType();

        System.out.println("Metadata key type = " + mapType.getActualTypeArguments()[0]);
        // Metadata key type = class java.lang.String

        System.out.println("Metadata value type = " + mapType.getActualTypeArguments()[1]);
        // Metadata value type = class java.lang.Object


        // ----------------------------------------------------------
        // METHODS + PARAMETERS + RETURN TYPES + MODIFIERS
        // ----------------------------------------------------------
        for (Method m : cls.getDeclaredMethods()) {

            System.out.println("\nMethod = " + m.getName());
            // Example: addRole

            System.out.println("Return = " + m.getReturnType());
            // e.g. void

            System.out.println("Params = " + Arrays.toString(m.getParameterTypes()));
            // e.g. [class java.lang.String]

            System.out.println("Modifiers = " + Modifier.toString(m.getModifiers()));
            // public or private
        }


        // ----------------------------------------------------------
        // CONSTRUCTORS + MODIFIERS
        // ----------------------------------------------------------
        for (Constructor<?> c : cls.getDeclaredConstructors()) {
            System.out.println("\nConstructor = " + c);
            // e.g. private User(long)

            System.out.println("Param types = " + Arrays.toString(c.getParameterTypes()));
            // Constructor parameters

            System.out.println("Modifiers = " + Modifier.toString(c.getModifiers()));
            // public or private
        }


        // ----------------------------------------------------------
        // PUBLIC CONSTRUCTOR INVOCATION
        // ----------------------------------------------------------
        Constructor<?> publicCons = cls.getConstructor(long.class, String.class, String.class);
        Object user = publicCons.newInstance(2001L, "Ahmed", "ahmed@mail.com");

        System.out.println("\nUser created = " + user);
        // User created = User@HASHCODE


        // ----------------------------------------------------------
        // INVOKING PUBLIC METHODS
        // ----------------------------------------------------------
        Method addRole = cls.getMethod("addRole", String.class);
        addRole.invoke(user, "ADMIN");

        Method addMeta = cls.getMethod("addMetadata", String.class, Object.class);
        addMeta.invoke(user, "ip", "127.0.0.1");

        Method greet = cls.getMethod("greet", String.class);

        System.out.println("Greet() = " + greet.invoke(user, "Hello!"));
        // Greet() = Ahmed: Hello!


        // ----------------------------------------------------------
        // ACCESSING PRIVATE FIELD
        // ----------------------------------------------------------
        Field name = cls.getDeclaredField("name");
        name.setAccessible(true);

        System.out.println("Private field name BEFORE = " + name.get(user));
        // Private field name BEFORE = Ahmed

        name.set(user, "Mohamed");

        System.out.println("Private field name AFTER = " + name.get(user));
        // Private field name AFTER = Mohamed


        // ----------------------------------------------------------
        // INVOKING PRIVATE METHOD
        // ----------------------------------------------------------
        Method token = cls.getDeclaredMethod("privateToken");
        token.setAccessible(true);

        System.out.println("Private token = " + token.invoke(user));
        // Private token = TOKEN-SECRET


        // ----------------------------------------------------------
        // PRIVATE CONSTRUCTOR INVOCATION
        // ----------------------------------------------------------
        Constructor<?> privateCons = cls.getDeclaredConstructor(long.class);
        privateCons.setAccessible(true);

        Object u2 = privateCons.newInstance(555L);

        Field idField = cls.getDeclaredField("id");
        idField.setAccessible(true);

        System.out.println("Private constructor user id = " + idField.get(u2));
        // Private constructor user id = 555


        // ----------------------------------------------------------
        // FINAL STATE OF OBJECT
        // ----------------------------------------------------------
        System.out.println("\n=== FINAL USER STATE ===");
        for (Field f : cls.getDeclaredFields()) {
            f.setAccessible(true);
            System.out.println(f.getName() + " = " + f.get(user));
        }
        /*
            id = 2001
            name = Mohamed
            email = ahmed@mail.com
            roles = [ADMIN]
            metadata = {ip=127.0.0.1}
        */
    }
}
```

---
