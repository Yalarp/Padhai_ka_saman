# 🔍 RTTI and typeid Operator in C++

## Table of Contents
1. [What is RTTI?](#what-is-rtti)
2. [The typeid Operator](#the-typeid-operator)
3. [Using typeid with Polymorphism](#using-typeid-with-polymorphism)
4. [type_info Class](#type_info-class)
5. [Key Takeaways](#key-takeaways)

---

## What is RTTI?

> **RTTI (Runtime Type Identification)**: A mechanism that allows the type of an object to be determined during program execution (runtime).

```
┌─────────────────────────────────────────────────────────────────┐
│                        RTTI CONCEPT                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   WHY DO WE NEED RTTI?                                           │
│                                                                  │
│   Consider:                                                      │
│   base *ptr = getObject();  // Returns some derived type        │
│                                                                  │
│   Question: What is the ACTUAL type of object ptr points to?    │
│                                                                  │
│   • Compile-time: ptr is of type base*                          │
│   • Runtime: Object could be base, sub1, sub2, or any derived   │
│                                                                  │
│   RTTI lets us:                                                  │
│   1. IDENTIFY the actual runtime type of an object              │
│   2. SAFELY downcast (dynamic_cast)                             │
│   3. COMPARE types of objects                                   │
│                                                                  │
│   C++ provides two RTTI features:                               │
│   • typeid operator - Get type information                      │
│   • dynamic_cast operator - Safe downcasting                    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### RTTI Requirements

> [!IMPORTANT]
> RTTI works correctly only with **polymorphic types** (classes with at least one virtual function).

```
┌─────────────────────────────────────────────────────────────────┐
│                    RTTI REQUIREMENTS                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   For RTTI to work correctly:                                    │
│   1. Base class must have at least ONE virtual function         │
│   2. Without virtual function, typeid gives STATIC type         │
│   3. Virtual function enables runtime type checking             │
│                                                                  │
│   class NonPolymorphic { };           // No virtual function    │
│   class Polymorphic { virtual ~Polymorphic() { } };  // Virtual │
│                                                                  │
│   Polymorphic* p = new Derived;                                 │
│   typeid(*p) → Gives "Derived" (runtime type) ✓                │
│                                                                  │
│   NonPolymorphic* np = new DerivedNP;                           │
│   typeid(*np) → Gives "NonPolymorphic" (static type) ✗         │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## The typeid Operator

### Syntax and Basic Usage

```cpp
#include <typeinfo>  // Required header!

typeid(expression)   // Returns reference to type_info object
typeid(type)         // Also works with type names
```

### Example 1: Basic typeid Usage

```cpp
#include<iostream>                      // Line 1: Include iostream
#include<typeinfo>                      // Line 2: REQUIRED for typeid!
using namespace std;                    // Line 3: Use standard namespace

class base                              // Line 5: Base class
{
public:
    virtual void disp()                 // Line 8: Virtual function (enables RTTI!)
    {
        cout << "base disp" << endl;
    }
};

class sub : public base                 // Line 14: Derived class
{
public:
    void disp() override                // Line 17: Override
    {
        cout << "sub disp" << endl;
    }
};

int main()                              // Line 23: Main function
{
    // Using typeid with objects
    base b;
    sub s;
    
    cout << "Type of b: " << typeid(b).name() << endl;  // Line 28
    cout << "Type of s: " << typeid(s).name() << endl;  // Line 29
    
    // Using typeid with pointers
    base *ptr = new sub;                // Line 32: Upcasting
    
    cout << "Type of ptr: " << typeid(ptr).name() << endl;      // Line 34: Type of POINTER
    cout << "Type of *ptr: " << typeid(*ptr).name() << endl;    // Line 35: Type of OBJECT
    
    // Comparing types
    if(typeid(*ptr) == typeid(sub))     // Line 38: Type comparison
    {
        cout << "ptr points to a sub object" << endl;
    }
    
    if(typeid(*ptr) == typeid(b))       // Line 43: Won't match
    {
        cout << "ptr points to a base object" << endl;
    }
    
    delete ptr;
    return 0;
}
```

**Output (may vary by compiler):**
```
Type of b: 4base
Type of s: 3sub
Type of ptr: P4base          ← POINTER type (P = Pointer)
Type of *ptr: 3sub           ← OBJECT type (runtime)
ptr points to a sub object
```

### Important Distinction

```
┌─────────────────────────────────────────────────────────────────────────────┐
│              typeid(ptr) vs typeid(*ptr)                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   base *ptr = new sub;                                                       │
│                                                                              │
│   typeid(ptr)                         typeid(*ptr)                           │
│   ─────────────                       ────────────                           │
│   • Returns type of POINTER           • Returns type of OBJECT              │
│   • Always "base*"                    • "sub" (runtime type)                │
│   • Static information                • Dynamic information (RTTI)          │
│                                                                              │
│   Example:                                                                   │
│   cout << typeid(ptr).name();  // "P4base" (pointer to base)               │
│   cout << typeid(*ptr).name(); // "3sub" (actual object type)              │
│                                                                              │
│   REMEMBER: Use *ptr to get the actual object type!                        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Using typeid with Polymorphism

### Example 2: Complete Polymorphic Example

```cpp
#include<iostream>                      // Line 1: Include iostream
#include<typeinfo>                      // Line 2: Include typeinfo
using namespace std;                    // Line 3: Use standard namespace

class Animal                            // Line 5: Abstract base class
{
public:
    virtual void speak() = 0;           // Line 8: Pure virtual (makes it polymorphic)
    virtual ~Animal() { }               // Line 9: Virtual destructor
};

class Dog : public Animal               // Line 12: Derived class
{
public:
    void speak() override { cout << "Woof!" << endl; }
    void fetch() { cout << "Fetching ball!" << endl; }  // Dog-specific
};

class Cat : public Animal               // Line 19: Derived class
{
public:
    void speak() override { cout << "Meow!" << endl; }
    void scratch() { cout << "Scratching post!" << endl; }  // Cat-specific
};

void identifyAnimal(Animal *ptr)        // Line 26: Polymorphic function
{
    cout << "Actual type: " << typeid(*ptr).name() << endl;
    
    if(typeid(*ptr) == typeid(Dog))     // Line 30: Check if it's a Dog
    {
        cout << "It's a Dog!" << endl;
        Dog *dptr = static_cast<Dog*>(ptr);  // Now safe to cast
        dptr->fetch();
    }
    else if(typeid(*ptr) == typeid(Cat))// Line 36: Check if it's a Cat
    {
        cout << "It's a Cat!" << endl;
        Cat *cptr = static_cast<Cat*>(ptr);
        cptr->scratch();
    }
    else
    {
        cout << "Unknown animal type" << endl;
    }
}

int main()                              // Line 47: Main function
{
    Animal *animals[2];
    animals[0] = new Dog;               // Line 50: Create Dog
    animals[1] = new Cat;               // Line 51: Create Cat
    
    cout << "=== Identifying animals ===" << endl;
    for(int i = 0; i < 2; i++)
    {
        identifyAnimal(animals[i]);
        cout << "---" << endl;
    }
    
    // Cleanup
    for(int i = 0; i < 2; i++)
        delete animals[i];
    
    return 0;
}
```

**Output:**
```
=== Identifying animals ===
Actual type: 3Dog
It's a Dog!
Fetching ball!
---
Actual type: 3Cat
It's a Cat!
Scratching post!
---
```

---

## type_info Class

### The type_info Object

> `typeid` returns a reference to a `type_info` object. This class provides methods to compare and get information about types.

```cpp
class type_info
{
public:
    bool operator==(const type_info& rhs) const;  // Compare types
    bool operator!=(const type_info& rhs) const;  // Compare types
    bool before(const type_info& rhs) const;      // Ordering
    const char* name() const;                      // Type name (implementation-defined)
    size_t hash_code() const;                      // Hash value (C++11)
    
    // Cannot copy or assign
    type_info& operator=(const type_info&) = delete;
};
```

### Using type_info Methods

```cpp
#include<iostream>
#include<typeinfo>
using namespace std;

class base { virtual void f() { } };
class sub : public base { };

int main()
{
    base *ptr1 = new base;
    base *ptr2 = new sub;
    base *ptr3 = new sub;
    
    // Using name()
    cout << "ptr1 points to: " << typeid(*ptr1).name() << endl;
    cout << "ptr2 points to: " << typeid(*ptr2).name() << endl;
    
    // Using == and !=
    if(typeid(*ptr1) == typeid(*ptr2))
        cout << "ptr1 and ptr2 point to same type" << endl;
    else
        cout << "ptr1 and ptr2 point to different types" << endl;
    
    if(typeid(*ptr2) == typeid(*ptr3))
        cout << "ptr2 and ptr3 point to same type" << endl;
    
    // Using hash_code()
    cout << "Hash of base: " << typeid(base).hash_code() << endl;
    cout << "Hash of sub: " << typeid(sub).hash_code() << endl;
    
    delete ptr1;
    delete ptr2;
    delete ptr3;
    return 0;
}
```

**Output:**
```
ptr1 points to: 4base
ptr2 points to: 3sub
ptr1 and ptr2 point to different types
ptr2 and ptr3 point to same type
Hash of base: 12345678
Hash of sub: 87654321
```

---

## typeid with Non-Polymorphic Types

> [!WARNING]
> Without virtual functions, typeid gives the STATIC (compile-time) type, not the runtime type!

```cpp
#include<iostream>
#include<typeinfo>
using namespace std;

class NonPolyBase                       // NO virtual function!
{
public:
    void show() { cout << "NonPolyBase" << endl; }
};

class NonPolySub : public NonPolyBase
{
public:
    void show() { cout << "NonPolySub" << endl; }
};

int main()
{
    NonPolyBase *ptr = new NonPolySub;
    
    // typeid gives STATIC type because no virtual function!
    cout << "typeid(*ptr): " << typeid(*ptr).name() << endl;
    
    // Compare: with polymorphic base
    class PolyBase { public: virtual ~PolyBase() { } };
    class PolySub : public PolyBase { };
    
    PolyBase *ptr2 = new PolySub;
    cout << "typeid(*ptr2): " << typeid(*ptr2).name() << endl;
    
    delete ptr;
    delete ptr2;
    return 0;
}
```

**Output:**
```
typeid(*ptr): 11NonPolyBase     ← STATIC type (wrong!)
typeid(*ptr2): 7PolySub         ← RUNTIME type (correct!)
```

### Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│               typeid BEHAVIOR: POLYMORPHIC vs NON-POLYMORPHIC                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   NON-POLYMORPHIC CLASS:              POLYMORPHIC CLASS:                     │
│   class Base { };                     class Base { virtual ~Base() { } };   │
│                                                                              │
│   Base *ptr = new Derived;            Base *ptr = new Derived;              │
│   typeid(*ptr);                       typeid(*ptr);                          │
│                                                                              │
│   Result: "Base"                      Result: "Derived"                      │
│   (Static type at compile time)       (Runtime type - RTTI)                  │
│                                                                              │
│   WHY?                                 WHY?                                   │
│   • No VPTR, no VTABLE                • VPTR and VTABLE exist               │
│   • Compiler uses static type         • typeid looks at VPTR                │
│   • No runtime information            • Gets actual object type             │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────────────┐
│              RTTI AND typeid - KEY POINTS                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. RTTI (Runtime Type Identification):                          │
│     • Mechanism to determine type at runtime                    │
│     • Requires polymorphic types (virtual functions)            │
│     • Two operators: typeid and dynamic_cast                    │
│                                                                  │
│  2. typeid OPERATOR:                                             │
│     • Returns type_info reference                               │
│     • #include <typeinfo> required                              │
│     • Works with expressions and type names                     │
│                                                                  │
│  3. typeid(ptr) vs typeid(*ptr):                                 │
│     • typeid(ptr) = pointer type (always same)                  │
│     • typeid(*ptr) = actual object type (RTTI)                  │
│                                                                  │
│  4. type_info CLASS:                                             │
│     • name() - type name (compiler dependent)                   │
│     • operator== / operator!= - compare types                   │
│     • hash_code() - unique hash (C++11)                         │
│                                                                  │
│  5. NON-POLYMORPHIC WARNING:                                     │
│     • Without virtual function, typeid gives STATIC type        │
│     • Add virtual function (even destructor) for RTTI          │
│                                                                  │
│  6. COMMON USES:                                                 │
│     • Debug printing of types                                   │
│     • Type checking before casting                              │
│     • Factory patterns                                          │
│     • Serialization                                             │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Previous: [Object Slicing](44_Object_Slicing.md)*
*Next: [Type Casting in C++](46_Type_Casting_in_Cpp.md)*
