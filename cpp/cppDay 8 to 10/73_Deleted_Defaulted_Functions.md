# C++ Deleted and Defaulted Functions (C++11)

## Table of Contents
- [Introduction](#introduction)
- [= default](#-default)
- [= delete](#-delete)
- [Use Cases](#use-cases)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

C++11 introduced `= default` and `= delete` to explicitly control special member function generation.

| Syntax | Purpose |
|--------|---------|
| `= default` | Request compiler-generated default implementation |
| `= delete` | Prevent function from being used |

---

## = default

Explicitly requests the compiler to generate the default implementation:

```cpp
class MyClass {
public:
    MyClass() = default;                    // Default constructor
    MyClass(const MyClass&) = default;      // Copy constructor
    MyClass& operator=(const MyClass&) = default; // Copy assignment
    ~MyClass() = default;                   // Destructor
};
```

### When to Use:
- After defining other constructors (default constructor is suppressed)
- To make intent explicit
- When you want compiler-generated default behavior

---

## = delete

Prevents a function from being called:

```cpp
class NonCopyable {
public:
    NonCopyable() = default;
    NonCopyable(const NonCopyable&) = delete;            // No copying
    NonCopyable& operator=(const NonCopyable&) = delete; // No assignment
};
```

### Common Uses:
- Prevent copying (singletons, unique resources)
- Prevent specific overloads
- Prevent implicit conversions

---

## Use Cases

### 1. Non-Copyable Class
```cpp
class Unique {
public:
    Unique() = default;
    Unique(const Unique&) = delete;
    Unique& operator=(const Unique&) = delete;
};

Unique a;
// Unique b = a;  // Error: deleted function
```

### 2. Prevent Implicit Conversions
```cpp
void process(int x) { }
void process(double) = delete;  // Prevent double overload

process(10);    // OK
// process(3.14); // Error: deleted
```

### 3. Restore Default Constructor
```cpp
class MyClass {
public:
    MyClass(int x) { }     // Suppresses default constructor
    MyClass() = default;   // Restore it
};
```

---

## Code Examples

### Example 1: Non-Copyable Singleton

```cpp
#include<iostream>
using namespace std;

class Singleton {
private:
    static Singleton* instance;
    Singleton() { cout << "Created" << endl; }

public:
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;
    
    static Singleton* getInstance() {
        if (!instance) instance = new Singleton();
        return instance;
    }
    
    void doSomething() { cout << "Working..." << endl; }
};

Singleton* Singleton::instance = nullptr;

int main() {
    Singleton* s1 = Singleton::getInstance();
    s1->doSomething();
    
    // Singleton s2 = *s1;  // Error: copy constructor deleted
    
    return 0;
}
```

### Example 2: Restored Default Constructor

```cpp
#include<iostream>
using namespace std;

class Person {
    string name;
    int age;
public:
    Person() = default;  // Explicitly request default
    
    Person(string n, int a) : name(n), age(a) { }
    
    void display() {
        cout << name << ", " << age << endl;
    }
};

int main() {
    Person p1;                    // Uses default constructor
    Person p2("Alice", 25);       // Uses parameterized
    
    p2.display();
    return 0;
}
```

### Example 3: Preventing Overloads

```cpp
#include<iostream>
using namespace std;

class IntProcessor {
public:
    void process(int x) {
        cout << "Processing int: " << x << endl;
    }
    
    void process(double) = delete;   // Prevent double
    void process(char) = delete;     // Prevent char
};

int main() {
    IntProcessor ip;
    ip.process(10);      // OK
    // ip.process(3.14); // Error: deleted
    // ip.process('A');  // Error: deleted
    
    return 0;
}
```

---

## Key Points

1. **= default**: Compiler generates default implementation.
2. **= delete**: Function cannot be called.
3. **Rule of Five**: If you delete copy, consider deleting assignment too.
4. **Clear Intent**: Makes code more self-documenting.
5. **Compile-Time**: Errors caught at compile time, not runtime.
6. **Any Function**: `= delete` can be used on any function, not just special members.

---

## Interview Questions

### Q1: What is the difference between = default and = delete?
**Answer**:
| = default | = delete |
|-----------|----------|
| Generates default implementation | Prevents function from being used |
| Only for special member functions | Can be used on any function |
| Makes compiler do the work | Makes function unusable |

### Q2: When would you use = delete?
**Answer**:
- Prevent copying (singletons, unique resources)
- Prevent specific overloads or implicit conversions
- Disable default constructor
- Implement non-copyable classes

### Q3: Why use = default instead of empty body?
**Answer**:
- More efficient (trivial special members optimization)
- Clearer intent
- Ensures correct behavior (e.g., move semantics)
- Allows aggregates to remain aggregates

### Q4: Can you delete a destructor?
**Answer**: Yes, but you can only create stack objects if destructor is accessible. Deleted destructor means objects can only exist dynamically and never be deleted directly.

### Q5: What happens if you call a deleted function?
**Answer**: Compile-time error. The compiler reports that the function is explicitly deleted.
