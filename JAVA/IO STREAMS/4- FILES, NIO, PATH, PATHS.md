# **1. `java.io.File` (Legacy)**

`File` represents a file or directory path in the filesystem.  
It does **not** perform actual file I/O—only metadata and path operations.

---

## **Key Capabilities**

- Test existence and type (`exists`, `isFile`, `isDirectory`)
    
- Query metadata (`length`, `lastModified`)
    
- Create/delete entries (`mkdir`, `mkdirs`, `delete`)
    
- Rename/move via `renameTo`
    
- List directory contents
    
- Convert to absolute/canonical path
    

---

## **Limitations**

- No built-in I/O (must use streams)
    
- Poor error handling
    
- Weak symbolic link support
    
- `renameTo` is unreliable across platforms
    

**Use only for legacy code. Prefer Path + Files.**

---

# **2. `Path` (Modern Path Representation)**

A `Path` is an immutable, type-safe representation of a file or directory path.

---

## **Strengths**

- OS-independent path handling
    
- Normalization (`normalize`)
    
- Composition (`resolve`, `relativize`)
    
- Safe comparisons (`startsWith`, `endsWith`)
    
- Converts to `File` with `toFile()`
    

---

## **Typical Usage**

`Path p = Paths.get("data", "input.txt"); Path absolute = p.toAbsolutePath(); Path normalized = p.normalize();`

---

# **3. `Paths` (Factory for Path)**

A simple utility class that creates `Path` objects.

---

## **Main Method**

`Paths.get(String first, String... more)`

---

## **Example**

`Path p = Paths.get("C:/data/log.txt");`

---

# **4. `Files` (Modern File Operations — NIO.2)**

The **core API** for all file-system I/O, metadata, copying, moving, reading, and writing.

---

## **Creation and Deletion**

- `createFile`
    
- `createDirectory`
    
- `createDirectories`
    
- `delete`
    
- `deleteIfExists`
    

---

## **File Copy / Move**

- `copy(src, dest, options...)`
    
- `move(src, dest, options...)`
    

---

## **Reading and Writing**

- `readAllBytes`
    
- `readAllLines`
    
- `write(Path, byte[])`
    
- `write(Path, List<String>)`
    
- `newBufferedReader`
    
- `newBufferedWriter`
    
- `newInputStream`
    
- `newOutputStream`
    

---

## **Metadata Queries**

- `exists`, `isDirectory`, `isRegularFile`
    
- `size`
    
- `getLastModifiedTime`
    
- `getOwner`
    
- `getPosixFilePermissions`
    

---

## **Directory Traversal**

- `newDirectoryStream`
    
- `walk` (stream of files)
    
- `walkFileTree` (with FileVisitor)
    

---

# **5. Supporting NIO Components (Overview)**

---

## **FileVisitor / SimpleFileVisitor**

Used with `Files.walkFileTree()` for recursive operations (searching, deleting, filtering).

---

## **DirectoryStream**

Memory-efficient iterator for directory entries (better than loading full lists).

---

## **WatchService**

Watches directories for:

- create
    
- modify
    
- delete
    

Used for log monitoring, auto-reload, syncing tasks.

---

## **Attribute Views**

- `BasicFileAttributes`
    
- `DosFileAttributes`
    
- `PosixFileAttributes`
    
- `UserDefinedFileAttributeView`  
    Allows full metadata inspection/modification.
    

---

# **6. Consolidated Summary Table**

|API|Purpose|Strengths|Recommended?|
|---|---|---|---|
|`File`|Path + metadata|Simple, legacy-compatible|No (legacy only)|
|`Path`|Modern path model|Immutable, safe, OS-independent|Yes|
|`Paths`|Path factory|Simple creation|Yes|
|`Files`|All file I/O|Complete, reliable, fast|Yes|
|`FileVisitor`|Recursive directory walking|Fine-grained control|Yes|
|`DirectoryStream`|Efficient directory iteration|Memory-safe|Yes|
|`WatchService`|Directory monitoring|Real-time event notifications|Yes|
|Attribute Views|Metadata operations|POSIX, DOS, owner, ACL|Yes|

---

# **FULL WORKED EXAMPLE**

Complete Java File Demonstrating Major File, Path, and Files Operations  
(Including comments explaining expected results)

