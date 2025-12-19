# C++ Template Specialization

## Table of Contents
- [Introduction](#introduction)
- [Full Template Specialization](#full-template-specialization)
- [Why Specialize Templates?](#why-specialize-templates)
- [Specialization Syntax](#specialization-syntax)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

Template specialization allows you to define different implementations for a template based on specific types. While the general template handles most types, you can create specialized versions for types that require different treatment.

> **Definition**: Template specialization provides a custom implementation for a template when used with a specific type.

---

## Full Template Specialization

Full specialization replaces the entire template for a specific type:

```cpp
// Primary template (general case)
template <typename T>
void print(T value) {
    cout << "General template: " << value << endl;
}

// Full specialization for int
template <>
void print<int>(int value) {
    cout << "Specialized for int: " << value << endl;
}
```

### Specialization Syntax:
- `template <>` - Empty angle brackets indicate full specialization
- `function<Type>` - Specify the type being specialized

---

## Why Specialize Templates?

| Reason | Example |
|--------|---------|
| **Type-specific behavior** | Different algorithm for strings vs numbers |
| **Optimization** | Use faster method for specific type |
| **Workaround limitations** | Handle types that don't support certain operations |
| **Custom formatting** | Print booleans as "true/false" instead of 1/0 |

---

## Specialization Syntax

### Function Template Specialization:

```cpp
// Primary template
template <typename T>
void process(T value) {
    // General implementation
}

// Specialization for char*
template <>
void process<char*>(char* value) {
    // Special implementation for C-strings
}
```

### Class Template Specialization:

```cpp
// Primary template
template <typename T>
class Container {
    T data;
public:
    void print() { cout << data; }
};

// Specialization for bool
template <>
class Container<bool> {
    bool data;
public:
    void print() { cout << (data ? "true" : "false"); }
};
```

---

## Code Examples

### Example 1: Function Template Specialization

```cpp
#include <iostream>      // Line 1
using namespace std;     // Line 2

// Primary template
template <typename T>    // Line 5
void print(T value) {    // Line 6: General case
    cout << "General template: " << value << endl;  // Line 7
}

// Full specialization for int
template <>              // Line 11: Empty angle brackets
void print<int>(int value) {  // Line 12: Specific type
    cout << "Specialized template for int: " << value << endl;  // Line 13
}

int main() {
    print(5);      // Line 17: Calls specialized version
    print(3.14);   // Line 18: Calls general template

    return 0;
}
```

### Line-by-Line Explanation:

| Line | Code | Explanation |
|------|------|-------------|
| 5-7 | Primary template | Handles all types by default |
| 11 | `template <>` | Signals full specialization |
| 12 | `print<int>(int value)` | Specifies this is for `int` type |
| 17 | `print(5)` | 5 is int → calls specialized version |
| 18 | `print(3.14)` | 3.14 is double → calls general template |

### Output:
```
Specialized template for int: 5
General template: 3.14
```

### Execution Flow:

```
┌─────────────────────────────────────┐
│ Compiler sees print(5)              │
│ - 5 is int                          │
│ - Specialized version exists for int│
│ - Uses specialized version          │
└─────────────┬───────────────────────┘
              │
              ▼
Output: "Specialized template for int: 5"

┌─────────────────────────────────────┐
│ Compiler sees print(3.14)           │
│ - 3.14 is double                    │
│ - No specialized version for double │
│ - Uses general template             │
└─────────────┬───────────────────────┘
              │
              ▼
Output: "General template: 3.14"
```

### Example 2: Specialization Still Maintains Template Benefits

```cpp
#include <iostream>
using namespace std;

// Primary template - handles ALL types generically
template <typename T>
void display(T value) {
    cout << "Value: " << value << endl;
}

// Specialized for bool - custom behavior
template <>
void display<bool>(bool value) {
    cout << "Boolean: " << (value ? "TRUE" : "FALSE") << endl;
}

int main() {
    display(42);           // Uses general template
    display(3.14159);      // Uses general template
    display("Hello");      // Uses general template
    display('X');          // Uses general template
    display(true);         // Uses specialized version
    display(false);        // Uses specialized version
    
    return 0;
}
```

### Output:
```
Value: 42
Value: 3.14159
Value: Hello
Value: X
Boolean: TRUE
Boolean: FALSE
```

---

## Key Points

1. **Syntax**: `template <>` with empty angle brackets for full specialization.

2. **Resolution Order**: Compiler checks specialized versions first, then general template.

3. **Maintains Flexibility**: Specialization is an optimization - general template still works for other types.

4. **Must Declare Primary First**: Specialization must come after the primary template declaration.

5. **Complete Override**: Specialization completely replaces the primary template for that type.

6. **Partial Specialization**: Possible for class templates (not function templates).

---

## Interview Questions

### Q1: What is template specialization?
**Answer**: Template specialization allows you to provide a different implementation for a template when used with a specific type. The general template handles most types while specialized versions handle specific types differently.

### Q2: What does `template <>` mean?
**Answer**: Empty angle brackets indicate full template specialization. It tells the compiler this is not a new template but a specialized version of an existing template for a specific type.

### Q3: What is the difference between full and partial specialization?
**Answer**:
| Full Specialization | Partial Specialization |
|--------------------|----------------------|
| All template parameters are fixed | Some parameters remain generic |
| Works for functions and classes | Works only for classes |
| `template <>` | `template <class T>` with some fixed |

### Q4: Can you specialize a single function in a class template?
**Answer**: No, you must specialize the entire class. For specific function behavior, use function overloading or tag dispatch instead.

### Q5: How does the compiler choose between general and specialized templates?
**Answer**: The compiler follows a "most specialized wins" rule. It checks specializations first and uses them if they match; otherwise, it falls back to the general template.
