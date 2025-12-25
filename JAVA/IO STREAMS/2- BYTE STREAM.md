# **1. InputStream (abstract)**

### **What it is**

- Base class for all byte-input classes.
    
- Reads raw **8-bit bytes**.
    
- You normally do not instantiate it directly.
    

### **Example**

```java
import java.io.*;

public class InputStreamExample {
    public static void main(String[] args) throws Exception {
        InputStream in = new FileInputStream("data.bin");

        int b;
        while ((b = in.read()) != -1) {
            System.out.println("Byte: " + b);
        }

        in.close();
    }
}
```

---

# **2. FileInputStream**

### **What it is**

- Reads raw bytes from a **file**.
    
- Use for binary files (images, MP4, ZIP).
    

### **Example**

```java
import java.io.*;

public class FileInputStreamExample {
    public static void main(String[] args) throws Exception {
        FileInputStream fis = new FileInputStream("image.png");

        int b;
        while ((b = fis.read()) != -1) {
            // read binary data
        }

        fis.close();
    }
}
```

---

# **3. BufferedInputStream**

### **What it is**

- Wraps another InputStream.
    
- Provides fast buffered reading.
    

### **Example**

```java
import java.io.*;

public class BufferedInputStreamExample {
    public static void main(String[] args) throws Exception {
        BufferedInputStream bis =
            new BufferedInputStream(new FileInputStream("large.bin"));

        int b;
        while ((b = bis.read()) != -1) { }

        bis.close();
    }
}
```

---

# **4. DataInputStream**

### **What it is**

- Reads **primitive types** (int, double, long, UTF) in binary format.
    

### **Example**

```java
import java.io.*;

public class DataInputStreamExample {
    public static void main(String[] args) throws Exception {
        DataInputStream dis =
            new DataInputStream(new FileInputStream("numbers.dat"));

        int x = dis.readInt();
        double y = dis.readDouble();
        String s = dis.readUTF();

        System.out.println(x);
        System.out.println(y);
        System.out.println(s);

        dis.close();
    }
}
```

---

# **5. OutputStream (abstract)**

### **What it is**

- Base class for all byte-output streams.
    
- Writes raw bytes.
    

### **Example**

```java
import java.io.*;

public class OutputStreamExample {
    public static void main(String[] args) throws Exception {
        OutputStream out = new FileOutputStream("out.bin");

        out.write(65);
        out.write(66);

        out.close();
    }
}
```

---

# **6. FileOutputStream**

### **What it is**

- Writes raw bytes to a file.
    
- Use for binary file output.
    

### **Example**

```java
import java.io.*;

public class FileOutputStreamExample {
    public static void main(String[] args) throws Exception {
        FileOutputStream fos = new FileOutputStream("pic_copy.png");

        fos.write(0x89);
        fos.write(0x50);

        fos.close();
    }
}
```

---

# **7. BufferedOutputStream**

### **What it is**

- Buffers output for efficiency.
    
- Use for large binary output.
    

### **Example**

```java
import java.io.*;

public class BufferedOutputStreamExample {
    public static void main(String[] args) throws Exception {
        BufferedOutputStream bos =
            new BufferedOutputStream(new FileOutputStream("big.bin"));

        for (int i = 0; i < 100000; i++) {
            bos.write(i % 256);
        }

        bos.close();
    }
}
```

---

# **8. DataOutputStream**

### **What it is**

- Writes primitive types in binary format.
    

### **Example**

```java
import java.io.*;

public class DataOutputStreamExample {
    public static void main(String[] args) throws Exception {
        DataOutputStream dos =
            new DataOutputStream(new FileOutputStream("data.dat"));

        dos.writeInt(100);
        dos.writeDouble(5.55);
        dos.writeUTF("Hello");

        dos.close();
    }
}
```

---

# **9. PrintStream**

### **What it is**

- Prints formatted text, but still a **byte stream**.
    
- Used by System.out.
    

### **Example**

```java
import java.io.*;

public class PrintStreamExample {
    public static void main(String[] args) throws Exception {
        PrintStream ps = new PrintStream("log.txt");

        ps.println("Hello from PrintStream");
        ps.printf("Value = %d", 50);

        ps.close();
    }
}
```