```java
import java.io.*;
import java.nio.file.*;
import java.nio.file.attribute.*;
import java.util.*;
import java.nio.charset.StandardCharsets;

public class AllFileOperationsDemo {

    public static void main(String[] args) throws Exception {

        System.out.println("=== Starting File/NIO Demonstration ===");

        //--------------------------------------------------------------------
        // 1. Using java.io.File (legacy)
        //--------------------------------------------------------------------
        File legacy = new File("legacyDir");
        legacy.mkdirs(); // creates directory

        System.out.println("Legacy exists: " + legacy.exists());   // true
        System.out.println("Is Directory: " + legacy.isDirectory()); // true
        System.out.println("Absolute Path: " + legacy.getAbsolutePath());

        File legacyFile = new File(legacy, "oldFile.txt");
        legacyFile.createNewFile(); // create empty file
        System.out.println("Legacy file created: " + legacyFile.exists()); // true


        //--------------------------------------------------------------------
        // 2. Path and Paths — modern path handling
        //--------------------------------------------------------------------
        Path path = Paths.get("data", "example.txt");  // creates no file yet

        System.out.println("\n--- Path Info ---");
        System.out.println("File Name: " + path.getFileName()); // example.txt
        System.out.println("Parent: " + path.getParent());      // data
        System.out.println("Absolute Path: " + path.toAbsolutePath());


        //--------------------------------------------------------------------
        // 3. Files — creation, write, read, copy, move
        //--------------------------------------------------------------------
        Files.createDirectories(path.getParent()); // data/

        // Write text file
        Files.write(path,
                Arrays.asList("Line 1", "Line 2", "Line 3"),
                StandardCharsets.UTF_8);

        // Read text file
        System.out.println("\n--- File Contents ---");
        List<String> lines = Files.readAllLines(path);
        for (String l : lines) System.out.println(l);

        // Write bytes
        Path binary = Paths.get("data", "binary.dat");
        Files.write(binary, new byte[]{10, 20, 30, 40, 50});

        // Read bytes
        byte[] bytes = Files.readAllBytes(binary);
        System.out.println("\nBinary length: " + bytes.length); // 5


        //--------------------------------------------------------------------
        // 4. Copy and Move Operations
        //--------------------------------------------------------------------
        Path copyPath = Paths.get("data", "example_copy.txt");
        Files.copy(path, copyPath, StandardCopyOption.REPLACE_EXISTING);

        Path movedPath = Paths.get("data", "example_moved.txt");
        Files.move(copyPath, movedPath, StandardCopyOption.REPLACE_EXISTING);

        System.out.println("Copy exists? " + Files.exists(copyPath));  // false
        System.out.println("Moved exists? " + Files.exists(movedPath)); // true


        //--------------------------------------------------------------------
        // 5. Metadata (attributes)
        //--------------------------------------------------------------------
        BasicFileAttributes attr = Files.readAttributes(path, BasicFileAttributes.class);

        System.out.println("\n--- Attributes ---");
        System.out.println("Size: " + attr.size());
        System.out.println("Is Regular File: " + attr.isRegularFile());
        System.out.println("Creation Time: " + attr.creationTime());

        FileOwnerAttributeView ownerView =
                Files.getFileAttributeView(path, FileOwnerAttributeView.class);
        System.out.println("Owner: " + ownerView.getOwner().getName());


        //--------------------------------------------------------------------
        // 6. DirectoryStream — memory-efficient directory listing
        //--------------------------------------------------------------------
        System.out.println("\n--- DirectoryStream ---");
        try (DirectoryStream<Path> stream = Files.newDirectoryStream(Paths.get("data"))) {
            for (Path p : stream) {
                System.out.println("Found: " + p.getFileName());
            }
        }


        //--------------------------------------------------------------------
        // 7. Files.walk — recursive traversal using streams
        //--------------------------------------------------------------------
        System.out.println("\n--- Walk ---");
        Files.walk(Paths.get("data")).forEach(System.out::println);


        //--------------------------------------------------------------------
        // 8. walkFileTree — full visitor pattern
        //--------------------------------------------------------------------
        System.out.println("\n--- WalkFileTree (listing only) ---");
        Files.walkFileTree(Paths.get("data"), new SimpleFileVisitor<Path>() {
            @Override
            public FileVisitResult visitFile(Path file, BasicFileAttributes attrs)
                    throws IOException {
                System.out.println("Visited file: " + file);
                return FileVisitResult.CONTINUE;
            }
        });


        //--------------------------------------------------------------------
        // 9. WatchService — monitor directory for changes
        //--------------------------------------------------------------------
        System.out.println("\n--- WatchService Demo (1 cycle) ---");

        WatchService watcher = FileSystems.getDefault().newWatchService();
        Paths.get("data").register(watcher,
                StandardWatchEventKinds.ENTRY_CREATE,
                StandardWatchEventKinds.ENTRY_MODIFY,
                StandardWatchEventKinds.ENTRY_DELETE);

        // trigger event by modifying file
        Files.write(path, Arrays.asList("Updated content"), StandardCharsets.UTF_8);

        WatchKey key = watcher.take(); // waits until event occurs

        for (WatchEvent<?> event : key.pollEvents()) {
            System.out.println("Event kind: " + event.kind());
            System.out.println("Affected: " + event.context());
        }

        key.reset();

        //--------------------------------------------------------------------
        // End
        //--------------------------------------------------------------------
        System.out.println("\n=== End of Demonstration ===");
    }
}
```
