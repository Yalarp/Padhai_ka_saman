# 📜 Source File Rules in Java

## Table of Contents
1. [Basic Source File Structure](#basic-source-file-structure)
2. [Public Class Rules](#public-class-rules)
3. [Multiple Classes in One File](#multiple-classes-in-one-file)
4. [Compilation Rules](#compilation-rules)
5. [Package and Import Order](#package-and-import-order)
6. [Code Examples](#code-examples)
7. [Interview Questions](#interview-questions)

---

## Basic Source File Structure

A Java source file has the following structure:

```java
// 1. Package declaration (optional, must be first)
package com.example.myapp;

// 2. Import statements (optional, after package)
import java.util.ArrayList;
import java.util.List;

// 3. Class/Interface declarations
public class MyClass {
    // Class content
}
```

### Order Rules:

```
1. Package statement (0 or 1, must be first)
         ↓
2. Import statements (0 or more)
         ↓
3. Class/Interface declarations (1 or more)
```

---

## Public Class Rules

### Rule 1: At Most ONE Public Class

A source file can have **at most one public class**.

```java
// File: MyFile.java

public class MyClass { }    // OK - public class

class Helper { }            // OK - non-public class

// public class Another { } // ERROR! Only one public class allowed
```

### Rule 2: Public Class Name = File Name

If there is a public class, the **file name must match** the public class name.

```java
// File MUST be named: Calculator.java

public class Calculator {   // Public class name determines filename
    // content
}

class Helper {              // Non-public - name doesn't affect filename
    // content
}
```

### Rule 3: No Public Class = Any Name

If no public class exists, file name can be anything.

```java
// File can be named: AnyName.java

class ClassA {              // No public class
    // content
}

class ClassB {
    // content
}
```

---

## Multiple Classes in One File

### Valid Scenarios:

```java
// File: Main.java

public class Main {         // Public class (matches filename)
    public static void main(String[] args) {
        Helper h = new Helper();
        Utility u = new Utility();
    }
}

class Helper {              // Non-public class
    void help() { }
}

class Utility {             // Another non-public class
    void doSomething() { }
}
```

### After Compilation:

```
Main.java  ──compile──►  Main.class
                         Helper.class
                         Utility.class
```

Each class gets its **own .class file**, regardless of source file structure.

---

## Compilation Rules

### Compiling Single File:

```batch
javac MyClass.java
# Creates: MyClass.class (and any other class .class files in same file)
```

### Compiling Multiple Files:

```batch
javac *.java
# Compiles all .java files in current directory
```

### Running the Program:

```batch
java MyClass
# Runs the class containing main() method
# Don't include .class extension!
```

### Common Scenarios:

| Scenario | Command | Output Files |
|----------|---------|--------------|
| Single class file | `javac Main.java` | Main.class |
| Multiple classes in one file | `javac Main.java` | Main.class, Helper.class, ... |
| Multiple source files | `javac *.java` | All corresponding .class files |

---

## Package and Import Order

### Order Requirements:

```java
// MUST be in this order:

package com.example;        // 1. Package (first, optional)

import java.util.List;      // 2. Imports (after package)
import java.util.ArrayList;

public class MyClass {      // 3. Classes (after imports)
    // content
}
```

### Invalid Orders:

```java
import java.util.List;      // ERROR! Import before package
package com.example;

// ---

public class MyClass { }    // ERROR! Class before package
package com.example;

// ---

public class MyClass { }    // ERROR! Class before import
import java.util.List;
```

---

## Code Examples

### Example 1: Single Public Class

```java
// File: Calculator.java

package com.math;

import java.util.Scanner;

public class Calculator {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("Enter number: ");
        int num = sc.nextInt();
        System.out.println("Square: " + (num * num));
    }
}
```

### Example 2: Multiple Classes, One Public

```java
// File: BankApp.java

package com.bank;

public class BankApp {                      // Public class = filename
    public static void main(String[] args) {
        Account acc = new Account(1001, 5000);
        acc.deposit(1000);
        acc.display();
    }
}

class Account {                             // Non-public helper class
    private int id;
    private double balance;
    
    Account(int id, double balance) {
        this.id = id;
        this.balance = balance;
    }
    
    void deposit(double amount) {
        balance += amount;
    }
    
    void display() {
        System.out.println("ID: " + id + ", Balance: " + balance);
    }
}
```

### Example 3: No Public Class

```java
// File: Utilities.java (any name works)

class StringHelper {
    static String reverse(String s) {
        return new StringBuilder(s).reverse().toString();
    }
}

class MathHelper {
    static int factorial(int n) {
        if (n <= 1) return 1;
        return n * factorial(n - 1);
    }
}

class Tester {
    public static void main(String[] args) {
        System.out.println(StringHelper.reverse("Hello"));
        System.out.println(MathHelper.factorial(5));
    }
}
```

Run with: `java Tester`

---

## Summary Table

| Rule | Description |
|------|-------------|
| File name | Must match public class name (if public class exists) |
| Public classes | At most 1 per source file |
| Non-public classes | Any number allowed |
| main() method | Can be in any class (public or not) |
| Package statement | Must be first (if present) |
| Import statements | Must come after package, before classes |
| Compilation output | One .class file per class |

---

## Interview Questions

### Q1: Can a source file have multiple public classes?
**Answer**: No. At most one public class per source file.

### Q2: What if public class name doesn't match filename?
**Answer**: Compilation error. Public class name must match filename.

### Q3: Can a file have no public class?
**Answer**: Yes. Then filename can be anything with .java extension.

### Q4: How many .class files are created from one .java file?
**Answer**: One .class file for each class defined in the source file.

### Q5: What is the order of package, import, and class?
**Answer**: Package (first), then imports, then class declarations.

### Q6: Can main() method be in a non-public class?
**Answer**: Yes. You can run any class with main() method using `java ClassName`.

### Q7: What happens if two files define same class name?
**Answer**: Depends on package. Same package = error. Different packages = OK (fully qualified names differ).

---

*Previous: [11_Final_Keyword.md](./11_Final_Keyword.md)*  
*Next: [13_Arrays.md](./13_Arrays.md)*
