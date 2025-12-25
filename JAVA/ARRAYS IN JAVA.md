## 🔹 **1. Arrays Are Objects**

- Even if you declare:
    

```java
int[] numbers = {1, 2, 3, 4};
```

`numbers` is **not a primitive**, it’s a **reference to an object** on the heap.

- So internally:
    
    - `numbers` (reference) → points to an **array object** in memory.
        
    - That object knows:
        
        - Its **length**
            
        - Its **type** (`int[]`, `String[]`, etc.)
            

---

## 🔹 **2. Array Properties**

1. **length** (field, not method):
    

```java
int[] arr = {1, 2, 3};
System.out.println(arr.length); // 3
```

- Unlike Strings, it’s not `length()` but just `length`.
    

---

## 🔹 **3. Functions from `java.util.Arrays`**

Java provides a helper class **`Arrays`** with many useful static methods.

### Commonly Used:

- **Printing**
    

```java
int[] arr = {1, 2, 3};
System.out.println(Arrays.toString(arr));  // [1, 2, 3]
```

- **Sorting**
    

```java
Arrays.sort(arr);
```

- **Binary Search**
    

```java
int idx = Arrays.binarySearch(arr, 2); // index of value 2
```

- **Fill**
    

```java
int[] arr = new int[5];
Arrays.fill(arr, 7); // [7, 7, 7, 7, 7]
```

- **Equals**
    

```java
int[] a = {1, 2};
int[] b = {1, 2};
System.out.println(Arrays.equals(a, b)); // true
```

- **Copy**
    

```java
int[] copy = Arrays.copyOf(arr, 5); // new length 5
```

- **Deep Equals / Deep ToString**  
    (For multidimensional arrays)
    

```java
int[][] mat = {{1,2}, {3,4}};
System.out.println(Arrays.deepToString(mat)); // [[1, 2], [3, 4]]
```

---

## 🔹 **4. Object Methods (from `Object` class)**

Since arrays are objects, they also inherit methods from `Object`, like:

- `getClass()` → gives type at runtime
    

```java
int[] arr = {1,2,3};
System.out.println(arr.getClass().getName()); // [I  (means int array)
```

- `hashCode()`, `toString()` → default behavior unless overridden with `Arrays` methods.
    

---

## 🔹 **5. Arrays of Objects**

You can also have arrays **holding objects**:

```java
String[] names = {"Ali", "Sara", "Omar"};
System.out.println(Arrays.toString(names));
```

---

# ✅ **Summary**

- Arrays in Java are **objects**, stored on the heap.
    
- They have a built-in `length` field.
    
- For most operations (sort, search, compare, copy, print), you use **`java.util.Arrays`** utility methods.
    
- For 2D/multidimensional arrays → `Arrays.deepToString` and `Arrays.deepEquals`.
    
- Arrays also inherit `Object` methods (`getClass`, `hashCode`).
    
