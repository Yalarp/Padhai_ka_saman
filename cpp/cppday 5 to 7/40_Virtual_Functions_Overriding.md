# 🔮 Virtual Functions and Overriding in C++

## Table of Contents
1. [What are Virtual Functions?](#what-are-virtual-functions)
2. [Overriding vs Redefinition](#overriding-vs-redefinition)
3. [How Virtual Functions Work](#how-virtual-functions-work)
4. [Virtual Functions and Constructors](#virtual-functions-and-constructors)
5. [Virtual Functions Best Practices](#virtual-functions-best-practices)
6. [Key Takeaways](#key-takeaways)

---

## What are Virtual Functions?

> **Virtual Function**: A member function declared with the `virtual` keyword in the base class, enabling late binding (runtime polymorphism).

```
┌─────────────────────────────────────────────────────────────────┐
│                 VIRTUAL FUNCTIONS CONCEPT                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   class Base                                                     │
│   {                                                              │
│   public:                                                        │
│       virtual void func()    ← VIRTUAL keyword!                 │
│       {                                                          │
│           // base implementation                                 │
│       }                                                          │
│   };                                                             │
│                                                                  │
│   class Derived : public Base                                    │
│   {                                                              │
│   public:                                                        │
│       void func()            ← OVERRIDING (not just redefinition)│
│       {                                                          │
│           // derived implementation                              │
│       }                                                          │
│   };                                                             │
│                                                                  │
│   KEY BEHAVIOR:                                                  │
│   Base *ptr = new Derived;                                       │
│   ptr->func();  // Calls Derived::func() at RUNTIME!            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Complete Example: Virtual Function

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class base                              // Line 4: Base class
{
public:
    virtual void disp()                 // Line 7: VIRTUAL function
    {                                   // virtual keyword here is CRUCIAL!
        cout << "base disp" << endl;    // Line 9: Base implementation
    }
    void show()                         // Line 11: NON-virtual function
    {
        cout << "base show" << endl;
    }
};

class sub : public base                 // Line 16: sub inherits from base
{
public:
    void disp()                         // Line 19: OVERRIDING virtual disp()
    {                                   // No need to write 'virtual' here
        cout << "sub disp" << endl;     // Line 21: Derived implementation
    }
    void show()                         // Line 23: REDEFINITION (hiding) show()
    {
        cout << "sub show" << endl;
    }
};

int main()                              // Line 28: Main function
{
    base *ptr;                          // Line 30: Base class pointer
    
    ptr = new base;                     // Line 32: Point to base object
    ptr->disp();                        // Line 33: "base disp" (virtual)
    ptr->show();                        // Line 34: "base show" (non-virtual)
    
    ptr = new sub;                      // Line 36: UPCASTING - point to sub
    ptr->disp();                        // Line 37: "sub disp" (virtual - LATE BINDING!)
    ptr->show();                        // Line 38: "base show" (non-virtual - EARLY BINDING!)
    
    return 0;
}
```

**Output:**
```
base disp
base show
sub disp        ← Virtual function: calls sub's version!
base show       ← Non-virtual: STILL calls base's version!
```

### Execution Flow Diagram

```
┌────────────────────────────────────────────────────────────────────────────┐
│                    VIRTUAL vs NON-VIRTUAL BEHAVIOR                          │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ptr = new sub;       // ptr is base*, points to sub object               │
│                                                                             │
│   ptr->disp();         [VIRTUAL FUNCTION]                                  │
│   ├── Runtime checks: "What is ptr actually pointing to?"                  │
│   ├── Answer: A sub object                                                 │
│   ├── Decision: Call sub::disp()                                           │
│   └── Output: "sub disp"                                                   │
│                                                                             │
│   ptr->show();         [NON-VIRTUAL FUNCTION]                              │
│   ├── Compiler checks: "What is the TYPE of ptr?"                          │
│   ├── Answer: base*                                                        │
│   ├── Decision: Call base::show()                                          │
│   └── Output: "base show"                                                  │
│                                                                             │
│   This is the KEY difference between virtual and non-virtual!              │
│                                                                             │
└────────────────────────────────────────────────────────────────────────────┘
```

---

## Overriding vs Redefinition

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                 OVERRIDING vs REDEFINITION (HIDING)                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   REDEFINITION (Hiding):                  OVERRIDING:                        │
│   ─────────────────────                   ───────────                        │
│   class Base {                            class Base {                       │
│   public:                                 public:                            │
│       void func() { }  ← NON-virtual          virtual void func() { } ← VIRTUAL│
│   };                                      };                                 │
│                                                                              │
│   class Derived : public Base {           class Derived : public Base {      │
│   public:                                 public:                            │
│       void func() { }  ← HIDES base           void func() { }  ← OVERRIDES   │
│   };                                      };                                 │
│                                                                              │
│   Base *p = new Derived;                  Base *p = new Derived;             │
│   p->func();  // Calls BASE::func()       p->func();  // Calls DERIVED::func()│
│               ↑ EARLY BINDING                         ↑ LATE BINDING         │
│                                                                              │
│   ───────────────────────────────────────────────────────────────────────    │
│                                                                              │
│   REQUIREMENTS FOR OVERRIDING:                                               │
│   1. Base function must be VIRTUAL                                          │
│   2. Same function signature (name, parameters, const-ness)                 │
│   3. Same return type (or covariant return type)                            │
│   4. Access level can be different in derived class                         │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### The `override` Keyword (C++11)

```cpp
class base
{
public:
    virtual void disp() { cout << "base disp" << endl; }
};

class sub : public base
{
public:
    void disp() override           // 'override' keyword - RECOMMENDED!
    {                              // Compiler verifies this ACTUALLY overrides
        cout << "sub disp" << endl;
    }
    
    // void disp(int x) override   // ERROR! This doesn't override any base function
                                   // Compiler catches the mistake!
};
```

### Why Use `override`?

```
┌─────────────────────────────────────────────────────────────────┐
│                WHY USE 'override' KEYWORD?                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   WITHOUT override:                                              │
│   class sub : public base                                        │
│   {                                                              │
│       void disp(int x) { }  // Typo! Different signature        │
│   };                        // No error - creates NEW function  │
│                             // But we INTENDED to override!      │
│                                                                  │
│   WITH override:                                                 │
│   class sub : public base                                        │
│   {                                                              │
│       void disp(int x) override { }  // COMPILE ERROR!          │
│   };                                  // Compiler tells us this │
│                                       // doesn't override anything│
│                                                                  │
│   BENEFIT: Compiler catches signature mismatches!               │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## How Virtual Functions Work

### Virtual Table (VTABLE) and Virtual Pointer (VPTR)

> When a class has at least one virtual function, the compiler creates a **Virtual Table (VTABLE)** for that class and adds a hidden **Virtual Pointer (VPTR)** to each object.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    VTABLE AND VPTR MECHANISM                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   class base { virtual void disp(); virtual void show(); void fun(); };     │
│   class sub : public base { void disp(); void show(); };                    │
│                                                                              │
│   Base Class VTABLE:                   Derived Class VTABLE:                │
│   ┌─────────────────────────┐          ┌─────────────────────────┐          │
│   │ [0] base::disp          │          │ [0] sub::disp ← overridden│         │
│   │ [1] base::show          │          │ [1] sub::show ← overridden│         │
│   └─────────────────────────┘          └─────────────────────────┘          │
│              ↑                                    ↑                          │
│              │                                    │                          │
│   base object:                        sub object:                            │
│   ┌────────────────────────┐         ┌────────────────────────┐             │
│   │ vptr ─────────────────>│         │ vptr ─────────────────>│             │
│   │ base members           │         │ base members           │             │
│   └────────────────────────┘         │ sub members            │             │
│                                      └────────────────────────┘             │
│                                                                              │
│   WHEN ptr->disp() IS CALLED:                                               │
│   1. Follow ptr to get object                                               │
│   2. From object, get vptr                                                  │
│   3. From vptr, find VTABLE                                                 │
│   4. In VTABLE, lookup disp() entry                                         │
│   5. Call that function                                                     │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Memory Layout Example

```cpp
#include<iostream>
using namespace std;

class NoVirtual                         // NO virtual functions
{
    int x;                              // 4 bytes
};

class WithVirtual                       // HAS virtual functions
{
    int x;                              // 4 bytes
    virtual void func() { }             // Adds vptr!
};

int main()
{
    cout << "Size of NoVirtual: " << sizeof(NoVirtual) << " bytes" << endl;
    cout << "Size of WithVirtual: " << sizeof(WithVirtual) << " bytes" << endl;
    return 0;
}
```

**Output (64-bit system):**
```
Size of NoVirtual: 4 bytes
Size of WithVirtual: 16 bytes    ← Extra 8 bytes for vptr (pointer size)
```

### VTABLE Lookup Process

```
┌────────────────────────────────────────────────────────────────┐
│           VIRTUAL FUNCTION CALL PROCESS                         │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│   base *ptr = new sub;                                          │
│   ptr->disp();                                                  │
│                                                                 │
│   Step 1: Dereference ptr to get object                         │
│           ┌─────────────────────┐                               │
│   ptr ───>│ sub object          │                               │
│           │ ┌─────────────────┐ │                               │
│           │ │ vptr ───────────│─┼──┐                            │
│           │ └─────────────────┘ │  │                            │
│           │ (base members)      │  │                            │
│           │ (sub members)       │  │                            │
│           └─────────────────────┘  │                            │
│                                    │                            │
│   Step 2: Follow vptr to VTABLE    │                            │
│                                    ▼                            │
│           ┌────────────────────────────────┐                    │
│           │ sub's VTABLE                   │                    │
│           │ ┌────────────────────────────┐ │                    │
│           │ │ [0] sub::disp ←──────────────── Step 3: Lookup   │
│           │ │ [1] sub::show              │ │                    │
│           │ └────────────────────────────┘ │                    │
│           └────────────────────────────────┘                    │
│                                                                 │
│   Step 4: Call sub::disp()                                      │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

---

## Virtual Functions and Constructors

### Can Constructors Be Virtual?

> **No!** Constructors CANNOT be virtual because the virtual table is set up AFTER the constructor runs.

```
┌─────────────────────────────────────────────────────────────────┐
│              WHY CONSTRUCTORS CAN'T BE VIRTUAL?                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   1. Virtual function call requires VPTR                        │
│   2. VPTR is initialized by constructor                         │
│   3. Constructor runs BEFORE VPTR exists!                       │
│   4. So constructor can't use virtual mechanism                 │
│                                                                  │
│   Object Creation Sequence:                                      │
│   ┌──────────────────────────────────────────────────────────┐  │
│   │ 1. Memory allocated for object                           │  │
│   │ 2. Constructor is called                                 │  │
│   │    ├── VPTR is set to point to correct VTABLE           │  │
│   │    └── Member initialization                             │  │
│   │ 3. Object is ready (VPTR now valid)                      │  │
│   └──────────────────────────────────────────────────────────┘  │
│                                                                  │
│   At step 2, the VPTR is being SET UP - it can't be used yet!  │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Calling Virtual Functions from Constructor

```cpp
#include<iostream>
using namespace std;

class base
{
public:
    base()
    {
        cout << "base constructor" << endl;
        disp();                         // Calling virtual function from constructor
    }
    virtual void disp()
    {
        cout << "base disp" << endl;
    }
};

class sub : public base
{
public:
    sub()
    {
        cout << "sub constructor" << endl;
    }
    void disp() override
    {
        cout << "sub disp" << endl;
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
base constructor
base disp       ← NOT sub::disp! Base's version is called!
sub constructor
```

> [!WARNING]
> During base class constructor, the object is still a "base" object! The VPTR hasn't been updated to point to sub's VTABLE yet. So virtual function calls in constructor behave like non-virtual calls!

---

## Virtual Functions Best Practices

### 1. Should All Functions Be Virtual?

> **No!** Not all functions should be virtual. Virtual functions have overhead.

```
┌─────────────────────────────────────────────────────────────────┐
│          WHEN TO MAKE A FUNCTION VIRTUAL?                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   MAKE VIRTUAL when:                                             │
│   • You expect derived classes to provide different behavior    │
│   • The function is part of the "contract" for polymorphism    │
│   • Examples: draw(), makeSound(), process()                    │
│                                                                  │
│   DON'T MAKE VIRTUAL when:                                       │
│   • Function behavior should NOT change in derived classes      │
│   • Function is marked 'final' (cannot be overridden)          │
│   • Performance is critical and polymorphism isn't needed      │
│   • Examples: utility functions, getters/setters                │
│                                                                  │
│   OVERHEAD OF VIRTUAL:                                           │
│   • Extra memory: VPTR in each object (~8 bytes on 64-bit)     │
│   • Extra indirection: VTABLE lookup at runtime                 │
│   • Can prevent inlining (optimizer limitation)                 │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 2. Once Virtual, Always Virtual

> If base class function is virtual, the derived class function (with same signature) is AUTOMATICALLY virtual, even without the keyword.

```cpp
class A
{
public:
    virtual void func() { }             // Virtual in A
};

class B : public A
{
public:
    void func() { }                     // AUTOMATICALLY virtual in B!
};                                      // No need to write 'virtual'

class C : public B
{
public:
    void func() { }                     // Still virtual in C!
};
```

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────────────┐
│          VIRTUAL FUNCTIONS - KEY POINTS                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. VIRTUAL KEYWORD:                                             │
│     • Makes function use late binding                           │
│     • Enables runtime polymorphism                              │
│     • Compiler creates VTABLE for class                         │
│                                                                  │
│  2. OVERRIDING:                                                  │
│     • Redefining a VIRTUAL function in derived class           │
│     • Same signature required                                    │
│     • Use 'override' keyword (C++11) for safety                 │
│                                                                  │
│  3. VTABLE/VPTR:                                                 │
│     • VTABLE: Table of virtual function pointers               │
│     • VPTR: Hidden pointer in object to VTABLE                 │
│     • Runtime lookup determines which function to call          │
│                                                                  │
│  4. RULES:                                                       │
│     • Constructor CANNOT be virtual                             │
│     • Virtual calls in constructor use BASE version            │
│     • Once virtual, always virtual in derived classes          │
│     • Virtual function adds memory overhead (VPTR)             │
│                                                                  │
│  5. BEST PRACTICES:                                              │
│     • Use 'override' keyword in derived classes                 │
│     • Use 'final' to prevent further overriding                │
│     • Only make functions virtual when polymorphism needed     │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Previous: [Upcasting and Binding](39_Upcasting_and_Binding.md)*
*Next: [VTABLE and VPTR Mechanism](41_VTABLE_VPTR_Mechanism.md)*
