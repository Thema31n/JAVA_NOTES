# ✅ **1. ArrayList**

## **Overview**

- Most commonly used dynamic array in Java.
    
- **Not synchronized** → _Not thread-safe_.
    
- Fast for reading, moderate for writing.
    

## **Key Points**

- Grows dynamically by 50% of its size.
    
- Implements **List**, **RandomAccess**.
    
- Best for **single-threaded** or lightly multi-threaded environments.
    

## **Performance**

- **Read (get/index access):** O(1) → very fast
    
- **Add:** Amortized O(1)
    
- **Remove from middle:** O(n)
    

## **Use Case**

Use when:  
✔ You need fast random access  
✔ You don’t need thread safety

---

# ✅ **2. Vector**

## **Overview**

- Legacy version of ArrayList.
    
- **Synchronized** → _Thread-safe_ (every method is synchronized).
    
- Rarely recommended today.
    

## **Key Points**

- Grows by **100%** of its size (doubles) when full.
    
- Implements **List**, **RandomAccess**.
    
- Much slower than ArrayList due to synchronized methods.
    

## **Performance**

- **Slower** than ArrayList due to locking.
    
- Same time complexity as ArrayList.
    

## **Use Case**

Use when:  
✔ You need a thread-safe list  
✘ But better alternatives now exist → **use Collections.synchronizedList(new ArrayList())** or **CopyOnWriteArrayList** instead.

---

# ✅ **3. CopyOnWriteArrayList**

## **Overview**

- Thread-safe list designed for **concurrent reads**.
    
- On every **write**, it creates a **new copy** of the entire array.
    

## **Key Points**

- Extremely efficient for **reads (no locking)**.
    
- Very expensive for **writes** (copy entire array).
    
- Iterators never throw `ConcurrentModificationException`.
    

## **Performance**

- **Read:** O(1), no locking
    
- **Write (add/remove):** O(n) → slow
    
- **Iterator:** Snapshot → safe during multithreading
    

## **Use Case**

Use when:  
✔ Many threads read  
✔ Very few writes  
✔ Required to avoid concurrency bugs

Used frequently in:

- event listeners
    
- caching
    
- configuration values
    

---

# ✅ **4. Stack**

## **Overview**

- Legacy class for LIFO (Last-In-First-Out).
    
- **Synchronized**.
    
- Internally based on **Vector**.
    

## **Key Points**

- Implements **List**, **RandomAccess**.
    
- Operations: `push()`, `pop()`, `peek()`.
    

## **Performance**

- Same performance as Vector (slower than ArrayList due to locking).
    

## **Use Case**

✔ Historical LIFO stack  
✘ Modern recommendation → use **Deque** (ArrayDeque) instead.

---

# 📘 **Side-by-Side Summary Table**

|Feature|ArrayList|Vector|CopyOnWriteArrayList|Stack|
|---|---|---|---|---|
|Thread-safe|❌ No|✔ Yes (synchronized)|✔ Yes|✔ Yes|
|Legacy|❌ No|✔ Yes|❌ No|✔ Yes|
|Speed|Fast|Slower|Fast reads, slow writes|Slow|
|Growth|+50%|+100% (double)|Copy full array|Vector growth|
|Iterator safe?|❌ No|❌ No|✔ Yes|❌ No|
|Best for|Normal use|Old thread-safe code|Read-heavy multithreading|LIFO (use Deque instead)|

---

# 📌 **What to Use Today?**

- **ArrayList** → general purpose list
    
- **Vector** → avoid (legacy)
    
- **CopyOnWriteArrayList** → many readers + few writers
    
- **Stack** → avoid → use **ArrayDeque**
    
