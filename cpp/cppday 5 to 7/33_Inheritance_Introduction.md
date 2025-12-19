# 🏛️ Inheritance in C++ - Complete Introduction

## Table of Contents
1. [What is Inheritance?](#what-is-inheritance)
2. [Why Do We Need Inheritance?](#why-do-we-need-inheritance)
3. [Reusability Concept](#reusability-concept)
4. [Inheritance Syntax](#inheritance-syntax)
5. [Access Specifiers in Inheritance](#access-specifiers-in-inheritance)
6. [What is Inherited and What is Not](#what-is-inherited-and-what-is-not)
7. [Protected Access Specifier](#protected-access-specifier)
8. [Inheritance Access Table](#inheritance-access-table)
9. [Key Takeaways](#key-takeaways)

---

## What is Inheritance?

> **Definition**: The ability for a new class to be created from an existing class by extending it is known as **inheritance**.

```
┌─────────────────────────────────────────────────────────────┐
│                    INHERITANCE CONCEPT                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│     ┌─────────────────┐                                      │
│     │   Base Class    │  ← Parent/Super Class                │
│     │   (Existing)    │                                      │
│     └────────┬────────┘                                      │
│              │                                               │
│              │ extends (inherits)                            │
│              ▼                                               │
│     ┌─────────────────┐                                      │
│     │  Derived Class  │  ← Child/Sub Class                   │
│     │     (New)       │                                      │
│     └─────────────────┘                                      │
│                                                              │
│  Derived class automatically gets all members of base class │
│  (except constructors, destructor, and operator=)           │
└─────────────────────────────────────────────────────────────┘
```

### Key Terminology

| Term | Meaning |
|------|---------|
| **Base Class** | The existing class from which we inherit (also called Parent/Super class) |
| **Derived Class** | The new class that inherits from base class (also called Child/Sub class) |
| **Inherit** | To receive properties and behaviors from parent class |
| **Extend** | To add new features to inherited class |

---

## Why Do We Need Inheritance?

### 1. **Code Reusability**
Instead of writing the same code again, we can reuse existing code from parent class.

### 2. **Establishing Relationships**
Inheritance creates an "IS-A" relationship between classes.
- A `Car` IS-A `Vehicle`
- A `Dog` IS-A `Animal`
- A `Teacher` IS-A `Person`

### 3. **Extensibility**
We can extend existing functionality without modifying the original class.

### 4. **Polymorphism Support**
Inheritance is the foundation for runtime polymorphism in C++.

---

## Reusability Concept

> **Reusability** means using existing types while defining a new type.

### Two Ways to Achieve Reusability

```
┌─────────────────────────────────────────────────────────────────┐
│                     REUSABILITY IN C++                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────────────────┐   ┌──────────────────────────┐    │
│  │      COMPOSITION         │   │      INHERITANCE         │    │
│  │    (Has-A Relationship)  │   │    (Is-A Relationship)   │    │
│  └──────────────────────────┘   └──────────────────────────┘    │
│                                                                  │
│  Example:                        Example:                        │
│  Car HAS-A Engine               Car IS-A FourWheeler            │
│  House HAS-A Room               Dog IS-A Animal                  │
│  Department HAS-A Teacher       Teacher IS-A Person              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### When to Use Composition (Has-A)
You go for **composition** when you want to use some of the functionalities of an existing type inside a new type.

**Example**: While designing `Car`, you would reuse `Engine` by composition because `Car` is NOT an `Engine` - it just needs some functionalities of Engine.

### When to Use Inheritance (Is-A)
You go for **inheritance** when you realize that the new type is "same as" the existing type.

**Example**: While designing `Car`, you would reuse `FourWheeler` because `Car` IS same as `FourWheeler`.

---

## Inheritance Syntax

### Basic Syntax

```cpp
class DerivedClass : access_specifier BaseClass
{
    // derived class members
};
```

### Access Specifiers in Inheritance

| Access Specifier | Meaning |
|-----------------|---------|
| `public` | Public members of base become public in derived |
| `protected` | Public members of base become protected in derived |
| `private` | Public members of base become private in derived (default) |

### Example: Basic Inheritance

```cpp
#include<iostream>
using namespace std;

// Line 1: Include the iostream header for input/output operations
// Line 2: Use the standard namespace to avoid writing std:: prefix

class base                          // Line 4: Define the base (parent) class
{
private:                            // Line 6: Private section - accessible only within this class
    int num1 = 10;                  // Line 7: Private member variable initialized to 10
public:                             // Line 8: Public section - accessible from anywhere
    void disp1()                    // Line 9: Public member function
    {
        cout << num1 << endl;       // Line 11: Print num1 (can access private member within class)
    }
};

class sub : public base             // Line 15: Derived class 'sub' publicly inherits from 'base'
{                                   // 'public' means public members of base remain public in sub
private:
    int num2 = 20;                  // Line 18: sub's own private member
public:
    void disp2()                    // Line 20: sub's own public function
    {
        cout << num2 << endl;       // Line 22: Print sub's member
    }
};

int main()
{
    sub s;                          // Line 27: Create object of derived class
    s.disp2();                      // Line 28: Call sub's own function - prints 20
    s.disp1();                      // Line 29: Call inherited function from base - prints 10
    return 0;
}
```

### Execution Flow

```
┌────────────────────────────────────────────────────────────────┐
│                     EXECUTION FLOW                              │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. sub s;                                                     │
│      └──> Creates object 's' of class 'sub'                    │
│      └──> Memory allocated for: num1 (from base) + num2 (sub)  │
│                                                                 │
│   2. s.disp2();                                                 │
│      └──> Calls disp2() of 'sub' class                         │
│      └──> Prints: 20                                           │
│                                                                 │
│   3. s.disp1();                                                 │
│      └──> Calls inherited disp1() from 'base' class            │
│      └──> Prints: 10                                           │
│                                                                 │
│   Output:                                                       │
│   20                                                            │
│   10                                                            │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

---

## Access Specifiers in Inheritance

### The Three Modes of Inheritance

```cpp
class Derived : public Base { };     // Public Inheritance
class Derived : protected Base { };  // Protected Inheritance  
class Derived : private Base { };    // Private Inheritance (DEFAULT)
```

> [!WARNING]
> **Default Inheritance Mode**: If no access specifier is given, C++ uses **private inheritance** by default!
> ```cpp
> class sub : base { }  // Same as: class sub : private base { }
> ```

### Impact on Member Accessibility

```
┌──────────────────────────────────────────────────────────────────────────┐
│            INHERITANCE ACCESS TRANSFORMATION TABLE                        │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│  Base Class Member    │  public      │  protected   │  private           │
│  Access Specifier     │  inheritance │  inheritance │  inheritance       │
│  ─────────────────────┼──────────────┼──────────────┼───────────────────  │
│  public member        │  public      │  protected   │  private           │
│  protected member     │  protected   │  protected   │  private           │
│  private member       │  NOT         │  NOT         │  NOT               │
│                       │  accessible  │  accessible  │  accessible        │
│                                                                           │
└──────────────────────────────────────────────────────────────────────────┘
```

### Example: Public vs Private Inheritance

```cpp
#include<iostream>
using namespace std;

class base
{
private:
    int num1 = 10;                  // Private - never inherited directly
public:
    void disp1()                    // Public member function
    {
        cout << num1 << endl;
    }
};

// PRIVATE INHERITANCE (default)
class sub1 : base                   // Same as: class sub1 : private base
{
public:
    void disp2()
    {
        disp1();                    // OK - disp1 is accessible inside sub1
    }
};

// PUBLIC INHERITANCE
class sub2 : public base
{
public:
    void disp2()
    {
        disp1();                    // OK - disp1 is accessible inside sub2
    }
};

int main()
{
    sub1 s1;
    s1.disp2();                     // OK
    // s1.disp1();                  // ERROR! disp1 is private in sub1
    
    sub2 s2;
    s2.disp2();                     // OK
    s2.disp1();                     // OK - disp1 is public in sub2
    
    return 0;
}
```

---

## What is Inherited and What is Not

### Members That ARE Inherited
- ✅ Data members (public and protected become accessible)
- ✅ Member functions (public and protected)
- ✅ Static members
- ✅ Friend functions have access to inherited private members
- ✅ Nested types

### Members That are NOT Inherited

```
┌─────────────────────────────────────────────────────────────┐
│          MEMBERS NOT INHERITED IN C++                        │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│   a) Constructors                                            │
│      └── Each class must define its own constructors         │
│                                                              │
│   b) Destructor                                              │
│      └── Each class must define its own destructor           │
│                                                              │
│   c) Assignment Operator (operator=)                         │
│      └── Each class gets its own copy assignment             │
│                                                              │
│   Note: Private members ARE inherited but NOT accessible     │
│         directly - they can be accessed via public/protected │
│         methods of base class                                │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Example: Private Members are Inherited but Not Accessible

```cpp
#include<iostream>
using namespace std;

class base
{
private:
    int num1 = 10;                  // Private member - inherited but not directly accessible
public:
    int getNum1()                   // Getter to access private member
    {
        return num1;
    }
};

class sub : public base
{
public:
    void show()
    {
        // cout << num1;            // ERROR! Cannot access private member directly
        cout << getNum1();          // OK - Access via inherited public method
    }
};

int main()
{
    sub s;
    s.show();                       // Prints: 10
    return 0;
}
```

---

## Protected Access Specifier

### What is Protected?

> **Protected** members are like private members but with one key difference - they are accessible in derived classes.

```
┌─────────────────────────────────────────────────────────────────┐
│                ACCESS SPECIFIER COMPARISON                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Access       │ Within  │ Derived  │ Outside                    │
│  Specifier    │ Class   │ Class    │ Class                      │
│  ─────────────┼─────────┼──────────┼──────────                   │
│  private      │   ✅    │    ❌    │   ❌                        │
│  protected    │   ✅    │    ✅    │   ❌                        │
│  public       │   ✅    │    ✅    │   ✅                        │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Complete Example: Protected Demo

```cpp
#include<iostream>           // Line 1: Include iostream for input/output
using namespace std;         // Line 2: Use standard namespace

/*
 * Important Note: private and protected both differ ONLY in case of inheritance
 * Outside inheritance context, they behave the same way from outside the class
 */

class MyClass                // Line 8: Define MyClass
{
private:                     // Line 10: Private section
    int num1;                // Line 11: Private member - not accessible in derived class
protected:                   // Line 12: Protected section
    int num2;                // Line 13: Protected member - accessible in derived class
public:                      // Line 14: Public section
    MyClass(int num1, int num2)      // Line 15: Parameterized constructor
    {
        this->num1 = num1;           // Line 17: Initialize num1
        this->num2 = num2;           // Line 18: Initialize num2
    }
    int getNum1()                    // Line 20: Getter for private num1
    {
        return num1;
    }
    int getNum2()                    // Line 24: Getter for protected num2
    {
        return num2;
    }
};

class SubClass : public MyClass      // Line 29: SubClass inherits from MyClass
{
public:
    SubClass(int a, int b) : MyClass(a, b) { }  // Line 32: Constructor calls parent constructor
    
    void display()                   // Line 34: Display function
    {
        // cout << num1;             // ERROR! num1 is private in parent
        cout << "num2 = " << num2 << endl;  // OK! num2 is protected, accessible here
        cout << "num1 via getter = " << getNum1() << endl;  // OK! Using getter
    }
};

int main()
{
    MyClass m1(100, 200);            // Line 42: Create MyClass object
    // cout << m1.num1 << endl;      // ERROR! Private - not accessible outside
    // cout << m1.num2 << endl;      // ERROR! Protected - not accessible outside
    cout << m1.getNum1() << endl;    // Line 45: OK - prints 100
    cout << m1.getNum2() << endl;    // Line 46: OK - prints 200
    
    SubClass s1(10, 20);             // Line 48: Create SubClass object
    s1.display();                    // Line 49: Prints num2 directly, num1 via getter
    
    return 0;
}
```

### Execution Flow

```
┌────────────────────────────────────────────────────────────────┐
│                     EXECUTION FLOW                              │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│   MyClass m1(100, 200):                                         │
│   ├── num1 = 100 (private)                                      │
│   └── num2 = 200 (protected)                                    │
│                                                                 │
│   m1.getNum1() → 100                                            │
│   m1.getNum2() → 200                                            │
│                                                                 │
│   SubClass s1(10, 20):                                          │
│   ├── Calls MyClass(10, 20)                                     │
│   ├── num1 = 10 (private, inherited but not accessible)         │
│   └── num2 = 20 (protected, inherited and accessible)           │
│                                                                 │
│   s1.display():                                                 │
│   ├── Accesses num2 directly (protected allows this)            │
│   ├── Accesses num1 via getNum1() (only way to access private)  │
│   └── Output:                                                   │
│       num2 = 20                                                 │
│       num1 via getter = 10                                      │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

---

## Inheritance Access Table

### Complete Reference Table

| Base Class Member | Public Inheritance | Protected Inheritance | Private Inheritance |
|-------------------|-------------------|----------------------|---------------------|
| **public** | public in derived | protected in derived | private in derived |
| **protected** | protected in derived | protected in derived | private in derived |
| **private** | Not accessible | Not accessible | Not accessible |

### Visual Representation

```
                        BASE CLASS
            ┌──────────────────────────────────┐
            │  private: int a;                 │
            │  protected: int b;               │
            │  public: int c;                  │
            └──────────────────────────────────┘
                          │
         ┌────────────────┼────────────────┐
         │                │                │
    public inherit   protected inherit   private inherit
         │                │                │
         ▼                ▼                ▼
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│ a: N/A       │  │ a: N/A       │  │ a: N/A       │
│ b: protected │  │ b: protected │  │ b: private   │
│ c: public    │  │ c: protected │  │ c: private   │
└──────────────┘  └──────────────┘  └──────────────┘
```

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────────────┐
│                    KEY POINTS TO REMEMBER                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. Inheritance = Creating new class from existing class         │
│                                                                  │
│  2. Reusability achieved via:                                    │
│     • Inheritance (Is-A) - Car IS-A Vehicle                     │
│     • Composition (Has-A) - Car HAS-A Engine                    │
│                                                                  │
│  3. Access Specifiers transformation:                            │
│     • public inheritance - keeps access levels                   │
│     • protected inheritance - public becomes protected          │
│     • private inheritance - all become private (DEFAULT)        │
│                                                                  │
│  4. NOT inherited:                                               │
│     • Constructors                                               │
│     • Destructor                                                 │
│     • Assignment operator (operator=)                            │
│                                                                  │
│  5. Protected members:                                           │
│     • Not accessible outside class (like private)               │
│     • Accessible in derived classes (unlike private)            │
│                                                                  │
│  6. Private members ARE inherited but NOT directly accessible   │
│     - Use public/protected methods to access them               │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Practice Questions

1. What is the default inheritance mode in C++ if not specified?
2. Can a derived class access private members of base class directly?
3. What is the difference between protected and private access specifiers?
4. Which members are NOT inherited in C++?
5. When would you use composition instead of inheritance?

---

*Next: [Single Inheritance - Complete Guide](34_Single_Inheritance.md)*
