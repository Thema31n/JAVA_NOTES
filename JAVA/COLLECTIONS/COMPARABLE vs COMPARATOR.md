# 🧩 **1. Comparable**

**Package:** `java.lang`  
**Purpose:** Defines **natural order** for objects.  
Used when the class itself decides _its default sorting rule_.

## **Method**

```java
int compareTo(T other)
```

## **Rules**

- `< 0` → this < other
    
- `0` → this == other
    
- `> 0` → this > other
    

## **Example**

```java
class Student implements Comparable<Student> {
    int age;

    @Override
    public int compareTo(Student other) {
        return Integer.compare(this.age, other.age);
    }
}
```

---

# 🧩 **2. Comparator**

**Package:** `java.util`  
**Purpose:** Provides **custom sorting**, external to the class.  
Allows **multiple sorting strategies**.

## **Core Method**

```java
int compare(T a, T b)
```

## **Java 8+ Default/Static Methods**

- `comparing(...)`
    
- `thenComparing(...)`
    
- `reversed()`
    
- `naturalOrder()`
    
- `reverseOrder()`
    
- `nullsFirst(...)`
    
- `nullsLast(...)`
    
- `comparingInt`, `comparingLong`, `comparingDouble`
    

---

# 🔥 **3. Creating Custom Comparators**

## ✔ **A. Using a Lambda**

```java
Comparator<Student> byName =
        (a, b) -> a.getName().compareTo(b.getName());
```

## ✔ **B. Using an Anonymous Class**

(Manually overriding `compare()`)

```java
Comparator<Student> byAge = new Comparator<Student>() {
    @Override
    public int compare(Student a, Student b) {
        return Integer.compare(a.getAge(), b.getAge());
    }
};
```

## ✔ **C. Using comparing() (Most Modern Style)**

```java
Comparator<Student> byGrade =
        Comparator.comparing(Student::getGrade);
```

## ✔ **D. Custom multi-level Comparator**

```java
Comparator<Student> complexComparator =
        Comparator.comparing(Student::getAge)
                  .thenComparing(Student::getName)
                  .thenComparing(Student::getGpa, Comparator.reverseOrder());
```

---

# 🛠 **4. Overriding compare() (Full Custom Logic)**

When you want full manual control:

```java
Comparator<Employee> custom = new Comparator<Employee>() {
    @Override
    public int compare(Employee a, Employee b) {

        // Example: sort by salary DESC, then name ASC, then id ASC:

        // 1. Salary DESC
        int salaryCompare = Double.compare(b.getSalary(), a.getSalary());
        if (salaryCompare != 0) return salaryCompare;

        // 2. Name ASC
        int nameCompare = a.getName().compareTo(b.getName());
        if (nameCompare != 0) return nameCompare;

        // 3. ID ASC
        return Integer.compare(a.getId(), b.getId());
    }
};
```

---

# 🔥 **5. All Sorting Methods (Complete)**

## ✔ **Natural order (Comparable)**

```java
Collections.sort(list);
list.sort(null);
list.sort(Comparator.naturalOrder());
Arrays.sort(array);
```

## ✔ **Reverse natural order**

```java
list.sort(Comparator.reverseOrder());
```

## ✔ **Custom Comparator**

```java
list.sort(byName);
list.sort(byAge);
list.sort(customComparator);
```

## ✔ **Sort by field**

```java
list.sort(Comparator.comparing(Person::getAge));
```

## ✔ **Sort by two fields**

```java
list.sort(
     Comparator.comparing(Person::getAge)
               .thenComparing(Person::getName)
);
```

## ✔ **Descending**

```java
list.sort(Comparator.comparing(Person::getAge).reversed());
```

## ✔ **Null-safe**

```java
list.sort(Comparator.nullsFirst(Comparator.naturalOrder()));
```

---

# 🌳 **6. Collections That Use Comparable/Comparator Internally**

## **TreeSet**

```java
new TreeSet<>();        // natural
new TreeSet<>(comp);   // custom
```

## **TreeMap (keys only)**

```java
new TreeMap<>();        // natural
new TreeMap<>(comp);    // custom
```

## **PriorityQueue**

```java
new PriorityQueue<>();
new PriorityQueue<>(comp);
```

---

# ⚖️ **7. Comparable vs Comparator**

|Feature|Comparable|Comparator|
|---|---|---|
|Package|java.lang|java.util|
|Method|compareTo()|compare()|
|Defined in|Class itself|Separate object|
|# of sorting rules|1|Unlimited|
|Sorting type|Natural|Custom|
|Best use|Default ordering|Multiple sorting strategies|

---

# ⚠️ **8. Common Pitfalls**

## ❌ Using subtraction

May overflow:

```java
return a - b;
```

✔ Correct:

```java
return Integer.compare(a, b);
```

## ❌ compareTo inconsistent with equals

Causes TreeSet/TreeMap bugs.

## ❌ Forgetting null handling

Use:

```java
Comparator.nullsFirst(...)
Comparator.nullsLast(...)
```

---

# 🧠 **9. One-Line Sorting Recipes**

## Sort by field ASC

```java
list.sort(Comparator.comparing(Person::getAge));
```

## Sort by field DESC

```java
list.sort(Comparator.comparing(Person::getAge).reversed());
```

## Sort by age then name

```java
list.sort(
     Comparator.comparing(Person::getAge)
               .thenComparing(Person::getName)
);
```

## Natural order

```java
list.sort(Comparator.naturalOrder());
```

## Reverse natural order

```java
list.sort(Comparator.reverseOrder());
```

## Nulls first

```java
list.sort(Comparator.nullsFirst(Comparator.naturalOrder()));
```

---

# 🎯 **10. Summary (For Quick Revision)**

- **Comparable = natural order, inside the class, compareTo()**
    
- **Comparator = custom order, outside the class, compare()**
    
- Use **Comparator.comparing()** for clean, modern sorting
    
- Use **reversed()**, **thenComparing()**, and **nullsFirst()** for powerful sorting chains
    
- Avoid subtraction comparisons
    
- TreeSet, TreeMap, PriorityQueue rely on these interfaces
    
