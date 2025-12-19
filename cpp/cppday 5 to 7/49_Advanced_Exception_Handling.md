# 🛡️ Advanced Exception Handling in C++

## Table of Contents
1. [Rethrowing Exceptions](#rethrowing-exceptions)
2. [Exception Specifications (noexcept)](#exception-specifications-noexcept)
3. [Exception Safety Guarantees](#exception-safety-guarantees)
4. [Exceptions with Inheritance](#exceptions-with-inheritance)
5. [Best Practices](#best-practices)
6. [Key Takeaways](#key-takeaways)

---

## Rethrowing Exceptions

> **Rethrowing**: Catching an exception, performing some action, then throwing it again for a higher-level handler.

### Syntax

```cpp
try
{
    // code that throws
}
catch(ExceptionType& e)
{
    // partial handling
    throw;         // Rethrow SAME exception
}
```

### Example: Logging and Rethrowing

```cpp
#include<iostream>                      // Line 1: Include iostream
#include<stdexcept>                     // Line 2: Include stdexcept
using namespace std;                    // Line 3: Use standard namespace

void lowLevelFunction()                 // Line 5: Low-level function
{
    throw runtime_error("Database connection failed!");  // Line 7: Throw
}

void middleFunction()                   // Line 10: Middle layer
{
    try
    {
        lowLevelFunction();
    }
    catch(const runtime_error& e)       // Line 16: Catch exception
    {
        cout << "[MIDDLE] Logging error: " << e.what() << endl;  // Line 18: Log
        throw;                          // Line 19: RETHROW same exception!
    }
}

void highLevelFunction()                // Line 23: High-level handler
{
    try
    {
        middleFunction();
    }
    catch(const exception& e)           // Line 29: Final handler
    {
        cout << "[HIGH] Handling error: " << e.what() << endl;
        cout << "[HIGH] Notifying user..." << endl;
    }
}

int main()                              // Line 36: Main function
{
    highLevelFunction();
    cout << "Program continues..." << endl;
    return 0;
}
```

**Output:**
```
[MIDDLE] Logging error: Database connection failed!
[HIGH] Handling error: Database connection failed!
[HIGH] Notifying user...
Program continues...
```

### throw vs throw e

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    throw vs throw e                                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   throw;           ← Rethrows the SAME exception object                     │
│                      Preserves the actual type (polymorphism works)         │
│                                                                              │
│   throw e;         ← Throws a COPY of the exception                         │
│                      May cause object slicing if e is base reference!       │
│                                                                              │
│   EXAMPLE:                                                                   │
│                                                                              │
│   class DerivedEx : public BaseEx { ... };                                  │
│                                                                              │
│   catch(BaseEx& e)                                                          │
│   {                                                                          │
│       throw;       // Rethrows DerivedEx (correct)                          │
│       throw e;     // Throws copy of BaseEx (sliced!)                       │
│   }                                                                          │
│                                                                              │
│   RULE: Always use `throw;` (without argument) for rethrowing              │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Exception Specifications (noexcept)

### The noexcept Specifier

> **noexcept**: Specifies that a function will NOT throw any exceptions. Violation calls std::terminate().

```cpp
void safe_function() noexcept           // Promises not to throw
{
    // If this throws, program terminates!
}

void may_throw_function()               // May throw (default)
{
    throw "error";                       // OK
}

void conditional() noexcept(true)       // Same as noexcept
{ }

void conditional2() noexcept(false)     // Same as default (may throw)
{ }
```

### Example: noexcept Usage

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

void safeSwap(int& a, int& b) noexcept  // Line 4: noexcept function
{
    int temp = a;
    a = b;
    b = temp;
    // No exceptions possible here
}

void unsafeFunc() noexcept              // Line 12: noexcept but throws!
{
    cout << "About to throw from noexcept function..." << endl;
    throw "This is bad!";               // Line 15: VIOLATION!
    // Program will terminate!
}

int main()                              // Line 19: Main function
{
    int x = 5, y = 10;
    
    cout << "Before swap: x = " << x << ", y = " << y << endl;
    safeSwap(x, y);                     // Safe, guaranteed no throw
    cout << "After swap: x = " << x << ", y = " << y << endl;
    
    // Uncomment to see termination:
    // unsafeFunc();                    // Would terminate program!
    
    return 0;
}
```

### noexcept Operator

```cpp
#include<iostream>
using namespace std;

void f1() noexcept { }
void f2() { }

int main()
{
    // noexcept operator checks if expression is noexcept
    cout << "f1() is noexcept: " << noexcept(f1()) << endl;  // 1 (true)
    cout << "f2() is noexcept: " << noexcept(f2()) << endl;  // 0 (false)
    
    return 0;
}
```

### When to Use noexcept

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    WHEN TO USE noexcept                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   USE noexcept FOR:                                                          │
│   • Move constructors and move assignment operators                         │
│   • Swap functions                                                           │
│   • Destructors (always implicitly noexcept)                                │
│   • Functions that truly cannot fail                                        │
│                                                                              │
│   BENEFITS:                                                                  │
│   • Compiler optimization (no exception handling overhead)                  │
│   • Better performance in STL containers                                    │
│   • Clear documentation of intent                                           │
│                                                                              │
│   CAUTION:                                                                   │
│   • If noexcept function throws → std::terminate() called                  │
│   • No chance to catch the exception                                        │
│   • Use carefully!                                                           │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Exception Safety Guarantees

```
┌─────────────────────────────────────────────────────────────────────────────┐
│               EXCEPTION SAFETY GUARANTEE LEVELS                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   LEVEL 1: NO GUARANTEE (Worst)                                              │
│   ─────────────────────────────                                              │
│   • Code may leave program in invalid state                                 │
│   • Memory leaks possible                                                    │
│   • Data corruption possible                                                │
│   • AVOID this!                                                              │
│                                                                              │
│   LEVEL 2: BASIC GUARANTEE                                                   │
│   ─────────────────────────────                                              │
│   • No resource leaks                                                        │
│   • All invariants preserved                                                │
│   • But state may be different than before                                  │
│   • Program remains in "valid" (but changed) state                          │
│                                                                              │
│   LEVEL 3: STRONG GUARANTEE (Commit-or-Rollback)                            │
│   ─────────────────────────────────────────────                              │
│   • Either operation succeeds completely                                    │
│   • OR state remains exactly as before                                      │
│   • Transaction semantics                                                    │
│                                                                              │
│   LEVEL 4: NO-THROW GUARANTEE (Best)                                         │
│   ─────────────────────────────────                                          │
│   • Operation will NEVER throw                                              │
│   • Use noexcept                                                            │
│   • Destructors, swap, move operations                                      │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Example: Strong Exception Safety

```cpp
#include<iostream>
#include<vector>
#include<stdexcept>
using namespace std;

class Account
{
    string name;
    double balance;
public:
    Account(const string& n, double b) : name(n), balance(b) { }
    
    // Strong exception safety: either both succeed or nothing changes
    void transfer(Account& target, double amount)
    {
        if(amount > balance)
            throw runtime_error("Insufficient funds!");
        
        // Use copy-and-swap for strong guarantee
        double newSourceBalance = balance - amount;
        double newTargetBalance = target.balance + amount;
        
        // Only modify if no exceptions occurred
        balance = newSourceBalance;
        target.balance = newTargetBalance;
    }
    
    void print() const
    {
        cout << name << ": $" << balance << endl;
    }
};

int main()
{
    Account alice("Alice", 1000);
    Account bob("Bob", 500);
    
    cout << "Before transfer:" << endl;
    alice.print();
    bob.print();
    
    try
    {
        alice.transfer(bob, 300);      // Should succeed
        cout << "\nAfter successful transfer:" << endl;
        alice.print();
        bob.print();
        
        alice.transfer(bob, 1000);     // Should fail
    }
    catch(const exception& e)
    {
        cout << "\nTransfer failed: " << e.what() << endl;
        cout << "Balances unchanged:" << endl;
        alice.print();                  // Still $700!
        bob.print();                    // Still $800!
    }
    
    return 0;
}
```

---

## Exceptions with Inheritance

### Catching by Hierarchy

```cpp
#include<iostream>                      // Line 1: Include iostream
#include<exception>                     // Line 2: Include exception
using namespace std;                    // Line 3: Use standard namespace

class FileException : public exception  // Line 5: Base exception
{
public:
    const char* what() const noexcept override
    {
        return "File error occurred";
    }
};

class FileNotFoundException : public FileException  // Line 14: Derived
{
public:
    const char* what() const noexcept override
    {
        return "File not found";
    }
};

class FileAccessDeniedException : public FileException  // Line 23: Another derived
{
public:
    const char* what() const noexcept override
    {
        return "File access denied";
    }
};

void processFile(int code)              // Line 32: Test function
{
    switch(code)
    {
        case 1: throw FileNotFoundException();
        case 2: throw FileAccessDeniedException();
        case 3: throw FileException();
    }
}

int main()                              // Line 42: Main function
{
    for(int i = 1; i <= 3; i++)
    {
        try
        {
            processFile(i);
        }
        // Order matters! Catch derived BEFORE base
        catch(const FileNotFoundException& e)    // Most specific first
        {
            cout << "Caught FileNotFoundException: " << e.what() << endl;
        }
        catch(const FileAccessDeniedException& e)
        {
            cout << "Caught FileAccessDeniedException: " << e.what() << endl;
        }
        catch(const FileException& e)            // Base class last
        {
            cout << "Caught FileException: " << e.what() << endl;
        }
    }
    
    return 0;
}
```

**Output:**
```
Caught FileNotFoundException: File not found
Caught FileAccessDeniedException: File access denied
Caught FileException: File error occurred
```

> [!IMPORTANT]
> Always catch derived exceptions BEFORE base exceptions! Otherwise, the base catch will handle all derived types.

---

## Best Practices

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                EXCEPTION HANDLING BEST PRACTICES                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   1. CATCH BY CONST REFERENCE:                                               │
│      catch(const exception& e)  ← Preferred                                 │
│      catch(exception e)         ← Causes slicing, copies                   │
│                                                                              │
│   2. ORDER CATCHES CORRECTLY:                                                │
│      catch(DerivedEx& e)  ← More specific first                             │
│      catch(BaseEx& e)     ← Less specific later                             │
│      catch(...)           ← Catch-all last                                  │
│                                                                              │
│   3. USE RAII FOR RESOURCES:                                                 │
│      • Wrap resources in smart pointers                                     │
│      • Destructor handles cleanup                                           │
│      • Stack unwinding calls destructors                                    │
│                                                                              │
│   4. THROW BY VALUE, CATCH BY REFERENCE:                                     │
│      throw MyException();       ← Throw by value                            │
│      catch(const MyException& e) ← Catch by const reference                │
│                                                                              │
│   5. DON'T THROW IN DESTRUCTORS:                                             │
│      • Destructor is noexcept by default                                    │
│      • Throwing during stack unwinding = std::terminate()                   │
│                                                                              │
│   6. USE STANDARD EXCEPTIONS:                                                │
│      • Inherit from std::exception                                          │
│      • Use stdexcept exceptions when appropriate                            │
│      • Implement what() correctly                                           │
│                                                                              │
│   7. USE noexcept APPROPRIATELY:                                             │
│      • Move operations                                                       │
│      • Swap functions                                                        │
│      • Destructors                                                           │
│                                                                              │
│   8. DON'T USE EXCEPTIONS FOR FLOW CONTROL:                                  │
│      • Exceptions are for exceptional situations                            │
│      • Not for normal program flow                                          │
│      • Performance overhead is significant                                   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────────────┐
│          ADVANCED EXCEPTION HANDLING - KEY POINTS                │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. RETHROWING:                                                  │
│     • Use `throw;` to rethrow same exception                    │
│     • Don't use `throw e;` (causes slicing)                     │
│     • Log and rethrow for layered handling                      │
│                                                                  │
│  2. noexcept:                                                    │
│     • Specifies function won't throw                            │
│     • Violation calls std::terminate()                          │
│     • Use for move, swap, destructors                           │
│     • Enables compiler optimizations                            │
│                                                                  │
│  3. EXCEPTION SAFETY LEVELS:                                     │
│     • No guarantee (avoid!)                                     │
│     • Basic guarantee (no leaks)                                │
│     • Strong guarantee (rollback)                               │
│     • No-throw guarantee (noexcept)                             │
│                                                                  │
│  4. INHERITANCE:                                                 │
│     • Create exception hierarchies                              │
│     • Catch derived before base                                 │
│     • Inherit from std::exception                               │
│                                                                  │
│  5. RAII:                                                        │
│     • Resource Acquisition Is Initialization                    │
│     • Stack unwinding calls destructors                         │
│     • Smart pointers manage resources                           │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Previous: [Exception Handling Basics](48_Exception_Handling_Basics.md)*
*Next Index: [Return to Notes Index](../README.md)*
