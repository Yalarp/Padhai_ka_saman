# C++ Delegating Constructors (C++11)

## Table of Contents
- [Introduction](#introduction)
- [Syntax](#syntax)
- [Benefits](#benefits)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

Delegating constructors allow one constructor to call another constructor of the same class, reducing code duplication in constructor initialization.

> **Definition**: A constructor that delegates to another constructor in the same class.

---

## Syntax

```cpp
class MyClass {
public:
    MyClass(int a, int b) {
        // Main initialization logic
    }
    
    MyClass(int a) : MyClass(a, 0) {  // Delegates to above
    }
    
    MyClass() : MyClass(0, 0) {       // Delegates to above
    }
};
```

The delegation happens in the member initializer list.

---

## Benefits

| Benefit | Description |
|---------|-------------|
| **No duplication** | Common code in one constructor |
| **Easier maintenance** | Single point of change |
| **Less error-prone** | Consistency across constructors |
| **Cleaner code** | Simpler constructor bodies |

---

## Code Examples

### Example 1: Basic Delegating Constructor

```cpp
#include<iostream>
using namespace std;

class Rectangle {
    int width, height;
public:
    // Target (main) constructor
    Rectangle(int w, int h) : width{w}, height{h} {
        cout << "Main constructor: " << w << "x" << h << endl;
    }
    
    // Delegating constructors
    Rectangle(int size) : Rectangle(size, size) {  // Square
        cout << "Square constructor" << endl;
    }
    
    Rectangle() : Rectangle(1, 1) {  // Unit square
        cout << "Default constructor" << endl;
    }
    
    int area() { return width * height; }
};

int main() {
    Rectangle r1(10, 20);  // Main constructor
    Rectangle r2(5);        // Delegates to main
    Rectangle r3;           // Delegates to main
    
    cout << "Areas: " << r1.area() << ", " 
         << r2.area() << ", " << r3.area() << endl;
    return 0;
}
```

### Output:
```
Main constructor: 10x20
Main constructor: 5x5
Square constructor
Main constructor: 1x1
Default constructor
Areas: 200, 25, 1
```

### Example 2: Complex Initialization

```cpp
#include<iostream>
#include<string>
using namespace std;

class Person {
    string name;
    int age;
    string email;
    
    void validate() {
        if (age < 0) age = 0;
        if (name.empty()) name = "Unknown";
    }
    
public:
    Person(string n, int a, string e) 
        : name{n}, age{a}, email{e} {
        validate();
        cout << "Full constructor" << endl;
    }
    
    Person(string n, int a) 
        : Person(n, a, "none@email.com") {
        cout << "Name+Age constructor" << endl;
    }
    
    Person(string n) 
        : Person(n, 0) {
        cout << "Name-only constructor" << endl;
    }
    
    void display() {
        cout << name << ", " << age << ", " << email << endl;
    }
};

int main() {
    Person p1("Alice", 25, "alice@mail.com");
    Person p2("Bob", 30);
    Person p3("Charlie");
    
    p1.display();
    p2.display();
    p3.display();
    return 0;
}
```

---

## Key Points

1. **Syntax**: Use initializer list with another constructor.
2. **Single Delegation**: Only one constructor call allowed.
3. **Execution Order**: Target constructor runs first.
4. **No Member Init**: Can't mix delegation with member init.
5. **Reduces Duplication**: Common code in one place.

---

## Interview Questions

### Q1: What is a delegating constructor?
**Answer**: A delegating constructor is a constructor that calls another constructor of the same class to perform initialization, reducing code duplication.

### Q2: Can you have member initializers with delegation?
**Answer**: No. If a constructor delegates, it cannot have other member initializers in the same initializer list.

### Q3: What runs first in delegation?
**Answer**: The target (delegated-to) constructor runs first, then the delegating constructor's body executes.
