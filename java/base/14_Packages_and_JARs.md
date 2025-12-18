# 📦 Packages and JAR Files in Java

## Table of Contents
1. [What is a Package?](#what-is-a-package)
2. [Types of Packages](#types-of-packages)
3. [Creating User-Defined Packages](#creating-user-defined-packages)
4. [Import Statement](#import-statement)
5. [Access Modifiers with Packages](#access-modifiers-with-packages)
6. [JAR Files](#jar-files)
7. [CLASSPATH](#classpath)
8. [Code Examples](#code-examples)
9. [Interview Questions](#interview-questions)

---

## What is a Package?

A **package** is a collection of related classes and interfaces bundled together.

### Purpose of Packages:
1. **Avoid naming conflicts** - Same class name in different packages
2. **Organize code** - Group related classes logically
3. **Access protection** - Control visibility of classes
4. **Reusability** - Share code across projects

### Analogy:
```
Package = Folder/Directory
Class = File inside folder

Example:
java/
├── lang/          ← Package
│   ├── String.java
│   ├── Object.java
│   └── Math.java
├── util/          ← Package
│   ├── ArrayList.java
│   └── HashMap.java
└── io/            ← Package
    ├── File.java
    └── InputStream.java
```

---

## Types of Packages

### 1. Built-in Packages (Java API)

| Package | Description |
|---------|-------------|
| `java.lang` | Core classes (String, Math, Object) - Auto-imported |
| `java.util` | Utility classes (ArrayList, HashMap, Date) |
| `java.io` | Input/Output classes (File, InputStream) |
| `java.net` | Networking classes (Socket, URL) |
| `java.awt` | GUI components (Window, Button) |
| `javax.swing` | Advanced GUI (JFrame, JButton) |
| `java.sql` | Database connectivity (Connection, Statement) |

### 2. User-Defined Packages

Custom packages created by developers.

```java
package com.company.project.module;
```

### Package Naming Convention:

```
Reverse domain name + project structure

Example:
Company: oracle.com
Project: banking
Module: accounts

Package: com.oracle.banking.accounts
```

---

## Creating User-Defined Packages

### Step 1: Declare Package

```java
// File: MyClass.java
package mypackage;    // MUST be first statement

public class MyClass {
    public void display() {
        System.out.println("Hello from mypackage");
    }
}
```

### Step 2: Compile with Package Structure

```batch
# Create directory structure
mkdir mypackage

# Compile (class file goes into package folder)
javac -d . MyClass.java

# This creates: mypackage/MyClass.class
```

### Step 3: Use the Package

```java
// File: Test.java
import mypackage.MyClass;

public class Test {
    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.display();
    }
}
```

### Directory Structure:

```
project/
├── mypackage/
│   └── MyClass.class
└── Test.java
```

---

## Import Statement

The `import` statement allows using classes from other packages without fully qualified names.

### Types of Import:

```java
// Import specific class
import java.util.ArrayList;

// Import all classes from a package
import java.util.*;

// Static import (import static members)
import static java.lang.Math.PI;
import static java.lang.Math.*;
```

### With vs Without Import:

```java
// WITHOUT import - use fully qualified name
java.util.ArrayList<String> list = new java.util.ArrayList<>();

// WITH import
import java.util.ArrayList;
ArrayList<String> list = new ArrayList<>();
```

### Important Notes:

1. **import is for compiler** - No runtime overhead
2. **java.lang is auto-imported** - No need to import String, Object, etc.
3. **Subpackages not included** - `import java.util.*` doesn't import `java.util.concurrent.*`

---

## Access Modifiers with Packages

| Modifier | Same Class | Same Package | Subclass (Different Package) | Different Package |
|----------|------------|--------------|------------------------------|-------------------|
| `public` | ✓ | ✓ | ✓ | ✓ |
| `protected` | ✓ | ✓ | ✓ | ✗ |
| `default` (no modifier) | ✓ | ✓ | ✗ | ✗ |
| `private` | ✓ | ✗ | ✗ | ✗ |

### Example:

```java
// Package: pkg1
package pkg1;

public class A {
    public int pub = 1;
    protected int pro = 2;
    int def = 3;          // default
    private int pri = 4;
}

// Package: pkg2
package pkg2;
import pkg1.A;

public class B extends A {
    void test() {
        System.out.println(pub);  // ✓ public accessible
        System.out.println(pro);  // ✓ protected - subclass
        // System.out.println(def); // ✗ default - different package
        // System.out.println(pri); // ✗ private - only in same class
    }
}
```

---

## JAR Files

**JAR (Java Archive)** is a compressed file format for distributing Java classes.

### Characteristics:
- Uses ZIP compression
- Can reduce size up to 50%
- Contains `.class` files, metadata, resources
- Can be executable (with Main-Class manifest)

### Common JAR Commands:

| Command | Description |
|---------|-------------|
| `jar cf file.jar files` | Create JAR |
| `jar tf file.jar` | List contents |
| `jar xf file.jar` | Extract JAR |
| `jar uf file.jar files` | Update JAR |

### Creating a JAR:

```batch
# Create JAR from class files
jar cf mylib.jar *.class

# Create JAR from package
jar cf mylib.jar com/

# Create JAR with verbose output
jar cvf mylib.jar *.class

# View JAR contents
jar tf mylib.jar
```

### JAR Options:

| Option | Description |
|--------|-------------|
| `c` | Create new archive |
| `t` | List table of contents |
| `x` | Extract from archive |
| `u` | Update existing archive |
| `v` | Verbose output |
| `f` | Specify archive filename |
| `m` | Include manifest file |

### Using a JAR:

```batch
# Run class inside JAR
java -cp mylib.jar com.example.MainClass

# Add JAR to classpath for compilation
javac -cp mylib.jar MyProgram.java

# Run with JAR in classpath
java -cp .;mylib.jar MyProgram
```

---

## CLASSPATH

**CLASSPATH** tells JVM where to find class files.

### Setting CLASSPATH:

```batch
# Windows
set CLASSPATH=.;C:\libs\mylib.jar;C:\projects\classes

# Linux/Mac
export CLASSPATH=.:/libs/mylib.jar:/projects/classes

# Using -cp flag (recommended)
java -cp .;libs/mylib.jar MainClass
```

### CLASSPATH Components:
- `.` - Current directory
- Directory paths - For .class files
- JAR file paths - For packaged classes

---

## Code Examples

### Package Creation Example:

```java
// File: pack/Calculator.java
package pack;                                       // Line 1: Package declaration

public class Calculator {                           // Line 2: Public class
    
    public int add(int a, int b) {                  // Line 3: Public method
        return a + b;
    }
    
    public int subtract(int a, int b) {             // Line 4
        return a - b;
    }
    
    int multiply(int a, int b) {                    // Line 5: Default access
        return a * b;                               // Only accessible in same package
    }
}
```

### Using Package:

```java
// File: TestCalculator.java
import pack.Calculator;                             // Line 1: Import class

public class TestCalculator {                       // Line 2
    public static void main(String[] args) {        // Line 3
        
        Calculator calc = new Calculator();         // Line 4: Create object
        
        System.out.println(calc.add(5, 3));         // Line 5: 8
        System.out.println(calc.subtract(10, 4));   // Line 6: 6
        
        // calc.multiply(2, 3);  // ERROR! default access
    }
}
```

### Static Import Example:

```java
// Without static import
double area = Math.PI * Math.pow(radius, 2);

// With static import
import static java.lang.Math.PI;
import static java.lang.Math.pow;

double area = PI * pow(radius, 2);
```

---

## Interview Questions

### Q1: What is a package?
**Answer**: A package is a namespace that organizes related classes and interfaces. It helps avoid naming conflicts and provides access control.

### Q2: What is the difference between import and package?
**Answer**:
- `package`: Declares which package the current class belongs to
- `import`: Allows using classes from other packages

### Q3: What is java.lang package?
**Answer**: java.lang is the default package auto-imported in every Java program. It contains core classes like String, Object, Math, System.

### Q4: Can we import same class from two packages?
**Answer**: No. It causes compilation error. You must use fully qualified names for at least one of them.

### Q5: What is a JAR file?
**Answer**: JAR (Java Archive) is a compressed file format that bundles multiple Java class files and resources into one file for distribution.

### Q6: What is CLASSPATH?
**Answer**: CLASSPATH is an environment variable that tells JVM where to find class files and libraries.

### Q7: What is static import?
**Answer**: Static import allows using static members (fields, methods) of a class directly without class name prefix.

### Q8: Can a class exist without a package?
**Answer**: Yes. It goes into the "default package" (unnamed package). Not recommended for production code.

### Q9: What is the naming convention for packages?
**Answer**: Reverse domain name followed by project structure. Example: `com.company.project.module`

### Q10: Does import affect performance?
**Answer**: No. Import is only for compiler convenience. It has no runtime overhead.

---

## Quick Reference

### Package Commands

```batch
# Compile with package
javac -d . MyClass.java

# Run packaged class
java mypackage.MyClass

# Create JAR
jar cf lib.jar mypackage/

# Run with JAR
java -cp lib.jar mypackage.MyClass
```

### Import Syntax

```java
// Single class
import java.util.ArrayList;

// All classes
import java.util.*;

// Static import
import static java.lang.Math.PI;
import static java.lang.Math.*;
```

---

*Previous: [13_Arrays.md](./13_Arrays.md)*  
*Next: [15_Inheritance_Basics.md](./15_Inheritance_Basics.md)*
