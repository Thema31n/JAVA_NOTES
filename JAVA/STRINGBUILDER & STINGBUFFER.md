## **1. What is StringBuilder?**

`StringBuilder` is a **mutable** sequence of characters used to efficiently build and modify strings **without creating many new objects**.

### Key Points

- Introduced in **Java 5**
    
- **Not synchronized** → **not thread-safe**
    
- **Fastest** option for string manipulation in **single-threaded** contexts
    
- Used when you need to concatenate or modify strings frequently
    

---

## **2. What is StringBuffer?**

`StringBuffer` is also a **mutable** sequence of characters, very similar to `StringBuilder`, but…

### Key Points

- **Synchronized** → **thread-safe**
    
- Slower than `StringBuilder`
    
- Used when multiple threads modify the same string buffer
    

---

## **3. Purpose of Using StringBuilder/StringBuffer**

Using normal `String` for concatenation (`+`) repeatedly is inefficient because `String` is **immutable**.  
Every modification creates a **new object**, wasting memory.

`StringBuilder` and `StringBuffer` allow modifications **in place**, making operations like:

- append
    
- insert
    
- delete
    
- replace
    

much faster.

---

## **4. Difference Between String, StringBuilder, and StringBuffer**

|Feature|String|StringBuilder|StringBuffer|
|---|---|---|---|
|Mutability|❌ Immutable|✔ Mutable|✔ Mutable|
|Thread-safe|Not needed|❌ No|✔ Yes|
|Performance|Slow for many changes|Fastest|Slower (due to sync)|
|Usage|Constants, keys, safe values|Heavy text manipulation|Multi-threaded text manipulation|

---

## **5. CharSequence Interface**

`CharSequence` is an interface implemented by:

- `String`
    
- `StringBuilder`
    
- `StringBuffer`
    
- `CharBuffer`
    
- etc.
    

### Purpose

Provides **read-only** access to character sequences via methods:

- `charAt(int)`
    
- `length()`
    
- `subSequence(int, int)`
    
- `toString()`
    

### Why?

It gives Java APIs the ability to accept **any kind of character data**, not just `String`.

---

## **6. Why StringBuilder / StringBuffer differ from String**

### **String = Immutable**

- Once created, cannot be changed.
    
- Safe for use as hash keys.
    
- Thread-safe by design.
    

### **SB/SBuffer = Mutable**

- Efficient for building/modifying dynamic text.
    
- Not stored in the string pool.
    
- Should not be used as immutable values.
    

Immutability is the main difference.

---

# **7. Java Code Example — All Common Functions**

```java
public class StringBuilderBufferExamples {
    public static void main(String[] args) {

        // -------------------------
        // StringBuilder
        // -------------------------
        StringBuilder sb = new StringBuilder("Hello");

        sb.append(" World")            // add text
          .append("!");

        sb.insert(5, ",")              // insert at index
          .insert(0, "[Start] ");

        sb.delete(0, 8);               // delete range (remove "[Start] ")

        sb.replace(0, 5, "Hi");        // replace part of sequence

        sb.reverse();                  // reverse content
        sb.reverse();                  // restore original

        int length = sb.length();      // get length
        int capacity = sb.capacity();  // internal buffer size

        char c = sb.charAt(1);         // access char
        sb.setCharAt(0, 'h');          // modify char

        String sbResult = sb.toString();


        // -------------------------
        // StringBuffer
        // -------------------------
        StringBuffer sbuf = new StringBuffer("Java");

        sbuf.append(" Programming")
            .append(" Language");

        sbuf.insert(4, " -");          // insert
        sbuf.delete(4, 6);             // delete inserted " -"

        sbuf.replace(0, 4, "JAVA");    // replace characters

        sbuf.reverse();                // reverse
        sbuf.reverse();                // restore

        int len2 = sbuf.length();
        int cap2 = sbuf.capacity();

        char c2 = sbuf.charAt(2);
        sbuf.setCharAt(0, 'J');

        String bufResult = sbuf.toString();


        // -------------------------
        // CharSequence usage
        // -------------------------
        CharSequence seq = "Hello CharSequence";
        System.out.println(seq.charAt(1));     // 'e'
        System.out.println(seq.subSequence(0, 5)); // "Hello"
    }
}
```

---

# **8. Quick Summary

- **StringBuilder**: mutable, fast, not thread-safe
    
- **StringBuffer**: mutable, slower, thread-safe
    
- **String**: immutable
    
- **CharSequence**: read-only interface implemented by all character classes
    
- Use SB/SBuffer when modifying strings frequently
    
- Use `String` when value must remain constant
    
