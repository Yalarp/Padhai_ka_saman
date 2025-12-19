# 🔗 Single Inheritance in C++ - Complete Guide

## Table of Contents
1. [What is Single Inheritance?](#what-is-single-inheritance)
2. [Basic Single Inheritance Examples](#basic-single-inheritance-examples)
3. [Constructor and Destructor Behavior](#constructor-and-destructor-behavior)
4. [Invoking Parent Constructor Explicitly](#invoking-parent-constructor-explicitly)
5. [Parent Constructor: Private vs Protected](#parent-constructor-private-vs-protected)
6. [Making Inherited Members Public](#making-inherited-members-public)
7. [Friend Functions and Inheritance](#friend-functions-and-inheritance)
8. [Key Takeaways](#key-takeaways)

---

## What is Single Inheritance?

> **Single Inheritance**: When a class inherits from only ONE parent class.

```
┌─────────────────────────────────────────────────────────────┐
│                    SINGLE INHERITANCE                        │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│                   ┌─────────────┐                            │
│                   │  Base Class │                            │
│                   │   (Parent)  │                            │
│                   └──────┬──────┘                            │
│                          │                                   │
│                          │ inherits                          │
│                          ▼                                   │
│                   ┌─────────────┐                            │
│                   │Derived Class│                            │
│                   │   (Child)   │                            │
│                   └─────────────┘                            │
│                                                              │
│   Syntax: class Derived : access_specifier Base { }          │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Basic Single Inheritance Examples

### Example 1: Basic Inheritance (Private by Default)

```cpp
#include<iostream>                      // Line 1: Include iostream header
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Define base class
{
private:                                // Line 6: Private section
    int num1 = 10;                      // Line 7: Private member with default value
public:                                 // Line 8: Public section
    void disp1()                        // Line 9: Public member function
    {
        cout << num1 << endl;           // Line 11: Prints private member (allowed within class)
    }
};

class sub : base                        // Line 15: sub inherits from base (PRIVATE inheritance by default!)
{
private:
    int num2 = 20;                      // Line 18: sub's own private member
public:
    void disp2()                        // Line 20: sub's own public function
    {
        cout << num2 << endl;           // Line 22: Print sub's member
    }
};

int main()                              // Line 26: Main function
{
    sub s;                              // Line 28: Create sub object
    s.disp2();                          // Line 29: OK - prints 20
    // s.disp1();                       // Line 30: ERROR! disp1 is private in sub (private inheritance)
    return 0;                           // Line 31: Return 0
}
```

**Output:**
```
20
```

> [!WARNING]
> Since no access specifier is given (`class sub : base`), it's **private inheritance** by default.
> This means `disp1()` becomes private in `sub` and cannot be called from outside!

---

### Example 2: Calling Inherited Function Internally

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Base class definition
{
private:
    int num1 = 10;                      // Line 7: Private - only accessible within base
public:
    void disp1()                        // Line 9: Public function
    {
        cout << num1 << endl;           // Line 11: Print num1
    }
};

class sub : base                        // Line 15: Private inheritance (default)
{
private:
    int num2 = 20;                      // Line 18: sub's private member
public:
    void disp2()                        // Line 20: sub's public function
    {
        disp1();                        // Line 22: OK! disp1 is accessible INSIDE sub class
        cout << num2 << endl;           // Line 23: Print num2
    }
};

int main()                              // Line 27: Main function
{
    sub s;                              // Line 29: Create sub object
    s.disp2();                          // Line 30: Call disp2, which internally calls disp1
    return 0;                           // Line 31: Return 0
}
```

**Output:**
```
10
20
```

### Execution Flow Diagram

```
┌────────────────────────────────────────────────────────────────┐
│                     EXECUTION FLOW                              │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│   main()                                                        │
│   │                                                             │
│   ├──► sub s;                                                   │
│   │    └── Object created with num1=10, num2=20                │
│   │                                                             │
│   └──► s.disp2();                                               │
│        │                                                        │
│        ├──► disp1();  [called internally - allowed]             │
│        │    └── cout << num1  →  Output: 10                    │
│        │                                                        │
│        └──► cout << num2  →  Output: 20                        │
│                                                                 │
│   Final Output:                                                 │
│   10                                                            │
│   20                                                            │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

---

## Constructor and Destructor Behavior

### What Happens When You Instantiate Child Class?

```
┌─────────────────────────────────────────────────────────────────┐
│       CONSTRUCTOR/DESTRUCTOR ORDER IN INHERITANCE               │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   CONSTRUCTION ORDER (Bottom to Top Call, Top to Bottom Execute) │
│   1. Control goes to child class constructor                    │
│   2. From there, parent class constructor is invoked (implicitly)│
│   3. Parent constructor executes completely                     │
│   4. Then child constructor body executes                       │
│                                                                  │
│   DESTRUCTION ORDER (Reverse of Construction)                   │
│   1. Child destructor executes first                            │
│   2. Then parent destructor executes                            │
│                                                                  │
│   Memory Model:                                                  │
│   ┌──────────────────────────────────────┐                       │
│   │  Sub Object                          │                       │
│   │  ┌────────────────────────────────┐  │                       │
│   │  │  Base part (num1)              │  │ ← Constructed first   │
│   │  └────────────────────────────────┘  │                       │
│   │  ┌────────────────────────────────┐  │                       │
│   │  │  Sub's own part (num2)         │  │ ← Constructed second  │
│   │  └────────────────────────────────┘  │                       │
│   └──────────────────────────────────────┘                       │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Example 3: Constructor and Destructor Order

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Base class
{
private:
    int num1 = 10;                      // Line 7: Private member
public:
    void disp1()                        // Line 9: Display function
    {
        cout << num1 << endl;
    }
    base()                              // Line 13: Default constructor
    {
        cout << "base no-arg" << endl;  // Line 15: Print message
    }
    ~base()                             // Line 17: Destructor
    {
        cout << "base destr" << endl;   // Line 19: Print message
    }
};

class sub : base                        // Line 23: sub inherits from base
{
private:
    int num2 = 20;                      // Line 26: Private member
public:
    sub()                               // Line 28: Default constructor
    {
        cout << "sub no-arg" << endl;   // Line 30: Print message
    }
    void disp2()                        // Line 32: Display function
    {
        disp1();
        cout << num2 << endl;
    }
    ~sub()                              // Line 37: Destructor
    {
        cout << "sub destr" << endl;    // Line 39: Print message
    }
};

int main()                              // Line 43: Main function
{
    sub s;                              // Line 45: Create sub object
    s.disp2();                          // Line 46: Call disp2
    return 0;                           // Line 47: Return - triggers destructors
}
```

**Output:**
```
base no-arg
sub no-arg
10
20
sub destr
base destr
```

### Detailed Execution Flow

```
┌────────────────────────────────────────────────────────────────┐
│                 DETAILED EXECUTION FLOW                         │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│   sub s;                                                        │
│   │                                                             │
│   ├──► sub()               // Control enters sub's constructor  │
│   │    │                                                        │
│   │    └──► base()         // Implicitly calls base constructor │
│   │         │                                                   │
│   │         └── cout << "base no-arg"   OUTPUT: base no-arg    │
│   │                                                             │
│   │    └── cout << "sub no-arg"        OUTPUT: sub no-arg      │
│   │                                                             │
│   s.disp2();                                                    │
│   │                                                             │
│   ├──► disp1()             OUTPUT: 10                          │
│   └──► cout << num2        OUTPUT: 20                          │
│                                                                 │
│   return 0;  // Object goes out of scope                        │
│   │                                                             │
│   ├──► ~sub()              OUTPUT: sub destr                   │
│   └──► ~base()             OUTPUT: base destr                  │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

---

## Invoking Parent Constructor Explicitly

### Problem: Parent Without No-Arg Constructor

```cpp
// Example 4: This will cause an ERROR!
#include<iostream>
using namespace std;

class base
{
private:
    int num1;
public:
    void disp1()
    {
        cout << num1 << endl;
    }
    base(int num1)                      // Only parameterized constructor!
    {
        this->num1 = num1;
        cout << "base param" << endl;
    }
    ~base()
    {
        cout << "base destr" << endl;
    }
};

class sub : base
{
private:
    int num2;
public:
    sub()                               // ERROR! Cannot find base::base()
    {
        cout << "sub no-arg" << endl;
    }
    ~sub()
    {
        cout << "sub destr" << endl;
    }
};

int main()
{
    sub s;  // ERROR: base does not have default constructor!
    return 0;
}
```

> [!CAUTION]
> If parent class does NOT have a no-arg constructor, the child class constructor must explicitly invoke the parent's parameterized constructor!

### Example 5: Solution - Explicit Constructor Call

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Base class
{
private:
    int num1;                           // Line 7: Private member (no default value)
public:
    void disp1()                        // Line 9: Display function
    {
        cout << num1 << endl;
    }
    base(int num1)                      // Line 13: ONLY parameterized constructor
    {
        this->num1 = num1;              // Line 15: Initialize num1
        cout << "base param" << endl;   // Line 16: Print message
    }
    ~base()
    {
        cout << "base destr" << endl;
    }
};

class sub : base                        // Line 24: sub inherits from base
{
private:
    int num2;                           // Line 27: Private member
public:
    sub() : base(10)                    // Line 29: Explicitly call base(int) with value 10!
    {                                   //          This is called "Member Initializer List"
        cout << "sub no-arg" << endl;   // Line 31: Print message
    }
    void disp2()
    {
        disp1();
        cout << num2 << endl;
    }
    ~sub()
    {
        cout << "sub destr" << endl;
    }
};

int main()                              // Line 44: Main function
{
    sub s;                              // Line 46: Now it works!
    s.disp2();                          // Line 47: Call disp2
    return 0;
}
```

**Output:**
```
base param
sub no-arg
10
-858993460
sub destr
base destr
```

> [!NOTE]
> num2 shows garbage value because it's not initialized! Always initialize your members.

### Example 6: All Constructors Must Call Parent Explicitly

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

/* 
 * RULE: If parent class does not have no-arg constructor,
 * ALL constructors of child class should invoke the 
 * parameterized constructor of parent explicitly.
 */

class base                              // Line 10: Base class
{
private:
    int num1;                           // Line 13: Private member
public:
    void disp1()
    {
        cout << num1 << endl;
    }
    base(int num1)                      // Line 19: Only param constructor
    {
        this->num1 = num1;
        cout << "base param" << endl;
    }
    ~base()
    {
        cout << "base destr" << endl;
    }
};

class sub : base                        // Line 30: sub inherits from base
{
private:
    int num2;                           // Line 33: Private member
public:
    sub() : base(10)                    // Line 35: Default constructor
    {
        cout << "sub no-arg" << endl;
    }
    sub(int num2) : base(20)            // Line 39: Parameterized constructor
    {                                   //          MUST also call base constructor!
        this->num2 = num2;
    }
    void disp2()
    {
        disp1();
        cout << num2 << endl;
    }
    ~sub()
    {
        cout << "sub destr" << endl;
    }
};

int main()                              // Line 53: Main function
{
    sub s;                              // Line 55: Uses sub()
    s.disp2();
    return 0;
}
```

---

## Example 7-12: Different Inheritance Modes

### Example 7: Public Inheritance - External Access

```cpp
#include<iostream>
using namespace std;

class base
{
private:
    int num1;
public:
    void disp1()
    {
        cout << num1 << endl;
    }
    base(int num1)
    {
        this->num1 = num1;
        cout << "base param" << endl;
    }
    ~base()
    {
        cout << "base destr" << endl;
    }
};

class sub : public base                 // PUBLIC inheritance - disp1 remains public!
{
private:
    int num2;
public:
    sub() : base(10)
    {
        cout << "sub no-arg" << endl;
    }
    sub(int num2) : base(20)
    {
        this->num2 = num2;
    }
    void disp2()
    {
        cout << num2 << endl;
    }
    ~sub()
    {
        cout << "sub destr" << endl;
    }
};

int main()
{
    sub s;
    s.disp2();                          // OK - sub's own method
    s.disp1();                          // OK! disp1 is PUBLIC in sub now!
    return 0;
}
```

**Output:**
```
base param
sub no-arg
-858993460
10
sub destr
base destr
```

### Example 8: Protected Inheritance

```cpp
class sub : protected base              // PROTECTED inheritance
{
    // disp1 becomes protected in sub
    // Can be accessed inside sub and sub's children
    // CANNOT be accessed from main()
};
```

### Example 9: Accessing Protected Members

```cpp
#include<iostream>
using namespace std;

class base
{
private:
    int num1;                           // Private - never directly accessible
protected:
    int var;                            // Protected - accessible in sub classes
public:
    void disp1()
    {
        cout << num1 << "\t" << var << endl;
    }
    base(int num1)
    {
        this->num1 = num1;
        var = 100;                      // Initialize protected member
        cout << "base param" << endl;
    }
    ~base()
    {
        cout << "base destr" << endl;
    }
};

class sub1 : base                       // Private inheritance (default)
{
private:
    int num2;
public:
    sub1() : base(0)
    {
        cout << "sub1 no-arg constr" << endl;
    }
    void disp2()
    {
        cout << num2 << endl;
        cout << "var is\t" << var << endl;  // OK! var is protected, accessible here
        // Even in private inheritance, protected members of base
        // become private in sub but are still accessible within sub
    }
    ~sub1()
    {
        cout << "sub1 destr" << endl;
    }
};

int main()
{
    sub1 s;
    s.disp2();
    // s.disp1();                       // ERROR in private inheritance
    return 0;
}
```

---

## Parent Constructor: Private vs Protected

### Key Concept

```
┌─────────────────────────────────────────────────────────────────┐
│         PARENT CONSTRUCTOR: PRIVATE vs PROTECTED                │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   PRIVATE Constructor:                                          │
│   • Cannot instantiate the class directly                       │
│   • Cannot inherit from this class (child can't call constructor)│
│   • Use case: Singleton pattern                                 │
│                                                                  │
│   PROTECTED Constructor:                                         │
│   • Cannot instantiate the class directly                       │
│   • CAN inherit from this class (child can call constructor)    │
│   • Use case: Abstract-like classes meant only for inheritance  │
│                                                                  │
│   Example: istream and ostream classes in C++                   │
│   - Their constructors are protected                            │
│   - You cannot create istream/ostream objects directly          │
│   - But you can create ifstream/ofstream (child classes)        │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Example: Protected Constructor

```cpp
#include<fstream>                       // Line 1: Include fstream for file streams
using namespace std;                    // Line 2: Use standard namespace

/* 
 * istream and ostream classes have their constructor as protected.
 * This means you cannot instantiate them directly, but you can 
 * instantiate their child classes like ifstream and ofstream.
 */

class base                              // Line 10: Base class
{
    int num1;
protected:                              // Line 13: Protected constructor!
    base()
    {
        cout << "in base const" << endl;
    }
};

class sub : public base                 // Line 20: sub inherits from base
{
    int num2;
public:
    sub()                               // Line 24: sub's constructor
    {                                   // This can call protected base()!
        cout << "in sub const" << endl;
    }
};

void main()
{
    sub s;                              // Line 31: OK! sub can be instantiated
    
    // base b;                          // ERROR! base() is protected
    // istream ist;                     // ERROR! istream constructor is protected
    // ostream ost;                     // ERROR! ostream constructor is protected
    
    fstream fm;                         // OK! fstream is child of iostream
}
```

---

## Making Inherited Members Public

### Using Declaration

When you use private inheritance but want specific members to be public, you can use the `using` declaration:

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Base class
{
private:
    int num1 = 100;                     // Line 7: Private member
public:
    void disp1()                        // Line 9: Public member function
    {
        cout << num1 << endl;
    }
};

class sub : base                        // Line 14: Private inheritance (default)
{
private:
    int num2 = 200;                     // Line 17: Private member
public:
    using base::disp1;                  // Line 19: Make disp1 PUBLIC in sub!
    
    void disp2()
    {
        cout << num2 << endl;
    }
};

int main()                              // Line 27: Main function
{
    sub s;                              // Line 29: Create sub object
    s.disp2();                          // Line 30: OK - prints 200
    s.disp1();                          // Line 31: OK now! using declaration makes it public
    return 0;
}
```

**Output:**
```
200
100
```

---

## Friend Functions and Inheritance

### Important Rule

> **Friend is NOT inherited!** If a function is a friend of the base class, it is NOT automatically a friend of the derived class.

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class sub;                              // Line 4: Forward declaration of sub

class base                              // Line 6: Base class
{
private:
    int num1 = 10;                      // Line 9: Private member
    friend void fun(base&, sub&);       // Line 10: Declare friend function
};

class sub : public base                 // Line 13: sub inherits from base
{
private:
    int num2 = 20;                      // Line 16: Private member
};

void fun(base& ref1, sub& ref2)         // Line 19: Friend function definition
{
    cout << ref1.num1;                  // Line 21: OK - fun is friend of base
    sub s;
    // cout << s.num2;                  // Line 23: ERROR! fun is NOT friend of sub!
    // Base class's friend is NOT sub class's friend
}

int main()                              // Line 27: Main function
{
    base b;                             // Line 29: Create base object
    sub s;                              // Line 30: Create sub object
    fun(b, s);                          // Line 31: Call friend function
    return 0;
}
```

### Diagram: Friend and Inheritance

```
┌─────────────────────────────────────────────────────────────────┐
│              FRIEND FUNCTIONS AND INHERITANCE                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│    ┌─────────────┐                                               │
│    │    base     │ ◄────── friend void fun()                    │
│    │  - num1     │         ✓ Can access num1                    │
│    └──────┬──────┘                                               │
│           │                                                      │
│           │ inherits                                             │
│           ▼                                                      │
│    ┌─────────────┐                                               │
│    │    sub      │ ◄────── fun() is NOT friend here!            │
│    │  - num2     │         ✗ Cannot access num2                 │
│    └─────────────┘                                               │
│                                                                  │
│   KEY: Friend relationship is NOT inherited!                    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────────────┐
│               SINGLE INHERITANCE - KEY POINTS                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. Single Inheritance = One parent, one child                  │
│                                                                  │
│  2. Constructor Order:                                          │
│     Parent constructor first → Child constructor                │
│                                                                  │
│  3. Destructor Order:                                           │
│     Child destructor first → Parent destructor                  │
│                                                                  │
│  4. If parent has no default constructor:                       │
│     ALL child constructors MUST call parent constructor         │
│     explicitly using initializer list: sub() : base(value)      │
│                                                                  │
│  5. Protected Constructor:                                       │
│     • Class cannot be instantiated directly                     │
│     • But can be inherited (child can call it)                  │
│     • Example: istream, ostream classes                         │
│                                                                  │
│  6. Friend is NOT Inherited:                                    │
│     Base class's friend ≠ Derived class's friend               │
│                                                                  │
│  7. using declaration:                                          │
│     Makes privately inherited member public                     │
│     Syntax: using base::member;                                 │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Previous: [Inheritance Introduction](33_Inheritance_Introduction.md)*
*Next: [Types of Inheritance](35_Types_of_Inheritance.md)*
