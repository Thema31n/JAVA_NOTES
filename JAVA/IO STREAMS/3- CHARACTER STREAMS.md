# **1. Reader (abstract)**

### **What it is**

- Base class for character-input streams.
    
- Reads Unicode chars.
    

### **Example**

```java
import java.io.*;

public class ReaderExample {
    public static void main(String[] args) throws Exception {
        Reader r = new FileReader("text.txt");

        int ch;
        while ((ch = r.read()) != -1) {
            System.out.print((char) ch);
        }

        r.close();
    }
}
```

---

# **2. FileReader**

### **What it is**

- Reads characters from a file.
    
- Use for simple text reading.
    

### **Example**

```java
import java.io.*;

public class FileReaderExample {
    public static void main(String[] args) throws Exception {
        FileReader fr = new FileReader("notes.txt");

        int ch;
        while ((ch = fr.read()) != -1) {
            System.out.print((char) ch);
        }

        fr.close();
    }
}
```

---

# **3. BufferedReader**

### **What it is**

- Adds buffering + readLine().
    

### **Example**

```java
import java.io.*;

public class BufferedReaderExample {
    public static void main(String[] args) throws Exception {
        BufferedReader br =
            new BufferedReader(new FileReader("lines.txt"));

        String line;
        while ((line = br.readLine()) != null) {
            System.out.println("Line: " + line);
        }

        br.close();
    }
}
```

---

# **4. InputStreamReader**

### **What it is**

- Converts bytes → chars using encoding.
    

### **Example**

```java
import java.io.*;

public class InputStreamReaderExample {
    public static void main(String[] args) throws Exception {
        InputStreamReader isr =
            new InputStreamReader(new FileInputStream("utf8.txt"), "UTF-8");

        int ch;
        while ((ch = isr.read()) != -1) {
            System.out.print((char) ch);
        }

        isr.close();
    }
}
```

---

# **5. Writer (abstract)**

### **What it is**

- Base class for all character-output streams.
    

### **Example**

```java
import java.io.*;

public class WriterExample {
    public static void main(String[] args) throws Exception {
        Writer w = new FileWriter("demo.txt");

        w.write("Hello Writer");
        w.close();
    }
}
```

---

# **6. FileWriter**

### **What it is**

- Writes characters to a file.
    

### **Example**

```java
import java.io.*;

public class FileWriterExample {
    public static void main(String[] args) throws Exception {
        FileWriter fw = new FileWriter("hello.txt");

        fw.write("Line 1\nLine 2");
        fw.close();
    }
}
```

---

# **7. BufferedWriter**

### **What it is**

- Buffered writing + newLine().
    

### **Example**

```java
import java.io.*;

public class BufferedWriterExample {
    public static void main(String[] args) throws Exception {
        BufferedWriter bw =
            new BufferedWriter(new FileWriter("output.txt"));

        bw.write("Hello");
        bw.newLine();
        bw.write("BufferedWriter line 2");

        bw.close();
    }
}
```

---

# **8. OutputStreamWriter**

### **What it is**

- Converts characters → bytes using encoding.
    

### **Example**

```java
import java.io.*;

public class OutputStreamWriterExample {
    public static void main(String[] args) throws Exception {
        OutputStreamWriter osw =
            new OutputStreamWriter(new FileOutputStream("utf8-out.txt"), "UTF-8");

        osw.write("こんにちは世界\n");
        osw.write("مرحبا بالعالم\n");

        osw.close();
    }
}
```

---

# **9. PrintWriter**

### **What it is**

- High-level character writer.
    
- Supports print(), println(), printf().
    

### **Example**

```java
import java.io.*;

public class PrintWriterExample {
    public static void main(String[] args) throws Exception {
        PrintWriter pw = new PrintWriter("pw.txt");

        pw.println("PrintWriter text");
        pw.printf("Number: %.2f", 12.345);

        pw.close();
    }
}
```
