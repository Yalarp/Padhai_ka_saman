# Serialization in Java

**Serialization** is the process of converting an object's state (field values) into a byte stream. This stream can be stored in a file (Persistence), sent over a network, or saved in a database.
**Deserialization** is the reverse process: creating an object from a byte stream.

## 1. Key Concepts

-   **Interface**: `java.io.Serializable`. It is a **Marker Interface** (no methods).
-   **Classes**:
    -   `ObjectOutputStream`: usage `writeObject(obj)`
    -   `ObjectInputStream`: usage `readObject()`
-   **Transient Keyword**: If you define a field as `transient`, it will **not** be serialized. Its value is lost (reset to default) during deserialization.

---

## 2. Basic Serialization Example

#### Code Example: `First.java`

```java
import java.io.*;

// 1. Implement Serializable
public class First implements Serializable {
    String name = "sachin";
    int age = 20;
    transient Thread t = new Thread(); // Transient field: NOT saved

    public static void main(String args[]) {
        First s = new First();
        
        // --- SERIALIZATION ---
        try(FileOutputStream fos = new FileOutputStream("e:\\ab1.txt");
            ObjectOutputStream oos = new ObjectOutputStream(fos)) {
            
            oos.writeObject(s); // Writes object state to file
            System.out.println("Object Serialized");
        } catch(Exception e) { e.printStackTrace(); }

        s = null; // Remove reference to original object

        // --- DESERIALIZATION ---
        try(FileInputStream fis = new FileInputStream("e:\\ab1.txt");
            ObjectInputStream ois = new ObjectInputStream(fis)) {
            
            // Read object (Downcast required)
            First s1 = (First) ois.readObject(); 
            
            // Output: sachin    20    null
            System.out.println(s1.name + "\t" + s1.age + "\t" + s1.t);
        } catch(Exception e) { e.printStackTrace(); }
    }
}
```
**Observation**: The `Thread t` field is `null` after deserialization because it was `transient`.

---

## 3. Serialization with Inheritance

There are two interesting scenarios when inheritance is involved.

### Case 1: Parent is NOT Serializable, Child IS Serializable

#### Code Example: `Second.java`

```java
import java.io.*;

class Base {
    int num1 = 30;
    Base() { System.out.println("Base Const"); }
}

class Sub extends Base implements Serializable {
    int num2 = 60;
    Sub() { System.out.println("Sub Const"); }
}

public class Second {
    public static void main(String args[]) throws Exception {
        Sub s = new Sub(); // Prints: Base Const, Sub Const
        s.num1 = 100;
        s.num2 = 200;
        
        // Serialize
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("my.txt"));
        oos.writeObject(s);
        oos.close();
        
        // Deserialize
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("my.txt"));
        Sub ref = (Sub) ois.readObject(); // Prints: Base Const !!
        
        // Output: 30    200
        System.out.println(ref.num1 + "\t" + ref.num2);
    }
}
```

**Critical Analysis**:
1.  **Why `num1` is 30 (not 100)?**
    -   Since `Base` is NOT Serializable, JVM ignores its fields during serialization. "Instance variable inheritance" logic doesn't apply to state persistence here.
2.  **Why "Base Const" printed during Deserialization?**
    -   During deserialization, JVM creates the `Sub` object without calling `Sub` constructor.
    -   HOWEVER, because `Base` is non-serializable, JVM **MUST run the Base constructor** to initialize the inherited fields (to default values).
    -   Hence, `num1` resets to 30 (default/init value).

### Case 2: Parent IS Serializable

#### Code Example: `Third.java`
If `Base implements Serializable`, then `Sub` is automatically Serializable.
-   **Behavior**:
    -   `num1` would be **100** (Persisted).
    -   No constructors are called during deserialization (Pure byte-stream reconstruction).

---

## 4. Object Graph Serialization

If you serialize an object `A` that has a reference to object `B`, Java automatically serializes `B` as well. This is called the **Object Graph**.
-   Requirement: `B` must also implement `Serializable`.
-   If `B` is not Serializable, `NotSerializableException` is thrown at runtime.
