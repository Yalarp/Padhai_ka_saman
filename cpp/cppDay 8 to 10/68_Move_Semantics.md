# C++ Move Semantics (C++11)

## Table of Contents
- [Introduction](#introduction)
- [Lvalue vs Rvalue](#lvalue-vs-rvalue)
- [Rvalue References](#rvalue-references)
- [Move Constructor](#move-constructor)
- [Move Assignment Operator](#move-assignment-operator)
- [std::move](#stdmove)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

Move semantics, introduced in C++11, allows resources to be "moved" from one object to another instead of being copied. This significantly improves performance for objects that manage dynamic memory, file handles, or other resources.

> **Key Insight**: Instead of copying resources, move semantics transfers ownership, avoiding expensive deep copies.

---

## Lvalue vs Rvalue

| Type | Description | Example | Can Take Address? |
|------|-------------|---------|-------------------|
| **Lvalue** | Has identity, persists | Variables, references | Yes |
| **Rvalue** | Temporary, no identity | Literals, temporaries | No |

### Examples:
```cpp
int x = 10;      // x is lvalue, 10 is rvalue
int y = x + 5;   // y is lvalue, (x + 5) is rvalue

string s1 = "Hello";           // s1 is lvalue
string s2 = s1 + " World";     // s2 is lvalue, (s1 + " World") is rvalue
```

---

## Rvalue References

Rvalue references bind to temporary objects using `&&`:

```cpp
int&& rref = 10;              // OK: binds to rvalue
int x = 5;
// int&& rref2 = x;           // Error: can't bind rvalue ref to lvalue
int&& rref3 = std::move(x);   // OK: move converts lvalue to rvalue
```

---

## Move Constructor

The move constructor transfers resources from a temporary object:

```cpp
class MyClass {
    int* data;
public:
    // Move Constructor
    MyClass(MyClass&& other) noexcept : data(other.data) {
        other.data = nullptr;  // Leave source in valid state
    }
};
```

### Key Points:
- Takes rvalue reference parameter
- Transfers ownership of resources
- Leaves source object in valid but unspecified state
- Should be `noexcept` for STL compatibility

---

## Move Assignment Operator

Similar to move constructor, but for assignment:

```cpp
class MyClass {
    int* data;
public:
    // Move Assignment
    MyClass& operator=(MyClass&& other) noexcept {
        if (this != &other) {
            delete data;           // Free existing resource
            data = other.data;     // Transfer ownership
            other.data = nullptr;  // Leave source valid
        }
        return *this;
    }
};
```

---

## std::move

`std::move` converts an lvalue to an rvalue, enabling move semantics:

```cpp
#include <utility>  // for std::move

vector<int> v1 = {1, 2, 3, 4, 5};
vector<int> v2 = std::move(v1);  // v1's resources moved to v2
// v1 is now empty (valid but unspecified state)
```

> **Important**: `std::move` doesn't actually move anything - it just casts to rvalue reference, enabling the move constructor/assignment to be called.

---

## Code Examples

### Example 1: Move vs Copy Performance

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <utility>
using namespace std;

class HeavyObject {
    string* data;
public:
    HeavyObject(const string& s) {
        data = new string(s);
        cout << "Constructor" << endl;
    }
    
    // Copy Constructor
    HeavyObject(const HeavyObject& other) {
        data = new string(*other.data);  // Deep copy
        cout << "Copy Constructor (expensive!)" << endl;
    }
    
    // Move Constructor
    HeavyObject(HeavyObject&& other) noexcept {
        data = other.data;    // Just pointer copy
        other.data = nullptr; // Invalidate source
        cout << "Move Constructor (cheap!)" << endl;
    }
    
    ~HeavyObject() {
        delete data;
    }
};

int main() {
    HeavyObject obj1("Hello World");
    
    HeavyObject obj2 = obj1;              // Copy (expensive)
    HeavyObject obj3 = std::move(obj1);   // Move (cheap)
    
    return 0;
}
```

### Output:
```
Constructor
Copy Constructor (expensive!)
Move Constructor (cheap!)
```

### Example 2: Complete Class with Move Semantics

```cpp
#include <iostream>
#include <cstring>
using namespace std;

class String {
    char* data;
    size_t len;
    
public:
    // Constructor
    String(const char* s) {
        len = strlen(s);
        data = new char[len + 1];
        strcpy(data, s);
        cout << "Created: " << data << endl;
    }
    
    // Copy Constructor
    String(const String& other) {
        len = other.len;
        data = new char[len + 1];
        strcpy(data, other.data);
        cout << "Copied: " << data << endl;
    }
    
    // Move Constructor
    String(String&& other) noexcept {
        data = other.data;
        len = other.len;
        other.data = nullptr;
        other.len = 0;
        cout << "Moved: " << data << endl;
    }
    
    // Copy Assignment
    String& operator=(const String& other) {
        if (this != &other) {
            delete[] data;
            len = other.len;
            data = new char[len + 1];
            strcpy(data, other.data);
        }
        return *this;
    }
    
    // Move Assignment
    String& operator=(String&& other) noexcept {
        if (this != &other) {
            delete[] data;
            data = other.data;
            len = other.len;
            other.data = nullptr;
            other.len = 0;
        }
        return *this;
    }
    
    ~String() {
        delete[] data;
    }
};
```

---

## Key Points

1. **Performance**: Move is O(1), copy can be O(n).
2. **Rvalue Reference**: `T&&` binds to temporaries.
3. **std::move**: Casts to rvalue, doesn't actually move.
4. **Valid State**: Moved-from object must be in valid state.
5. **noexcept**: Mark move operations noexcept for STL.
6. **Rule of Five**: If you define any of destructor, copy constructor, copy assignment, move constructor, move assignment - define all five.

---

## Interview Questions

### Q1: What is move semantics?
**Answer**: Move semantics allows transferring resources from one object to another instead of copying. This improves performance by avoiding expensive deep copies for objects with dynamic resources.

### Q2: What is the difference between lvalue and rvalue?
**Answer**:
- **Lvalue**: Has a name, persists beyond expression, can take address
- **Rvalue**: Temporary, no persistent identity, can't take address

### Q3: What does std::move do?
**Answer**: `std::move` is a cast that converts an lvalue to an rvalue reference, enabling move operations. It doesn't actually move anything - it just enables the move constructor/assignment to be chosen.

### Q4: What is the difference between copy and move constructor?
**Answer**:
| Copy Constructor | Move Constructor |
|------------------|------------------|
| Takes `const T&` | Takes `T&&` |
| Makes deep copy | Transfers ownership |
| O(n) for resources | O(1) |
| Source unchanged | Source left in valid but empty state |

### Q5: Why should move operations be noexcept?
**Answer**: STL containers (like vector) need to know if move operations can throw. If move constructor is noexcept, containers will use move during reallocation. Otherwise, they fall back to slower copy operations for exception safety.
