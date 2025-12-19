# C++ Lambda Expressions (C++11)

## Table of Contents
- [Introduction](#introduction)
- [Lambda Syntax](#lambda-syntax)
- [Capture Clause](#capture-clause)
- [Mutable Lambdas](#mutable-lambdas)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

Lambda functions in C++ are anonymous, in-place functions for short code snippets that don't need to be reused. Introduced in C++11, they're perfect for callbacks, algorithms, and one-time operations.

> **Definition**: A lambda is an unnamed function object capable of capturing variables in scope.

---

## Lambda Syntax

```cpp
[capture](parameters) -> return_type { body }
```

| Part | Description | Required |
|------|-------------|----------|
| `[]` | Capture clause | Yes |
| `()` | Parameter list | Optional (if no params) |
| `->` | Return type | Optional (deduced) |
| `{}` | Function body | Yes |

### Minimal Lambda:
```cpp
auto greet = []() { cout << "Hello!"; };
greet();  // Outputs: Hello!
```

---

## Capture Clause

The capture clause `[]` specifies which external variables the lambda can access:

| Syntax | Meaning |
|--------|---------|
| `[]` | Capture nothing |
| `[=]` | Capture all by value |
| `[&]` | Capture all by reference |
| `[x]` | Capture x by value |
| `[&x]` | Capture x by reference |
| `[=, &x]` | All by value, x by reference |
| `[&, x]` | All by reference, x by value |
| `[this]` | Capture this pointer |

### Examples:
```cpp
int a = 10, b = 20;

auto f1 = [=]() { return a + b; };    // a, b copied
auto f2 = [&]() { a++; b++; };        // a, b by reference
auto f3 = [a, &b]() { b = a * 2; };   // a copied, b by ref
```

---

## Mutable Lambdas

By default, captured-by-value variables are `const`. Use `mutable` to modify them:

```cpp
int x = 10;
auto f = [x]() mutable { 
    x++;           // Allowed with mutable
    return x; 
};
// x is still 10 outside (was captured by value)
```

---

## Code Examples

### Example 1: Basic Lambda

```cpp
#include<iostream>
using namespace std;

int main()
{
    // Lambda with no parameters
    auto greet = []() {
        cout << "Hello, Lambda!" << endl;
    };
    greet();
    
    // Lambda with parameters
    auto add = [](int a, int b) {
        return a + b;
    };
    cout << "Sum: " << add(5, 3) << endl;
    
    // Lambda with explicit return type
    auto divide = [](double a, double b) -> double {
        if (b == 0) return 0;
        return a / b;
    };
    cout << "Division: " << divide(10.0, 3.0) << endl;
    
    return 0;
}
```

### Example 2: Capture Clause

```cpp
#include<iostream>
using namespace std;

int main()
{
    int x = 10;
    int y = 20;
    
    // Capture by value
    auto f1 = [x, y]() {
        cout << "x + y = " << x + y << endl;
    };
    f1();  // 30
    
    // Capture by reference
    auto f2 = [&x, &y]() {
        x++;
        y++;
    };
    f2();
    cout << "x = " << x << ", y = " << y << endl;  // 11, 21
    
    // Capture all by value
    auto f3 = [=]() {
        cout << "Sum: " << x + y << endl;
    };
    f3();  // 32
    
    return 0;
}
```

### Example 3: Lambda with STL Algorithms

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main()
{
    vector<int> nums = {5, 2, 8, 1, 9, 3};
    
    // Sort with custom comparator
    sort(nums.begin(), nums.end(), [](int a, int b) {
        return a > b;  // Descending order
    });
    
    // Print each element
    for_each(nums.begin(), nums.end(), [](int n) {
        cout << n << " ";
    });
    
    // Count elements greater than 4
    int count = count_if(nums.begin(), nums.end(), [](int n) {
        return n > 4;
    });
    cout << "\nCount > 4: " << count << endl;
    
    return 0;
}
```

---

## Key Points

1. **Anonymous Functions**: No name required, defined in-place.
2. **Capture Clause**: Controls access to external variables.
3. **Type Deduction**: Return type usually deduced automatically.
4. **Closures**: Lambda with captures is called a closure.
5. **STL Integration**: Perfect for algorithm predicates.
6. **mutable Keyword**: Allows modification of by-value captures.

---

## Interview Questions

### Q1: What is a lambda expression?
**Answer**: A lambda is an anonymous function object that can be defined inline. It can capture variables from its enclosing scope and is commonly used for short operations, callbacks, and STL algorithm predicates.

### Q2: What is the capture clause?
**Answer**: The capture clause `[]` specifies which variables from the surrounding scope the lambda can access. Variables can be captured by value `[x]` or by reference `[&x]`.

### Q3: What is the difference between [=] and [&]?
**Answer**:
- `[=]`: Captures all external variables by value (copies)
- `[&]`: Captures all external variables by reference

### Q4: When would you use the mutable keyword?
**Answer**: Use `mutable` when you need to modify a variable captured by value inside the lambda. By default, by-value captures are const.

### Q5: What is a closure?
**Answer**: A closure is a lambda expression that captures variables from its surrounding scope. The lambda "closes over" these variables, keeping access to them even when the lambda is executed outside its original scope.
