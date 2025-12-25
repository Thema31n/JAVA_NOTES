## **Java – `TreeMap`**

### **Overview**

- `TreeMap<K,V>` is a **sorted Map** implementation backed by a **Red-Black Tree**.
    
- Maintains keys in **ascending sorted order**.
    
- Sorting is defined by:
    
    - the key’s natural ordering (`Comparable`)
        
    - or a custom comparator.
        

---

## **Core Properties**

- Time complexity:
    
    - `put`, `get`, `remove`, `containsKey`: **O(log n)**
        
- Ordering: **sorted by key**
    
- Null keys: **not allowed**
    
- Null values: allowed
    
- Deterministic iteration order
    

---

## **When to Use**

Use when you need:

- sorted keys
    
- ordered iteration
    
- range queries
    
- floor/ceiling/first/last key access
    

---

## **Key API**

```java
TreeMap<K,V> map = new TreeMap<>();

map.put(key, value);
map.get(key);
map.remove(key);

map.firstKey();
map.lastKey();
map.floorKey(x);
map.ceilingKey(x);

map.entrySet();
map.keySet();
map.values();
```

---

## **Example**

```java
TreeMap<Integer, String> map = new TreeMap<>();
map.put(5, "E");
map.put(2, "B");
map.put(9, "I");

System.out.println(map); // {2=B, 5=E, 9=I}
```

---

# **Red-Black Tree (RBT)**

## **Definition**

A **Red-Black Tree** is a self-balancing binary search tree enforcing strict color and structural rules to maintain balanced height, guaranteeing **O(log n)** operations.

---

## **Invariants (the 5 rules)**

1. Every node is either **red** or **black**
    
2. The **root is always black**
    
3. **All leaves (null references) are black**
    
4. **No red node can have a red child** (no double-red)
    
5. Every path from a node to its leaves has the **same number of black nodes** (black-height property)
    

These enforce near-perfect balancing.

---

## **Why This Matters**

- Ensures that tree height stays strictly bounded:
    
    - height <= 2 * log2(n+1)
        
- Prevents pathological cases present in ordinary BSTs
    
- Enables guaranteed logarithmic operations
    

---

## **How Balancing Works (Conceptual)**

### **Insertion procedures**

1. Insert new node as you would in a BST
    
2. Color new node red
    
3. Fix violations by:
    
    - **color flips**
        
    - **left/right rotations**
        
    - or both, depending on case
        
4. Ensure root is black
    

### **Deletion procedures**

1. Remove as in a BST
    
2. If properties violated, apply:
    
    - recoloring
        
    - rotations
        
    - double-black fix ups
        

---

## **Balancing Operations**

### **Rotations**

Used to restructure:

- Left rotation
    
- Right rotation
    

Effect:

- keeps in-order sequence intact
    
- reduces path imbalances
    

### **Color Flips**

Used to restore:

- red/black property
    
- uniform black-height
    

---

## **Typical Insert Cases (simplified)**

- Parent black → OK
    
- Parent red, Uncle red → recolor parent, uncle, grandparent
    
- Parent red, Uncle black → rotation + recolor
    

(TreeMap handles this internally.)

---

## **Strengths**

- Always balanced
    
- Fast ordered operations
    
- Predictable performance
    

## **Weaknesses**

- More overhead than HashMap
    
- More complex implementation
    
- Higher constant factors
    
