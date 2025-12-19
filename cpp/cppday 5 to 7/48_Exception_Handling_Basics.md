# ⚠️ Exception Handling Basics in C++

## Table of Contents
1. [What is Exception Handling?](#what-is-exception-handling)
2. [try, throw, and catch](#try-throw-and-catch)
3. [Multiple catch Blocks](#multiple-catch-blocks)
4. [Exception Flow](#exception-flow)
5. [Standard Exception Classes](#standard-exception-classes)
6. [Key Takeaways](#key-takeaways)

---

## What is Exception Handling?

> **Exception Handling**: A mechanism to handle runtime errors gracefully, separating error-handling code from normal program logic.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                  WHY EXCEPTION HANDLING?                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   WITHOUT EXCEPTION HANDLING:                                                │
│   ───────────────────────────                                                │
│   int divide(int a, int b)                                                   │
│   {                                                                          │
│       if(b == 0) return -1;      // Error code (ambiguous!)                 │
│       return a / b;                                                          │
│   }                                                                          │
│                                                                              │
│   int result = divide(10, 0);                                                │
│   if(result == -1) { ... }       // But what if -1 is valid result?         │
│                                                                              │
│   PROBLEMS:                                                                  │
│   • Error codes can be ignored                                              │
│   • Error codes can be ambiguous                                            │
│   • Error handling mixed with logic                                         │
│   • Hard to propagate errors up call stack                                  │
│                                                                              │
│   WITH EXCEPTION HANDLING:                                                   │
│   ─────────────────────────                                                  │
│   int divide(int a, int b)                                                   │
│   {                                                                          │
│       if(b == 0) throw "Division by zero!";                                 │
│       return a / b;                                                          │
│   }                                                                          │
│                                                                              │
│   try { result = divide(10, 0); }                                           │
│   catch(...) { /* handle error */ }                                         │
│                                                                              │
│   BENEFITS:                                                                  │
│   • Cannot be ignored (program crashes if not caught)                       │
│   • Clear separation of error handling                                       │
│   • Automatic propagation up call stack                                     │
│   • Rich error information                                                   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## try, throw, and catch

### The Three Keywords

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    EXCEPTION HANDLING KEYWORDS                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   try {                                                                      │
│       // Code that might throw an exception                                 │
│       throw expression;   // Throw an exception                             │
│   }                                                                          │
│   catch(type identifier) {                                                   │
│       // Handle exceptions of 'type'                                        │
│   }                                                                          │
│                                                                              │
│   TRY:     Block of code that might throw exceptions                        │
│   THROW:   Signal that an error occurred (raise an exception)               │
│   CATCH:   Block that handles the exception                                 │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Example 1: Basic Exception Handling

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

int divide(int a, int b)                // Line 4: Division function
{
    if(b == 0)                          // Line 6: Check for error condition
    {
        throw "Division by zero error!";// Line 8: THROW exception (string)
    }
    return a / b;                       // Line 10: Normal execution
}

int main()                              // Line 13: Main function
{
    int x = 10, y = 0;
    
    try                                 // Line 17: TRY block
    {
        cout << "Attempting division..." << endl;
        int result = divide(x, y);      // Line 20: This will throw!
        cout << "Result: " << result << endl;  // Line 21: NOT executed
    }
    catch(const char* msg)              // Line 23: CATCH block
    {
        cout << "Exception caught: " << msg << endl;  // Line 25: Handle exception
    }
    
    cout << "Program continues..." << endl;  // Line 28: Execution continues!
    
    return 0;
}
```

**Output:**
```
Attempting division...
Exception caught: Division by zero error!
Program continues...
```

### Exception Flow Diagram

```
┌────────────────────────────────────────────────────────────────────────────┐
│                      EXCEPTION FLOW                                         │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   NORMAL FLOW (no exception):                                               │
│   try {                                                                     │
│       code1;                 → executed                                     │
│       code2;                 → executed                                     │
│       code3;                 → executed                                     │
│   }                                                                         │
│   catch(...) {                                                              │
│       handler;               → SKIPPED                                      │
│   }                                                                         │
│   afterCatch;                → executed                                     │
│                                                                             │
│   EXCEPTION FLOW:                                                           │
│   try {                                                                     │
│       code1;                 → executed                                     │
│       throw exception;       → EXCEPTION THROWN!                            │
│       code3;                 → SKIPPED (never executed)                     │
│   }                                ↓                                        │
│   catch(matching_type) {     ←─────┘                                        │
│       handler;               → executed (handles exception)                 │
│   }                                                                         │
│   afterCatch;                → executed (program continues)                 │
│                                                                             │
└────────────────────────────────────────────────────────────────────────────┘
```

---

## Multiple catch Blocks

### Handling Different Exception Types

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

void process(int code)                  // Line 4: Function that throws
{
    switch(code)
    {
        case 1: throw 100;              // Line 8: Throw int
        case 2: throw 3.14;             // Line 9: Throw double
        case 3: throw "Error message";  // Line 10: Throw string
        case 4: throw 'X';              // Line 11: Throw char
        default: cout << "No error" << endl;
    }
}

int main()                              // Line 16: Main function
{
    for(int i = 0; i <= 4; i++)
    {
        cout << "Testing code " << i << ": ";
        try
        {
            process(i);
        }
        catch(int e)                    // Line 25: Catch int
        {
            cout << "Caught int: " << e << endl;
        }
        catch(double e)                 // Line 29: Catch double
        {
            cout << "Caught double: " << e << endl;
        }
        catch(const char* e)            // Line 33: Catch string
        {
            cout << "Caught string: " << e << endl;
        }
        catch(...)                      // Line 37: Catch ALL others (catch-all)
        {
            cout << "Caught unknown exception" << endl;
        }
    }
    
    return 0;
}
```

**Output:**
```
Testing code 0: No error
Testing code 1: Caught int: 100
Testing code 2: Caught double: 3.14
Testing code 3: Caught string: Error message
Testing code 4: Caught unknown exception
```

### catch Block Rules

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                     CATCH BLOCK RULES                                        │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   1. ORDER MATTERS:                                                          │
│      • Catch blocks are checked FROM TOP TO BOTTOM                          │
│      • First matching catch is executed                                     │
│      • Put specific types BEFORE general types                              │
│                                                                              │
│   2. CATCH-ALL:                                                              │
│      catch(...) { }                                                         │
│      • Catches ANY exception                                                │
│      • Should be LAST (else it catches everything)                          │
│      • Cannot access exception object                                       │
│                                                                              │
│   3. INHERITANCE HIERARCHY:                                                  │
│      • Catch derived class BEFORE base class                                │
│      catch(DerivedEx& e) { }    // More specific, check first              │
│      catch(BaseEx& e) { }       // More general, check second              │
│                                                                              │
│   4. ONLY ONE CATCH EXECUTES:                                                │
│      • After catch executes, control goes after all catches                 │
│      • Other catches are NOT checked                                        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Exception Flow

### Stack Unwinding

> **Stack Unwinding**: When an exception is thrown, C++ "unwinds" the stack, destroying local objects in reverse order of construction, until a matching catch is found.

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class Resource                          // Line 4: Class with destructor
{
    string name;
public:
    Resource(const string& n) : name(n)
    {
        cout << "Resource " << name << " created" << endl;
    }
    ~Resource()
    {
        cout << "Resource " << name << " destroyed" << endl;
    }
};

void func3()                            // Line 18: Function that throws
{
    Resource r3("R3");
    cout << "In func3, about to throw..." << endl;
    throw "Error in func3!";            // Line 22: THROW!
    cout << "After throw (never executed)" << endl;
}

void func2()                            // Line 26: Intermediate function
{
    Resource r2("R2");
    cout << "In func2, calling func3..." << endl;
    func3();                            // Line 30: Call func3 (throws)
    cout << "After func3 (never executed)" << endl;
}

void func1()                            // Line 34: Another intermediate
{
    Resource r1("R1");
    cout << "In func1, calling func2..." << endl;
    func2();                            // Line 38: Call func2
    cout << "After func2 (never executed)" << endl;
}

int main()                              // Line 42: Main function
{
    try
    {
        cout << "In main, calling func1..." << endl;
        func1();
        cout << "After func1 (never executed)" << endl;
    }
    catch(const char* msg)
    {
        cout << "\nException caught: " << msg << endl;
    }
    
    cout << "\nProgram ends normally" << endl;
    return 0;
}
```

**Output:**
```
In main, calling func1...
Resource R1 created
In func1, calling func2...
Resource R2 created
In func2, calling func3...
Resource R3 created
In func3, about to throw...
Resource R3 destroyed         ← Stack unwinding begins!
Resource R2 destroyed         ← Automatic cleanup!
Resource R1 destroyed         ← All objects destroyed!

Exception caught: Error in func3!

Program ends normally
```

### Stack Unwinding Diagram

```
┌────────────────────────────────────────────────────────────────────────────┐
│                      STACK UNWINDING                                        │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   Call Stack:                             After throw:                      │
│   ┌─────────────┐                         ┌─────────────┐                   │
│   │   func3()   │ ← throw here!           │   func3()   │ ← r3 destroyed    │
│   │   r3        │                         │             │     ↓             │
│   ├─────────────┤                         ├─────────────┤     │             │
│   │   func2()   │                         │   func2()   │ ← r2 destroyed    │
│   │   r2        │                         │             │     ↓             │
│   ├─────────────┤                         ├─────────────┤     │             │
│   │   func1()   │                         │   func1()   │ ← r1 destroyed    │
│   │   r1        │                         │             │     ↓             │
│   ├─────────────┤                         ├─────────────┤     │             │
│   │   main()    │ ← try-catch here        │   main()    │ ← catch executed  │
│   └─────────────┘                         └─────────────┘                   │
│                                                                             │
│   IMPORTANT: Destructors are called during stack unwinding!                │
│   This is why RAII (Resource Acquisition Is Initialization) works.        │
│                                                                             │
└────────────────────────────────────────────────────────────────────────────┘
```

---

## Standard Exception Classes

### The std::exception Hierarchy

```cpp
#include <exception>    // Base exception class

// Hierarchy (simplified):
// std::exception
//    ├── std::logic_error
//    │      ├── std::invalid_argument
//    │      ├── std::domain_error
//    │      ├── std::length_error
//    │      └── std::out_of_range
//    └── std::runtime_error
//           ├── std::range_error
//           ├── std::overflow_error
//           └── std::underflow_error
```

### Using Standard Exceptions

```cpp
#include<iostream>                      // Line 1: Include iostream
#include<stdexcept>                     // Line 2: Include stdexcept
#include<vector>                        // Line 3: Include vector
using namespace std;                    // Line 4: Use standard namespace

int main()                              // Line 6: Main function
{
    // Example 1: out_of_range
    try
    {
        vector<int> v = {1, 2, 3};
        cout << v.at(10) << endl;       // Throws out_of_range!
    }
    catch(const out_of_range& e)
    {
        cout << "Out of range: " << e.what() << endl;
    }
    
    // Example 2: invalid_argument
    try
    {
        int value = stoi("not a number");  // Throws invalid_argument!
    }
    catch(const invalid_argument& e)
    {
        cout << "Invalid argument: " << e.what() << endl;
    }
    
    // Example 3: Using base exception class
    try
    {
        throw runtime_error("Something went wrong!");
    }
    catch(const exception& e)           // Catches ALL standard exceptions
    {
        cout << "Exception: " << e.what() << endl;
    }
    
    return 0;
}
```

**Output:**
```
Out of range: vector::_M_range_check: __n (which is 10) >= this->size() (which is 3)
Invalid argument: stoi
Exception: Something went wrong!
```

### Creating Custom Exceptions

```cpp
#include<iostream>
#include<exception>
#include<string>
using namespace std;

class DivisionByZeroException : public exception  // Custom exception
{
    string message;
public:
    DivisionByZeroException(int numerator)
    {
        message = "Cannot divide " + to_string(numerator) + " by zero!";
    }
    
    const char* what() const noexcept override   // Override what()
    {
        return message.c_str();
    }
};

int divide(int a, int b)
{
    if(b == 0)
        throw DivisionByZeroException(a);        // Throw custom exception
    return a / b;
}

int main()
{
    try
    {
        cout << divide(10, 0) << endl;
    }
    catch(const DivisionByZeroException& e)
    {
        cout << "Error: " << e.what() << endl;
    }
    
    return 0;
}
```

**Output:**
```
Error: Cannot divide 10 by zero!
```

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────────────┐
│          EXCEPTION HANDLING - KEY POINTS                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. THE THREE KEYWORDS:                                          │
│     • try: Block that might throw                               │
│     • throw: Signal an error                                    │
│     • catch: Handle the error                                   │
│                                                                  │
│  2. EXCEPTION FLOW:                                              │
│     • throw jumps to matching catch                             │
│     • Code after throw in try is NOT executed                   │
│     • After catch, execution continues normally                 │
│                                                                  │
│  3. MULTIPLE CATCHES:                                            │
│     • Order matters (specific before general)                   │
│     • Only ONE catch executes                                   │
│     • catch(...) catches everything                             │
│                                                                  │
│  4. STACK UNWINDING:                                             │
│     • Local objects destroyed in reverse order                  │
│     • Destructors called automatically                          │
│     • Enables RAII pattern                                      │
│                                                                  │
│  5. STANDARD EXCEPTIONS:                                         │
│     • Inherit from std::exception                               │
│     • Override what() for error message                         │
│     • Use stdexcept header                                      │
│                                                                  │
│  6. BEST PRACTICES:                                              │
│     • Catch by const reference                                  │
│     • Catch specific before general                             │
│     • Use standard exceptions when possible                     │
│     • Don't use exceptions for flow control                     │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Previous: [Covariant Return Types](47_Covariant_Return_Types.md)*
*Next: [Advanced Exception Handling](49_Advanced_Exception_Handling.md)*
