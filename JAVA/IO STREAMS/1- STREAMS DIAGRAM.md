# **Stream Classifications (based on file types)**

```
                         Stream Classifications
                           based on file types
                                    
                 ┌────────────────────┐   ┌────────────────────┐
                 │    ByteStream      │   │  CharacterStream    │
                 └──────┬─────────────┘   └───────────┬────────┘
                        ▼                              ▼
                   (Binary Streams)             (Character Streams)
```

---

## **Byte Streams**

```
BufferedInputStream  ◄────────────┐
DataInputStream      ◄────────────┤
FileInputStream      ◄────────────┤
InputStream          ◄────────────┤
PrintStream          ◄────────────┤
BufferedOutputStream ◄────────────┤
DataOutputStream     ◄────────────┤
FileOutputStream     ◄────────────┤
OutputStream         ◄────────────┘
```

---

## **Character Streams**

```
BufferedReader      ◄──────────────┐
FileReader          ◄──────────────┤
InputStreamReader   ◄──────────────┤
OutputStreamWriter  ◄──────────────┤
Reader              ◄──────────────┤
PrintWriter         ◄──────────────┤
Writer              ◄──────────────┤
BufferedWriter      ◄──────────────┤
FileWriter          ◄──────────────┘
```

---

# **1. The Core Difference**

---

## **Byte Streams (InputStream / OutputStream)**

- Work with **raw bytes** (8-bit data).
    
- Good for **binary files** or any data that is _not text_.
    

---

## **Character Streams (Reader / Writer)**

- Work with **characters** (16-bit Unicode).
    
- Automatically handles **text encoding (UTF-8, etc.)**.
    
- Good for **text files**.
    

---

# **2. When to Use Byte Streams**

Use **InputStream / OutputStream** when the data is **NOT text**.

### **Examples**

- Images (PNG, JPG)
    
- Audio files (MP3, WAV)
    
- Videos (MP4)
    
- Binary data (zip, exe, pdf)
    
- Network packets
    
- Any file you don't want altered by character encoding
    

### **Why?**

Byte streams do _not_ transform the bytes.  
They keep the file exactly the same.

### **Example (Reading binary file)**

```java
InputStream in = new FileInputStream("image.png");
```

---

# **3. When to Use Character Streams**

Use **Reader / Writer** when the data **is text**.

### **Examples**

- `.txt` files
    
- `.csv` files
    
- `.json`, `.xml`, `.html`
    
- Logs
    
- Any human-readable file
    

### **Why?**

Character streams convert bytes → characters using an encoding (UTF-8).  
They support reading text line-by-line.

### **Example (Reading text file)**

```java
BufferedReader br = new BufferedReader(new FileReader("data.txt"));
```

---

# **4. Easy Rule to Remember**

### **Is the file text?**

If yes → **use Reader/Writer**.

### **Is the file anything else (binary)?**

If yes → **use InputStream/OutputStream**.

---

# **5. More Detailed Differences**

|Feature|Byte Streams|Character Streams|
|---|---|---|
|Base Classes|InputStream, OutputStream|Reader, Writer|
|Data Type|Bytes (raw data)|Characters (text)|
|Encoding Handling|No|Yes|
|Suitable For|Binary files|Text files|
|Uses Buffering?|Yes (BufferedInputStream, etc.)|Yes (BufferedReader, etc.)|
|Safe for Images?|Yes|No|
|Safe for Text?|Yes but inconvenient|Yes, best option|

---

# **6. Why Not Use Byte Streams for Text?**

You **can**, but:

- You need to manually decode bytes using Charset.
    
- You lose `readLine()`
    
- Multibyte characters break (e.g., Arabic, Japanese, emojis)
    

Example problem:

- UTF-8 char may use 1–4 bytes
    
- Byte streams do not understand this
    

---

# **7. Why Not Use Character Streams for Binary?**

Character streams _transform_ bytes into characters.  
Binary files contain byte patterns that do **not form valid characters**.

This corrupts binary data.

---

# **8. Simple Real-World Guidance**

### **For images, audio, video, PDF → Byte Streams**

```java
new FileInputStream("video.mp4");
```

---

### **For text files → Character Streams**

```java
new BufferedReader(new FileReader("text.txt"));
```

---

### **For “easy text reading” → Scanner**

```java
Scanner sc = new Scanner(new File("data.txt"));
```

---

### **For modern Java (text or binary) → NIO Files API**

```java
Files.readString(Path.of("file.txt"));      // text
Files.readAllBytes(Path.of("image.png"));   // binary
```

---

# **9. TL;DR Summary**

```
USE BYTE STREAMS:
    - images, audio, video, zip, exe, pdf
    - anything not text
    - when preserving exact bytes is critical

USE CHARACTER STREAMS:
    - plain text files
    - csv, html, xml, json
    - anything human-readable
    - when encoding (UTF-8) matters
```
