# C++ Structured Bindings (C++17)

## Table of Contents
- [Introduction](#introduction)
- [Syntax](#syntax)
- [Supported Types](#supported-types)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

Structured bindings allow you to decompose objects into their individual members, providing a convenient way to work with pairs, tuples, arrays, and structs.

> **Feature**: Automatically unpacks multi-value objects.

---

## Syntax

```cpp
auto [var1, var2, ...] = expression;
```

### Examples:
```cpp
pair<int, string> p{1, "Hello"};
auto [id, name] = p;  // id = 1, name = "Hello"

tuple<int, double, char> t{1, 3.14, 'A'};
auto [x, y, z] = t;  // x = 1, y = 3.14, z = 'A'
```

---

## Supported Types

| Type | Example |
|------|---------|
| `pair` | `auto [first, second] = myPair;` |
| `tuple` | `auto [a, b, c] = myTuple;` |
| `array` | `auto [x, y, z] = myArray;` |
| `struct` | `auto [m1, m2] = myStruct;` |
| `map` iteration | `for (auto& [key, val] : myMap)` |

---

## Code Examples

### Example 1: Pair Decomposition

```cpp
#include <iostream>
#include <utility>
using namespace std;

pair<int, string> getUser() {
    return {42, "Alice"};
}

int main() {
    auto [id, name] = getUser();
    
    cout << "ID: " << id << endl;
    cout << "Name: " << name << endl;
    
    return 0;
}
```

### Example 2: Map Iteration

```cpp
#include <iostream>
#include <map>
using namespace std;

int main() {
    map<string, int> ages = {
        {"Alice", 25},
        {"Bob", 30},
        {"Charlie", 35}
    };
    
    // Structured binding in range-based for
    for (const auto& [name, age] : ages) {
        cout << name << " is " << age << " years old" << endl;
    }
    
    return 0;
}
```

### Example 3: Struct Decomposition

```cpp
#include <iostream>
using namespace std;

struct Point {
    int x, y, z;
};

int main() {
    Point p{10, 20, 30};
    auto [x, y, z] = p;
    
    cout << "x=" << x << ", y=" << y << ", z=" << z << endl;
    
    return 0;
}
```

---

## Key Points

1. **auto Required**: Must use `auto` keyword.
2. **Compile-Time**: Binding happens at compile time.
3. **Reference Binding**: Use `auto&` for references.
4. **Order Matters**: Variables match struct member order.
5. **Map Friendly**: Perfect for map iteration.

---

## Interview Questions

### Q1: What are structured bindings?
**Answer**: Structured bindings allow decomposing objects like pairs, tuples, arrays, and structs into individual named variables using `auto [...]` syntax.

### Q2: How do you iterate a map with structured bindings?
**Answer**:
```cpp
for (auto& [key, value] : myMap) {
    // Use key and value directly
}
```
