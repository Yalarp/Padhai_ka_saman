# 🧹 Memory Management and Garbage Collection in Java

## Table of Contents
1. [Object Lifecycle](#object-lifecycle)
2. [Automatic Garbage Collection](#automatic-garbage-collection)
3. [When Objects Become Eligible for GC](#when-objects-become-eligible-for-gc)
4. [The finalize() Method](#the-finalize-method)
5. [System.gc() and Runtime.gc()](#systemgc-and-runtimegc)
6. [Destructor vs finalize()](#destructor-vs-finalize)
7. [Final Keyword](#final-keyword)
8. [Code Examples](#code-examples)
9. [Interview Questions](#interview-questions)

---

## Object Lifecycle

Every Java object goes through these stages:

```mermaid
graph LR
    A[Object Creation] --> B[Object in Use]
    B --> C[Object Unreferenced]
    C --> D[Eligible for GC]
    D --> E[finalize called]
    E --> F[Memory Reclaimed]
```

### Lifecycle Stages:

| Stage | Description |
|-------|-------------|
| **Creation** | `new` allocates memory, constructor runs |
| **In Use** | Object referenced and being used |
| **Unreferenced** | No reference pointing to object |
| **GC Eligible** | Can be garbage collected |
| **Finalization** | finalize() method called |
| **Reclaimed** | Memory freed |

---

## Automatic Garbage Collection

**Garbage Collection (GC)** is the process of automatically reclaiming memory by deleting objects that are no longer in use.

### Definition:
> Automatic garbage collection is the process of looking at heap memory, identifying which objects are in use and which are not, and deleting the unused objects.

### Key Concepts:

| Term | Definition |
|------|------------|
| **Referenced Object** | Some part of program still has a reference to it |
| **Unreferenced Object** | No reference in program points to it |
| **Garbage** | Unreferenced objects |
| **Garbage Collector** | JVM component that reclaims garbage |

### How GC Works:

```
┌─────────────────────────────────────────────────────────────────┐
│                          HEAP MEMORY                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   Referenced Objects (IN USE):                                   │
│   ┌──────┐  ┌──────┐  ┌──────┐                                  │
│   │ Obj1 │◄-│ ref1 │  │ Obj3 │◄───── ref3                       │
│   └──────┘  └──────┘  └──────┘                                  │
│        Stack reference                                           │
│                                                                  │
│   Unreferenced Objects (GARBAGE):                                │
│   ┌──────┐  ┌──────┐                                            │
│   │ Obj2 │  │ Obj4 │  ← No references! Will be collected        │
│   └──────┘  └──────┘                                            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Advantages of Automatic GC:

1. **No manual memory management** - Unlike C/C++
2. **Prevents memory leaks** - Unused memory automatically freed
3. **Prevents dangling pointers** - No explicit deallocation
4. **Developer productivity** - Focus on business logic

---

## When Objects Become Eligible for GC

An object becomes eligible for garbage collection when it's no longer reachable.

### Case 1: Setting Reference to null

```java
MyClass obj = new MyClass();  // Object created
obj = null;                    // Object now unreferenced - eligible for GC
```

### Case 2: Reassigning Reference

```java
MyClass obj = new MyClass();  // Object 1 created
obj = new MyClass();          // Object 1 unreferenced (eligible for GC)
                              // obj now points to Object 2
```

### Case 3: Object Created Inside Method

```java
void myMethod() {
    MyClass obj = new MyClass();  // Object created
    obj.doSomething();
}  // Method ends - obj goes out of scope - Object eligible for GC
```

### Case 4: Island of Isolation

```java
class Node {
    Node next;
}

Node a = new Node();
Node b = new Node();
a.next = b;
b.next = a;  // Circular reference

a = null;
b = null;
// Both objects still reference each other
// But no external reference - BOTH eligible for GC
```

---

## The finalize() Method

`finalize()` is called by the Garbage Collector just before an object is destroyed.

### Syntax:
```java
protected void finalize() throws Throwable {
    // Cleanup code
}
```

### Purpose:
- Release resources (files, connections, sockets)
- Perform cleanup before object destruction

### Example:

```java
class Resource {
    private String name;
    
    Resource(String name) {
        this.name = name;
        System.out.println("Resource " + name + " created");
    }
    
    @Override
    protected void finalize() throws Throwable {
        System.out.println("Resource " + name + " being finalized");
        // Release any resources here
        super.finalize();
    }
}

public class FinalizeDemo {
    public static void main(String[] args) {
        Resource r1 = new Resource("R1");
        r1 = null;  // Eligible for GC
        
        // Request GC (not guaranteed)
        System.gc();
        
        System.out.println("End of main");
    }
}
```

### Possible Output:
```
Resource R1 created
End of main
Resource R1 being finalized
```

> ⚠️ **Warning**: There's NO GUARANTEE when or if finalize() will be called!

---

## System.gc() and Runtime.gc()

You can REQUEST garbage collection, but cannot force it.

### Two Ways to Request GC:

```java
// Method 1: Using System class
System.gc();

// Method 2: Using Runtime class
Runtime.getRuntime().gc();
```

### Key Points:
- Both are just **requests** to JVM
- JVM may or may not run GC immediately
- GC runs on its own schedule
- Cannot be forced

### Example:

```java
public class GCDemo {
    @Override
    protected void finalize() {
        System.out.println("finalize called for " + this);
    }
    
    public static void main(String[] args) {
        GCDemo obj1 = new GCDemo();
        GCDemo obj2 = new GCDemo();
        
        obj1 = null;
        obj2 = null;
        
        // Request garbage collection
        System.gc();  // Or: Runtime.getRuntime().gc();
        
        System.out.println("GC requested");
        
        // Give GC thread time to run
        try { Thread.sleep(1000); } catch (Exception e) {}
    }
}
```

---

## Destructor vs finalize()

### C++ Destructor:
- Called automatically when object goes out of scope
- Guaranteed to be called
- Called immediately when object is destroyed
- Used for resource cleanup

### Java finalize():
- Called by Garbage Collector (maybe)
- NOT guaranteed to be called
- Timing is uncertain
- Cannot rely on it for critical cleanup

### Comparison:

| Aspect | C++ Destructor | Java finalize() |
|--------|----------------|-----------------|
| Called by | Runtime (deterministic) | GC (non-deterministic) |
| Timing | Immediate | Uncertain |
| Guaranteed | Yes | No |
| Syntax | ~ClassName() | finalize() |
| Reliability | High | Low |

### Best Practice in Java:

Since finalize() is unreliable, use **try-with-resources** for cleanup:

```java
// Modern Java approach for resource cleanup
try (FileInputStream fis = new FileInputStream("file.txt")) {
    // Use the resource
} // Automatically closed here, even if exception occurs
```

---

## Final Keyword

The `final` keyword can be applied to:

### 1. Final Variables (Constants)

```java
public class FinalDemo {
    final int INSTANCE_CONST = 10;       // Instance constant
    static final int CLASS_CONST = 20;   // Class constant
    
    public void method() {
        final int LOCAL_CONST = 30;      // Local constant
        
        // INSTANCE_CONST = 100;  // ERROR! Cannot reassign
        // CLASS_CONST = 200;     // ERROR!
        // LOCAL_CONST = 300;     // ERROR!
    }
}
```

### 2. Final Methods (Cannot be Overridden)

```java
class Parent {
    final void display() {
        System.out.println("Cannot override this");
    }
}

class Child extends Parent {
    // void display() { }  // ERROR! Cannot override final method
}
```

### 3. Final Classes (Cannot be Extended)

```java
final class FinalClass {
    // Class content
}

// class SubClass extends FinalClass { }  // ERROR! Cannot extend final class
```

### Final Variable Initialization:

Final instance variables can be initialized in:
1. At declaration
2. In constructor
3. In instance initializer block

```java
class FinalInit {
    final int a = 10;           // At declaration
    final int b;                // Blank final
    final int c;                // Blank final
    
    {
        c = 30;                 // In instance block
    }
    
    FinalInit() {
        b = 20;                 // In constructor
    }
    
    FinalInit(int value) {
        b = value;              // Different value per object
    }
}
```

---

## Code Examples

### Complete Object Lifecycle Demo:

```java
main() {
    MyClass m1 = new MyClass();  // Steps:
        // a) instance member/s will be allocated memory
        // b) default constructor will be called

}  // What happens here?
    // a) scope of m1 gets over
    // b) m1 gets removed from stack
    // c) object to which m1 was referring becomes unreferred
    //    so that object is going to be garbage collected.
    // d) just before the object gets garbage collected, 
    //    "finalize" method gets called.
```

### Why Not Rely on finalize():

```java
class DatabaseConnection {
    private Connection conn;
    
    DatabaseConnection() {
        // Open connection
        conn = DriverManager.getConnection(url);
    }
    
    // BAD: Don't rely on finalize for closing connection
    protected void finalize() {
        conn.close();  // May never be called!
    }
    
    // GOOD: Provide explicit close method
    public void close() {
        if (conn != null) {
            conn.close();
        }
    }
}

// Usage
DatabaseConnection db = new DatabaseConnection();
try {
    // Use connection
} finally {
    db.close();  // Always called, guaranteed
}
```

---

## Interview Questions

### Q1: What is Garbage Collection?
**Answer**: GC is the automatic process of reclaiming memory by identifying and deleting objects that are no longer referenced by any part of the program.

### Q2: Can we force Garbage Collection?
**Answer**: No. We can only request GC using `System.gc()` or `Runtime.getRuntime().gc()`. JVM decides when to actually run GC.

### Q3: What is the purpose of finalize() method?
**Answer**: finalize() is called by GC before destroying an object. It's used to perform cleanup operations like closing resources.

### Q4: Is finalize() guaranteed to be called?
**Answer**: No. There's no guarantee that finalize() will ever be called. Don't rely on it for critical cleanup.

### Q5: What is the difference between final, finally, and finalize?
**Answer**:
- **final**: Keyword for constants, prevent override/inheritance
- **finally**: Block that always executes after try-catch
- **finalize**: Method called by GC before object destruction

### Q6: When is an object eligible for GC?
**Answer**: When no live thread can access the object (no references pointing to it).

### Q7: What is a memory leak in Java?
**Answer**: When objects that are no longer needed still have references to them, preventing GC from reclaiming the memory.

### Q8: Can final variable be initialized in constructor?
**Answer**: Yes. Blank final variables must be initialized either at declaration, in constructor, or in instance initializer block.

### Q9: What is the difference between destructors and finalize()?
**Answer**: Destructors (C++) are deterministic and guaranteed. finalize() (Java) is non-deterministic and not guaranteed.

### Q10: How many times does finalize() get called?
**Answer**: At most once per object. If object is resurrected in finalize(), it won't be called again.

---

## Quick Reference

### Memory Management Summary

```java
// Object creation
MyClass obj = new MyClass();

// Make eligible for GC
obj = null;                    // Method 1: Nullify
obj = new MyClass();          // Method 2: Reassign

// Request GC
System.gc();
Runtime.getRuntime().gc();

// Cleanup (deprecated approach)
protected void finalize() {
    // Don't use for important cleanup
}

// Modern cleanup (recommended)
try (Resource r = new Resource()) {
    // Use resource
}  // Automatically cleaned up
```

### Final Keyword Usage

```java
// Final variable - constant
final int MAX = 100;

// Final method - cannot override
final void method() { }

// Final class - cannot extend
final class MyClass { }

// Final parameter - cannot reassign
void method(final int x) { }
```

---

*Previous: [08_Static_Members.md](./08_Static_Members.md)*  
*Next: [10_Class_Loading.md](./10_Class_Loading.md)*
