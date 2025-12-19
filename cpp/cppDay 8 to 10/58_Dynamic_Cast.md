# C++ dynamic_cast and RTTI

## Table of Contents
- [Introduction](#introduction)
- [What is RTTI?](#what-is-rtti)
- [dynamic_cast Syntax](#dynamic_cast-syntax)
- [dynamic_cast with Pointers](#dynamic_cast-with-pointers)
- [dynamic_cast with References](#dynamic_cast-with-references)
- [Requirements for dynamic_cast](#requirements-for-dynamic_cast)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Common Mistakes](#common-mistakes)
- [Interview Questions](#interview-questions)

---

## Introduction

`dynamic_cast` is a C++ operator used for safe downcasting in class hierarchies. Unlike `static_cast`, it performs runtime type checking to ensure the cast is valid. If the cast is not valid, `dynamic_cast` returns `nullptr` (for pointers) or throws a `bad_cast` exception (for references).

---

## What is RTTI?

> **RTTI** = Run-Time Type Information

RTTI is a mechanism that allows the type of an object to be determined during program execution. C++ provides two features for RTTI:

| Feature | Purpose |
|---------|---------|
| `typeid` | Returns type information of an expression |
| `dynamic_cast` | Safely converts pointers/references within class hierarchy |

### When is RTTI Available?
- Only for **polymorphic classes** (classes with at least one virtual function)
- The compiler stores type info in the vtable

---

## dynamic_cast Syntax

### For Pointers:
```cpp
Derived* ptr = dynamic_cast<Derived*>(basePtr);
if (ptr) {
    // Cast successful
} else {
    // Cast failed, ptr is nullptr
}
```

### For References:
```cpp
try {
    Derived& ref = dynamic_cast<Derived&>(baseRef);
    // Cast successful
} catch (bad_cast& e) {
    // Cast failed
}
```

---

## dynamic_cast with Pointers

When using `dynamic_cast` with pointers:
- Returns valid pointer if cast is successful
- Returns `nullptr` if cast fails
- Must check return value before using

### Visualization:

```
Base class hierarchy:
                    ┌──────────┐
                    │  Course  │ (virtual start())
                    └────┬─────┘
           ┌─────────────┼─────────────┐
           ▼             ▼             ▼
     ┌─────────┐   ┌─────────┐   ┌─────────┐
     │ Predac  │   │  Mscit  │   │   Dac   │
     └─────────┘   └─────────┘   └─────────┘
                                 orientation()

Course* ptr = new Dac();
Dac* dacPtr = dynamic_cast<Dac*>(ptr);  // ✓ Success

Course* ptr2 = new Mscit();
Dac* dacPtr2 = dynamic_cast<Dac*>(ptr2); // ✗ Returns nullptr
```

---

## dynamic_cast with References

When using `dynamic_cast` with references:
- Cannot return `nullptr` (references can't be null)
- Throws `std::bad_cast` exception if cast fails
- Must use try-catch for error handling

### Syntax:
```cpp
try {
    Derived& ref = dynamic_cast<Derived&>(baseRef);
    // Use ref safely
} catch (std::bad_cast& e) {
    // Handle failed cast
}
```

---

## Requirements for dynamic_cast

1. **Polymorphic Class Required**: Base class must have at least one virtual function
2. **Include `<typeinfo>`**: For `bad_cast` exception
3. **RTTI Must Be Enabled**: Most compilers enable by default

### Why Virtual Function is Required?
Without virtual functions, the compiler doesn't generate RTTI information (stored in vtable). The cast would have no way to verify the actual object type at runtime.

---

## Code Examples

### Example 1: dynamic_cast with Pointers

```cpp
#include<iostream>      // Line 1
using namespace std;    // Line 2

class Course            // Line 4: Base class
{
public:
    virtual void start()  // Line 7: MUST be virtual for dynamic_cast
    {
    }
};

class Predac : public Course     // Line 12: Derived class
{
public:
    void start()         // Line 15: Override
    {
        cout << "predac start" << endl;
    }
};

class Mscit : public Course      // Line 21: Another derived class
{
public:
    void start()         // Line 24
    {
        cout << "Mscit start" << endl;
    }
};

class Dac : public Course        // Line 30: Derived with extra method
{
public:
    void start()         // Line 33
    {
        cout << "Dac start" << endl;
    }
    void orientation()   // Line 37: Unique to Dac
    {
        cout << "Dac orientation" << endl;
    }
};

void perform(Course *ptr)        // Line 43: Accepts base class pointer
{
    Dac *temp = dynamic_cast<Dac*>(ptr);  // Line 45: Try to cast to Dac*
    
    if (temp)            // Line 47: Check if cast succeeded
    {
        temp->orientation();  // Line 49: Safe to call Dac-specific method
    }
    ptr->start();        // Line 51: Call virtual method (works for all)
}

void main()
{
    perform(new Mscit);  // Line 55: Pass Mscit object
    perform(new Predac); // Line 56: Pass Predac object
    perform(new Dac);    // Line 57: Pass Dac object
}
```

### Line-by-Line Explanation:

| Line | Code | Explanation |
|------|------|-------------|
| 7 | `virtual void start()` | Makes Course polymorphic (required for dynamic_cast) |
| 37 | `void orientation()` | Method unique to Dac class |
| 45 | `dynamic_cast<Dac*>(ptr)` | Attempts to cast base pointer to Dac* |
| 47 | `if (temp)` | Checks if cast was successful (non-null) |
| 49 | `temp->orientation()` | Safe to call - we verified temp is valid Dac* |
| 55-57 | `perform(new ...)` | Tests with different derived types |

### Execution Flow:

```
perform(new Mscit):
┌─────────────────────────────────────┐
│ ptr points to Mscit object          │
│ dynamic_cast<Dac*>(ptr) → nullptr   │
│ (Mscit is NOT a Dac)                │
│ if(temp) is FALSE - skip block      │
│ ptr->start() → "Mscit start"        │
└─────────────────────────────────────┘

perform(new Predac):
┌─────────────────────────────────────┐
│ ptr points to Predac object         │
│ dynamic_cast<Dac*>(ptr) → nullptr   │
│ (Predac is NOT a Dac)               │
│ if(temp) is FALSE - skip block      │
│ ptr->start() → "predac start"       │
└─────────────────────────────────────┘

perform(new Dac):
┌─────────────────────────────────────┐
│ ptr points to Dac object            │
│ dynamic_cast<Dac*>(ptr) → valid Dac*│
│ (Dac IS a Dac - success!)           │
│ if(temp) is TRUE                    │
│ temp->orientation() → "Dac orient." │
│ ptr->start() → "Dac start"          │
└─────────────────────────────────────┘
```

### Output:
```
Mscit start
predac start
Dac orientation
Dac start
```

### Example 2: dynamic_cast with References

```cpp
#include<iostream>      // Line 1
using namespace std;    // Line 2

class Course            // Line 4
{
public:
    virtual void start()  // Line 7
    {
    }
};

class Predac : public Course     // Line 12
{
public:
    void start()
    {
        cout << "predac start" << endl;
    }
};

class Mscit : public Course      // Line 21
{
public:
    void start()
    {
        cout << "Mscit start" << endl;
    }
};

class Dac : public Course        // Line 30
{
public:
    void start()
    {
        cout << "Dac start" << endl;
    }
    void orientation()
    {
        cout << "Dac orientation" << endl;
    }
};

void perform(Course& ref)        // Line 43: Accepts base class reference
{
    try                          // Line 45: Must use try-catch for references
    {
        Dac& temp = dynamic_cast<Dac&>(ref);  // Line 47: Try to cast
        temp.orientation();      // Line 48: Only reached if cast succeeds
    }
    catch (bad_cast& b)          // Line 50: Catch failed cast
    {
        // Cast failed - ref is not a Dac
    }
    ref.start();                 // Line 54: Always executed
}

void main()
{
    Predac p;            // Line 58: Create objects
    Mscit m;             // Line 59
    Dac d;               // Line 60
    
    perform(p);          // Line 62: Pass by reference
    perform(m);          // Line 63
    perform(d);          // Line 64
}
```

### Key Differences from Pointer Version:

| Aspect | Pointer Version | Reference Version |
|--------|-----------------|-------------------|
| Failure | Returns `nullptr` | Throws `bad_cast` |
| Check | `if (temp)` | `try-catch` block |
| Syntax | `Dac* temp = dynamic_cast<Dac*>(ptr)` | `Dac& temp = dynamic_cast<Dac&>(ref)` |

### Example 3: Array of Mixed Objects

```cpp
#include<iostream>      // Line 1
using namespace std;    // Line 2

class Course { public: virtual void start() {} };       // Line 4
class Predac : public Course { public: void start() { cout << "predac\n"; } };
class Mscit : public Course { public: void start() { cout << "mscit\n"; } };
class Dac : public Course { 
public: 
    void start() { cout << "dac\n"; }
    void orientation() { cout << "orientation\n"; }
};

void main()
{
    Course *arr[] = {new Predac, new Mscit, new Dac};  // Line 14: Array of base pointers
    
    for (int i = 0; i < 3; i++)     // Line 16: Loop through all
    {
        Dac *temp = dynamic_cast<Dac*>(arr[i]);  // Line 18: Try cast each
        if (temp)                   // Line 19: Only Dac objects
        {
            temp->orientation();    // Line 21: Call special method
        }
        arr[i]->start();            // Line 23: Call common method
    }
}
```

### Output:
```
predac
mscit
orientation
dac
```

### Example 4: Reference Cast with Array Dereference

```cpp
void main()
{
    Course *arr[] = {new Predac, new Mscit, new Dac};  // Line 3
    
    for (int i = 0; i < 3; i++)
    {
        try
        {
            Dac& ref = dynamic_cast<Dac&>(*arr[i]);  // Line 9: Dereference then cast
            ref.orientation();
        }
        catch (bad_cast& b)       // Line 12: Catch for Predac and Mscit
        {
            // Not a Dac, silently continue
        }
        arr[i]->start();          // Line 16: Always call start
    }
}
```

---

## Key Points

1. **Runtime Check**: `dynamic_cast` performs type checking at runtime.

2. **Virtual Function Required**: Base class must have at least one virtual function.

3. **Pointer vs Reference**:
   - Pointer: Returns `nullptr` on failure
   - Reference: Throws `bad_cast` exception on failure

4. **Downcast Only**: Typically used for downcasting (base → derived).

5. **Safer than static_cast**: Won't allow invalid casts.

6. **Performance Cost**: Small runtime overhead for type checking.

7. **Cross-Cast**: Can cast between sibling classes in multiple inheritance.

---

## Common Mistakes

### 1. Forgetting Virtual Function
```cpp
class Base { };  // No virtual function
class Derived : public Base { };

Base* b = new Derived();
Derived* d = dynamic_cast<Derived*>(b);  // Compile ERROR!
```

### 2. Not Checking Pointer Result
```cpp
Base* b = new OtherDerived();
Derived* d = dynamic_cast<Derived*>(b);
d->method();  // CRASH! d is nullptr
```

### 3. Not Catching bad_cast for References
```cpp
Derived& d = dynamic_cast<Derived&>(baseRef);  // May throw!
// Must use try-catch
```

---

## Interview Questions

### Q1: What is dynamic_cast?
**Answer**: `dynamic_cast` is a C++ operator for safe downcasting in polymorphic class hierarchies. It uses RTTI to verify at runtime whether a cast is valid, returning `nullptr` (for pointers) or throwing `bad_cast` (for references) if the cast fails.

### Q2: What is the difference between dynamic_cast and static_cast?
**Answer**:
| dynamic_cast | static_cast |
|--------------|-------------|
| Runtime type checking | No runtime check |
| Requires virtual functions | No requirements |
| Returns nullptr on failure | Undefined behavior on invalid cast |
| Slower (RTTI overhead) | Faster |

### Q3: Why does dynamic_cast require virtual functions?
**Answer**: Virtual functions cause the compiler to generate a vtable, which contains RTTI information. Without RTTI, there's no way to determine the actual object type at runtime.

### Q4: What happens when dynamic_cast fails?
**Answer**:
- **Pointers**: Returns `nullptr`
- **References**: Throws `std::bad_cast` exception

### Q5: What is RTTI?
**Answer**: Run-Time Type Information. It's a mechanism that allows the type of an object to be determined during program execution. C++ provides `typeid` and `dynamic_cast` as RTTI features.

### Q6: Can dynamic_cast be used with non-polymorphic classes?
**Answer**: No. The compiler will generate an error. The base class must have at least one virtual function for `dynamic_cast` to work.

### Q7: When should you use dynamic_cast vs static_cast?
**Answer**:
- Use `dynamic_cast` when you need runtime safety and aren't 100% sure of the object's type
- Use `static_cast` when you're certain of the type and want better performance
- Prefer `static_cast` for upcasting (always safe)
- Use `dynamic_cast` for downcasting (needs verification)
