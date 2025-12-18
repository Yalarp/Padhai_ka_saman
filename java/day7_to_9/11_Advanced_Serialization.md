# Advanced Serialization

Beyond standard serialization, Java provides mechanisms for fine-grained control over the process: the `Externalizable` interface and Custom Serialization hooks.

## 1. Externalizable Interface

While `Serializable` is a marker interface (automatic serialization), `Externalizable` extends `Serializable` and provides two methods that give you **complete control** over the serialization process.

| Feature | Serializable | Externalizable |
| :--- | :--- | :--- |
| **Methods** | None (Marker) | `writeExternal()`, `readExternal()` |
| **Process** | Automatic (Default JVM logic) | Manual (Programmer logic) |
| **Constructor** | No-arg constructor NOT called | **Public No-arg constructor IS REQUIRED** |
| **Performance** | Slower (Reflection usage) | Faster (Direct writing) |

#### Code Example: `ExternalizableDemo1.java`

```java
import java.io.*;

class Account implements Externalizable {
    String username;
    String pwd;

    // IMPORTANT: Public no-arg constructor required for Deserialization
    public Account() {
        System.out.println("Default Constructor");
    }

    public Account(String username, String pwd) {
        this.username = username;
        this.pwd = pwd;
    }

    @Override
    public void writeExternal(ObjectOutput out) throws IOException {
        System.out.println("In writeExternal");
        // Custom Logic: Encrypt password before saving
        String encryptedPwd = pwd.replace('t', '*'); 
        
        out.writeUTF(username);
        out.writeUTF(encryptedPwd);
    }

    @Override
    public void readExternal(ObjectInput in) throws IOException, ClassNotFoundException {
        System.out.println("In readExternal");
        // Custom Logic: Decrypt password after reading
        username = in.readUTF();
        String temp = in.readUTF();
        pwd = temp.replace('*', 't'); 
    }
}
```
**Flow**: `new Account()` -> `writeExternal()` -> ... -> `new Account()` (Default/No-arg Const) -> `readExternal()`.

---

## 2. Customizing Default Serialization

You can customize the default serialization logic (without implementing `Externalizable`) by defining two private methods in your class. The JVM checks for these methods using Reflection and calls them if present.

**Signatures must be exact**:
-   `private void writeObject(ObjectOutputStream oos) throws IOException`
-   `private void readObject(ObjectInputStream ois) throws IOException, ClassNotFoundException`

**Why use this?**
To perform encryption/decryption or add extra data (like timestamp) while keeping the convenience of `Serializable`.

```java
private void writeObject(ObjectOutputStream oos) throws IOException {
    // 1. Perform default serialization for non-transient fields
    oos.defaultWriteObject(); 
    
    // 2. Add custom encryption logic for 'pwd' field usually marked transient
    String encrypted = encrypt(this.pwd);
    oos.writeObject(encrypted);
}

private void readObject(ObjectInputStream ois) throws IOException, ClassNotFoundException {
    // 1. Perform default deserialization
    ois.defaultReadObject();
    
    // 2. Read custom encrypted data
    String encrypted = (String) ois.readObject();
    this.pwd = decrypt(encrypted);
}
```

---

## 3. SerialVersionUID

The `serialVersionUID` is a unique identifier for Serializable classes. It is used during **Deserialization** to verify that the sender and receiver of a serialized object have loaded classes for that object that are compatible.

### How it works
1.  **Calculation**: If you don't declare it preventing, JVM calculates a hash based on class structure (class name, fields, methods).
2.  **Versioning**: If you modify the class (e.g., add a field) and recompile, the calculated UID changes.
3.  **Conflict**: If you deserialize an "Old" object with the "New" class, JVM sees mismatched UIDs and throws `java.io.InvalidClassException`.

### Best Practice
Always explicit declare it to maintain compatibility across different versions of the class.

```java
public class Employee implements Serializable {
    // Fixed ID prevents invalidation even if class methods change
    private static final long serialVersionUID = 1L; 
    
    // ... fields
}
```
