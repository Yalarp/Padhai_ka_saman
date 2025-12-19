# C++ Namespaces

## Table of Contents
- [Introduction](#introduction)
- [Purpose of Namespaces](#purpose-of-namespaces)
- [Syntax and Declaration](#syntax-and-declaration)
- [Accessing Namespace Members](#accessing-namespace-members)
- [Nested Namespaces](#nested-namespaces)
- [Namespaces in Libraries](#namespaces-in-libraries)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

Namespaces in C++ are used to organize code into logical groups and prevent name collisions that can occur when multiple libraries define functions, classes, or variables with the same name.

> **Definition**: A namespace creates a separate region for a group of variables, functions, classes, and other identifiers.

---

## Purpose of Namespaces

| Problem | Namespace Solution |
|---------|-------------------|
| Name collisions between libraries | Each library uses its own namespace |
| Ambiguity with common names | Qualify names with namespace |
| Code organization | Group related code logically |
| Scalability | Large projects can avoid conflicts |

### Example of Name Collision Problem:
```cpp
// Library A
class MyClass { void display(); };

// Library B
class MyClass { void display(); };  // Conflict!

// Using both libraries causes error!
```

---

## Syntax and Declaration

### Basic Namespace Declaration

```cpp
namespace namespace_name
{
    // Declarations of classes, functions, variables
    class MyClass { };
    void myFunction() { }
    int myVariable;
}
```

### Standard Namespace `std`

The C++ Standard Library uses the namespace `std`:
```cpp
std::cout << "Hello";
std::string name;
std::vector<int> numbers;
```

---

## Accessing Namespace Members

### Method 1: Scope Resolution Operator (::)

```cpp
namespace_name::class_name object;
namespace_name::function_name();
```

### Method 2: using directive

```cpp
using namespace namespace_name;  // Makes all members available
class_name object;               // No prefix needed
```

### Method 3: using declaration

```cpp
using namespace_name::specific_member;  // Makes only one member available
specific_member();  // Use directly
```

---

## Nested Namespaces

Namespaces can be nested inside each other:

```cpp
namespace outer
{
    namespace inner
    {
        class MyClass
        {
            void method() { }
        };
    }
}

// Access with:
outer::inner::MyClass obj;

// Or with:
using namespace outer::inner;
MyClass obj;
```

---

## Namespaces in Libraries

When creating libraries, namespaces organize code across header and implementation files:

### Header File (Header1.h)
```cpp
namespace group1
{
    class First
    {
    private:
        int num;
    public:
        First();
        First(int);
        ~First();
        void setNum(int);
        int getNum();
    };
}
```

### Implementation File
```cpp
#include "Header1.h"
using namespace std;

group1::First::First()
{
    cout << "in no-arg const" << endl;
    num = 0;
}

group1::First::First(int num)
{
    cout << "in parameterized const" << endl;
    this->num = num;
}

void group1::First::setNum(int num)
{
    this->num = num;
}

int group1::First::getNum()
{
    return num;
}
```

### Client Code
```cpp
#include "Header1.h"
using namespace group1;

int main()
{
    First f1;           // Uses group1::First
    f1.setNum(200);
    cout << f1.getNum() << endl;
}
```

---

## Code Examples

### Example 1: Avoiding Name Conflicts

```cpp
#include<iostream>      // Line 1
using namespace std;    // Line 2

namespace com_sun       // Line 4: First namespace
{
    class myclass       // Line 6: Class in com_sun
    {
    public:
        void disp()     // Line 9
        {
            cout << endl << "in disp of myclass of com_sun" << endl;
        }
    };
}

namespace com_oracle    // Line 16: Second namespace
{
    class myclass       // Line 18: Same class name, different namespace!
    {
    public:
        void disp()
        {
            cout << endl << "in disp of myclass of com_oracle" << endl;
        }
    };
}

int main()
{
    com_sun::myclass m1;      // Line 29: Use scope resolution
    m1.disp();                 // Line 30: Calls com_sun version

    com_oracle::myclass m2;    // Line 32: Use scope resolution
    m2.disp();                 // Line 33: Calls com_oracle version
}
```

### Execution Flow:

```
Start
  │
  ▼
┌─────────────────────────────────────┐
│ com_sun::myclass m1 created         │
│ m1.disp() outputs:                  │
│ "in disp of myclass of com_sun"     │
└─────────────┬───────────────────────┘
              │
              ▼
┌─────────────────────────────────────┐
│ com_oracle::myclass m2 created      │
│ m2.disp() outputs:                  │
│ "in disp of myclass of com_oracle"  │
└─────────────┬───────────────────────┘
              │
              ▼
       Program Ends
```

### Example 2: Using Directive with Scope

```cpp
#include<iostream>
using namespace std;

namespace com_sun
{
    class myclass
    {
    public:
        void disp()
        {
            cout << endl << "in disp of myclass of com_sun" << endl;
        }
    };
}

namespace com_oracle
{
    class myclass
    {
    public:
        void disp()
        {
            cout << endl << "in disp of myclass of com_oracle" << endl;
        }
    };
}

int main()
{
    {   // Inner scope block
        using namespace com_sun;  // Line 34: Active only in this block
        myclass m1;               // Line 35: Resolves to com_sun::myclass
        m1.disp();
    }   // Line 37: com_sun goes out of scope

    using namespace com_oracle;   // Line 39: Now use com_oracle
    myclass m2;                   // Line 40: Resolves to com_oracle::myclass
    m2.disp();
}
```

### Key Insight:
- The `using namespace` inside `{ }` is limited to that block
- After the block ends, that namespace is no longer in effect

### Example 3: Nested Namespaces

```cpp
namespace com_sun
{
    namespace dev
    {
        class myclass
        {
        public:
            void disp()
            {
                cout << endl << "in disp of myclass of com_sun::dev" << endl;
            }
        };
    }
}

int main()
{
    // Method 1: Full qualification
    com_sun::dev::myclass m1;
    m1.disp();

    // Method 2: Using directive
    using namespace com_sun::dev;
    myclass m2;
    m2.disp();
    
    return 0;
}
```

---

## Key Points

1. **Purpose**: Prevent name collisions and organize code logically.

2. **std Namespace**: The C++ Standard Library uses `std`.

3. **Access Methods**:
   - Scope resolution: `namespace::member`
   - Using directive: `using namespace name;`
   - Using declaration: `using name::member;`

4. **Scope of using**: Can be limited to a block `{ }`.

5. **Nesting**: Namespaces can be nested.

6. **Libraries**: Use namespaces to avoid conflicts with client code.

7. **Anonymous Namespaces**: Create file-local names (alternative to `static`).

---

## Interview Questions

### Q1: What is a namespace in C++?
**Answer**: A namespace is a declarative region that provides a scope for identifiers (names of types, functions, variables, etc.). It is used to organize code and prevent name collisions, especially when using multiple libraries.

### Q2: What is the purpose of `using namespace std;`?
**Answer**: It brings all names from the `std` namespace into the current scope, allowing you to use `cout`, `cin`, `string`, `vector`, etc. without the `std::` prefix.

### Q3: What is the difference between `using namespace` and `using` declaration?
**Answer**:
| `using namespace X;` | `using X::member;` |
|---------------------|-------------------|
| Imports all names from namespace X | Imports only one specific member |
| Can cause more conflicts | More controlled, safer |
| Less specific | More specific |

### Q4: How do you access a class inside a nested namespace?
**Answer**: Use the scope resolution operator for each level:
```cpp
outer::inner::ClassName obj;
// Or
using namespace outer::inner;
ClassName obj;
```

### Q5: Can you have the same class name in different namespaces?
**Answer**: Yes! That's the main purpose of namespaces. Each class is distinct and accessed through its namespace qualifier.

### Q6: What is an anonymous namespace?
**Answer**: A namespace without a name that makes its contents visible only within the current translation unit (file):
```cpp
namespace {
    int localVar;  // Only visible in this file
}
```
