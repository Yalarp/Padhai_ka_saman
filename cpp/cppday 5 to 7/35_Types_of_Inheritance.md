# 🌳 Types of Inheritance in C++ - Complete Guide

## Table of Contents
1. [Overview of Inheritance Types](#overview-of-inheritance-types)
2. [Multi-Level Inheritance](#multi-level-inheritance)
3. [Hierarchical Inheritance](#hierarchical-inheritance)
4. [Multiple Inheritance](#multiple-inheritance)
5. [Constructor Order in Multiple Inheritance](#constructor-order-in-multiple-inheritance)
6. [Ambiguity in Multiple Inheritance](#ambiguity-in-multiple-inheritance)
7. [Key Takeaways](#key-takeaways)

---

## Overview of Inheritance Types

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       TYPES OF INHERITANCE IN C++                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  1. SINGLE INHERITANCE          2. MULTI-LEVEL INHERITANCE                  │
│         ┌───┐                          ┌───┐                                 │
│         │ A │                          │ A │                                 │
│         └─┬─┘                          └─┬─┘                                 │
│           │                              │                                   │
│           ▼                              ▼                                   │
│         ┌───┐                          ┌───┐                                 │
│         │ B │                          │ B │                                 │
│         └───┘                          └─┬─┘                                 │
│                                          │                                   │
│                                          ▼                                   │
│                                        ┌───┐                                 │
│                                        │ C │                                 │
│                                        └───┘                                 │
│                                                                              │
│  3. HIERARCHICAL INHERITANCE    4. MULTIPLE INHERITANCE                     │
│         ┌───┐                      ┌───┐   ┌───┐                             │
│         │ A │                      │ A │   │ B │                             │
│         └─┬─┘                      └─┬─┘   └─┬─┘                             │
│     ┌─────┼─────┐                    │       │                               │
│     ▼     ▼     ▼                    └───┬───┘                               │
│   ┌───┐ ┌───┐ ┌───┐                      ▼                                   │
│   │ B │ │ C │ │ D │                    ┌───┐                                 │
│   └───┘ └───┘ └───┘                    │ C │                                 │
│                                        └───┘                                 │
│                                                                              │
│  5. HYBRID INHERITANCE (Combination of above)                               │
│         ┌───┐                                                                │
│         │ A │                                                                │
│         └─┬─┘                                                                │
│     ┌─────┴─────┐                                                            │
│     ▼           ▼                                                            │
│   ┌───┐       ┌───┐                                                          │
│   │ B │       │ C │                                                          │
│   └─┬─┘       └─┬─┘                                                          │
│     └─────┬─────┘                                                            │
│           ▼                                                                  │
│         ┌───┐                                                                │
│         │ D │  ← Diamond Problem!                                            │
│         └───┘                                                                │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Multi-Level Inheritance

> **Multi-Level Inheritance**: A class inherits from a class that already inherits from another class, forming a chain.

```
    Base
      ↓
    Sub1 (inherits from Base)
      ↓
    Sub2 (inherits from Sub1)
      ↓
    Sub3 (inherits from Sub2)
```

### Complete Example: Multi-Level Inheritance

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: TOP-LEVEL base class
{
private:
    int num1 = 10;                      // Line 7: Private member
protected:
    int var = 20;                       // Line 9: Protected - accessible in children
public:
    void disp1()                        // Line 11: Public function
    {
        cout << num1 << "\t" << var << endl;  // Line 13: Print both
    }
    base(int num1)                      // Line 15: Parameterized constructor
    {
        this->num1 = num1;
        var = 100;                      // Line 18: Set protected member
        cout << "base param" << endl;
    }
    ~base()                             // Line 21: Destructor
    {
        cout << "base destr" << endl;
    }
};

class sub1 : protected base             // Line 27: LEVEL 1 - sub1 inherits from base
{                                       // protected inheritance: public→protected
private:
    int num2 = 30;                      // Line 30: sub1's own member
public:
    sub1() : base(0)                    // Line 32: Call base constructor
    {
        cout << "sub1 no-arg constr" << endl;
    }
    void disp2()                        // Line 36: sub1's function
    {
        disp1();                        // Line 38: Call inherited function (protected now)
        cout << num2 << endl;
        cout << "var is\t" << var << endl;  // Line 40: Access protected member
    }
    ~sub1()
    {
        cout << "sub1 destr" << endl;
    }
};

class sub2 : sub1                       // Line 48: LEVEL 2 - sub2 inherits from sub1
{                                       // private inheritance (default)
private:
    int num3 = 40;                      // Line 51: sub2's own member
public:
    sub2(int num3)                      // Line 53: Parameterized constructor
    {                                   // Implicitly calls sub1() which calls base(0)
        this->num3 = num3;
        cout << "sub2 param constr" << endl;
    }
    void disp3()                        // Line 59: sub2's function
    {
        cout << num3 << endl;           // Line 61: Access own member
        cout << var << endl;            // Line 62: Access protected (from base)
        disp1();                        // Line 63: Call base's function
        disp2();                        // Line 64: Call sub1's function
    }
    ~sub2()
    {
        cout << "sub2 destr" << endl;
    }
};

class sub3 : public sub2                // Line 72: LEVEL 3 - sub3 inherits from sub2
{
private:
    int num4 = 50;                      // Line 75: sub3's own member
public:
    sub3() : sub2(6)                    // Line 77: Call sub2 constructor
    {
        this->num4 = num4;
        cout << "sub3 param constr" << endl;
    }
    void disp4()                        // Line 82: sub3's function
    {
        cout << num4 << endl;
    }
    ~sub3()
    {
        cout << "sub3 destr" << endl;
    }
};

int main()                              // Line 92: Main function
{
    sub3 s;                             // Line 94: Create sub3 object
    s.disp3();                          // Line 95: Call disp3 (from sub2)
    s.disp4();                          // Line 96: Call disp4 (from sub3)
    return 0;
}
```

### Execution Flow Diagram

```
┌────────────────────────────────────────────────────────────────────────────┐
│                    CONSTRUCTOR CHAIN IN MULTI-LEVEL                         │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   sub3 s;                                                                   │
│   │                                                                         │
│   ├──► sub3() : sub2(6)              // sub3 constructor called             │
│   │    │                                                                    │
│   │    ├──► sub2(6)                  // sub2 constructor called             │
│   │    │    │                                                               │
│   │    │    ├──► sub1()              // sub1 constructor called (implicitly)│
│   │    │    │    │                                                          │
│   │    │    │    ├──► base(0)        // base constructor called             │
│   │    │    │    │    │                                                     │
│   │    │    │    │    └── OUTPUT: "base param"                             │
│   │    │    │    │                                                          │
│   │    │    │    └── OUTPUT: "sub1 no-arg constr"                          │
│   │    │    │                                                               │
│   │    │    └── OUTPUT: "sub2 param constr"                                │
│   │    │                                                                    │
│   │    └── OUTPUT: "sub3 param constr"                                     │
│                                                                             │
│   CONSTRUCTOR OUTPUT ORDER:                                                 │
│   base param                                                                │
│   sub1 no-arg constr                                                        │
│   sub2 param constr                                                         │
│   sub3 param constr                                                         │
│                                                                             │
│   DESTRUCTOR OUTPUT ORDER (reverse):                                        │
│   sub3 destr                                                                │
│   sub2 destr                                                                │
│   sub1 destr                                                                │
│   base destr                                                                │
│                                                                             │
└────────────────────────────────────────────────────────────────────────────┘
```

---

## Hierarchical Inheritance

> **Hierarchical Inheritance**: Multiple classes inherit from a SINGLE base class.

```
       base
      /  |  \
     /   |   \
   sub1 sub2 sub3
```

### Complete Example: Hierarchical Inheritance

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Common base class
{
private:
    int num1 = 10;                      // Line 7: Private member
protected:
    int var = 20;                       // Line 9: Protected member
public:
    void disp1()                        // Line 11: Public function
    {
        cout << num1 << "\t" << var << endl;
    }
    base(int num1)                      // Line 15: Parameterized constructor
    {
        this->num1 = num1;
        var = 100;
        cout << "base param" << endl;
    }
    ~base()
    {
        cout << "base destr" << endl;
    }
};

class sub1 : protected base             // Line 27: FIRST child of base
{
private:
    int num2 = 30;
public:
    sub1() : base(0)                    // Line 32: Call base(0)
    {
        cout << "sub1 no-arg constr" << endl;
    }
    void disp2()
    {
        disp1();                        // Protected - accessible inside sub1
        cout << num2 << endl;
        cout << "var is\t" << var << endl;
    }
    ~sub1()
    {
        cout << "sub1 destr" << endl;
    }
};

class sub2 : base                       // Line 48: SECOND child of base (private)
{
private:
    int num3 = 40;
public:
    sub2(int num3) : base(30)           // Line 53: Call base(30)
    {
        this->num3 = num3;
        cout << "sub2 param constr" << endl;
    }
    void disp3()
    {
        cout << num3 << endl;
        cout << var << endl;            // Access protected from base
        disp1();
    }
    ~sub2()
    {
        cout << "sub2 destr" << endl;
    }
};

class sub3 : public base                // Line 70: THIRD child of base (public)
{
private:
    int num4 = 50;
public:
    sub3() : base(6)                    // Line 75: Call base(6)
    {
        this->num4 = num4;
        cout << "sub3 param constr" << endl;
    }
    void disp4()
    {
        cout << num4 << endl;
        cout << var << endl;
    }
    ~sub3()
    {
        cout << "sub3 destr" << endl;
    }
};

int main()                              // Line 91: Main function
{
    sub1 s1;                            // Line 93: Create sub1 object
    s1.disp2();                         // Line 94: Call sub1's method
    
    sub2 s2(500);                       // Line 96: Create sub2 with value
    s2.disp3();                         // Line 97: Call sub2's method
    
    sub3 s3;                            // Line 99: Create sub3 object
    s3.disp1();                         // Line 100: PUBLIC inheritance - disp1 accessible!
    s3.disp4();                         // Line 101: Call sub3's method
    
    return 0;
}
```

### Key Observation

```
┌─────────────────────────────────────────────────────────────────┐
│          HIERARCHICAL INHERITANCE - KEY POINTS                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   base                                                           │
│   ├── sub1 (protected inheritance)                               │
│   │   └── s1.disp1() ❌ CANNOT call from outside                 │
│   │   └── Inside sub1, disp1() is accessible                    │
│   │                                                              │
│   ├── sub2 (private inheritance - default)                       │
│   │   └── s2.disp1() ❌ CANNOT call from outside                 │
│   │   └── Inside sub2, disp1() is accessible                    │
│   │                                                              │
│   └── sub3 (public inheritance)                                  │
│       └── s3.disp1() ✅ CAN call from outside                    │
│       └── Inside sub3, disp1() is accessible                    │
│                                                                  │
│   Each child class:                                              │
│   • Has its own copy of base class members                      │
│   • Independently inherits from base                            │
│   • Changes to one child don't affect others                    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Multiple Inheritance

> **Multiple Inheritance**: A class inherits from TWO OR MORE base classes.

```
   base1    base2    base3
      \       |       /
       \      |      /
        \     |     /
         \    |    /
            sub
```

### Example 1: Basic Multiple Inheritance

```cpp
/* 
 * In multiple inheritance, constructors are invoked in the 
 * ORDER OF INHERITANCE (left to right in the inheritance list)
 */

#include<iostream>                      // Line 6: Include iostream
using namespace std;                    // Line 7: Use standard namespace

class base1                             // Line 9: First base class
{
private:
    int num1 = 10;                      // Line 12: Private member
public:
    void disp1()                        // Line 14: Public function
    {
        cout << num1 << endl;
    }
    base1()                             // Line 18: Default constructor
    {
        cout << "base1 no-arg" << endl;
    }
    ~base1()
    {
        cout << "base1 destr" << endl;
    }
};

class base2                             // Line 28: Second base class
{
private:
    int num2 = 20;
public:
    void disp2()
    {
        cout << num2 << endl;
    }
    base2()
    {
        cout << "base2 no-arg" << endl;
    }
    ~base2()
    {
        cout << "base2 destr" << endl;
    }
};

class base3                             // Line 47: Third base class
{
private:
    int num3 = 30;
public:
    void disp3()
    {
        cout << num3 << endl;
    }
    base3()
    {
        cout << "base3 no-arg" << endl;
    }
    ~base3()
    {
        cout << "base3 destr" << endl;
    }
};

// sub inherits from base1 (private), base2 (public), base3 (protected)
class sub : base1, public base2, protected base3   // Line 67: Multiple inheritance
{
private:
    int num4 = 40;
public:
    sub()                               // Line 72: Constructor
    {
        cout << "sub no-arg constr" << endl;
    }
    void disp4()
    {
        disp1();                        // OK - private inherited, accessible inside
        disp3();                        // OK - protected inherited, accessible inside
        cout << num4 << endl;
    }
    ~sub()
    {
        cout << "sub destr" << endl;
    }
};

int main()                              // Line 88: Main function
{
    sub s;                              // Line 90: Create sub object
    s.disp2();                          // Line 91: OK - public inherited!
    s.disp4();                          // Line 92: OK - sub's own method
    return 0;
}
```

**Output:**
```
base1 no-arg
base2 no-arg
base3 no-arg
sub no-arg constr
20
10
30
40
sub destr
base3 destr
base2 destr
base1 destr
```

---

## Constructor Order in Multiple Inheritance

### Key Rule

> **Constructor Order**: Constructors are ALWAYS invoked in the **order of inheritance declaration**, NOT the order in the initializer list!

```cpp
#include<iostream>
using namespace std;

class base1 { public: base1(int) { cout << "base1 param" << endl; } };
class base2 { public: base2()    { cout << "base2 no-arg" << endl; } };
class base3 { public: base3(int) { cout << "base3 param" << endl; } };

// Inheritance order: base1, base2, base3
class sub : base1, public base2, protected base3
{
public:
    // Even though we write base3(10) first, base1(50) second...
    sub() : base3(10), base1(50)        // Order in initializer list DOESN'T matter!
    {
        cout << "sub no-arg constr" << endl;
    }
};

int main()
{
    sub s;
    return 0;
}
```

**Output:**
```
base1 param      ← base1 first (inheritance order)
base2 no-arg     ← base2 second (inheritance order)
base3 param      ← base3 third (inheritance order)
sub no-arg constr
```

### Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│        CONSTRUCTOR ORDER IN MULTIPLE INHERITANCE                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   class sub : base1, base2, base3                               │
│                  ↑      ↑      ↑                                │
│                  1      2      3   ← ORDER OF CONSTRUCTION      │
│                                                                  │
│   CONSTRUCTION:   base1 → base2 → base3 → sub                   │
│   DESTRUCTION:    sub → base3 → base2 → base1                   │
│                                                                  │
│   The initializer list order is IGNORED for construction order! │
│   Compiler may warn if initializer list order doesn't match     │
│   inheritance order.                                             │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Ambiguity in Multiple Inheritance

### The Problem: Same Member Name in Multiple Parents

```cpp
#include<iostream>
using namespace std;

class base1
{
public:
    void disp()                         // disp() in base1
    {
        cout << "base1 disp" << endl;
    }
};

class base2
{
public:
    void disp()                         // Same name disp() in base2!
    {
        cout << "base2 disp" << endl;
    }
};

class sub : base1, base2                // Inherits from both
{
public:
    void fun()
    {
        disp();                         // ERROR! Ambiguous - which disp()?
        cout << "fun" << endl;
    }
};

int main()
{
    sub s;
    s.fun();                            // Compilation ERROR!
    return 0;
}
```

### Error Message
```
error: request for member 'disp' is ambiguous
note: candidates are: void base2::disp()
                      void base1::disp()
```

### The Solution: Scope Resolution Operator

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base1                             // Line 4: First base class
{
public:
    void disp()                         // Line 7: disp() in base1
    {
        cout << "base1 disp" << endl;
    }
};

class base2                             // Line 13: Second base class
{
public:
    void disp()                         // Line 16: disp() in base2
    {
        cout << "base2 disp" << endl;
    }
};

class sub : base1, base2                // Line 22: Multiple inheritance
{
public:
    void fun()                          // Line 25: sub's function
    {
        base1::disp();                  // Line 27: Explicitly call base1's disp()
        base2::disp();                  // Line 28: Explicitly call base2's disp()
        cout << "fun" << endl;
    }
};

int main()                              // Line 33: Main function
{
    sub s;                              // Line 35: Create sub object
    s.fun();                            // Line 36: Call fun()
    return 0;
}
```

**Output:**
```
base1 disp
base2 disp
fun
```

### Diagram: Resolving Ambiguity

```
┌─────────────────────────────────────────────────────────────────┐
│            RESOLVING AMBIGUITY IN MULTIPLE INHERITANCE           │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│        base1::disp()           base2::disp()                     │
│              ↑                       ↑                           │
│              │                       │                           │
│              └───────────┬───────────┘                           │
│                          │                                       │
│                    ┌─────┴─────┐                                 │
│                    │    sub    │                                 │
│                    │           │                                 │
│                    │  fun() {  │                                 │
│                    │    base1::disp();  ← Explicit call          │
│                    │    base2::disp();  ← Explicit call          │
│                    │  }        │                                 │
│                    └───────────┘                                 │
│                                                                  │
│   USE SCOPE RESOLUTION OPERATOR (::) TO SPECIFY WHICH VERSION   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Memory Layout in Multiple Inheritance

```
┌─────────────────────────────────────────────────────────────────┐
│         MEMORY LAYOUT: sub object in Multiple Inheritance       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   sub object                                                     │
│   ┌──────────────────────────────────────────────────────────┐   │
│   │  base1 sub-object                                        │   │
│   │  ┌────────────────────────────────────────────────────┐  │   │
│   │  │  num1 = 10                                         │  │   │
│   │  │  (other base1 members)                             │  │   │
│   │  └────────────────────────────────────────────────────┘  │   │
│   │                                                          │   │
│   │  base2 sub-object                                        │   │
│   │  ┌────────────────────────────────────────────────────┐  │   │
│   │  │  num2 = 20                                         │  │   │
│   │  │  (other base2 members)                             │  │   │
│   │  └────────────────────────────────────────────────────┘  │   │
│   │                                                          │   │
│   │  base3 sub-object                                        │   │
│   │  ┌────────────────────────────────────────────────────┐  │   │
│   │  │  num3 = 30                                         │  │   │
│   │  │  (other base3 members)                             │  │   │
│   │  └────────────────────────────────────────────────────┘  │   │
│   │                                                          │   │
│   │  sub's own members                                       │   │
│   │  ┌────────────────────────────────────────────────────┐  │   │
│   │  │  num4 = 40                                         │  │   │
│   │  └────────────────────────────────────────────────────┘  │   │
│   └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────────────┐
│              TYPES OF INHERITANCE - KEY POINTS                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  MULTI-LEVEL INHERITANCE:                                        │
│  • Chain: A → B → C → D                                         │
│  • Constructor order: A → B → C → D                             │
│  • Destructor order:  D → C → B → A                             │
│  • Protected members remain accessible through chain            │
│                                                                  │
│  HIERARCHICAL INHERITANCE:                                       │
│  • One parent, multiple children: A → (B, C, D)                  │
│  • Each child independently inherits from parent                │
│  • Children don't affect each other                             │
│                                                                  │
│  MULTIPLE INHERITANCE:                                           │
│  • One child, multiple parents: (A, B, C) → D                   │
│  • Constructor order = ORDER OF INHERITANCE DECLARATION         │
│  • NOT the order in initializer list!                           │
│                                                                  │
│  AMBIGUITY RESOLUTION:                                           │
│  • Same member name in multiple parents = AMBIGUOUS             │
│  • Solution: Use scope resolution operator                      │
│  • Syntax: ParentClass::memberName                              │
│                                                                  │
│  ACCESS SPECIFIERS IN LIST:                                      │
│  class sub : base1, public base2, protected base3               │
│              ↑          ↑              ↑                        │
│           private    public       protected                     │
│           (default)                                              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Previous: [Single Inheritance](34_Single_Inheritance.md)*
*Next: [Hybrid Inheritance and Diamond Problem](36_Hybrid_Inheritance_Diamond_Problem.md)*
