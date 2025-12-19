# 🗑️ Virtual Destructor in C++

## Table of Contents
1. [The Problem: Memory Leak](#the-problem-memory-leak)
2. [Solution: Virtual Destructor](#solution-virtual-destructor)
3. [Why Destructors Are Not Virtual by Default](#why-destructors-are-not-virtual-by-default)
4. [When to Use Virtual Destructor](#when-to-use-virtual-destructor)
5. [Pure Virtual Destructor](#pure-virtual-destructor)
6. [Key Takeaways](#key-takeaways)

---

## The Problem: Memory Leak

### What Happens Without Virtual Destructor?

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Base class
{
public:
    int *ptr;                           // Line 7: Pointer member
    
    base()                              // Line 9: Constructor
    {
        ptr = new int[100];             // Line 11: Allocate memory (400 bytes)
        cout << "base const" << endl;
    }
    
    ~base()                             // Line 15: NON-virtual destructor!
    {
        delete[] ptr;                   // Line 17: Free memory
        cout << "base destr" << endl;
    }
};

class sub : public base                 // Line 22: Derived class
{
public:
    int *sptr;                          // Line 25: sub's pointer member
    
    sub()                               // Line 27: Constructor
    {
        sptr = new int[500];            // Line 29: Allocate memory (2000 bytes)
        cout << "sub const" << endl;
    }
    
    ~sub()                              // Line 33: Destructor
    {
        delete[] sptr;                  // Line 35: Free memory
        cout << "sub destr" << endl;
    }
};

int main()                              // Line 40: Main function
{
    base *ptr = new sub;                // Line 42: UPCASTING!
    delete ptr;                         // Line 43: Delete through base pointer
    return 0;
}
```

**Output:**
```
base const
sub const
base destr       ← ONLY base destructor called!
                 ← sub destructor NEVER called!
                 ← 2000 bytes LEAKED!
```

### Visual Explanation

```
┌─────────────────────────────────────────────────────────────────────────────┐
│               MEMORY LEAK WITHOUT VIRTUAL DESTRUCTOR                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   base *ptr = new sub;                                                       │
│                                                                              │
│   Memory allocated:                                                          │
│   ┌────────────────────────────────────┐                                     │
│   │ sub object                         │                                     │
│   │ ┌────────────────────────────────┐ │       ┌──────────────────┐         │
│   │ │ ptr ─────────────────────────────────────>│ 400 bytes       │         │
│   │ └────────────────────────────────┘ │       │ (base's memory)  │         │
│   │ ┌────────────────────────────────┐ │       └──────────────────┘         │
│   │ │ sptr ────────────────────────────────────>┌──────────────────┐         │
│   │ └────────────────────────────────┘ │       │ 2000 bytes       │         │
│   └────────────────────────────────────┘       │ (sub's memory)   │         │
│                                                └──────────────────┘         │
│                                                                              │
│   delete ptr; (ptr is base*)                                                 │
│                                                                              │
│   ┌────────────────────────────────────────────────────────────────────────┐│
│   │ Compiler uses EARLY BINDING because ~base() is not virtual!           ││
│   │ Decision at compile-time: ptr is base* → call base::~base()           ││
│   └────────────────────────────────────────────────────────────────────────┘│
│                                                                              │
│   ~base() runs:                                                              │
│   ├── delete[] ptr;  ✓ (400 bytes freed)                                    │
│   ├── cout << "base destr"                                                  │
│   └── ~sub() NEVER called!                                                  │
│                                                                              │
│   RESULT: 2000 bytes pointed by sptr → MEMORY LEAK!                         │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Solution: Virtual Destructor

### Making Destructor Virtual

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Base class
{
public:
    int *ptr;                           // Line 7: Pointer member
    
    base()                              // Line 9: Constructor
    {
        ptr = new int[100];             // Line 11: Allocate memory
        cout << "base const" << endl;
    }
    
    virtual ~base()                     // Line 15: VIRTUAL destructor!
    {                                   // This is the KEY change!
        delete[] ptr;                   // Line 17: Free memory
        cout << "base destr" << endl;
    }
};

class sub : public base                 // Line 22: Derived class
{
public:
    int *sptr;                          // Line 25: Pointer member
    
    sub()                               // Line 27: Constructor
    {
        sptr = new int[500];            // Line 29: Allocate memory
        cout << "sub const" << endl;
    }
    
    ~sub()                              // Line 33: Destructor (automatically virtual!)
    {
        delete[] sptr;                  // Line 35: Free memory
        cout << "sub destr" << endl;
    }
};

int main()                              // Line 40: Main function
{
    base *ptr = new sub;                // Line 42: UPCASTING
    delete ptr;                         // Line 43: Delete through base pointer
    return 0;
}
```

**Output:**
```
base const
sub const
sub destr        ← sub destructor called FIRST!
base destr       ← THEN base destructor called!
                 ← ALL memory properly freed!
```

### What Changed?

```
┌─────────────────────────────────────────────────────────────────────────────┐
│               CORRECT BEHAVIOR WITH VIRTUAL DESTRUCTOR                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   delete ptr; (ptr is base*, but ~base() is virtual!)                       │
│                                                                              │
│   ┌────────────────────────────────────────────────────────────────────────┐│
│   │ Runtime uses LATE BINDING because ~base() is virtual!                  ││
│   │ Decision at runtime: ptr points to sub object → call sub::~sub()      ││
│   └────────────────────────────────────────────────────────────────────────┘│
│                                                                              │
│   Destruction sequence:                                                      │
│   1. ~sub() runs:                                                            │
│      ├── delete[] sptr;  ✓ (2000 bytes freed)                               │
│      └── cout << "sub destr"                                                │
│                                                                              │
│   2. ~base() runs automatically (as part of destruction chain):             │
│      ├── delete[] ptr;   ✓ (400 bytes freed)                                │
│      └── cout << "base destr"                                               │
│                                                                              │
│   RESULT: ALL memory properly freed! No leak!                               │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Why Destructors Are Not Virtual by Default

### Common Question: Why Not Make All Destructors Virtual?

```
┌─────────────────────────────────────────────────────────────────────────────┐
│          WHY DESTRUCTORS ARE NOT VIRTUAL BY DEFAULT                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   REASON 1: MEMORY OVERHEAD                                                  │
│   • Virtual destructor requires VPTR in every object                        │
│   • Each object gets 8 extra bytes (on 64-bit)                              │
│   • For small classes with many objects, this adds up!                      │
│                                                                              │
│   Example:                                                                   │
│   class Point { int x, y; };  // 8 bytes per object                        │
│   Point points[1000000];      // 8 MB total                                 │
│                                                                              │
│   With virtual destructor:                                                   │
│   class Point { int x, y; virtual ~Point() {} };  // 16 bytes per object   │
│   Point points[1000000];      // 16 MB total (DOUBLE!)                      │
│                                                                              │
│   REASON 2: RUNTIME OVERHEAD                                                 │
│   • Virtual calls have indirection overhead                                 │
│   • VTABLE lookup is slower than direct call                                │
│   • Prevents some compiler optimizations                                    │
│                                                                              │
│   REASON 3: NOT ALWAYS NEEDED                                                │
│   • Many classes are never used polymorphically                             │
│   • If class is final (not meant to be inherited)                           │
│   • If you never delete through base pointer                                │
│                                                                              │
│   C++ PHILOSOPHY: "You don't pay for what you don't use"                    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## When to Use Virtual Destructor

### The Rule

> **Rule of Thumb**: If a class has AT LEAST ONE virtual function, make the destructor virtual too!

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                   WHEN TO USE VIRTUAL DESTRUCTOR                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ALWAYS use virtual destructor when:                                        │
│   ✓ Class has other virtual functions                                       │
│   ✓ Class is designed to be a base class for polymorphism                   │
│   ✓ You might delete derived objects through base pointers                  │
│   ✓ Class is part of a class hierarchy                                      │
│                                                                              │
│   DON'T need virtual destructor when:                                        │
│   ✗ Class is final (won't be inherited)                                     │
│   ✗ Class has no virtual functions                                          │
│   ✗ You never use polymorphism with this class                              │
│   ✗ Performance is critical and memory is limited                           │
│                                                                              │
│   EXAMPLES:                                                                  │
│                                                                              │
│   // SHOULD have virtual destructor                                          │
│   class Shape {                                                              │
│       virtual void draw() = 0;    // Has virtual function                   │
│       virtual ~Shape() {}         // MUST be virtual!                       │
│   };                                                                         │
│                                                                              │
│   // DON'T need virtual destructor                                           │
│   class Point {                                                              │
│       int x, y;                   // No virtual functions                   │
│   };                              // No inheritance planned                  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Pure Virtual Destructor

### Can Destructor Be Pure Virtual?

> **Yes!** A destructor can be pure virtual, but it MUST have a definition (body).

```cpp
#include<iostream>
using namespace std;

class AbstractBase                      // Abstract class
{
public:
    int *ptr;
    
    AbstractBase()
    {
        ptr = new int[100];
        cout << "AbstractBase const" << endl;
    }
    
    virtual ~AbstractBase() = 0;        // Pure virtual destructor DECLARATION
};

// MUST provide definition for pure virtual destructor!
AbstractBase::~AbstractBase()           // Definition REQUIRED!
{
    delete[] ptr;
    cout << "AbstractBase destr" << endl;
}

class Derived : public AbstractBase
{
public:
    int *dptr;
    
    Derived()
    {
        dptr = new int[200];
        cout << "Derived const" << endl;
    }
    
    ~Derived()
    {
        delete[] dptr;
        cout << "Derived destr" << endl;
    }
};

int main()
{
    // AbstractBase ab;              // ERROR! Cannot instantiate abstract class
    
    AbstractBase *ptr = new Derived; // OK - create derived
    delete ptr;                       // Calls both destructors properly
    
    return 0;
}
```

**Output:**
```
AbstractBase const
Derived const
Derived destr
AbstractBase destr
```

### Why Pure Virtual Destructor Needs Definition?

```
┌─────────────────────────────────────────────────────────────────┐
│      WHY PURE VIRTUAL DESTRUCTOR NEEDS DEFINITION?               │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   Normal pure virtual function:                                  │
│   virtual void draw() = 0;  // No definition needed             │
│   • Derived class REPLACES it                                   │
│   • Base version is never called                                │
│                                                                  │
│   Destructor:                                                    │
│   virtual ~Base() = 0;      // Definition REQUIRED!             │
│   • Derived destructor ~Derived() calls ~Base()                 │
│   • Destructors form a CHAIN - all must execute                 │
│   • Base destructor WILL be called during destruction           │
│                                                                  │
│   Example destruction chain:                                     │
│   delete derivedPtr;                                             │
│   ├── ~Derived() executes                                        │
│   └── ~Base() is AUTOMATICALLY called after!                    │
│       ↑ This is why definition is needed!                       │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Complete Example: Virtual Destructor in Practice

```cpp
#include<iostream>                      // Line 1: Include iostream
#include<string>                        // Line 2: Include string
using namespace std;                    // Line 3: Use standard namespace

class Animal                            // Line 5: Abstract base class
{
protected:
    string name;                        // Line 8: Name of animal
public:
    Animal(const string& n) : name(n)   // Line 10: Constructor
    {
        cout << name << " (Animal) created" << endl;
    }
    
    virtual void speak() = 0;           // Line 15: Pure virtual function
    
    virtual ~Animal()                   // Line 17: VIRTUAL destructor
    {
        cout << name << " (Animal) destroyed" << endl;
    }
};

class Dog : public Animal               // Line 23: Derived class
{
    string* breed;                      // Line 25: Dynamically allocated
public:
    Dog(const string& n, const string& b)  // Line 27: Constructor
        : Animal(n)
    {
        breed = new string(b);          // Line 30: Allocate memory
        cout << name << " (Dog) created, breed: " << *breed << endl;
    }
    
    void speak() override               // Line 34: Implement pure virtual
    {
        cout << name << " says: Woof!" << endl;
    }
    
    ~Dog() override                     // Line 39: Destructor
    {
        cout << name << " (Dog) destroyed, freeing breed: " << *breed << endl;
        delete breed;                   // Line 42: Free memory
    }
};

class Cat : public Animal               // Line 46: Another derived class
{
    string* color;                      // Line 48: Dynamically allocated
public:
    Cat(const string& n, const string& c)
        : Animal(n)
    {
        color = new string(c);
        cout << name << " (Cat) created, color: " << *color << endl;
    }
    
    void speak() override
    {
        cout << name << " says: Meow!" << endl;
    }
    
    ~Cat() override
    {
        cout << name << " (Cat) destroyed, freeing color: " << *color << endl;
        delete color;
    }
};

void adoptAndRelease(Animal* pet)       // Line 68: Polymorphic function
{
    pet->speak();                       // Line 70: Virtual call
    cout << "Releasing pet..." << endl;
    delete pet;                         // Line 72: Delete through base pointer
}                                       // Works correctly because of virtual destructor!

int main()                              // Line 75: Main function
{
    cout << "=== Creating pets ===" << endl;
    
    Animal* dog = new Dog("Buddy", "Golden Retriever");
    Animal* cat = new Cat("Whiskers", "Orange");
    
    cout << "\n=== Using pets ===" << endl;
    adoptAndRelease(dog);               // Line 83: Properly destroys Dog
    cout << endl;
    adoptAndRelease(cat);               // Line 85: Properly destroys Cat
    
    cout << "\n=== Done ===" << endl;
    return 0;
}
```

**Output:**
```
=== Creating pets ===
Buddy (Animal) created
Buddy (Dog) created, breed: Golden Retriever
Whiskers (Animal) created
Whiskers (Cat) created, color: Orange

=== Using pets ===
Buddy says: Woof!
Releasing pet...
Buddy (Dog) destroyed, freeing breed: Golden Retriever
Buddy (Animal) destroyed

Whiskers says: Meow!
Releasing pet...
Whiskers (Cat) destroyed, freeing color: Orange
Whiskers (Animal) destroyed

=== Done ===
```

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────────────┐
│              VIRTUAL DESTRUCTOR - KEY POINTS                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. THE PROBLEM:                                                 │
│     delete base_ptr;  // base_ptr points to derived object      │
│     • Without virtual: Only base destructor called              │
│     • Derived destructor skipped → MEMORY LEAK!                 │
│                                                                  │
│  2. THE SOLUTION:                                                │
│     virtual ~Base() { }  // Make destructor virtual              │
│     • Destructor uses late binding (like other virtuals)        │
│     • Correct derived destructor called at runtime              │
│     • Then base destructor called automatically                 │
│                                                                  │
│  3. WHY NOT DEFAULT:                                             │
│     • Memory overhead (VPTR in every object)                    │
│     • Runtime overhead (VTABLE lookup)                          │
│     • Not always needed                                          │
│     • C++ philosophy: Don't pay for what you don't use         │
│                                                                  │
│  4. RULE OF THUMB:                                               │
│     If class has ANY virtual function → make destructor virtual │
│     If class is designed for polymorphism → virtual destructor  │
│                                                                  │
│  5. PURE VIRTUAL DESTRUCTOR:                                     │
│     virtual ~Base() = 0;  // Pure virtual                       │
│     Base::~Base() { }     // MUST provide definition!           │
│     (Because destructor is always called in chain)              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Previous: [VTABLE and VPTR Mechanism](41_VTABLE_VPTR_Mechanism.md)*
*Next: [Abstract Classes and Pure Virtual Functions](43_Abstract_Classes_Pure_Virtual.md)*
