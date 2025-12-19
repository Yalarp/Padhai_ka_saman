# C++ Unified Initialization (C++11)

## Table of Contents
- [Introduction](#introduction)
- [Brace Initialization Syntax](#brace-initialization-syntax)
- [Benefits](#benefits)
- [Narrowing Conversions](#narrowing-conversions)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

C++11 introduced **uniform initialization** (also called brace initialization), which provides a consistent syntax for initializing all types of objects using curly braces `{}`.

```cpp
int x{10};           // Variable
int arr[]{1, 2, 3}; // Array
vector<int> v{1, 2}; // Container
MyClass obj{1, 2};   // Class
```

---

## Brace Initialization Syntax

### Variables:
```cpp
int x{10};
double d{3.14};
string s{"Hello"};
```

### Arrays:
```cpp
int arr[]{1, 2, 3, 4, 5};
char word[]{'H', 'i'};
```

### Classes:
```cpp
class Point {
    int x, y;
public:
    Point(int a, int b) : x(a), y(b) {}
};

Point p{3, 4};
```

### Containers:
```cpp
vector<int> v{1, 2, 3, 4, 5};
map<string, int> m{{"one", 1}, {"two", 2}};
```

---

## Benefits

| Benefit | Description |
|---------|-------------|
| **Consistency** | Same syntax for all types |
| **Prevents narrowing** | Error on data loss conversions |
| **No vexing parse** | Avoids function declaration ambiguity |
| **Initializer lists** | Natural container initialization |

---

## Narrowing Conversions

Brace initialization prevents potentially dangerous narrowing conversions:

```cpp
int x = 3.14;    // OK: x = 3 (data loss, but compiles)
int y{3.14};     // Error: narrowing conversion

double d = 3.14;
int a = d;       // OK: a = 3
int b{d};        // Error: narrowing

long l = 1000000000L;
int m{l};        // Error if l doesn't fit in int
```

---

## Code Examples

### Example 1: Various Initialization Forms

```cpp
#include<iostream>
#include<vector>
using namespace std;

int main() {
    // Variables
    int x{10};
    double d{3.14};
    
    // Array
    int arr[]{1, 2, 3, 4, 5};
    
    // Vector
    vector<int> nums{10, 20, 30, 40};
    
    // Default initialization
    int y{};  // y = 0
    
    cout << "x = " << x << endl;
    cout << "y = " << y << endl;
    cout << "First element: " << arr[0] << endl;
    cout << "Vector size: " << nums.size() << endl;
    
    return 0;
}
```

### Example 2: Class Initialization

```cpp
#include<iostream>
using namespace std;

class Rectangle {
    int width, height;
public:
    Rectangle(int w, int h) : width{w}, height{h} {}
    int area() { return width * height; }
};

int main() {
    Rectangle r1{10, 20};
    Rectangle r2 = {5, 15};  // Also valid
    
    cout << "r1 area: " << r1.area() << endl;
    cout << "r2 area: " << r2.area() << endl;
    
    return 0;
}
```

### Example 3: Avoiding Most Vexing Parse

```cpp
#include<iostream>
using namespace std;

class Widget {
public:
    Widget() { cout << "Default constructor" << endl; }
};

int main() {
    // Most vexing parse - declares a function!
    // Widget w();  
    
    // Brace initialization - creates object
    Widget w{};  // Calls default constructor
    
    return 0;
}
```

---

## Key Points

1. **Universal Syntax**: Same `{}` works for all types.
2. **Prevents Narrowing**: Compile error on data loss.
3. **Default Init**: `int x{}` initializes to 0.
4. **Avoids Ambiguity**: No most vexing parse problem.
5. **Initializer List**: Enables container initialization.

---

## Interview Questions

### Q1: What is uniform initialization?
**Answer**: Uniform initialization is a C++11 feature that uses curly braces `{}` to initialize any type consistently, preventing narrowing conversions and ambiguous declarations.

### Q2: What is a narrowing conversion?
**Answer**: A narrowing conversion is an implicit conversion that may lose data, like `double` to `int`. Brace initialization prevents these at compile time.

### Q3: What is the most vexing parse?
**Answer**: The most vexing parse occurs when `Widget w();` is interpreted as a function declaration instead of object creation. Brace initialization `Widget w{};` avoids this.

### Q4: What does `int x{};` initialize x to?
**Answer**: Zero. Brace initialization with empty braces performs value initialization, which for built-in types means zero.
