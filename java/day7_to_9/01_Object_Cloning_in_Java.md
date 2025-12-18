# Object Cloning in Java

Object cloning is a mechanism to create an exact copy of an object. It involves creating a new object with the same state (field values) as the original object.

## 1. The `Cloneable` Interface and `clone()` Method

In Java, the `java.lang.Object` class provides a method named `clone()` to support object copying.

```java
protected native Object clone() throws CloneNotSupportedException;
```

### Key Points:
- **Protected Access**: The `clone()` method is `protected` in the `Object` class.
- **Marker Interface**: To use `clone()`, a class must implement the `Cloneable` interface. `Cloneable` is a **marker interface** (it has no methods). It simply serves as an indicator to the JVM that the class allows its objects to be cloned.
- **Exception**: If a class calls `clone()` but does not implement `Cloneable`, the JVM throws a `CloneNotSupportedException`.

### Why is `clone()` protected?
If `clone()` were public in `Object`, every object in Java could be cloned by anyone. Java designers wanted to give the **choice to the programmer**. Only if a class explicitly allows cloning (by overriding `clone()` as public and implementing `Cloneable`), should its objects be cloneable.

### Why doesn't `Object` implement `Cloneable`?
If `Object` implemented `Cloneable`, every class would automatically be cloneable. This is contrary to the design philosophy of making cloning an "opt-in" feature for specific classes.

---

## 2. Types of Copying

There are two main ways to copy objects in Java:

1.  **Shallow Copy**: The default behavior of `clone()`. It copies the field values. If a field is a reference to another object, it copies the **reference**, not the object itself.
2.  **Deep Copy**: Creates a completely independent copy. If a field refers to another object, a deep copy will recursively create a copy of that object too.

---

## 3. Basic Cloning Example

Here is a simple example of how to implement cloning.

#### Code Example: `Test1.java`

```java
package core;

class Engine {
    private int speed;

    public Engine(int speed) {
        super();
        this.speed = speed;
    }

    public int getSpeed() { return speed; }
    public void setSpeed(int speed) { this.speed = speed; }
}

// 1. Implement Cloneable interface
class Car implements Cloneable {
    private Engine ref;
    private String name;

    public Car(String name) {
        super();
        this.name = name;
        this.ref = new Engine(100);
    }

    // Getters and Setters ommitted for brevity...

    // 2. Override clone() method
    @Override
    protected Object clone() throws CloneNotSupportedException {
        // 3. Call super.clone() to perform default shallow copy
        return super.clone();
    }
}

public class Test1 {
    public static void main(String[] args) {
        Car c = new Car("Nano");
        try {
            // 4. Invoke clone()
            c.clone(); 
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        System.out.println("Done");
    }
}
```

**Flow Explanation:**
1.  `Car` class implements `Cloneable`.
2.  It overrides `clone()` and calls `super.clone()`.
3.  `super.clone()` (from `Object` class) performs a memory-wise copy of the object.
4.  If `Car` did not implement `Cloneable`, `super.clone()` would throw `CloneNotSupportedException`.

---

## 4. Shallow Copy (Default Behavior)

In a shallow copy, primitive fields are copied by value, but object references are copied by reference. This means both the original and the cloned object **share the same dependency**.

#### Code Example: `Test2.java`

```java
class Engine {
    private int speed;
    public Engine(int speed) { this.speed = speed; }
    public void setSpeed(int speed) { this.speed = speed; }
    
    @Override
    public String toString() { return "Engine [speed=" + speed + "]"; }
}

class Car implements Cloneable {
    private Engine ref;
    private String name;

    public Car(String name) {
        this.name = name;
        this.ref = new Engine(100); // Car has an Engine
    }
    
    public Engine getRef() { return ref; }

    // Shallow Copy Implementation
    public Object clone() {
        Car ob = null;
        try {
            // Default clone() creates a shallow copy
            ob = (Car) super.clone(); 
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        return ob;
    }

    @Override
    public String toString() { return "[name=" + name + " ref=" + ref + "]"; }
}

public class Test2 {
    public static void main(String[] args) {
        Car c = new Car("Nano");
        Car c1 = (Car) c.clone(); // Create a clone
        
        System.out.println(c);
        System.out.println(c1);

        // Modifying the Engine of the CLONED car
        c1.getRef().setSpeed(400);

        System.out.println("After Modification");
        System.out.println(c);  // Original Car
        System.out.println(c1); // Cloned Car
    }
}
```

**Output:**
```
[name=Nano ref=Engine [speed=100]]
[name=Nano ref=Engine [speed=100]]
After Modification
[name=Nano ref=Engine [speed=400]]  <-- AFFECTED!
[name=Nano ref=Engine [speed=400]]
```

**Analysis:**
- When `c1` modifies its engine speed (`c1.getRef().setSpeed(400)`), the engine of `c` (original car) ALSO changes to 400.
- **Reason**: `super.clone()` copied the `ref` variable (address of the Engine object). So both `c` and `c1` point to the **same** `Engine` object in memory.

---

## 5. Deep Copy (Independent Copy)

To solve the shallow copy issue, we strictly implement a "Deep Copy". This means explicitly creating a new instance for any mutable reference fields.

#### Code Example: `Test3.java`

```java
class Car implements Cloneable {
    private Engine ref;
    private String name;
    
    // ... Constructor and other methods ...

    // Deep Copy Implementation
    public Object clone() {
        Car ob = null;
        try {
            // 1. Perform default shallow copy first
            ob = (Car) super.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        
        // 2. EXPLICITLY create a new Engine for the clone
        // We take the speed from the current engine and create a NEW Engine object
        ob.ref = new Engine(this.ref.getSpeed());
        
        return ob; // Return the fully independent clone
    }
}
```

**Usage in `Test3` Main:**
```java
public class Test3 {
    public static void main(String[] args) {
        Car c = new Car("Nano");
        Car c1 = (Car) c.clone(); // Deep clone

        // Modifying the Engine of the CLONED car
        c1.getRef().setSpeed(400);

        System.out.println("After Modification");
        System.out.println(c);  // Original Car
        System.out.println(c1); // Cloned Car
    }
}
```

**Output:**
```
[name=Nano ref=Engine [speed=100]]
[name=Nano ref=Engine [speed=100]]
After Modification
[name=Nano ref=Engine [speed=100]]  <-- UNAFFECTED!
[name=Nano ref=Engine [speed=400]]
```

**Analysis:**
- Now, when `c1` changes its engine speed, `c` remains at 100.
- **Reason**: In `clone()`, we created a `new Engine(...)` for the cloned car. `c` and `c1` have their own separate `Engine` objects.

## Summary

| Feature | Shallow Copy | Deep Copy |
| :--- | :--- | :--- |
| **Explanation** | Copies field values directly. References point to same objects. | Recursively copies objects. References point to new copies. |
| **Dependency** | Cloned object depends on original's sub-objects. | Cloned object is fully independent. |
| **Implementation** | `super.clone()` (Default) | Manual coding required (e.g., `new Object()`). |
| **Use Case** | Performance is critical; sub-objects are immutable. | Complete isolation is required (typical case). |
