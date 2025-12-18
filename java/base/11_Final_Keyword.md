# 🔒 The Final Keyword in Java

## Table of Contents
1. [Introduction to Final](#introduction-to-final)
2. [Final Variables](#final-variables)
3. [Final Methods](#final-methods)
4. [Final Classes](#final-classes)
5. [Blank Final Variables](#blank-final-variables)
6. [Final with Reference Types](#final-with-reference-types)
7. [Code Examples](#code-examples)
8. [Interview Questions](#interview-questions)

---

## Introduction to Final

The `final` keyword is used to restrict modification. It can be applied to:

| Applied To | Effect |
|------------|--------|
| **Variable** | Becomes constant (cannot reassign) |
| **Method** | Cannot be overridden |
| **Class** | Cannot be extended (inherited) |

---

## Final Variables

A **final variable** is a constant - once initialized, its value cannot be changed.

### Types:

```java
class Example {
    // Final instance variable
    final int INSTANCE_CONST = 10;
    
    // Final static variable (class constant)
    static final double PI = 3.14159;
    
    void method() {
        // Final local variable
        final int LOCAL_CONST = 100;
        
        // Cannot reassign
        // INSTANCE_CONST = 20;  // ERROR!
        // PI = 3.0;             // ERROR!
        // LOCAL_CONST = 200;    // ERROR!
    }
}
```

### Naming Convention:

Final constants are typically named in `UPPER_SNAKE_CASE`:

```java
static final int MAX_SIZE = 100;
static final String DEFAULT_NAME = "Unknown";
static final double TAX_RATE = 0.18;
```

---

## Final Methods

A **final method** cannot be overridden in subclasses.

```java
class Parent {
    final void display() {
        System.out.println("Cannot override this");
    }
    
    void show() {
        System.out.println("Can override this");
    }
}

class Child extends Parent {
    // void display() { }  // ERROR! Cannot override final method
    
    @Override
    void show() {          // OK - not final
        System.out.println("Overridden show");
    }
}
```

### When to Use Final Methods:

- Prevent tampering with critical logic
- Security-sensitive methods
- Template method pattern (skeleton that shouldn't change)

---

## Final Classes

A **final class** cannot be extended (no inheritance).

```java
final class Utility {
    public static int add(int a, int b) {
        return a + b;
    }
}

// class Extended extends Utility { }  // ERROR! Cannot extend final class
```

### Examples of Final Classes in Java API:

- `java.lang.String`
- `java.lang.Integer`, `java.lang.Double` (all wrapper classes)
- `java.lang.Math`
- `java.lang.System`

### Why Make a Class Final:

- Immutability (like String)
- Security (prevent malicious subclassing)
- Utility classes (only static methods)

---

## Blank Final Variables

A **blank final** variable is declared without initialization. Must be initialized exactly once.

### Instance Blank Final:

```java
class Employee {
    final int id;  // Blank final - no initial value
    
    // Can initialize in constructor
    Employee(int id) {
        this.id = id;  // Must be initialized
    }
    
    // Or in instance block
    // {
    //     id = 0;
    // }
}
```

### Static Blank Final:

```java
class Config {
    static final String DB_URL;  // Blank final static
    
    // Must initialize in static block
    static {
        DB_URL = "jdbc:mysql://localhost:3306/mydb";
    }
}
```

### Initialization Locations:

| Type | Where to Initialize |
|------|---------------------|
| Instance final | Declaration, constructor, or instance block |
| Static final | Declaration or static block |
| Local final | Before first use |

---

## Final with Reference Types

When `final` is applied to a reference variable:
- **Reference cannot be changed** (cannot point to different object)
- **Object contents can still be modified**

```java
final int[] arr = {1, 2, 3};

// Cannot reassign reference
// arr = new int[5];  // ERROR!

// CAN modify contents
arr[0] = 100;  // OK!
arr[1] = 200;  // OK!
```

### Visual Representation:

```
final int[] arr = {1, 2, 3};

Stack:                     Heap:
┌────────────┐            ┌───────────────────┐
│ arr (final)│──────────► │ {1, 2, 3}         │
└────────────┘   Cannot   │  ↓ Can modify     │
       │         change   │ {100, 200, 3}     │
       ▼         arrow    └───────────────────┘
   NOT allowed
   to point to
   new array
```

### Same for Objects:

```java
final StringBuilder sb = new StringBuilder("Hello");

// sb = new StringBuilder("World");  // ERROR! Cannot reassign

sb.append(" World");  // OK! Modifying object's state
System.out.println(sb);  // Hello World
```

---

## Code Examples

### Comprehensive Final Example:

```java
final class Constants {                             // Line 1: Final class
    // Final static variables (constants)
    public static final int MAX_VALUE = 100;        // Line 2
    public static final String APP_NAME = "MyApp";  // Line 3
}

class Sample {                                      // Line 4
    final int instanceFinal = 10;                   // Line 5: Instance final
    final int blankFinal;                           // Line 6: Blank final
    static final double PI;                         // Line 7: Static blank final
    
    static {                                        // Line 8: Initialize static final
        PI = 3.14159;
    }
    
    Sample(int value) {                             // Line 9: Constructor
        blankFinal = value;                         // Line 10: Initialize blank final
    }
    
    final void display() {                          // Line 11: Final method
        System.out.println("Instance: " + instanceFinal);
        System.out.println("Blank: " + blankFinal);
        System.out.println("PI: " + PI);
    }
}

class Child extends Sample {                        // Line 12
    Child(int value) {
        super(value);
    }
    
    // Cannot override final method
    // void display() { }  // ERROR!
}

public class FinalDemo {                            // Line 13
    public static void main(String[] args) {        // Line 14
        // Cannot extend final class
        // class Extended extends Constants { }  // ERROR!
        
        Sample s = new Sample(50);
        s.display();
        
        // Access static final
        System.out.println("Max: " + Constants.MAX_VALUE);
        
        // Final with array
        final int[] nums = {1, 2, 3};
        nums[0] = 10;  // OK - modify content
        // nums = new int[5];  // ERROR - cannot reassign
    }
}
```

---

## Interview Questions

### Q1: What is the final keyword?
**Answer**: `final` is used to restrict modification. For variables (constant), methods (no override), classes (no inheritance).

### Q2: What is a blank final variable?
**Answer**: A final variable declared without initialization. Must be initialized exactly once before use.

### Q3: Can final variable be changed?
**Answer**: The variable itself cannot be reassigned. But if it's a reference to an object, the object's contents can be modified.

### Q4: Why is String class final?
**Answer**: For immutability and security. If String could be extended, subclass might override methods and compromise security.

### Q5: Can we initialize blank final in method?
**Answer**: No. Instance blank finals must be initialized in constructor or instance block. Static blank finals in static block.

### Q6: What is the difference between final and finally?
**Answer**: `final` is for constants/prevent override/prevent inheritance. `finally` is a block that always executes after try-catch.

### Q7: Can constructor be final?
**Answer**: No. Constructors cannot be inherited, so `final` has no meaning for them.

### Q8: Can abstract method be final?
**Answer**: No. Abstract methods must be overridden, which conflicts with final's purpose.

---

## Quick Reference

```java
// Final variable (constant)
final int VALUE = 100;

// Final method (cannot override)
final void method() { }

// Final class (cannot extend)
final class MyClass { }

// Blank final (initialize later)
final int x;
x = 10;  // Must initialize before use

// Final with reference
final int[] arr = {1, 2, 3};
arr[0] = 10;      // OK - content change
// arr = new int[5];  // ERROR - reference change
```

---

*Previous: [10_Class_Loading.md](./10_Class_Loading.md)*  
*Next: [12_Source_File_Rules.md](./12_Source_File_Rules.md)*
