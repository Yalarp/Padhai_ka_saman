# C++ Function Templates

## Table of Contents
- [Introduction](#introduction)
- [Why Use Templates?](#why-use-templates)
- [Function Template Syntax](#function-template-syntax)
- [Template Instantiation](#template-instantiation)
- [Multiple Template Parameters](#multiple-template-parameters)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

Function templates enable you to write a single function definition that works with different data types. Instead of writing separate functions for `int`, `double`, `char`, etc., you write one generic function that the compiler adapts as needed.

> **Definition**: A template is a mechanism that makes it possible to use one function (or class) to handle many different data types.

---

## Why Use Templates?

### Problems with Function Overloading

| Issue | Description |
|-------|-------------|
| **Code Duplication** | Same logic repeated for each type |
| **Maintenance** | Changes must be made in multiple places |
| **Disk Space** | Multiple copies of similar code |
| **Error Prone** | Bug fixes must be applied everywhere |

### Example Without Templates:
```cpp
void add(int a, int b) { cout << a + b; }
void add(double a, double b) { cout << a + b; }
void add(char a, char b) { cout << a + b; }
// Need a new function for every type!
```

### With Templates:
```cpp
template <class T>
void add(T a, T b) { cout << a + b; }
// One function works for all types!
```

---

## Function Template Syntax

### Basic Syntax:

```cpp
template <typename T>
return_type function_name(T parameter1, T parameter2)
{
    // function body using T
}
```

### Alternative Keyword:

```cpp
template <class T>  // 'class' and 'typename' are interchangeable here
return_type function_name(T parameter1, T parameter2)
{
    // function body
}
```

### Terminology:
- `T` is called the **template parameter** or **template argument**
- The entire `template <...>` is the **template header**

---

## Template Instantiation

When you call a template function, the compiler:
1. Examines the argument types
2. Generates a specific version of the function for those types
3. Replaces `T` with the actual type
4. Compiles the generated function

### Process Visualization:

```
Template Definition:
┌──────────────────────────────────┐
│ template <class T>               │
│ void add(T a, T b)               │
│ { cout << a + b; }               │
└──────────────────────────────────┘
              │
     Function Calls
              │
    ┌─────────┼─────────┐
    ▼         ▼         ▼
add(20,40)  add('A','B')  add(3.14,2.71)
    │         │               │
    ▼         ▼               ▼
┌─────────┐ ┌─────────┐ ┌───────────┐
│ int ver │ │ char ver│ │ double ver│
│ adds int│ │ adds chr│ │ adds dbl  │
└─────────┘ └─────────┘ └───────────┘
```

> **Important**: Templates don't save memory at runtime - the functions still get generated. The benefit is you write the code once.

---

## Multiple Template Parameters

You can use more than one template parameter:

```cpp
template <class T1, class T2>
void add(T1 a, T2 b)
{
    cout << a + b << endl;
}
```

This allows arguments of different types:
```cpp
add(20, 7.8);    // T1=int, T2=double
add('A', 4.5);   // T1=char, T2=double
add(34.30, 12);  // T1=double, T2=int
```

---

## Code Examples

### Example 1: Basic Function Template

```cpp
#include<iostream>      // Line 1
using namespace std;    // Line 2

template <class type>   // Line 3: Template declaration
void add(type a, type b)  // Line 4: Function using template type
{
    cout << endl << a + b << endl;  // Line 6
}

void main()
{
    add(20, 40);         // Line 11: Instantiates int version
    add('A', 'b');       // Line 12: Instantiates char version
    add(34.30, 45.89);   // Line 13: Instantiates double version
}
```

### Line-by-Line Explanation:

| Line | Code | Explanation |
|------|------|-------------|
| 3 | `template <class type>` | Declares template with parameter `type` |
| 4 | `void add(type a, type b)` | Function parameters use generic type |
| 6 | `cout << a + b` | Works because + is defined for int, char, double |
| 11 | `add(20, 40)` | Compiler deduces type=int, generates int version |
| 12 | `add('A', 'b')` | Compiler deduces type=char, generates char version |
| 13 | `add(34.30, 45.89)` | Compiler deduces type=double, generates double version |

### Execution Flow:

```
Compiler encounters template
  │
  ├── add(20, 40) called
  │   └── Generates: void add(int a, int b) { cout << a+b; }
  │   └── Output: 60
  │
  ├── add('A', 'b') called
  │   └── Generates: void add(char a, char b) { cout << a+b; }
  │   └── Output: 163 (65 + 98 = ASCII sum)
  │
  └── add(34.30, 45.89) called
      └── Generates: void add(double a, double b) { cout << a+b; }
      └── Output: 80.19
```

### Example 2: Two Template Parameters

```cpp
#include<iostream>      // Line 1
using namespace std;    // Line 2

template <class type1, class type2>  // Line 3: Two template parameters
void add(type1 a, type2 b)           // Line 4: Different types allowed
{
    cout << endl << a + b << endl;   // Line 6
}

void main()
{
    add(20, 7.8);       // Line 11: type1=int, type2=double → 27.8
    add('A', 4.5);      // Line 12: type1=char, type2=double → 69.5
    add(34.30, 12);     // Line 13: type1=double, type2=int → 46.3
    add(20, 30);        // Line 14: type1=int, type2=int → 50
}
```

### Example 3: Template with User-Defined Types (Error Case)

```cpp
#include<iostream>      // Line 1
using namespace std;    // Line 2

class MyClass           // Line 4
{
private:
    int num;
public:
    MyClass(int k)      // Line 9
    {
        num = k;
    }
};

template <class type1, class type2>  // Line 15
void add(type1 a, type2 b)
{
    cout << endl << a + b << endl;   // Line 18: ERROR for MyClass!
}

void main()
{
    add(20, 7.8);       // OK
    MyClass m1(20);
    add(m1, 50);        // ERROR: MyClass doesn't have operator+
    add(40, m1);        // ERROR
    add(m1, m1);        // ERROR
}
```

**Problem**: `MyClass` doesn't define `operator+`, so the template instantiation fails for `MyClass` types.

**Solution**: Overload `operator+` in `MyClass`:
```cpp
MyClass operator+(const MyClass& other) {
    return MyClass(num + other.num);
}
```

---

## Key Points

1. **Write Once, Use Many**: One template serves all compatible types.

2. **Compile-Time**: Template instantiation happens at compile time.

3. **Type Deduction**: Compiler automatically determines the type from arguments.

4. **No Runtime Overhead**: Generated code is just as fast as hand-written.

5. **Operator Requirements**: Types must support operations used in template (e.g., `+` for add).

6. **Separate Versions**: Compiler generates distinct functions for each type used.

7. **typename vs class**: In template parameters, they are interchangeable.

8. **All Parameters Must Be Used**: Every template parameter should appear in the function signature.

---

## Interview Questions

### Q1: What is a function template?
**Answer**: A function template is a blueprint for creating functions that can work with different data types. The compiler generates specific versions of the function for each type used.

### Q2: Do templates save memory?
**Answer**: No, templates don't save runtime memory. The compiler still generates separate function versions for each type. Templates save developer time and reduce code duplication in source files.

### Q3: What is template instantiation?
**Answer**: Template instantiation is the process where the compiler generates a specific version of a template function (or class) when it's used with a particular type. For `add(5, 3)`, the compiler instantiates `add<int>`.

### Q4: What is the difference between `typename` and `class` in templates?
**Answer**: In template parameter lists, they are interchangeable:
```cpp
template <typename T>  // Same as
template <class T>     // this
```
`typename` is preferred in modern C++ as it's more descriptive.

### Q5: Can you use templates with user-defined types?
**Answer**: Yes, but the user-defined type must support all operations used in the template. If the template uses `+`, the class must overload `operator+`.

### Q6: What happens if you call a template function with incompatible types?
**Answer**: The compiler generates an error. For example, if a template uses `+` and you pass a type that doesn't support `+`, compilation fails.
