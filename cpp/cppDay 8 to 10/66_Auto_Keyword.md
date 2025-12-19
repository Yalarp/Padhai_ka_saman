# C++ auto Keyword (C++11)

## Table of Contents
- [Introduction](#introduction)
- [How auto Works](#how-auto-works)
- [Use Cases](#use-cases)
- [Restrictions](#restrictions)
- [auto vs C++98 auto](#auto-vs-c98-auto)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

The `auto` keyword in C++11 introduces automatic type deduction, allowing the compiler to infer the type of a variable from its initializer. This feature is particularly useful when the type is complex or cumbersome to write explicitly.

> **Definition**: `auto` lets the compiler deduce the type from the initializer expression.

---

## How auto Works

```cpp
auto x = 10;          // x is deduced as int
auto y = 3.14;        // y is deduced as double
auto z = "Hello";     // z is deduced as const char*
auto b = true;        // b is deduced as bool
```

The compiler examines the right-hand side expression and determines the appropriate type.

---

## Use Cases

### 1. Complex Iterator Types
```cpp
// Without auto - verbose
std::vector<int>::iterator it = vec.begin();

// With auto - concise
auto it = vec.begin();
```

### 2. Lambda Expressions
```cpp
// Lambda type is complex and unwritable
auto add = [](int a, int b) { return a + b; };
```

### 3. Template Return Types
```cpp
template<class T, class U>
auto multiply(T a, U b) -> decltype(a * b) {
    return a * b;
}
```

### 4. Range-based For Loops
```cpp
std::vector<std::string> names = {"Alice", "Bob"};
for (auto& name : names) {
    cout << name << endl;
}
```

---

## Restrictions

| Restriction | Example |
|-------------|---------|
| Must have initializer | `auto x;` ❌ Error |
| Can't be function parameter | `void func(auto x)` ❌ (allowed in C++20) |
| Can't be class member (non-static) | `class C { auto x = 5; };` ❌ |
| Can't be array | `auto arr[] = {1, 2, 3};` ❌ |

---

## auto vs C++98 auto

| C++98 auto | C++11 auto |
|------------|------------|
| Storage class specifier | Type deduction keyword |
| Indicated automatic storage | Deduces type from initializer |
| Rarely used (was default) | Commonly used |

```cpp
// C++98: auto means automatic storage (the default)
auto int x = 5;  // Old meaning, rarely seen

// C++11: auto means type deduction
auto x = 5;      // New meaning, x is int
```

---

## Code Examples

### Example 1: Basic Type Deduction

```cpp
#include<iostream>
using namespace std;

int main()
{
    auto k{ 8.9 };           // k is double
    cout << k << endl;
    
    auto r{ 100 };           // r is int
    cout << r << endl;
    
    auto s{ "hello" };       // s is const char*
    cout << s << endl;
    
    auto flag = true;        // flag is bool
    cout << boolalpha << flag << endl;
    
    return 0;
}
```

### Example 2: auto with Iterators

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    vector<int> numbers = {1, 2, 3, 4, 5};
    
    // Without auto
    vector<int>::iterator it1 = numbers.begin();
    
    // With auto - much cleaner!
    auto it2 = numbers.begin();
    
    // Iterate using auto
    for (auto it = numbers.begin(); it != numbers.end(); ++it) {
        cout << *it << " ";
    }
    
    return 0;
}
```

### Example 3: auto with Lambdas

```cpp
#include<iostream>
#include<functional>
using namespace std;

int main()
{
    // Lambda with auto
    auto mymethod1 = []() { 
        cout << "inside lambda method" << endl; 
    };
    mymethod1();
    
    // Lambda with explicit type (verbose)
    function<void()> mymethod2 = []() { 
        cout << "another lambda function" << endl; 
    };
    mymethod2();
    
    // Check types
    cout << "Type of lambda: " << typeid(mymethod1).name() << endl;
    
    return 0;
}
```

---

## Key Points

1. **Type Deduction**: Compiler determines type from initializer.
2. **Simplifies Code**: Especially useful with complex types.
3. **Must Initialize**: Cannot declare without initializer.
4. **Preserves cv-qualifiers**: `const auto` keeps constness.
5. **Reference Decay**: `auto` drops references unless explicitly specified (`auto&`).
6. **Modern C++**: Part of C++11 and later standards.

---

## Interview Questions

### Q1: What is the purpose of the auto keyword in C++11?
**Answer**: The `auto` keyword allows the compiler to automatically deduce the type of a variable from its initializer. It simplifies code, especially when dealing with complex types like iterators or lambda expressions.

### Q2: Can auto be used without initialization?
**Answer**: No. Since `auto` relies on the initializer to determine the type, a variable declared with `auto` must be initialized at the point of declaration.

### Q3: What type does `auto x = 5;` deduce?
**Answer**: `int`. The literal `5` is an `int`, so `auto` deduces `x` as `int`.

### Q4: How does auto differ from C++98?
**Answer**: In C++98, `auto` was a storage class specifier (automatic storage, the default). In C++11, it was repurposed for type deduction. The old meaning is removed.

### Q5: How do you use auto with references?
**Answer**: Use `auto&` for lvalue references:
```cpp
int x = 10;
auto& ref = x;   // ref is int&
ref = 20;        // x is now 20
```
