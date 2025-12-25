# 🟦 **1. Visitor Pattern**

### ✔ Declarative Definition

> Add new operations to existing class hierarchies **without modifying them**, by separating algorithms from the objects they operate on.

### ✔ Why use Visitor?

- When you want to apply **multiple operations** on objects of a complex structure
    
- But **don’t** want to modify those classes every time
    
- Avoid `instanceof` or huge conditionals
    
- Add new operations easily (open/closed principle)
    

### ✔ Real-World Scenario

A document editor containing elements:

- Paragraph
    
- Heading
    
- Image
    

You want to add operations:

- Export to PDF
    
- Spell-check
    
- Word count
    
- HTML conversion
    

Adding these methods to every element class → violates SRP & open/closed principle.

**Visitor solves this** by defining operations externally.

---

## ▶️ Visitor — Java Example (Document Processing)

### Element Interface

```java
interface DocumentElement {
    void accept(DocumentVisitor visitor);
}
```

### Concrete Elements

```java
class Paragraph implements DocumentElement {
    public void accept(DocumentVisitor visitor) { visitor.visit(this); }
}

class ImageElement implements DocumentElement {
    public void accept(DocumentVisitor visitor) { visitor.visit(this); }
}
```

### Visitor Interface

```java
interface DocumentVisitor {
    void visit(Paragraph paragraph);
    void visit(ImageElement image);
}
```

### Concrete Visitor (Word Count)

```java
class WordCountVisitor implements DocumentVisitor {
    public void visit(Paragraph p) {
        System.out.println("Counting words in paragraph...");
    }
    public void visit(ImageElement img) {
        System.out.println("Skipping images in word count.");
    }
}
```

### Usage

```java
List<DocumentElement> doc = List.of(new Paragraph(), new ImageElement());
DocumentVisitor visitor = new WordCountVisitor();

doc.forEach(e -> e.accept(visitor));
```

---

# 🔥 **Visitor vs Decorator**

|Aspect|Visitor|Decorator|
|---|---|---|
|Purpose|Add operations|Add behavior dynamically|
|Changes|Adds new **methods**|Adds new **functionality**|
|Structure|Double-dispatch|Object wrapping|
|When to use|Need to add many operations|Need runtime behavior modification|
|Example Use|Word count, export, spell check|Logging, compression, encryption|

### ✔ Simple Summary:

- **Visitor = add new algorithms without touching elements**
    
- **Decorator = add new behavior to ONE object dynamically**
    

---

# 🟩 **2. State Pattern**

### ✔ Declarative Definition

> Allow an object to change its behavior when its internal state changes.

### ✔ Why use it?

- Replace large `if-else` state machines
    
- Encapsulate behavior based on state
    
- Each state becomes a class
    
- Cleaner transitions
    

### ✔ Real-World Scenario

A **Media Player** with states:

- Playing
    
- Paused
    
- Stopped
    

Each state changes how the “press button” behaves.

---

## ▶️ State — Java Example (Media Player)

### Context

```java
class MediaPlayer {
    private PlayerState state;

    public MediaPlayer() { state = new StoppedState(); }

    public void setState(PlayerState state) { this.state = state; }

    public void pressPlay() { state.play(this); }
}
```

### State Interface

```java
interface PlayerState {
    void play(MediaPlayer player);
}
```

### Concrete States

```java
class PlayingState implements PlayerState {
    public void play(MediaPlayer player) {
        System.out.println("Pausing...");
        player.setState(new PausedState());
    }
}

class PausedState implements PlayerState {
    public void play(MediaPlayer player) {
        System.out.println("Resuming...");
        player.setState(new PlayingState());
    }
}

class StoppedState implements PlayerState {
    public void play(MediaPlayer player) {
        System.out.println("Starting...");
        player.setState(new PlayingState());
    }
}
```

### Usage

```java
MediaPlayer mp = new MediaPlayer();
mp.pressPlay();   // Starting...
mp.pressPlay();   // Pausing...
mp.pressPlay();   // Resuming...
```

---

# 🟨 **3. Observer Pattern**

### ✔ Declarative Definition

> A subject notifies multiple observers about state changes automatically.

### ✔ Why use it?

- One-to-many event notification
    
- Decouple subject from observers
    
- Event-driven systems
    
- GUI frameworks
    
- Messaging systems
    

### ✔ Real-World Scenario

Stock price app:

- Price changes → notify multiple subscribers
    
    - UI widget
        
    - Logger
        
    - Alert system
        

---

## ▶️ Observer — Java Example (Stock Price Tracker)

### Subject Interface

```java
interface StockSubject {
    void add(StockObserver o);
    void remove(StockObserver o);
    void notifyObservers();
}
```

### Concrete Subject

```java
class Stock implements StockSubject {
    private List<StockObserver> observers = new ArrayList<>();
    private double price;

    public void setPrice(double price) {
        this.price = price;
        notifyObservers();
    }

    public void add(StockObserver o) { observers.add(o); }
    public void remove(StockObserver o) { observers.remove(o); }

    public void notifyObservers() {
        observers.forEach(o -> o.update(price));
    }
}
```

### Observer Interface

```java
interface StockObserver {
    void update(double price);
}
```

### Concrete Observers

```java
class MobileApp implements StockObserver {
    public void update(double price) { System.out.println("Mobile app: " + price); }
}

class DesktopApp implements StockObserver {
    public void update(double price) { System.out.println("Desktop app: " + price); }
}
```

### Usage

```java
Stock stock = new Stock();
stock.add(new MobileApp());
stock.add(new DesktopApp());

stock.setPrice(120.5);
```

---

# 🟫 **4. Memento Pattern**

### ✔ Declarative Definition

> Capture and store an object's internal state so it can be restored later.

### ✔ Why use it?

- Undo/Redo functionality
    
- Snapshots (editors, games)
    
- Versioning
    

### ✔ Real-World Scenario

A text editor with CTRL+Z (undo).

---

## ▶️ Memento — Java Example (Text Editor)

### Memento (stores state)

```java
class EditorMemento {
    private final String text;

    public EditorMemento(String text) {
        this.text = text;
    }

    public String getText() { return text; }
}
```

### Originator (editor)

```java
class TextEditor {
    private String text = "";

    public void type(String words) { text += words; }

    public EditorMemento save() {
        return new EditorMemento(text);
    }

    public void restore(EditorMemento memento) {
        text = memento.getText();
    }

    public String getText() { return text; }
}
```

### Caretaker (stores history)

```java
class History {
    private Stack<EditorMemento> states = new Stack<>();

    public void push(EditorMemento m) { states.push(m); }
    public EditorMemento pop() { return states.pop(); }
}
```

### Usage

```java
TextEditor editor = new TextEditor();
History history = new History();

editor.type("Hello ");
history.push(editor.save());

editor.type("World!");
history.push(editor.save());

editor.restore(history.pop());

System.out.println(editor.getText()); // Output: Hello 
```

---

# 🎯 FINAL SUMMARY

### ✔ Visitor

Add new **operations** without modifying existing classes.  
Great for complex object structures.

### ✔ Visitor vs Decorator

- Visitor = add methods/behaviors to _all_ elements
    
- Decorator = add functionality to _one object_
    

### ✔ State

Object changes behavior based on internal state.  
Replaces huge `if-else` blocks.

### ✔ Observer

Automatic event-based notifications.  
Used in UI, messaging, reactive systems.

### ✔ Memento

Snapshot mechanism → undo/redo.  
Stores previous state without exposing internals.
