## **1. `File.separator`**

- **What it is:** Directory separator for a _single_ file path.
    
- **Used in:** Building file system paths.
    

### **Values**

- Unix/macOS: `/`
    
- Windows: `\`
    

---

## **2. `File.pathSeparator`**

- **What it is:** Separator _between multiple paths_ in a list (e.g., `CLASSPATH`).
    
- **Used in:** Environment variables, classpaths, collections of paths.
    

### **Values**

- Unix/macOS: `:`
    
- Windows: `;`
    

---

## **3. `System.lineSeparator()`**

- **What it is:** Platform-specific newline (end-of-line).
    
- **Used in:** Text files, console output, logs.
    

### **Values**

- Unix/macOS: `\n`
    
- Windows: `\r\n`
    

---

## **Comparison Table**

|Java API|Purpose|Unix/macOS|Windows|
|---|---|---|---|
|`File.separator`|Directory separator in a path|`/`|`\`|
|`File.pathSeparator`|Separator between multiple paths|`:`|`;`|
|`System.lineSeparator()`|Newline sequence|`\n`|`\r\n`|
