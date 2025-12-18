# File Handling Basics in Java

File handling implies reading from and writing to files. Java provides the `java.io` package for all Input/Output operations.

## 1. The `File` Class

The `java.io.File` class does **not** read or write data. It is a representation of a file or directory path name. It is used to get metadata (properties) of a file.

#### Code Example: `FileDemo.java`

```java
import java.io.File;
import java.util.Date;

public class FileDemo {
    public static void main(String args[]) {
        // Create File object (does not create actual file on disk yet)
        File f = new File("c:\\temp\\FileDemo.java");
        
        System.out.println("File Name: " + f.getName());
        System.out.println("Path: " + f.getPath());
        System.out.println("Absolute Path: " + f.getAbsolutePath());
        
        System.out.println(f.exists() ? "Exists" : "Doesn't Exist");
        System.out.println(f.isDirectory() ? "Is Directory" : "Is File");
        
        // Metadata
        System.out.println("Last Modified: " + new Date(f.lastModified()));
        System.out.println("Size (bytes): " + f.length());
    }
}
```

---

## 2. Streams Concept

A **Stream** is a sequence of data flowing from a source to a destination.
-   **InputStream**: For reading data (Source -> Program).
-   **OutputStream**: For writing data (Program -> Destination).

### Byte vs Character Streams

1.  **Byte Streams**: Handle raw binary data (images, video, etc.) byte by byte (8-bit).
    -   Base Classes: `InputStream`, `OutputStream`.
    -   Impl Classes: `FileInputStream`, `FileOutputStream`.
2.  **Character Streams**: Handle text data, managing encoding (UTF-8, etc.) automatically.
    -   Base Classes: `Reader`, `Writer`.
    -   Impl Classes: `FileReader`, `FileWriter`.

---

## 3. Byte Stream Examples

#### Reading a File (`FileInputStream`)

```java
import java.io.*;

public class First {
    public static void main(String args[]) {
        File f = new File("d:\\FileDemo.java");
        if(!f.exists()) {
            System.out.println("File does not exist");
            return;
        }
        
        // Try-with-resources: Automatically closes stream
        try(FileInputStream fis = new FileInputStream(f)) {
            byte b[] = new byte[(int)f.length()];
            
            // Read bytes into array
            fis.read(b);
            
            // Convert bytes to String
            String content = new String(b);
            System.out.println(content);
        } catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

#### Writing to a File (`FileOutputStream`)

```java
import java.io.*;

public class Second {
    public static void main(String args[]) {
        // Second argument 'true' enables APPEND mode (don't overwrite)
        try(FileOutputStream fos = new FileOutputStream("d:\\abc1.txt", true)) {
            System.out.println("Enter data:");
            byte b[] = new byte[100];
            
            // Read from Keyboard (System.in)
            int k = System.in.read(b);
            
            // Write to file
            fos.write(b, 0, k); // Write 'k' bytes from index 0
        } catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

---

## 4. Character Stream Examples

#### Copying Text (`FileReader` / `FileWriter`)

```java
import java.io.*;

public class Third {
    public static void main(String args[]) {
        // Writing Chars
        try(FileWriter fw = new FileWriter("e:\\abc2.txt")) {
            char arr[] = {'a','b','c','d','e'};
            fw.write(arr);
        } catch(IOException ie) {
            ie.printStackTrace();
        }
        
        // Reading Chars
        try(FileReader fr = new FileReader("e:\\abc2.txt")) {
            char arr1[] = new char[(int)new File("e:\\abc2.txt").length()];
            fr.read(arr1);
            
            // Display
            for(char c : arr1) {
                System.out.println(c);
            }
        } catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

---

## 5. RandomAccessFile

Allows reading and writing to a random offset (position) in a file, unlike streams which are sequential.

#### Code Example: `Sixth.java`

```java
import java.io.*;

public class Sixth {
    public static void main(String args[]) {
        // Mode "rw" = Read/Write
        try(RandomAccessFile rf = new RandomAccessFile("e:\\temp\\xy.txt", "rw")) {
            
            // Go to end of file to Append
            rf.seek(rf.length());
            
            byte b[] = new byte[200];
            System.out.println("Enter data:");
            int k = System.in.read(b);
            rf.write(b, 0, k);
            
            // Go to beginning to Read
            rf.seek(0);
            byte c[] = new byte[(int)rf.length()];
            rf.read(c);
            System.out.println("File Content: " + new String(c));
        } catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```
