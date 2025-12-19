# C++ final and override Keywords (C++11)

## Table of Contents
- [Introduction](#introduction)
- [override Keyword](#override-keyword)
- [final Keyword](#final-keyword)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

C++11 introduced `override` and `final` keywords to make inheritance and polymorphism safer and more explicit.

| Keyword | Purpose |
|---------|---------|
| `override` | Explicitly indicates function overrides a virtual function |
| `final` | Prevents further overriding or inheritance |

---

## override Keyword

The `override` keyword ensures that a member function is actually overriding a base class virtual function.

### Problem Without override:
```cpp
class Base {
public:
    virtual void display(int x) { }
};

class Derived : public Base {
public:
    void display(double x) { }  // Oops! Different signature - NOT overriding!
};
```

### Solution With override:
```cpp
class Derived : public Base {
public:
    void display(double x) override { }  // Compile ERROR! Not overriding
    void display(int x) override { }     // OK - correctly overrides
};
```

### Benefits:
- Compile-time error if not actually overriding
- Documents intent clearly
- Catches signature mismatches

---

## final Keyword

### On Virtual Functions:
Prevents derived classes from overriding:
```cpp
class Base {
public:
    virtual void func() { }
};

class Middle : public Base {
public:
    void func() final { }  // Can override, but no further overriding
};

class Derived : public Middle {
public:
    // void func() { }  // Error: func is final in Middle
};
```

### On Classes:
Prevents inheritance:
```cpp
class FinalClass final {
    // ...
};

// class Derived : public FinalClass { };  // Error: cannot inherit
```

---

## Code Examples

### Example 1: override Catching Errors

```cpp
#include<iostream>
using namespace std;

class Base {
public:
    virtual void start() {
        cout << "Base start" << endl;
    }
    virtual void stop(int code) {
        cout << "Base stop: " << code << endl;
    }
};

class Derived : public Base {
public:
    void start() override {           // OK: correctly overrides
        cout << "Derived start" << endl;
    }
    
    void stop(int code) override {    // OK: correctly overrides
        cout << "Derived stop: " << code << endl;
    }
    
    // void stopp(int code) override { }  // Error: typo, no such function
    // void stop(double code) override { } // Error: wrong parameter type
};

int main() {
    Base* ptr = new Derived();
    ptr->start();      // Derived start
    ptr->stop(0);      // Derived stop: 0
    delete ptr;
    return 0;
}
```

### Example 2: final on Functions

```cpp
#include<iostream>
using namespace std;

class Animal {
public:
    virtual void speak() {
        cout << "Animal speaks" << endl;
    }
};

class Dog : public Animal {
public:
    void speak() override final {  // Override and mark final
        cout << "Dog barks" << endl;
    }
};

class Bulldog : public Dog {
public:
    // void speak() override { }  // Error: speak() is final in Dog
};

int main() {
    Animal* a = new Bulldog();
    a->speak();  // Dog barks
    delete a;
    return 0;
}
```

### Example 3: final on Classes

```cpp
#include<iostream>
using namespace std;

class Singleton final {  // Cannot be inherited
private:
    static Singleton* instance;
    Singleton() {}
public:
    static Singleton* getInstance() {
        if (!instance) instance = new Singleton();
        return instance;
    }
};

// class MySingleton : public Singleton { };  // Error!

Singleton* Singleton::instance = nullptr;

int main() {
    Singleton* s = Singleton::getInstance();
    return 0;
}
```

---

## Key Points

1. **override**: Ensures function actually overrides base virtual function.
2. **final on function**: Prevents further overriding in derived classes.
3. **final on class**: Prevents class from being inherited.
4. **Compile-time checking**: Both keywords catch errors at compile time.
5. **Documentation**: Makes code intent clearer.
6. **Position**: Both keywords come after the function declaration.

---

## Interview Questions

### Q1: What is the purpose of the override keyword?
**Answer**: The `override` keyword explicitly indicates that a member function is meant to override a virtual function in the base class. If the function doesn't actually override anything (due to typo, wrong signature), the compiler generates an error.

### Q2: What is the difference between override and final?
**Answer**:
| override | final |
|----------|-------|
| Ensures function overrides base | Prevents further overriding |
| Catches signature mismatches | Seals the function/class |
| Can be combined with final | Can be combined with override |

### Q3: Can you use final on a class?
**Answer**: Yes. A class marked `final` cannot be inherited from. This is useful for singleton patterns, security, or optimization hints.

### Q4: What happens without override if signature doesn't match?
**Answer**: Without `override`, a signature mismatch creates a new function instead of overriding. This is a silent bug that can be very hard to find:
```cpp
virtual void foo(int);   // Base
void foo(double);        // Derived - NOT overriding, new function!
```

### Q5: Can a final function be overridden?
**Answer**: No. A function marked `final` cannot be overridden in any derived class. Attempting to do so causes a compile error.
