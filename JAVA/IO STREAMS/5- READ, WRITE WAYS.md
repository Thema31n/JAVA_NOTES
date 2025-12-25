# **Complete Java I/O Landscape (Read & Write Data)**

Below is a **full, systematic, professional overview of every important way to READ and WRITE data in Java**, covering:

- Byte Streams
    
- Character Streams
    
- Buffered Streams
    
- Data Streams
    
- Object Streams
    
- Print Streams / Writers
    
- FileReader / FileWriter
    
- NIO.2 (Files, Path)
    
- NIO Channels
    
- MappedByteBuffer
    
- Scanner
    
- RandomAccessFile
    

For each approach:

1. What it is
    
2. When to use it
    
3. Code example
    

---

## **1. BYTE STREAMS**

(Byte-oriented, 8-bit operations)

### **Use When**

- Working with **binary data** (images, PDFs, videos, audio, serialized bytes)
    
- Reading / writing non-text files
    

### **Main Classes**

- `InputStream` / `FileInputStream`
    
- `OutputStream` / `FileOutputStream`
    
- `BufferedInputStream` / `BufferedOutputStream`
    
- `DataInputStream` / `DataOutputStream`
    
- `ObjectInputStream` / `ObjectOutputStream`
    
- `ByteArrayInputStream` / `ByteArrayOutputStream`
    

### **Example**

```java
try (InputStream in = new FileInputStream("image.png");
     OutputStream out = new FileOutputStream("copy.png")) {

    byte[] buffer = new byte[4096];
    int bytesRead;
    while ((bytesRead = in.read(buffer)) != -1) {
        out.write(buffer, 0, bytesRead);
    }
}
```

---

## **2. CHARACTER STREAMS**

(Character-based, Unicode-safe)

### **Use When**

- Reading / writing **text files**
    
- Handling multilingual text
    
- When encoding support is required
    

### **Main Classes**

- `Reader` / `FileReader`
    
- `Writer` / `FileWriter`
    
- `BufferedReader` / `BufferedWriter`
    
- `InputStreamReader` / `OutputStreamWriter`
    

### **Example**

```java
try (BufferedReader br = new BufferedReader(new FileReader("text.txt"));
     BufferedWriter bw = new BufferedWriter(new FileWriter("copy.txt"))) {

    String line;
    while ((line = br.readLine()) != null) {
        bw.write(line);
        bw.newLine();
    }
}
```

---

## **3. PRINT WRITERS / PRINT STREAMS**

(Formatted, convenient text output)

### **Use When**

- You want `print()`, `println()`, `printf()`
    
- Logging or report generation
    
- Writing formatted output easily
    

### **Classes**

- `PrintWriter`
    
- `PrintStream` (System.out)
    

### **Example**

```java
try (PrintWriter pw = new PrintWriter("log.txt")) {
    pw.println("Hello");
    pw.printf("Value: %.2f", 12.345);
}
```

---

## **4. DATA STREAMS**

(Primitive data in binary format)

### **Use When**

- Storing **primitive values** efficiently:
    
    - int, long, double, UTF strings
        

### **Classes**

- `DataInputStream`
    
- `DataOutputStream`
    

### **Example**

```java
try (DataOutputStream out =
         new DataOutputStream(new FileOutputStream("data.bin"))) {
    out.writeInt(42);
    out.writeDouble(3.14);
}

try (DataInputStream in =
         new DataInputStream(new FileInputStream("data.bin"))) {
    int x = in.readInt();
    double y = in.readDouble();
}
```

---

## **5. OBJECT STREAMS**

(Java serialization – legacy usage)

### **Use When**

- Saving Java objects in binary form (legacy systems)
    
- Java-to-Java transportation
    

### **Classes**

- `ObjectOutputStream`
    
- `ObjectInputStream`
    

### **Example**

```java
try (ObjectOutputStream out =
         new ObjectOutputStream(new FileOutputStream("obj.bin"))) {
    out.writeObject(new Person("Ahmed", 22));
}

try (ObjectInputStream in =
         new ObjectInputStream(new FileInputStream("obj.bin"))) {
    Person p = (Person) in.readObject();
}
```

---

## **6. SCANNER**

(Simple parsing-oriented text input)

### **Use When**

- Reading text **with parsing** (ints, doubles, tokens)
    
- Input from console or files
    

### **Note**

Slower than `BufferedReader` but very convenient.

### **Example**

