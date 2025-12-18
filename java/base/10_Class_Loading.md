# 🔄 Class Loading in Java

## Table of Contents
1. [What is Class Loading?](#what-is-class-loading)
2. [Class Loaders](#class-loaders)
3. [Class Loading Process](#class-loading-process)
4. [The Class Class](#the-class-class)
5. [Reflection Basics](#reflection-basics)
6. [Code Examples](#code-examples)
7. [Interview Questions](#interview-questions)

---

## What is Class Loading?

**Class Loading** is the process of loading `.class` files (bytecode) into JVM memory so that they can be used.

### When Does Class Loading Happen?

Class is loaded when:
1. First object of class is created
2. Static member is accessed
3. Class.forName() is called
4. Child class is loaded (loads parent first)

### Visual Representation:

```
┌─────────────────────────────────────────────────────────────────┐
│                    CLASS LOADING PROCESS                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   .class file     ──────►  Class Loader                         │
│   (bytecode)                    │                                │
│                                 ▼                                │
│                          ┌───────────────┐                      │
│                          │ Verify        │ (Bytecode verification)│
│                          └───────────────┘                      │
│                                 │                                │
│                                 ▼                                │
│                          ┌───────────────┐                      │
│                          │ Prepare       │ (Allocate memory)    │
│                          └───────────────┘                      │
│                                 │                                │
│                                 ▼                                │
│                          ┌───────────────┐                      │
│                          │ Initialize    │ (Static blocks/vars) │
│                          └───────────────┘                      │
│                                 │                                │
│                                 ▼                                │
│                          Method Area (Class Data)                │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Class Loaders

Java uses a hierarchical class loading mechanism.

### Types of Class Loaders:

| Class Loader | Loads | Location |
|--------------|-------|----------|
| **Bootstrap** | Core Java classes | `jre/lib/rt.jar` |
| **Extension** | Extension classes | `jre/lib/ext/` |
| **Application** | Application classes | Classpath |

### Hierarchy:

```
         ┌───────────────────┐
         │ Bootstrap Loader  │  (Native, loads rt.jar)
         └─────────┬─────────┘
                   │
         ┌─────────▼─────────┐
         │ Extension Loader  │  (Loads ext/*.jar)
         └─────────┬─────────┘
                   │
         ┌─────────▼─────────┐
         │ Application Loader│  (Loads classpath)
         └─────────┬─────────┘
                   │
                   ▼
           Your Classes
```

### Delegation Model:

When loading a class, class loader:
1. Checks if already loaded
2. Delegates to parent first
3. Loads itself if parent can't

---

## Class Loading Process

### Three Phases:

### 1. Loading
- Read `.class` file from disk
- Create Class object in memory

### 2. Linking
- **Verify**: Check bytecode validity
- **Prepare**: Allocate memory for static variables (default values)
- **Resolve**: Replace symbolic references with actual references

### 3. Initialization
- Execute static blocks
- Initialize static variables with assigned values

### Example:

```java
class MyClass {
    static int value = 10;  // Assigned in Initialization phase
    
    static {
        System.out.println("Class loaded!");  // Executes in Initialization
    }
}

// When does loading happen?
MyClass obj = new MyClass();  // Loading triggered
MyClass.value = 20;           // Also triggers loading if not loaded
Class.forName("MyClass");     // Explicitly triggers loading
```

---

## The Class Class

Every loaded class is represented by a `java.lang.Class` object.

### Getting Class Object:

```java
// Method 1: Using .class literal
Class<?> c1 = String.class;

// Method 2: Using getClass() on object
String s = "Hello";
Class<?> c2 = s.getClass();

// Method 3: Using Class.forName()
Class<?> c3 = Class.forName("java.lang.String");

// All three return same Class object
System.out.println(c1 == c2);  // true
System.out.println(c2 == c3);  // true
```

### Common Class Methods:

| Method | Description |
|--------|-------------|
| `getName()` | Fully qualified class name |
| `getSimpleName()` | Simple class name |
| `getSuperclass()` | Parent class |
| `getInterfaces()` | Implemented interfaces |
| `getMethods()` | All public methods |
| `getDeclaredMethods()` | All declared methods |
| `newInstance()` | Create new object (deprecated) |

---

## Reflection Basics

**Reflection** allows examining and modifying runtime behavior of classes.

### Example:

```java
import java.lang.reflect.*;

class Person {
    private String name;
    
    public void setName(String name) {
        this.name = name;
    }
    
    public String getName() {
        return name;
    }
}

public class ReflectionDemo {
    public static void main(String[] args) throws Exception {
        // Get Class object
        Class<?> clazz = Person.class;
        
        // Get class name
        System.out.println("Class: " + clazz.getName());
        
        // Get methods
        Method[] methods = clazz.getMethods();
        for (Method m : methods) {
            System.out.println("Method: " + m.getName());
        }
        
        // Create instance using reflection
        Object obj = clazz.getDeclaredConstructor().newInstance();
        
        // Call method using reflection
        Method setName = clazz.getMethod("setName", String.class);
        setName.invoke(obj, "John");
        
        Method getName = clazz.getMethod("getName");
        String name = (String) getName.invoke(obj);
        System.out.println("Name: " + name);
    }
}
```

---

## Code Examples

### Class Loading Demo:

```java
class Sample {
    static {
        System.out.println("Sample class loaded");
    }
    
    Sample() {
        System.out.println("Sample constructor");
    }
}

public class ClassLoadingDemo {
    public static void main(String[] args) throws Exception {
        System.out.println("Main started");
        
        // Class.forName loads class (runs static block)
        Class<?> c = Class.forName("Sample");
        System.out.println("Class object: " + c.getName());
        
        // Create instance
        Sample s = new Sample();
        
        System.out.println("Main ended");
    }
}
```

### Output:
```
Main started
Sample class loaded
Class object: Sample
Sample constructor
Main ended
```

---

## Interview Questions

### Q1: What is class loading?
**Answer**: Process of loading .class files into JVM memory for use.

### Q2: When is a class loaded?
**Answer**: When first object created, static member accessed, Class.forName() called, or child class loaded.

### Q3: What are the types of class loaders?
**Answer**: Bootstrap (core), Extension (ext), Application (classpath).

### Q4: What is the delegation model?
**Answer**: Class loader delegates to parent first, loads itself only if parent can't.

### Q5: What is the Class class?
**Answer**: Every loaded class is represented by a java.lang.Class object.

### Q6: What is reflection?
**Answer**: Ability to examine and modify runtime behavior of classes, methods, and fields.

### Q7: How to get Class object?
**Answer**: Using `.class`, `getClass()`, or `Class.forName()`.

---

*Previous: [09_Memory_Management.md](./09_Memory_Management.md)*  
*Next: [11_Final_Keyword.md](./11_Final_Keyword.md)*
