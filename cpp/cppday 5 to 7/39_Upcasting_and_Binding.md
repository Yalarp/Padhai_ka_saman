# ⬆️ Upcasting and Binding in C++

## Table of Contents
1. [What is Upcasting?](#what-is-upcasting)
2. [Why Do We Do Upcasting?](#why-do-we-do-upcasting)
3. [Early Binding vs Late Binding](#early-binding-vs-late-binding)
4. [Rules for Upcasting](#rules-for-upcasting)
5. [Upcasting with Pointers and References](#upcasting-with-pointers-and-references)
6. [Key Takeaways](#key-takeaways)

---

## What is Upcasting?

> **Upcasting**: Assigning the address of a child class object to a parent class pointer, OR making a parent class reference refer to a child class object.

```
┌─────────────────────────────────────────────────────────────────┐
│                    WHAT IS UPCASTING?                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   With Pointers:                                                 │
│   ┌────────────────────────────────────────────────────────────┐ │
│   │   base *ptr = new sub;    ← Upcasting                      │ │
│   │         ↑          ↑                                       │ │
│   │    Parent type   Child object                              │ │
│   │    pointer       address                                   │ │
│   └────────────────────────────────────────────────────────────┘ │
│                                                                  │
│   With References:                                               │
│   ┌────────────────────────────────────────────────────────────┐ │
│   │   sub s;                                                    │ │
│   │   base &ref = s;          ← Upcasting                      │ │
│   │         ↑     ↑                                            │ │
│   │    Parent   Child                                          │ │
│   │    type ref  object                                        │ │
│   └────────────────────────────────────────────────────────────┘ │
│                                                                  │
│   WHY IS IT CALLED "UP" CASTING?                                 │
│                                                                  │
│       Parent Class  ← UP in hierarchy                           │
│            ↑                                                     │
│            │ casting direction                                   │
│            │                                                     │
│       Child Class   ← DOWN in hierarchy                         │
│                                                                  │
│   We cast from Child (down) to Parent (up), hence "UPCASTING"   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Why Do We Do Upcasting?

### The Problem: Maintenance Overhead

Consider this code WITHOUT upcasting:

```cpp
class Animal { public: virtual void makeSound() { } };
class Tiger : public Animal { public: void makeSound() { cout << "roar" << endl; } };
class Dog : public Animal { public: void makeSound() { cout << "bark" << endl; } };
class Cat : public Animal { public: void makeSound() { cout << "meaw" << endl; } };

void perform()
{
    // Accept a choice from user: Dog, Cat or Tiger
    // Based on that create object and invoke "makeSound()"
    
    int choice;
    cin >> choice;
    
    switch(choice)
    {
        case 1: 
            Dog d;
            d.makeSound();
            break;
        case 2: 
            Cat c;
            c.makeSound();
            break;
        case 3: 
            Tiger t;
            t.makeSound();
            break;
        default: 
            cout << "invalid choice" << endl;
    }
}
```

### The Problem

```
┌─────────────────────────────────────────────────────────────────┐
│                   MAINTENANCE PROBLEM                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   What if we add a new Animal - "Elephant"?                     │
│   → We must add a new case in switch block!                     │
│                                                                  │
│   What if we remove "Tiger" from hierarchy?                     │
│   → We must remove the case from switch block!                  │
│                                                                  │
│   EVERY change in hierarchy = CHANGE in perform() function!     │
│   This is a MAINTENANCE NIGHTMARE!                              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### The Solution: Upcasting for Maintenance-Free Code

```cpp
// Solution 1: Using Parent Class Pointer
void perform(Animal *ptr)               // Accept ANY animal via pointer
{
    ptr->makeSound();                   // Call appropriate version
}

int main()
{
    perform(new Cat);                   // Pass Cat
    perform(new Dog);                   // Pass Dog
    perform(new Tiger);                 // Pass Tiger
    perform(new Elephant);              // Add new animal? No change to perform()!
}
```

```cpp
// Solution 2: Using Parent Class Reference
void perform(Animal &ref)               // Accept ANY animal via reference
{
    ref.makeSound();                    // Call appropriate version
}

int main()
{
    Cat c1;
    Dog d1;
    perform(c1);                        // Pass Cat
    perform(d1);                        // Pass Dog
}
```

### How Upcasting Solves the Problem

```
┌─────────────────────────────────────────────────────────────────┐
│              UPCASTING - MAINTENANCE FREE CODE                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   void perform(Animal *ptr)                                      │
│   {                                                              │
│       ptr->makeSound();                                          │
│   }                                                              │
│                                                                  │
│   The "ptr" is of type "Animal" but can accept ANY child:       │
│   • perform(new Cat);      → calls Cat::makeSound()             │
│   • perform(new Dog);      → calls Dog::makeSound()             │
│   • perform(new Tiger);    → calls Tiger::makeSound()           │
│   • perform(new Elephant); → calls Elephant::makeSound()        │
│                                                                  │
│   ADD NEW ANIMAL?                                                │
│   → Just create the class and call perform() with it            │
│   → NO CHANGE to perform() function!                            │
│                                                                  │
│   REMOVE AN ANIMAL?                                              │
│   → Just remove the class                                        │
│   → NO CHANGE to perform() function!                            │
│                                                                  │
│   This is called "PROGRAMMING TO INTERFACE"                     │
│   → Write code against the base class (interface)               │
│   → Not against specific implementations                        │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Early Binding vs Late Binding

### What is Binding?

> **Binding**: Resolving a function call to its function body.

```
┌─────────────────────────────────────────────────────────────────┐
│                    TYPES OF BINDING                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   EARLY BINDING (Static Binding / Compile-time Binding):        │
│   ──────────────────────────────────────────────────────        │
│   • Function call resolved at COMPILE TIME                      │
│   • Compiler decides which function to call                     │
│   • Based on the TYPE of pointer/reference                      │
│   • Fast but inflexible                                          │
│   • DEFAULT in C++                                              │
│                                                                  │
│   LATE BINDING (Dynamic Binding / Runtime Binding):             │
│   ──────────────────────────────────────────────────            │
│   • Function call resolved at RUNTIME                           │
│   • Decided when program is running                             │
│   • Based on the ACTUAL OBJECT type                             │
│   • Slightly slower but flexible                                 │
│   • Achieved using VIRTUAL functions                            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Example: Early Binding (Non-Virtual)

```cpp
#include<iostream>
using namespace std;

class base
{
public:
    void disp()                         // NON-virtual function
    {
        cout << "base disp" << endl;
    }
};

class sub : public base
{
public:
    void disp()                         // Redefinition
    {
        cout << "sub disp" << endl;
    }
};

int main()
{
    base *ptr = new base;
    ptr->disp();                        // base disp (pointer type decides)
    
    ptr = new sub;                      // Upcasting
    ptr->disp();                        // STILL base disp! (pointer type is base*)
    
    return 0;
}
```

**Output:**
```
base disp
base disp       ← Even though ptr points to sub object!
```

### Example: Late Binding (Virtual)

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Base class
{
public:
    virtual void disp()                 // Line 7: VIRTUAL function!
    {
        cout << "base disp" << endl;
    }
};

class sub : public base                 // Line 13: sub inherits from base
{
public:
    void disp()                         // Line 16: Overriding (not just redefinition)
    {
        cout << "sub disp" << endl;
    }
};

int main()                              // Line 22: Main function
{
    base *ptr = new base;               // Line 24: ptr points to base object
    ptr->disp();                        // Line 25: base disp (content is base)
    
    ptr = new sub;                      // Line 27: Upcasting - ptr points to sub
    ptr->disp();                        // Line 28: sub disp (content is sub!)
    
    return 0;
}
```

**Output:**
```
base disp
sub disp        ← Now it works! Virtual function = late binding
```

### Comparison Diagram

```
┌────────────────────────────────────────────────────────────────────────────┐
│               EARLY BINDING vs LATE BINDING                                 │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   base *ptr = new sub;                                                      │
│   ptr->disp();                                                              │
│                                                                             │
│   EARLY BINDING (Non-Virtual):       LATE BINDING (Virtual):               │
│   ┌────────────────────────────┐    ┌────────────────────────────┐         │
│   │ Compiler sees: ptr is base*│    │ Runtime checks: What does  │         │
│   │ Compiler decides: call     │    │ ptr actually point to?     │         │
│   │ base::disp()               │    │ It's a sub object!         │         │
│   │                            │    │ So call sub::disp()        │         │
│   └────────────────────────────┘    └────────────────────────────┘         │
│                                                                             │
│   OUTPUT: "base disp"                OUTPUT: "sub disp"                    │
│                                                                             │
│   ─────────────────────────────────────────────────────────────────────    │
│                                                                             │
│   KEY DIFFERENCE:                                                           │
│   • Non-Virtual: TYPE of pointer/reference decides                         │
│   • Virtual: CONTENT (actual object) decides                               │
│                                                                             │
└────────────────────────────────────────────────────────────────────────────┘
```

---

## Rules for Upcasting

### Rule 1: Inheritance Mode Must Be Public

> **Important**: Upcasting is only allowed with **public inheritance**. Private and protected inheritance do NOT allow upcasting!

```cpp
class sub : public base { };            // ✅ Upcasting allowed
class sub : protected base { };         // ❌ Upcasting NOT allowed
class sub : private base { };           // ❌ Upcasting NOT allowed
```

### Why?

```
┌─────────────────────────────────────────────────────────────────┐
│           WHY PUBLIC INHERITANCE REQUIRED?                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   With PUBLIC inheritance:                                       │
│   • sub IS-A base (relationship is visible)                     │
│   • Outside world knows sub inherits from base                  │
│   • Upcasting makes logical sense                               │
│                                                                  │
│   With PRIVATE/PROTECTED inheritance:                            │
│   • Inheritance relationship is HIDDEN                          │
│   • Outside world doesn't know sub inherits from base           │
│   • Upcasting would expose hidden relationship                  │
│   • Hence NOT allowed                                            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Rule 2: Virtual Function Required for Late Binding

For late binding (polymorphic behavior) to work:
1. Function MUST be `virtual` in base class
2. Upcasting must be done (pointer/reference of base type)

---

## Upcasting with Pointers and References

### Example: Late Binding with Reference

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Base class
{
public:
    virtual void disp()                 // Line 7: VIRTUAL function
    {
        cout << "base disp" << endl;
    }
};

class sub : public base                 // Line 13: sub inherits from base
{
public:
    void disp()                         // Line 16: Overriding
    {
        cout << "sub disp" << endl;
    }
};

int main()                              // Line 22: Main function
{
    base b1;                            // Line 24: Create base object
    sub s1;                             // Line 25: Create sub object
    
    base &ref1 = b1;                    // Line 27: Reference to base
    ref1.disp();                        // Line 28: base disp (referent is base)
    
    base &ref2 = s1;                    // Line 30: UPCASTING with reference
    ref2.disp();                        // Line 31: sub disp (referent is sub!)
    
    return 0;
}
```

**Output:**
```
base disp
sub disp
```

### Three Ways of Writing Polymorphic Code

```cpp
// Way 1: Using pointer, dynamic allocation
void perform(Animal *ptr)
{
    ptr->makeSound();
}
int main()
{
    perform(new Cat);
    perform(new Dog);
}

// Way 2: Using pointer, stack allocation
void perform(Animal *ptr)
{
    ptr->makeSound();
}
int main()
{
    Cat c;
    Dog d;
    perform(&c);
    perform(&d);
}

// Way 3: Using reference
void perform(Animal &ref)
{
    ref.makeSound();
}
int main()
{
    Cat c;
    Dog d;
    perform(c);
    perform(d);
}
```

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────────────┐
│              UPCASTING AND BINDING - KEY POINTS                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. UPCASTING:                                                   │
│     • Parent pointer = Child object address                     │
│     • Parent reference = Child object                           │
│     • Casting from derived to base (up the hierarchy)          │
│                                                                  │
│  2. WHY UPCAST:                                                  │
│     • Write maintenance-free, flexible code                     │
│     • "Program to interface, not implementation"               │
│     • One function handles all derived types                    │
│                                                                  │
│  3. EARLY vs LATE BINDING:                                       │
│     • Early: Compile-time, based on pointer/ref TYPE           │
│     • Late: Runtime, based on actual OBJECT                    │
│     • Late binding needs VIRTUAL functions                      │
│                                                                  │
│  4. RULES:                                                       │
│     • Upcasting needs PUBLIC inheritance                        │
│     • Late binding needs VIRTUAL keyword                        │
│     • C++ uses early binding by DEFAULT                         │
│                                                                  │
│  5. VIRTUAL KEYWORD:                                             │
│     • Makes function use late binding                           │
│     • Enables runtime polymorphism                              │
│     • Once virtual, always virtual (in derived classes)        │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Previous: [Aggregation and Composition](38_Aggregation_Composition.md)*
*Next: [Virtual Functions and Overriding](40_Virtual_Functions_Overriding.md)*
