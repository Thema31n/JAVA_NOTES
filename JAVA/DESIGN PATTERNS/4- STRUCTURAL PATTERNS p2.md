# 🟦 **1. Bridge Pattern**

### **Declarative Definition**

> Separate **abstraction** from **implementation** so they can evolve independently.

### **Why use it?**

- You have **multiple dimensions of variation**
    
- Avoid “class explosion” caused by combining choices
    
- Keep abstraction stable but switch implementation
    
- Runtime flexibility
    

### **Real-World Scenario**

A **remote control** controls multiple types of **TVs**.

Without Bridge, you'd need classes like:

- SamsungRemote
    
- LGRemote
    
- SonyRemote
    
- SamsungAdvancedRemote
    
- LGAdvancedRemote
    
- SonyAdvancedRemote
    

→ **Class explosion problem**.

### **Bridge solves this**:

RemoteControl = abstraction  
TV = implementation

---

## ▶️ **Bridge – Real Java Example**

### **Abstraction (Remote Control)**

```java
abstract class RemoteControl {
    protected TV tv;

    public RemoteControl(TV tv) {
        this.tv = tv;
    }

    public abstract void turnOn();
    public abstract void turnOff();
}
```

### **Implementation (TV brands)**

```java
interface TV {
    void on();
    void off();
}

class SamsungTV implements TV {
    public void on() { System.out.println("Samsung TV ON"); }
    public void off() { System.out.println("Samsung TV OFF"); }
}

class SonyTV implements TV {
    public void on() { System.out.println("Sony TV ON"); }
    public void off() { System.out.println("Sony TV OFF"); }
}
```

### **Refined Abstraction**

```java
class AdvancedRemote extends RemoteControl {
    public AdvancedRemote(TV tv) {
        super(tv);
    }

    public void turnOn() { tv.on(); }
    public void turnOff() { tv.off(); }

    public void mute() {
        System.out.println("Remote: Muting TV");
    }
}
```

### **Usage**

```java
RemoteControl remote = new AdvancedRemote(new SamsungTV());
remote.turnOn();
remote.mute();
```

### ✔ Benefits:

- Add new remote types → no changes to TV classes
    
- Add new TV brands → no changes to remote classes
    
- Both sides evolve independently
    

---

# 🟫 **2. Flyweight Pattern**

### **Declarative Definition**

> Share **common, reusable** state across multiple objects to reduce memory usage.

### **Why use it?**

- Many objects share **identical internal data**
    
- Save RAM (useful in millions-scale objects)
    
- Keep large systems light and efficient
    

### **Real-World Scenario**

A text editor displays **millions of characters**.  
Each character has:

- code (‘A’, ‘B’, etc.) → shared
    
- formatting (font, size, style) → shared
    
- position (x, y) → unique per character
    

Instead of storing 1 million font objects, Flyweight stores ONE and reuses it.

---

## ▶️ **Flyweight – Real Java Example: Text Editor**

### **Flyweight (shared state)**

```java
class CharacterStyle {
    private final String font;
    private final int size;
    private final boolean bold;

    public CharacterStyle(String font, int size, boolean bold) {
        this.font = font;
        this.size = size;
        this.bold = bold;
    }
}
```

### **Flyweight Factory**

```java
class CharacterStyleFactory {
    private static final Map<String, CharacterStyle> styles = new HashMap<>();

    public static CharacterStyle getStyle(String font, int size, boolean bold) {
        String key = font + size + bold;

        return styles.computeIfAbsent(key, k -> 
            new CharacterStyle(font, size, bold)
        );
    }
}
```

### **Character that uses flyweight**

```java
class StyledCharacter {
    private final char c;
    private final CharacterStyle style; // SHARED
    private final int x, y;             // UNIQUE

    public StyledCharacter(char c, CharacterStyle style, int x, int y) {
        this.c = c;
        this.style = style;
        this.x = x;
        this.y = y;
    }
}
```

### ✔ Usage: Save memory for millions of characters

```java
CharacterStyle boldStyle = CharacterStyleFactory.getStyle("Arial", 12, true);

StyledCharacter ch = new StyledCharacter('A', boldStyle, 10, 20);
```

### ✔ Benefits:

- Drastically reduced memory footprint
    
- Separate shared state (style) from unique state (coordinates)
    
- Perfect for text editors, games, map rendering, graphs
    

---

# 🟩 **3. Composite Pattern**

### **Declarative Definition**

> Treat **individual objects** and **groups of objects** the **same way**.

Composite helps build **tree-like structures**:

- directories
    
- menus
    
- UIs
    
- game objects
    
- organization charts
    
- geometrical shapes
    

---

# 🔥 **Real-World Scenario**

Building a **file explorer**:

- File → leaf (no children)
    
- Folder → composite (contains files/folders)
    

Composite lets you call:

```java
folder.show();
```

and it recursively handles all sub-items.

---

## ▶️ **Composite – Real Java Example: File System**

### **Component (common interface)**

```java
interface FileComponent {
    void show();
}
```

### **Leaf**

```java
class FileLeaf implements FileComponent {
    private final String name;

    public FileLeaf(String name) {
        this.name = name;
    }

    public void show() {
        System.out.println("File: " + name);
    }
}
```

### **Composite**

```java
class FolderComposite implements FileComponent {
    private final String name;
    private final List<FileComponent> children = new ArrayList<>();

    public FolderComposite(String name) {
        this.name = name;
    }

    public void add(FileComponent component) {
        children.add(component);
    }

    public void show() {
        System.out.println("Folder: " + name);
        for (FileComponent c : children) {
            c.show();
        }
    }
}
```

### **Usage**

```java
FolderComposite root = new FolderComposite("root");

FolderComposite images = new FolderComposite("images");
images.add(new FileLeaf("photo.png"));
images.add(new FileLeaf("logo.jpg"));

FolderComposite docs = new FolderComposite("docs");
docs.add(new FileLeaf("resume.pdf"));

root.add(images);
root.add(docs);

root.show();
```

### Output:

```
Folder: root
Folder: images
File: photo.png
File: logo.jpg
Folder: docs
File: resume.pdf
```

### ✔ Benefits:

- Treat file/folder uniformly
    
- Easy traversal
    
- Easily extend structure
    
- Matches real-world tree structures
    

---

# 🔥 FINAL COMPARATIVE SUMMARY

|Pattern|Purpose|Real Scenario|Key Difference|
|---|---|---|---|
|**Bridge**|Separate abstraction from implementation|Remote + TV brands|Avoid class explosion, enable dual independent hierarchies|
|**Flyweight**|Share common state|Text editor characters|Memory optimization using shared objects|
|**Composite**|Treat part-whole hierarchy uniformly|File system, organization trees|Works with tree-like structures|

---

# 🎯 Ultra-Simple One-Line Definitions

- **Bridge → two dimensions of change need separation.**
    
- **Flyweight → share memory for massive repeated data.**
    
- **Composite → treat individuals + groups the same.**
    
