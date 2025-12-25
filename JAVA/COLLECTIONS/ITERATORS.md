## 🧩 **1. Iterable**

- Interface in `java.lang`.
    
- Allows a collection to be used in a **for-each loop**.
    
- Must implement:
    

```java
Iterator<T> iterator();
```

### **Example:**

```java
class MyCollection<T> implements Iterable<T> {
    private List<T> list = new ArrayList<>();

    @Override
    public Iterator<T> iterator() {
        return list.iterator();
    }
}
```

---

## 🧩 **2. Iterator**

Basic cursor used to traverse collections **forward only**.

### **Methods:**

```java
boolean hasNext();
T next();
void remove(); // optional
```

### **Example:**

```java
List<String> list = Arrays.asList("A", "B", "C");
Iterator<String> itr = list.iterator();

while (itr.hasNext()) {
    System.out.println(itr.next());
}
```

---

## 🧩 **3. ListIterator**

Extended iterator for **List** types; supports **forward + backward traversal** and modifications.

### **Methods:**

```java
boolean hasPrevious();
T previous();
int nextIndex();
int previousIndex();
void set(T e);
void add(T e);
```

### **Example:**

```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));
ListIterator<String> itr = list.listIterator();

// Traverse forward
while (itr.hasNext())
    System.out.println(itr.next());

// Add element after "B"
itr = list.listIterator();
while (itr.hasNext()) {
    if (itr.next().equals("B")) {
        itr.add("X");
    }
}

System.out.println(list); // [A, B, X, C]
```

---

# 🧨 **Iterator Exceptions**

|Exception|When It Occurs|
|---|---|
|**NoSuchElementException**|Calling `next()` with no elements left|
|**IllegalStateException**|Calling `remove()` before `next()` or twice consecutively|
|**UnsupportedOperationException**|Iterator does not support remove/set/add|
|**ConcurrentModificationException**|Fail-fast iterator detects structural modification|

### **Example (fail-fast exception):**

```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B"));

for (String s : list) {
    list.add("C"); // throws ConcurrentModificationException
}
```

---

# 🧵 **Thread-Safety & Iterator Behavior**

## 🔥 **1. Fail-Fast Iterators**

- Default for most `java.util` collections (`ArrayList`, `HashMap`, etc.).
    
- Detect structural modification outside the iterator.
    
- Throw **ConcurrentModificationException**.
    

---

## 🌥 **2. Weakly-Consistent Iterators**

Used in **concurrent collections**:

- `ConcurrentHashMap`
    
- `CopyOnWriteArrayList`
    
- `ConcurrentLinkedQueue`
    
- `ConcurrentSkipListMap`
    

### **Properties:**

- **Never fail-fast**
    
- Safe for multi-threaded operations
    
- May reflect some or none of the recent updates
    

### **Example — ConcurrentHashMap:**

```java
ConcurrentHashMap<Integer, String> map = new ConcurrentHashMap<>();
map.put(1, "A");
map.put(2, "B");

Iterator<Integer> itr = map.keySet().iterator();

// Modify safely during iteration
map.put(3, "C");

while (itr.hasNext()) {
    System.out.println(itr.next());
}
```

### **Example — CopyOnWriteArrayList:**

```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
list.add("A");
list.add("B");

Iterator<String> itr = list.iterator();
list.add("C"); // Safe

while (itr.hasNext()) {
    System.out.println(itr.next());
    // Will NOT print "C" (snapshot)
}
```

---

# 🧭 **Iterator Types (with Examples)**

## 1️⃣ **Iterator**

### **Features:**

- Forward-only
    
- Remove allowed (optional)
    
- Fail-fast
    

### **Example:**

```java
Iterator<String> itr = list.iterator();
while (itr.hasNext())
    System.out.println(itr.next());
```

---

## 2️⃣ **ListIterator**

### **Features:**

- Forward + backward
    
- Add, set, remove supported
    
- Works only on Lists
    

### **Example:**

```java
ListIterator<String> itr = list.listIterator();
if (itr.hasNext()) itr.next();
itr.add("X");
```

---

## 3️⃣ **Fail-Fast Iterator**

### **Example:**

```java
for (String s : list) {
    list.remove(0); // causes ConcurrentModificationException
}
```

---

## 4️⃣ **Weakly-Consistent Iterator**

### **Example:**

```java
ConcurrentLinkedQueue<Integer> queue = new ConcurrentLinkedQueue<>();
queue.add(1);
queue.add(2);

Iterator<Integer> itr = queue.iterator();
queue.add(3); // allowed

while (itr.hasNext())
    System.out.println(itr.next());
```

---

# 📝 **Final Quick Notes Summary**

- **Iterable** → allows for-each.
    
- **Iterator** → forward traversal.
    
- **ListIterator** → forward/backward + modify.
    
- **Fail-Fast** → throws ConcurrentModificationException.
    
- **Weakly-Consistent** → thread-safe, no fail-fast (concurrent collections).
    
- **Iterator exceptions** → NoSuchElement, IllegalState, UnsupportedOperation, ConcurrentModification.
    
