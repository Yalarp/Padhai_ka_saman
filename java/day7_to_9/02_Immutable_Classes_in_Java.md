# Immutable Classes in Java

An immutable class is a class whose instances cannot be modified after they are created. Any modification results in a new object definition. String and Wrapper classes (Integer, Byte, Float, etc.) are classic examples of immutable classes in Java.

## 1. Rules to Create an Immutable Class

To create a custom immutable class, you must follow these 5 rules:

1.  **Declare the class as `final`**: This prevents inheritance. If a class can be extended, a subclass can override methods and change the state, breaking immutability.
2.  **Make all fields `private`**: Ensure direct access is not allowed.
3.  **Make all fields `final`**: This ensures the fields are initialized once (usually in the constructor) and cannot be reassigned.
4.  **No Setter Methods**: Do not provide any method that modifies the field values.
5.  **Defensive Copying**:
    - If a field is a reference to a **mutable** object (like an array or `Date`), do not return the reference directly in the getter. Return a copy (clone) of it.
    - Similarly, if the constructor takes a mutable object, store a copy of it, not the direct reference.

---

## 2. Step-by-Step Implementation

### Step 1: Basic Immutable Class (Primitive Fields)

If a class only contains primitives or immutable objects (like String), it is easy to make it immutable.

```java
final class SimpleImmutable {
    private final int id;
    private final String name;

    public SimpleImmutable(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() { return id; }
    public String getName() { return name; }
    
    // No Setters provided.
}
```

---

### Step 2: Handling Mutable Fields (The Risk)

If your class contains a mutable object (like an array), simply making it `final` is not enough. The *reference* is final, but the *content* of the array can be changed.

#### Improper Implementation (Broken Immutability)

```java
import java.util.Arrays;

final class Student {
    private final String name;
    private final int[] marks; // Mutable object!

    public Student(String name, int[] marks) {
        this.name = name;
        this.marks = marks; // REFERENCE ESCAPE!
    }

    public int[] getMarks() {
        return marks; // REFERENCE ESCAPE!
    }

    @Override
    public String toString() {
        return "Student [name=" + name + ", marks=" + Arrays.toString(marks) + "]";
    }
}

public class TestImmutability {
    public static void main(String[] args) {
        int[] m = {100, 200};
        Student s = new Student("Ram", m);
        System.out.println(s);

        // Breaking Immutability from outside!
        s.getMarks()[0] = 999; 
        
        System.out.println("After Modification:");
        System.out.println(s); // State changed! Not immutable.
    }
}
```
**Problem**: The caller gets a direct reference to the internal `marks` array and modifies it.

---

### Step 3: Correct Implementation (Defensive Copying)

To fix this, we must never share the reference of the mutable field. We used **cloning** or **array copying**.

#### Correct Implementation

```java
import java.util.Arrays;

final class SecureStudent {
    private final String name;
    private final int[] marks;

    public SecureStudent(String name, int[] marks) {
        this.name = name;
        // Defensive Copy in Constructor
        this.marks = marks.clone(); 
    }

    public int[] getMarks() {
        // Defensive Copy in Getter
        return marks.clone(); 
    }

    @Override
    public String toString() {
        return "Student [name=" + name + ", marks=" + Arrays.toString(marks) + "]";
    }
}

public class TestSecure {
    public static void main(String[] args) {
        int[] m = {100, 200};
        SecureStudent s = new SecureStudent("Ram", m);
        
        // 1. Trying to modify via input array
        m[0] = 500; 
        System.out.println(s); // Unaffected, because constructor made a copy.

        // 2. Trying to modify via getter
        s.getMarks()[0] = 999;
        System.out.println(s); // Unaffected, because getter returned a copy.
    }
}
```

---

## 3. Why must Immutable Classes be Final?

The `final` keyword is crucial. Without it, a malicious subclass can break the immutability contract.

#### The Subclass Attack Scenario

Imagine `MyString` is an immutable class but NOT final.

```java
// Vulnerable Immutable Class
class MyString {
    private String value;
    public MyString(String v) { this.value = v; }
    public String getValue() { return value; }
}

// Malicious Subclass
class EvilString extends MyString {
    private String realValue;
    
    public EvilString(String v) {
        super(v);
        this.realValue = v;
    }
    
    @Override
    public String getValue() {
        // Returns a value that changes every time!
        return realValue + Math.random();
    }
    
    public void setValue(String v) {
        this.realValue = v;
    }
}
```

If a method expects a `MyString` (assuming it's immutable) but receives an `EvilString` (polymorphism), the program's logic can break because the value keeps changing.

**Key Takeaway**: Making the class `final` guarantees that **no one can extend it** and change its behavior.

## Summary

1.  **Strong Immutability**: Requires `final` class, `private final` fields, no setters.
2.  **Deep Immutability**: Handle mutable fields like Arrays, Date, and Collections by returning/storing **copies** (Defensive Copying).
3.  **Security**: Immutability is heavily used in security (like String) because the state is guaranteed to be constant.
