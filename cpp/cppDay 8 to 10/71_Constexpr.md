# C++ constexpr (C++11)

## Table of Contents
- [Introduction](#introduction)
- [constexpr vs const](#constexpr-vs-const)
- [constexpr Functions](#constexpr-functions)
- [constexpr Variables](#constexpr-variables)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

`constexpr` specifies that the value of a variable or function can be evaluated at **compile time**. This enables optimizations and allows using values where compile-time constants are required.

> **Key Benefit**: Computation moves from runtime to compile time.

---

## constexpr vs const

| Feature | const | constexpr |
|---------|-------|-----------|
| Evaluation | Runtime or compile time | Compile time (guaranteed) |
| Initialization | Can be runtime value | Must be compile-time value |
| Functions | N/A | Can mark functions |
| Arrays | Can't use for size | Can use for array size |

### Example:
```cpp
const int x = getValue();      // OK: runtime initialization
constexpr int y = 10;          // OK: compile-time constant
// constexpr int z = getValue(); // Error: not compile-time

int arr1[y];  // OK: y is compile-time constant
// int arr2[x]; // Error if x is not constexpr
```

---

## constexpr Functions

Functions marked `constexpr` can be evaluated at compile time if:
- Arguments are compile-time constants
- Function body meets requirements

### Syntax:
```cpp
constexpr int square(int x) {
    return x * x;
}
```

### Usage:
```cpp
constexpr int result = square(5);  // Computed at compile time: 25
int arr[result];  // OK: result is compile-time constant

int runtime_val = 10;
int val2 = square(runtime_val);  // Computed at runtime
```

---

## constexpr Variables

Variables declared `constexpr` must be initialized with a compile-time constant:

```cpp
constexpr int size = 100;         // OK
constexpr double pi = 3.14159;    // OK
constexpr int arr_size = size * 2; // OK: computed at compile time

int x = 10;
// constexpr int y = x;  // Error: x is not constexpr
```

---

## Code Examples

### Example 1: constexpr Function

```cpp
#include<iostream>
using namespace std;

int fun1(int k) {           // Regular function
    return k * k;
}

constexpr int fun2(int k) {  // constexpr function
    return k * k;
}

int main() {
    int result1 = fun1(3);
    // int arr1[result1];  // Error: not compile-time constant
    
    constexpr int result2 = fun2(3);  // Computed at compile time
    int arr[result2];  // OK: result2 is 9 at compile time
    
    int result3 = fun2(20);  // Can also be used at runtime
    cout << result3 << endl;
    
    return 0;
}
```

### Example 2: Array Size with constexpr

```cpp
#include<iostream>
using namespace std;

constexpr int getSize() {
    return 5 * 10;
}

int main() {
    constexpr int size = getSize();  // 50 at compile time
    int arr[size];  // Array of 50 elements
    
    cout << "Array size: " << sizeof(arr)/sizeof(arr[0]) << endl;
    return 0;
}
```

### Example 3: constexpr Class Constructor (C++11)

```cpp
#include<iostream>
using namespace std;

class Point {
    int x, y;
public:
    constexpr Point(int a, int b) : x(a), y(b) {}
    constexpr int getX() const { return x; }
    constexpr int getY() const { return y; }
};

int main() {
    constexpr Point p(3, 4);
    constexpr int x = p.getX();  // 3 at compile time
    
    int arr[x];  // OK: x is compile-time constant
    
    return 0;
}
```

---

## Key Points

1. **Compile-Time Evaluation**: `constexpr` ensures evaluation at compile time.
2. **const vs constexpr**: `const` can be runtime; `constexpr` is always compile-time.
3. **Function Requirements**: constexpr functions must have simple bodies (relaxed in C++14).
4. **Array Sizes**: constexpr values can be used for static array sizes.
5. **Performance**: Moves computation from runtime to compile time.
6. **Must Be Initialized**: constexpr variables require initialization.

---

## Interview Questions

### Q1: What is the difference between const and constexpr?
**Answer**:
- `const`: Value cannot be changed, but may be initialized at runtime
- `constexpr`: Value must be computed at compile time

### Q2: When would you use constexpr?
**Answer**: Use `constexpr` when:
- You need a value computed at compile time
- For array sizes
- For template parameters
- For optimization (compile-time computation)

### Q3: Can a constexpr function be called at runtime?
**Answer**: Yes! If the arguments are not compile-time constants, the function runs at runtime. `constexpr` only guarantees compile-time evaluation when possible.

### Q4: What is the benefit of constexpr?
**Answer**:
- Performance (no runtime computation)
- Can be used where compile-time constants are required (array sizes, template args)
- Type safety and optimization opportunities

### Q5: Can constexpr functions have loops? (C++14)
**Answer**: In C++11, constexpr functions were very limited. C++14 relaxed these rules to allow loops, local variables, and multiple statements.