```java
try (Scanner sc = new Scanner(new File("input.txt"))) {
    while (sc.hasNext()) {
        System.out.println(sc.next());
    }
}
```

---

## **7. RANDOMACCESSFILE**

(Random-position file access)

### **Use When**

- You need **seek()**
    
- File-based databases
    
- Fixed-length record storage
    

### **Example**

```java
RandomAccessFile raf = new RandomAccessFile("data.bin", "rw");
raf.seek(100);
raf.writeInt(500);
raf.seek(100);
int x = raf.readInt();  // 500
```

---

## **8. NIO.2 — Files API**

(Modern, simple, high-level I/O)

### **Use When**

- Reading / writing whole files
    
- Directory traversal
    
- Fast, concise file operations
    

### **Example (Text)**

```java
Path p = Paths.get("data.txt");
Files.write(p, List.of("A", "B", "C"));
List<String> list = Files.readAllLines(p);
```

### **Example (Binary)**

```java
Files.write(p, new byte[]{1, 2, 3});
byte[] bytes = Files.readAllBytes(p);
```

---

## **9. NIO CHANNELS**

(High-performance, scalable I/O)

### **Use When**

- Large file transfers
    
- Server frameworks
    
- Networking and non-blocking I/O
    

### **Main Classes**

- `FileChannel`
    
- `ReadableByteChannel`
    
- `WritableByteChannel`
    
- `SocketChannel`
    
- `ServerSocketChannel`
    

### **Example**

```java
try (FileChannel in = FileChannel.open(Paths.get("file.bin"));
     FileChannel out = FileChannel.open(
         Paths.get("copy.bin"),
         StandardOpenOption.CREATE,
         StandardOpenOption.WRITE)) {

    in.transferTo(0, in.size(), out);
}
```

---

## **10. MEMORY-MAPPED FILES**

(Files mapped directly to memory)

### **Use When**

- Extremely fast I/O required
    
- Very large files (GBs)
    
- Database-like access patterns
    

### **Example**

```java
try (FileChannel fc = FileChannel.open(
         Paths.get("big.bin"),
         StandardOpenOption.READ,
         StandardOpenOption.WRITE)) {

    MappedByteBuffer buf =
        fc.map(FileChannel.MapMode.READ_WRITE, 0, fc.size());

    buf.put(0, (byte) 99);
}
```

---

## **11. ASYNCHRONOUS NIO.2**

(Non-blocking, async I/O)

### **Use When**

- High-performance servers
    
- Scalable asynchronous systems
    

### **Classes**

- `AsynchronousFileChannel`
    
- `CompletionHandler`
    

### **Example**

```java
AsynchronousFileChannel ch =
    AsynchronousFileChannel.open(
        Paths.get("a.txt"),
        StandardOpenOption.READ);

ByteBuffer buf = ByteBuffer.allocate(100);

ch.read(buf, 0, buf, new CompletionHandler<Integer, ByteBuffer>() {
    @Override
    public void completed(Integer result, ByteBuffer attachment) {
        System.out.println("Read: " + result + " bytes");
    }

    @Override
    public void failed(Throwable exc, ByteBuffer attachment) {
        exc.printStackTrace();
    }
});
```

---

# **COMPLETE SUMMARY TABLE**

|Method|Type|When to Use|
|---|---|---|
|FileInputStream / FileOutputStream|Byte|Binary files|
|BufferedInputStream / BufferedOutputStream|Byte buffered|Faster binary I/O|
|DataInputStream / DataOutputStream|Binary primitives|Efficient numeric storage|
|ObjectInputStream / ObjectOutputStream|Binary objects|Serialization (legacy)|
|Reader / Writer|Text|Basic text I/O|
|BufferedReader / BufferedWriter|Text buffered|Large text files|
|FileReader / FileWriter|Text|Legacy text I/O|
|InputStreamReader / OutputStreamWriter|Text encoding|UTF-8, UTF-16|
|PrintWriter / PrintStream|Text|Logging, formatted output|
|Scanner|Text parsing|Token-based input|
|RandomAccessFile|Random access|Seekable files|
|Files.readAllLines|Text|Small text files|
|Files.readAllBytes|Binary|Small binary files|
|Files.newBufferedReader / Writer|Modern I/O|Preferred text API|
|Channels|Binary performance|Large transfers|
|MappedByteBuffer|Memory-mapped|Extreme performance|
|AsynchronousFileChannel|Async|High-performance servers|
