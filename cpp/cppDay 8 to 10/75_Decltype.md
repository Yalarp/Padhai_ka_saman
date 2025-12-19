# C++ decltype (C++11)

## Table of Contents
- [Introduction](#introduction)
- [Syntax](#syntax)
- [decltype vs auto](#decltype-vs-auto)
- [Use Cases](#use-cases)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

`decltype` is a C++11 feature that deduces the type of an expression at compile time. Unlike `auto`, it doesn't require initialization and can deduce reference types.

> **Definition**: `decltype(expression)` yields the type of the expression.

---

## Syntax

```cpp
int x = 10;
decltype(x) y = 20;  // y is int

double d = 3.14;
decltype(d) e = 2.71;  // e is double
```

---

## decltype vs auto

| Feature | auto | decltype |
|---------|------|----------|
| Deduction | From initializer | From expression |
| Requires init | Yes | No |
| Reference | Decays | Preserves |
| Use case | Variable declaration | Template return types |

### Example:
```cpp
int x = 10;
int& ref = x;

auto a = ref;       // a is int (reference decays)
decltype(ref) b = x; // b is int& (reference preserved)
```

---

## Use Cases

### 1. Template Return Types
```cpp
template<typename T, typename U>
auto add(T a, U b) -> decltype(a + b) {
    return a + b;
}
```

### 2. Preserving Reference Types
```cpp
int x = 10;
int& getRef() { return x; }

auto a = getRef();       // a is int (copy)
decltype(getRef()) b = getRef();  // b is int& (reference)
```

### 3. Generic Programming
```cpp
template<typename Container>
auto getFirst(Container& c) -> decltype(c[0]) {
    return c[0];
}
```

---

## Code Examples

### Example 1: Basic decltype Usage

```cpp
#include<iostream>
using namespace std;

int main() {
    int x = 10;
    double d = 3.14;
    
    decltype(x) y = 20;      // y is int
    decltype(d) e = 2.71;    // e is double
    decltype(x + d) sum = x + d;  // sum is double
    
    cout << "y: " << y << endl;
    cout << "e: " << e << endl;
    cout << "sum: " << sum << endl;
    
    return 0;
}
```

### Example 2: Preserving References

```cpp
#include<iostream>
using namespace std;

int main() {
    int x = 10;
    int& ref = x;
    
    auto a = ref;              // a is int (not reference)
    decltype(ref) b = x;       // b is int&
    
    a = 100;
    cout << "x after a=100: " << x << endl;  // 10 (unchanged)
    
    b = 200;
    cout << "x after b=200: " << x << endl;  // 200 (changed!)
    
    return 0;
}
```

### Example 3: Template Return Type

```cpp
#include<iostream>
using namespace std;

template<typename T, typename U>
auto multiply(T a, U b) -> decltype(a * b) {
    return a * b;
}

int main() {
    auto r1 = multiply(5, 3);        // int * int = int
    auto r2 = multiply(5, 3.14);     // int * double = double
    auto r3 = multiply(2.5, 4);      // double * int = double
    
    cout << r1 << endl;  // 15
    cout << r2 << endl;  // 15.7
    cout << r3 << endl;  // 10
    
    return 0;
}
```

---

## Key Points

1. **Compile-Time**: Type deduction happens at compile time.
2. **Expression-Based**: Deduces from expression, not initializer.
3. **Reference Preservation**: Unlike auto, preserves reference types.
4. **Template Usage**: Essential for generic return types.
5. **No Evaluation**: Expression is not evaluated, only type is deduced.

---

## Interview Questions

### Q1: What is decltype?
**Answer**: `decltype` is a C++11 keyword that deduces the type of an expression at compile time without evaluating it.

### Q2: What is the difference between auto and decltype?
**Answer**:
| auto | decltype |
|------|----------|
| Deduces from initializer | Deduces from expression |
| Drops references | Preserves references |
| Requires initialization | No initialization needed |

### Q3: Does decltype evaluate its expression?
**Answer**: No, `decltype` only deduces the type. The expression is not evaluated at runtime.

### Q4: When would you use decltype?
**Answer**:
- Template return types
- Preserving reference types
- When you need the exact type of an expression
- Generic programming
