## **Queue**

A **Queue** is a **FIFO** (First-In-First-Out) data structure.

### **Main Methods**

|Method|Behavior|
|---|---|
|`add(e)`|Add element, throws exception on failure|
|`offer(e)`|Add element, returns false on failure|
|`remove()`|Remove head, throws exception if empty|
|`poll()`|Remove head, returns null if empty|
|`element()`|Read head, throws exception if empty|
|`peek()`|Read head, returns null if empty|

### **Popular Implementations**

- **LinkedList**
    
- **ArrayDeque**
    
- **PriorityQueue** (not FIFO)
    
- **Concurrent queues** in `java.util.concurrent.*`
    

---

## **Deque**

A **Deque** is a **Double-Ended Queue**, supporting both **FIFO and LIFO**.

### **Full Operation Comparison (Front vs Back)**

|**Operation**|**Front**|**Back**|
|---|---|---|
|**Insert**|`addFirst(e)` / `offerFirst(e)`|`addLast(e)` / `offerLast(e)`|
|**Remove**|`removeFirst()` / `pollFirst()`|`removeLast()` / `pollLast()`|
|**Peek**|`peekFirst()`|`peekLast()`|

### **Notes**

- Use **front** operations for **stack-like** behavior (`push`, `pop`).
    
- Use **back** operations for **queue-like** behavior.
    

### **Popular Deque Implementations**

- **ArrayDeque** → fastest & most common
    
- **LinkedList** → supports deque but slower
    
- **ConcurrentLinkedDeque**
    
- **LinkedBlockingDeque**
    

---

# **PriorityQueue (PQ)**

A **PriorityQueue** always removes elements based on **priority**, not insertion order.  
Uses a **binary heap** internally.

### **Key Features**

- Default order = **natural ordering**
    
- Smallest element = **highest priority**
    
- Can use custom **Comparator**
    

---

# **PriorityQueue Usage Examples**

## **Natural Ordering**

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.add(10);
pq.add(3);
pq.add(7);

System.out.println(pq.poll()); // 3
```

---

# **Comparator Example With Class (Full, Non-Lambda)**

### **Class to Compare**

```java
class Student {
    String name;
    int grade;

    Student(String name, int grade) {
        this.name = name;
        this.grade = grade;
    }
}
```

---

## **Comparator Class (Sort by grade ascending)**

```java
import java.util.Comparator;

class StudentGradeComparator implements Comparator<Student> {
    @Override
    public int compare(Student s1, Student s2) {
        return Integer.compare(s1.grade, s2.grade);
    }
}
```

---

## **Using PriorityQueue With This Comparator**

```java
PriorityQueue<Student> pq =
        new PriorityQueue<>(new StudentGradeComparator());

pq.add(new Student("Ahmed", 90));
pq.add(new Student("Omar", 75));
pq.add(new Student("Ali", 82));

System.out.println(pq.poll().name);
// Output: "Omar" (lowest grade = highest priority)
```

---

## **Another Comparator Example (Sort by name alphabetically)**

```java
class StudentNameComparator implements Comparator<Student> {
    @Override
    public int compare(Student s1, Student s2) {
        return s1.name.compareTo(s2.name);
    }
}
```

---

# **Quick Cheat Sheet (Memory Guide)**

- **Queue = FIFO**
    
- **Deque = supports both FIFO & LIFO**
    
- **ArrayDeque = fastest deque & stack replacement**
    
- **PriorityQueue = removes smallest/largest first based on comparator**
    
- Use **Comparator class** when you want reusable comparison logic
    