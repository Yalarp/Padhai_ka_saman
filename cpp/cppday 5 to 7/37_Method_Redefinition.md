# 🔄 Method Redefinition in C++

## Table of Contents
1. [What is Redefinition?](#what-is-redefinition)
2. [Basic Redefinition Example](#basic-redefinition-example)
3. [Calling Parent Class Version](#calling-parent-class-version)
4. [Name Hiding in Inheritance](#name-hiding-in-inheritance)
5. [Calling Base Method from Derived Method](#calling-base-method-from-derived-method)
6. [Key Takeaways](#key-takeaways)

---

## What is Redefinition?

> **Redefinition**: Re-writing a function (non-virtual) of the base class inside the sub class with the same signature.

```
┌─────────────────────────────────────────────────────────────────┐
│                    REDEFINITION CONCEPT                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   class Base                      class Sub : public Base        │
│   {                               {                              │
│   public:                         public:                        │
│       void disp()    ───────────>     void disp()  ← REDEFINES  │
│       {                               {                          │
│           // base impl                    // new impl            │
│       }                               }                          │
│   };                              };                             │
│                                                                  │
│   When Sub::disp() is called, it HIDES Base::disp()             │
│                                                                  │
│   IMPORTANT: This is NOT the same as Overriding!                │
│   • Redefinition = non-virtual functions                        │
│   • Overriding = virtual functions (covered later)              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Basic Redefinition Example

### Example 1: Simple Redefinition

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Base class
{
public:
    void disp()                         // Line 7: Base class disp()
    {
        cout << "base disp" << endl;    // Line 9: Base implementation
    }
};

class sub : public base                 // Line 13: sub inherits from base
{
public:
    void disp()                         // Line 16: REDEFINITION of disp()
    {                                   // Same name, same signature
        cout << "sub disp" << endl;     // Line 18: Different implementation
    }
};

int main()                              // Line 22: Main function
{
    sub s;                              // Line 24: Create sub object
    s.disp();                           // Line 25: Calls SUB's disp() - "sub disp"
    return 0;
}
```

**Output:**
```
sub disp
```

### Execution Flow

```
┌────────────────────────────────────────────────────────────────┐
│                     EXECUTION FLOW                              │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│   sub s;                                                        │
│   │                                                             │
│   └──► Object 's' created with both base and sub parts         │
│                                                                 │
│   s.disp();                                                     │
│   │                                                             │
│   ├──► Compiler looks for disp() in 'sub' class                │
│   ├──► Found! sub::disp() exists                               │
│   └──► Calls sub::disp() - base::disp() is HIDDEN              │
│                                                                 │
│   Output: "sub disp"                                            │
│                                                                 │
│   Note: base::disp() still EXISTS, but is not called           │
│         because sub::disp() HIDES it                            │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

---

## Calling Parent Class Version

### Example 2: Using Scope Resolution to Call Base Version

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Base class
{
public:
    void disp()                         // Line 7: Base class disp()
    {
        cout << "base disp" << endl;
    }
};

class sub : public base                 // Line 13: sub inherits from base
{
public:
    void disp()                         // Line 16: Redefinition
    {
        cout << "sub disp" << endl;
    }
};

int main()                              // Line 22: Main function
{
    sub s;                              // Line 24: Create sub object
    s.disp();                           // Line 25: Calls sub::disp() → "sub disp"
    s.base::disp();                     // Line 26: Explicitly call base::disp() → "base disp"
    return 0;
}
```

**Output:**
```
sub disp
base disp
```

### How Scope Resolution Works

```
┌─────────────────────────────────────────────────────────────────┐
│           CALLING PARENT VERSION WITH ::                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   sub s;                                                         │
│   │                                                              │
│   │  s.disp();                                                   │
│   │  └── Calls sub::disp()                                       │
│   │      └── Output: "sub disp"                                  │
│   │                                                              │
│   │  s.base::disp();                                             │
│   │  └── Scope Resolution (::) specifies EXACTLY which          │
│   │      class's version to call                                 │
│   │  └── Calls base::disp() directly                            │
│   │      └── Output: "base disp"                                 │
│   │                                                              │
│   Both methods exist in the object!                              │
│   Scope resolution lets you choose which one to call.           │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Name Hiding in Inheritance

### Important Concept

> **Name Hiding**: When a derived class has a function with the same NAME (regardless of parameters), ALL base class functions with that name become hidden!

### Example 3: Name Hiding with Different Parameters

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Base class
{
public:
    void disp()                         // Line 7: disp() with NO parameters
    {
        cout << "base disp" << endl;
    }
};

class sub : public base                 // Line 13: sub inherits from base
{
public:
    void disp(int k)                    // Line 16: disp() with DIFFERENT signature!
    {                                   // This has int parameter
        cout << "sub disp" << endl;
    }
};

int main()                              // Line 22: Main function
{
    sub s;
    // s.disp();                        // Line 25: ERROR! base::disp() is HIDDEN!
    s.disp(10);                         // Line 26: OK - calls sub::disp(int)
    s.base::disp();                     // Line 27: OK - explicitly call base version
    return 0;
}
```

**Output:**
```
sub disp
base disp
```

### Diagram: Name Hiding

```
┌─────────────────────────────────────────────────────────────────┐
│                    NAME HIDING IN C++                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   Base Class:                                                    │
│   ┌─────────────────────────────────┐                            │
│   │  void disp()  ← no parameters   │                            │
│   └─────────────────────────────────┘                            │
│                   │                                              │
│                   │ inherits                                     │
│                   ▼                                              │
│   Sub Class:                                                     │
│   ┌─────────────────────────────────┐                            │
│   │  void disp(int k)  ← parameter  │                            │
│   └─────────────────────────────────┘                            │
│                                                                  │
│   sub s;                                                         │
│   s.disp();      // ERROR! Name 'disp' found in sub,            │
│                  // but sub::disp needs an int argument          │
│                  // base::disp() is HIDDEN                       │
│                                                                  │
│   s.disp(10);    // OK - matches sub::disp(int)                 │
│   s.base::disp();// OK - explicit call to base version          │
│                                                                  │
│   NOTE: This is NOT overloading across inheritance!             │
│         The derived class function HIDES all base functions     │
│         with the same NAME.                                      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Example 4: Base Has Parameters, Sub Doesn't

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Base class
{
public:
    void disp(int k)                    // Line 7: disp() WITH parameter
    {
        cout << "base disp with arg" << endl;
    }
};

class sub : public base                 // Line 13: sub inherits from base
{
public:
    void disp()                         // Line 16: disp() WITHOUT parameter
    {                                   // HIDES base::disp(int)!
        cout << "sub disp w/o arg" << endl;
    }
};

int main()                              // Line 22: Main function
{
    sub s;
    s.disp();                           // Line 25: OK - calls sub::disp()
    // s.disp(10);                      // Line 26: ERROR! base::disp(int) is HIDDEN!
    s.base::disp(10);                   // Line 27: OK - explicitly call base version
    return 0;
}
```

**Output:**
```
sub disp w/o arg
base disp with arg
```

---

## Calling Base Method from Derived Method

### Example 5: Invoking Base Version Inside Redefined Method

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Base class
{
public:
    void disp()                         // Line 7: Base disp()
    {
        cout << "base disp with arg" << endl;
    }
};

class sub : public base                 // Line 13: sub inherits from base
{
public:
    void disp()                         // Line 16: Redefinition
    {
        cout << "sub disp w/o arg" << endl;  // Line 18: Sub's own work
        base::disp();                   // Line 19: ALSO call base version!
    }
};

int main()                              // Line 23: Main function
{
    sub s;
    s.disp();                           // Line 26: Calls sub::disp()
                                        // which internally calls base::disp()
    
    s.base::disp();                     // Line 29: Can still call directly
    return 0;
}
```

**Output:**
```
sub disp w/o arg
base disp with arg
base disp with arg
```

### Execution Flow

```
┌────────────────────────────────────────────────────────────────┐
│              CALLING BASE FROM DERIVED                          │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│   s.disp();                                                     │
│   │                                                             │
│   └──► sub::disp() executes:                                    │
│        ├── cout << "sub disp w/o arg"  → Output: sub disp w/o arg│
│        │                                                        │
│        └── base::disp() is called internally                    │
│            └── cout << "base disp with arg"                    │
│                → Output: base disp with arg                    │
│                                                                 │
│   s.base::disp();                                               │
│   │                                                             │
│   └──► base::disp() executes directly:                          │
│        └── cout << "base disp with arg"                        │
│            → Output: base disp with arg                        │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

### Common Pattern: Extending Base Behavior

```cpp
class sub : public base
{
public:
    void disp()
    {
        // Do sub-specific pre-processing
        cout << "Before base work..." << endl;
        
        base::disp();  // Call base implementation
        
        // Do sub-specific post-processing
        cout << "After base work..." << endl;
    }
};
```

---

## Redefinition vs Overriding (Preview)

```
┌─────────────────────────────────────────────────────────────────┐
│            REDEFINITION vs OVERRIDING (Preview)                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   REDEFINITION:                                                  │
│   • Base function is NON-virtual                                │
│   • Binding at COMPILE time (Early Binding)                     │
│   • Which function called depends on POINTER/REFERENCE TYPE     │
│   • Base version is HIDDEN, not replaced                        │
│                                                                  │
│   class Base {                                                   │
│   public:                                                        │
│       void func() { }  // NON-virtual                           │
│   };                                                             │
│                                                                  │
│   class Sub : public Base {                                      │
│   public:                                                        │
│       void func() { }  // REDEFINITION                          │
│   };                                                             │
│                                                                  │
│   ─────────────────────────────────────────────────────────────  │
│                                                                  │
│   OVERRIDING (Covered in Virtual Functions chapter):             │
│   • Base function is VIRTUAL                                    │
│   • Binding at RUNTIME (Late Binding)                           │
│   • Which function called depends on ACTUAL OBJECT TYPE         │
│   • Base version is REPLACED in derived class                   │
│                                                                  │
│   class Base {                                                   │
│   public:                                                        │
│       virtual void func() { }  // VIRTUAL                       │
│   };                                                             │
│                                                                  │
│   class Sub : public Base {                                      │
│   public:                                                        │
│       void func() { }  // OVERRIDING                            │
│   };                                                             │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────────────┐
│              METHOD REDEFINITION - KEY POINTS                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. REDEFINITION:                                                │
│     • Re-writing a non-virtual base function in derived class  │
│     • Derived version HIDES base version                        │
│     • Uses EARLY BINDING (compile-time decision)               │
│                                                                  │
│  2. NAME HIDING:                                                 │
│     • ANY function with same NAME in derived class              │
│       hides ALL base functions with that name                   │
│     • Even if parameters are different!                         │
│     • This is NOT overloading across inheritance               │
│                                                                  │
│  3. ACCESSING HIDDEN BASE FUNCTION:                              │
│     • Use scope resolution: s.base::disp();                     │
│     • Can call from outside OR inside derived class            │
│                                                                  │
│  4. COMMON PATTERN:                                              │
│     • Derived function calls base function internally           │
│     • base::function() inside derived function                 │
│     • Allows extending base behavior                            │
│                                                                  │
│  5. VS OVERRIDING:                                               │
│     • Redefinition = non-virtual (compile-time)                │
│     • Overriding = virtual (runtime)                           │
│     • Overriding covered in polymorphism chapter               │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Previous: [Hybrid Inheritance and Diamond Problem](36_Hybrid_Inheritance_Diamond_Problem.md)*
*Next: [Aggregation and Composition](38_Aggregation_Composition.md)*
