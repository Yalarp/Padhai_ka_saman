# 🔄 Type Casting in C++

## Table of Contents
1. [C-Style Casting](#c-style-casting)
2. [C++ Cast Operators](#c-cast-operators)
3. [static_cast](#static_cast)
4. [dynamic_cast](#dynamic_cast)
5. [const_cast](#const_cast)
6. [reinterpret_cast](#reinterpret_cast)
7. [Comparison of Cast Operators](#comparison-of-cast-operators)
8. [Key Takeaways](#key-takeaways)

---

## C-Style Casting

### The Old Way (Avoid!)

```cpp
// C-style cast syntax
(target_type) expression
target_type(expression)
```

```cpp
#include<iostream>
using namespace std;

int main()
{
    double d = 3.14159;
    int i = (int)d;                     // C-style cast
    int j = int(d);                     // Function-style cast
    
    cout << "d = " << d << endl;        // 3.14159
    cout << "i = " << i << endl;        // 3
    cout << "j = " << j << endl;        // 3
    
    return 0;
}
```

### Problems with C-Style Casts

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                  PROBLEMS WITH C-STYLE CASTS                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   1. TOO POWERFUL:                                                           │
│      • Can convert between almost any types                                 │
│      • No safety checks                                                      │
│      • Silent failures                                                       │
│                                                                              │
│   2. HARD TO FIND:                                                           │
│      • (int)x looks similar to normal code                                  │
│      • Difficult to search for in large codebase                            │
│      • grep "(int)" gives many false positives                              │
│                                                                              │
│   3. NOT SPECIFIC:                                                           │
│      • Same syntax for different types of conversions                       │
│      • Can't tell intent: numeric? pointer? const removal?                  │
│      • No distinction between safe and unsafe casts                         │
│                                                                              │
│   C++ SOLUTION: Four distinct cast operators                                │
│   • static_cast     - compile-time checked conversions                      │
│   • dynamic_cast    - runtime-checked downcasting                           │
│   • const_cast      - add/remove const/volatile                             │
│   • reinterpret_cast - bit reinterpretation                                 │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## C++ Cast Operators

### Syntax

```cpp
cast_operator<target_type>(expression)
```

### Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      C++ CAST OPERATORS                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   CAST              │ PURPOSE                     │ SAFETY                  │
│   ──────────────────│─────────────────────────────│──────────────────────── │
│   static_cast       │ Standard conversions        │ Compile-time check     │
│   dynamic_cast      │ Safe downcasting           │ Runtime check          │
│   const_cast        │ Remove/add const           │ No value conversion    │
│   reinterpret_cast  │ Bit reinterpretation       │ DANGEROUS              │
│                                                                              │
│   USAGE GUIDELINE:                                                           │
│   • Prefer static_cast for basic conversions                                │
│   • Use dynamic_cast for safe polymorphic downcasting                       │
│   • Use const_cast only when necessary (legacy APIs)                        │
│   • Avoid reinterpret_cast unless absolutely needed                         │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## static_cast

> **static_cast**: Compile-time checked cast for standard, well-defined conversions.

### Use Cases

1. Numeric type conversions
2. Upcasting (derived → base)
3. Downcasting (base → derived) - **NO runtime check!**
4. Void pointer conversions
5. Explicit constructor calls

### Example 1: Numeric Conversions

```cpp
#include<iostream>
using namespace std;

int main()
{
    // Numeric conversions
    double d = 3.14159;
    int i = static_cast<int>(d);        // double to int
    
    int x = 10, y = 3;
    double result = static_cast<double>(x) / y;  // Force double division
    
    cout << "d = " << d << ", i = " << i << endl;       // 3.14159, 3
    cout << "x/y = " << x/y << endl;                     // 3 (integer division)
    cout << "result = " << result << endl;               // 3.33333
    
    return 0;
}
```

### Example 2: Upcasting and Downcasting

```cpp
#include<iostream>
using namespace std;

class base
{
public:
    virtual void disp() { cout << "base disp" << endl; }
};

class sub : public base
{
public:
    int data = 42;
    void disp() override { cout << "sub disp" << endl; }
    void subOnly() { cout << "sub only method, data = " << data << endl; }
};

int main()
{
    sub s;
    
    // UPCASTING (always safe)
    base *bptr = static_cast<base*>(&s);    // OK
    bptr->disp();                            // "sub disp" (virtual)
    
    // DOWNCASTING (NO runtime check - programmer's responsibility!)
    sub *sptr = static_cast<sub*>(bptr);    // OK if bptr actually points to sub
    sptr->subOnly();                         // Works
    
    // DANGER: Incorrect downcast
    base b;
    base *bptr2 = &b;
    sub *sptr2 = static_cast<sub*>(bptr2);  // COMPILES but WRONG!
    // sptr2->subOnly();                     // UNDEFINED BEHAVIOR!
    
    return 0;
}
```

### static_cast Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    static_cast BEHAVIOR                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   UPCASTING (Derived → Base): ALWAYS SAFE                                   │
│   ┌─────────────────┐                                                        │
│   │     Base        │ ← static_cast<Base*>(derivedPtr)                      │
│   └────────┬────────┘   Always works, no data loss                          │
│            │                                                                 │
│   ┌────────┴────────┐                                                        │
│   │    Derived      │                                                        │
│   └─────────────────┘                                                        │
│                                                                              │
│   DOWNCASTING (Base → Derived): POTENTIALLY DANGEROUS!                      │
│   ┌─────────────────┐                                                        │
│   │     Base        │ ← static_cast<Derived*>(basePtr)                      │
│   └────────┬────────┘   Compiles but:                                       │
│            │            • If basePtr points to Derived: OK                  │
│   ┌────────┴────────┐   • If basePtr points to Base: UNDEFINED BEHAVIOR!   │
│   │    Derived      │                                                        │
│   └─────────────────┘                                                        │
│                                                                              │
│   NO RUNTIME CHECK! Use dynamic_cast for safe downcasting.                  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## dynamic_cast

> **dynamic_cast**: Runtime-checked cast for safe polymorphic downcasting. Returns nullptr (pointers) or throws exception (references) on failure.

### Requirements

1. Source class MUST be polymorphic (have virtual function)
2. Uses RTTI for runtime type checking
3. Only for pointers and references

### Example 1: Safe Downcasting

```cpp
#include<iostream>                      // Line 1: Include iostream
#include<typeinfo>                      // Line 2: Include typeinfo
using namespace std;                    // Line 3: Use standard namespace

class Animal                            // Line 5: Base class
{
public:
    virtual void speak() = 0;           // Line 8: Virtual function (REQUIRED!)
    virtual ~Animal() { }               // Line 9: Virtual destructor
};

class Dog : public Animal               // Line 12: Derived class
{
public:
    void speak() override { cout << "Woof!" << endl; }
    void fetch() { cout << "Fetching!" << endl; }   // Dog-specific
};

class Cat : public Animal               // Line 19: Derived class
{
public:
    void speak() override { cout << "Meow!" << endl; }
    void scratch() { cout << "Scratching!" << endl; }  // Cat-specific
};

void interact(Animal *ptr)              // Line 26: Polymorphic function
{
    ptr->speak();                       // Works for all animals
    
    // Try to cast to Dog
    Dog *dptr = dynamic_cast<Dog*>(ptr);  // Line 31: SAFE downcast
    if(dptr != nullptr)                 // Line 32: Check if cast succeeded
    {
        cout << "It's a Dog! Can fetch." << endl;
        dptr->fetch();
    }
    else
    {
        cout << "Not a Dog." << endl;
    }
    
    // Try to cast to Cat
    Cat *cptr = dynamic_cast<Cat*>(ptr);  // Line 43: SAFE downcast
    if(cptr != nullptr)
    {
        cout << "It's a Cat! Can scratch." << endl;
        cptr->scratch();
    }
}

int main()                              // Line 51: Main function
{
    Animal *animals[2];
    animals[0] = new Dog;
    animals[1] = new Cat;
    
    for(int i = 0; i < 2; i++)
    {
        cout << "=== Animal " << i+1 << " ===" << endl;
        interact(animals[i]);
        cout << endl;
    }
    
    // Cleanup
    for(int i = 0; i < 2; i++)
        delete animals[i];
    
    return 0;
}
```

**Output:**
```
=== Animal 1 ===
Woof!
It's a Dog! Can fetch.
Fetching!

=== Animal 2 ===
Meow!
Not a Dog.
It's a Cat! Can scratch.
Scratching!
```

### Example 2: dynamic_cast with References

```cpp
#include<iostream>
using namespace std;

class base { public: virtual ~base() { } };
class sub : public base { public: int x = 42; };

void process(base &ref)
{
    try
    {
        sub &sref = dynamic_cast<sub&>(ref);  // May throw!
        cout << "Successfully cast, sref.x = " << sref.x << endl;
    }
    catch(bad_cast &e)                  // Catch failed cast
    {
        cout << "Cast failed: " << e.what() << endl;
    }
}

int main()
{
    sub s;
    base b;
    
    cout << "Passing sub: ";
    process(s);                         // Works
    
    cout << "Passing base: ";
    process(b);                         // Throws!
    
    return 0;
}
```

**Output:**
```
Passing sub: Successfully cast, sref.x = 42
Passing base: Cast failed: Bad dynamic_cast!
```

### dynamic_cast Behavior

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                   dynamic_cast BEHAVIOR                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   WITH POINTERS:                                                             │
│   Derived* dptr = dynamic_cast<Derived*>(basePtr);                          │
│   • Returns Derived* if basePtr points to Derived                           │
│   • Returns nullptr if basePtr points to Base (or other type)              │
│                                                                              │
│   WITH REFERENCES:                                                           │
│   Derived& dref = dynamic_cast<Derived&>(baseRef);                          │
│   • Returns reference if baseRef refers to Derived                          │
│   • THROWS std::bad_cast if baseRef refers to other type                   │
│                                                                              │
│   REQUIREMENTS:                                                              │
│   1. Source type must be POLYMORPHIC (virtual function)                     │
│   2. Only works with pointers and references                                │
│   3. Uses RTTI (must be enabled)                                            │
│                                                                              │
│   CROSSCASTING (Multiple Inheritance):                                       │
│   class D : public B1, public B2 { };                                       │
│   B1 *b1 = new D;                                                           │
│   B2 *b2 = dynamic_cast<B2*>(b1);  // Crosscast from B1 to B2              │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## const_cast

> **const_cast**: Add or remove const (or volatile) qualifier. Does NOT change the actual value.

### Use Cases

1. Remove const from pointer/reference (to call legacy non-const API)
2. Add const to pointer/reference (rare)

### Example

```cpp
#include<iostream>
using namespace std;

void legacyAPI(char *str)               // Old API that doesn't use const
{
    cout << str << endl;
}

int main()
{
    const char *message = "Hello, World!";
    
    // legacyAPI(message);              // ERROR! Can't pass const to non-const
    
    legacyAPI(const_cast<char*>(message));  // OK, but don't modify!
    
    // Dangerous modification
    const int x = 10;
    int *ptr = const_cast<int*>(&x);
    // *ptr = 20;                       // UNDEFINED BEHAVIOR! x is truly const
    
    // Safe use: with non-const original
    int y = 100;
    const int *cptr = &y;               // const view of non-const data
    int *mptr = const_cast<int*>(cptr); // OK to remove const here
    *mptr = 200;                        // OK, y was not const
    cout << "y = " << y << endl;        // 200
    
    return 0;
}
```

> [!CAUTION]
> Using const_cast to modify a truly const object is **UNDEFINED BEHAVIOR**!

---

## reinterpret_cast

> **reinterpret_cast**: Low-level bit reinterpretation. Most dangerous cast - use sparingly!

### Use Cases

1. Convert between unrelated pointer types
2. Convert pointer to integer and back
3. Low-level memory operations

### Example

```cpp
#include<iostream>
using namespace std;

int main()
{
    int x = 65;
    
    // Treat int as char array
    char *cptr = reinterpret_cast<char*>(&x);
    cout << "First byte: " << *cptr << endl;  // 'A' (ASCII 65)
    
    // Convert pointer to integer
    uintptr_t addr = reinterpret_cast<uintptr_t>(&x);
    cout << "Address as int: " << addr << endl;
    
    // Convert back to pointer
    int *iptr = reinterpret_cast<int*>(addr);
    cout << "Value: " << *iptr << endl;       // 65
    
    return 0;
}
```

> [!WARNING]
> reinterpret_cast is platform-dependent and can lead to undefined behavior. Only use when you truly understand the memory layout!

---

## Comparison of Cast Operators

```
┌────────────────────────────────────────────────────────────────────────────────────┐
│                      C++ CAST OPERATORS COMPARISON                                  │
├────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│   CAST              │ SAFETY  │ CHECK     │ USE FOR                               │
│   ──────────────────│─────────│───────────│─────────────────────────────────────  │
│   static_cast       │ Medium  │ Compile   │ Numeric conversions, upcasting,       │
│                     │         │           │ explicit type conversions             │
│                     │         │           │                                        │
│   dynamic_cast      │ High    │ Runtime   │ Safe downcasting with polymorphism   │
│                     │         │           │ Returns nullptr or throws on failure │
│                     │         │           │                                        │
│   const_cast        │ Medium  │ None      │ Remove/add const qualifier           │
│                     │         │           │ For legacy API compatibility          │
│                     │         │           │                                        │
│   reinterpret_cast  │ Low     │ None      │ Low-level bit manipulation           │
│                     │         │           │ Pointer ↔ integer, unrelated types   │
│                                                                                     │
│   PREFERENCE ORDER:                                                                 │
│   1. No cast (prefer implicit conversions)                                         │
│   2. static_cast (for standard conversions)                                        │
│   3. dynamic_cast (for safe polymorphic downcasting)                               │
│   4. const_cast (when absolutely necessary)                                        │
│   5. reinterpret_cast (last resort)                                                │
│                                                                                     │
└────────────────────────────────────────────────────────────────────────────────────┘
```

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────────────┐
│              TYPE CASTING - KEY POINTS                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. AVOID C-STYLE CASTS:                                         │
│     • Hard to find, too powerful                                │
│     • Use C++ cast operators instead                            │
│                                                                  │
│  2. static_cast:                                                 │
│     • Safe for numeric and upcast conversions                  │
│     • NO runtime check for downcasting                          │
│     • Most common cast in C++ code                              │
│                                                                  │
│  3. dynamic_cast:                                                │
│     • Runtime-checked safe downcasting                          │
│     • Requires polymorphic types (virtual function)             │
│     • Returns nullptr (ptr) or throws bad_cast (ref)           │
│                                                                  │
│  4. const_cast:                                                  │
│     • Only for adding/removing const                            │
│     • Don't modify truly const data (UB!)                       │
│     • Use for legacy API compatibility                          │
│                                                                  │
│  5. reinterpret_cast:                                            │
│     • Low-level bit reinterpretation                            │
│     • Platform-dependent                                         │
│     • Use only when absolutely necessary                        │
│                                                                  │
│  6. BEST PRACTICE:                                               │
│     • Minimize casting                                           │
│     • Use the most specific cast                                │
│     • dynamic_cast for polymorphic downcasting                  │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Previous: [RTTI and typeid Operator](45_RTTI_typeid_Operator.md)*
*Next: [Covariant Return Types](47_Covariant_Return_Types.md)*
