# 🎭 Abstract Classes and Pure Virtual Functions

## Table of Contents
1. [What are Abstract Classes?](#what-are-abstract-classes)
2. [Pure Virtual Functions](#pure-virtual-functions)
3. [Purpose of Abstract Classes](#purpose-of-abstract-classes)
4. [Complete Example: Shape Hierarchy](#complete-example-shape-hierarchy)
5. [Rules for Abstract Classes](#rules-for-abstract-classes)
6. [Key Takeaways](#key-takeaways)

---

## What are Abstract Classes?

> **Abstract Class**: A class that cannot be instantiated directly. It contains at least one **pure virtual function**.

```
┌─────────────────────────────────────────────────────────────────┐
│                    ABSTRACT CLASS CONCEPT                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   ABSTRACT CLASS:                                                │
│   • Cannot create objects directly                              │
│   • Acts as a "contract" or "interface" for derived classes    │
│   • Contains at least one pure virtual function                 │
│   • Defines WHAT must be done, not HOW                          │
│                                                                  │
│   CONCRETE CLASS:                                                │
│   • Can create objects                                           │
│   • Implements all pure virtual functions from base             │
│   • Provides the actual implementation                          │
│                                                                  │
│   Example Hierarchy:                                             │
│   ┌───────────────────┐                                          │
│   │   Shape           │ ← ABSTRACT (cannot instantiate)         │
│   │   virtual draw()=0│                                          │
│   └─────────┬─────────┘                                          │
│       ┌─────┴─────┐                                              │
│       ▼           ▼                                              │
│   ┌───────┐   ┌────────┐                                         │
│   │Circle │   │Rectangle│ ← CONCRETE (can instantiate)          │
│   │draw() │   │ draw() │     (implements draw())                │
│   └───────┘   └────────┘                                         │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Pure Virtual Functions

### Syntax

```cpp
class AbstractClass
{
public:
    virtual void pureVirtualFunction() = 0;  // = 0 makes it PURE VIRTUAL
};
```

### What is a Pure Virtual Function?

```
┌─────────────────────────────────────────────────────────────────┐
│                PURE VIRTUAL FUNCTION                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   SYNTAX: virtual return_type function_name() = 0;              │
│                                                                  │
│   The "= 0" at the end tells the compiler:                      │
│   1. This function has NO implementation in this class          │
│   2. Any derived class MUST provide implementation              │
│   3. THIS makes the class ABSTRACT                              │
│                                                                  │
│   DIFFERENCE:                                                    │
│   ┌──────────────────────────────────────────────────────────┐  │
│   │ VIRTUAL FUNCTION:                                        │  │
│   │ virtual void func() { cout << "base"; }                  │  │
│   │ • Has default implementation                             │  │
│   │ • Derived class MAY override (optional)                  │  │
│   │ • Class is NOT abstract                                  │  │
│   └──────────────────────────────────────────────────────────┘  │
│                                                                  │
│   ┌──────────────────────────────────────────────────────────┐  │
│   │ PURE VIRTUAL FUNCTION:                                   │  │
│   │ virtual void func() = 0;                                 │  │
│   │ • No implementation in base class                        │  │
│   │ • Derived class MUST override (mandatory)                │  │
│   │ • Class IS abstract                                      │  │
│   └──────────────────────────────────────────────────────────┘  │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Basic Example

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class Animal                            // Line 4: ABSTRACT class
{
public:
    virtual void makeSound() = 0;       // Line 7: PURE virtual function
                                        // No implementation here!
    
    void breathe()                      // Line 10: Regular function (optional)
    {
        cout << "Breathing..." << endl;
    }
    
    virtual ~Animal() { }               // Line 15: Virtual destructor
};

class Dog : public Animal               // Line 18: CONCRETE class
{
public:
    void makeSound() override           // Line 21: MUST implement makeSound()
    {
        cout << "Woof!" << endl;
    }
};

class Cat : public Animal               // Line 27: CONCRETE class
{
public:
    void makeSound() override           // Line 30: MUST implement makeSound()
    {
        cout << "Meow!" << endl;
    }
};

int main()                              // Line 36: Main function
{
    // Animal a;                        // ERROR! Cannot instantiate abstract class
    
    Dog d;                              // Line 40: OK - Dog is concrete
    d.makeSound();                      // Woof!
    d.breathe();                        // Breathing...
    
    Cat c;                              // Line 44: OK - Cat is concrete
    c.makeSound();                      // Meow!
    
    Animal *ptr = new Dog;              // Line 47: Polymorphism works!
    ptr->makeSound();                   // Woof! (late binding)
    
    delete ptr;
    return 0;
}
```

**Output:**
```
Woof!
Breathing...
Meow!
Woof!
```

---

## Purpose of Abstract Classes

### 1. Define a Contract/Interface

```
┌─────────────────────────────────────────────────────────────────┐
│           ABSTRACT CLASS AS A CONTRACT                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   Abstract class says: "Every derived class MUST have these     │
│   functions. I don't care HOW you implement them, just          │
│   make sure you DO implement them."                              │
│                                                                  │
│   Example: Drawable interface                                    │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ class Drawable                                          │   │
│   │ {                                                        │   │
│   │ public:                                                  │   │
│   │     virtual void draw() = 0;   // Contract!             │   │
│   │     virtual void resize() = 0; // Contract!             │   │
│   │ };                                                       │   │
│   │                                                          │   │
│   │ // Any class that inherits Drawable MUST implement:     │   │
│   │ // • draw()                                              │   │
│   │ // • resize()                                            │   │
│   │ // Otherwise, it remains abstract and cannot be used.   │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 2. Provide Common Code

```cpp
class Shape                             // Abstract class with shared code
{
protected:
    int x, y;                           // Common data
public:
    Shape(int x, int y) : x(x), y(y) { }
    
    void moveTo(int newX, int newY)     // Common implementation
    {
        x = newX;
        y = newY;
    }
    
    virtual void draw() = 0;            // Contract - must implement
    virtual double area() = 0;          // Contract - must implement
};
```

### 3. Enable Polymorphism

```cpp
void renderAll(vector<Shape*>& shapes)  // Works with ANY Shape
{
    for(Shape* s : shapes)
    {
        s->draw();                      // Each shape draws itself
    }
}

int main()
{
    vector<Shape*> shapes;
    shapes.push_back(new Circle(0, 0, 5));
    shapes.push_back(new Rectangle(1, 1, 10, 20));
    shapes.push_back(new Triangle(2, 2, 3, 4, 5, 6));
    
    renderAll(shapes);                  // Polymorphism in action!
}
```

---

## Complete Example: Shape Hierarchy

```cpp
#include<iostream>                      // Line 1: Include iostream
#include<cmath>                         // Line 2: Include math for M_PI
using namespace std;                    // Line 3: Use standard namespace

const double PI = 3.14159265359;        // Line 5: Define PI

class Shape                             // Line 7: ABSTRACT base class
{
protected:
    string name;                        // Line 10: Shape name
public:
    Shape(const string& n) : name(n)    // Line 12: Constructor
    {
        cout << "Shape created: " << name << endl;
    }
    
    // Pure virtual functions - THE CONTRACT
    virtual void draw() const = 0;      // Line 18: MUST be implemented
    virtual double area() const = 0;    // Line 19: MUST be implemented
    virtual double perimeter() const = 0;// Line 20: MUST be implemented
    
    // Regular virtual function with default implementation
    virtual void display() const        // Line 23: Has default implementation
    {
        cout << "Shape: " << name << endl;
        cout << "  Area: " << area() << endl;
        cout << "  Perimeter: " << perimeter() << endl;
    }
    
    virtual ~Shape()                    // Line 30: Virtual destructor
    {
        cout << "Shape destroyed: " << name << endl;
    }
};

class Circle : public Shape             // Line 36: CONCRETE class
{
private:
    double radius;                      // Line 39: Circle specific data
public:
    Circle(double r)                    // Line 41: Constructor
        : Shape("Circle"), radius(r)
    {
        cout << "Circle created, radius: " << radius << endl;
    }
    
    void draw() const override          // Line 47: Implement draw()
    {
        cout << "Drawing a circle with radius " << radius << endl;
    }
    
    double area() const override        // Line 52: Implement area()
    {
        return PI * radius * radius;
    }
    
    double perimeter() const override   // Line 57: Implement perimeter()
    {
        return 2 * PI * radius;
    }
    
    ~Circle() override
    {
        cout << "Circle destroyed" << endl;
    }
};

class Rectangle : public Shape         // Line 67: CONCRETE class
{
private:
    double width, height;               // Line 70: Rectangle specific data
public:
    Rectangle(double w, double h)       // Line 72: Constructor
        : Shape("Rectangle"), width(w), height(h)
    {
        cout << "Rectangle created, w: " << width << ", h: " << height << endl;
    }
    
    void draw() const override          // Line 78: Implement draw()
    {
        cout << "Drawing a rectangle " << width << " x " << height << endl;
    }
    
    double area() const override        // Line 83: Implement area()
    {
        return width * height;
    }
    
    double perimeter() const override   // Line 88: Implement perimeter()
    {
        return 2 * (width + height);
    }
    
    ~Rectangle() override
    {
        cout << "Rectangle destroyed" << endl;
    }
};

void processShape(Shape* s)             // Line 99: Polymorphic function
{
    s->draw();                          // Line 101: Calls appropriate draw()
    s->display();                       // Line 102: Uses virtual display()
}

int main()                              // Line 105: Main function
{
    cout << "=== Creating shapes ===" << endl;
    
    // Shape s("test");                 // ERROR! Cannot instantiate abstract class!
    
    Shape* shapes[2];                   // Line 111: Array of Shape pointers
    shapes[0] = new Circle(5.0);        // Line 112: Circle is concrete
    shapes[1] = new Rectangle(4.0, 6.0);// Line 113: Rectangle is concrete
    
    cout << "\n=== Processing shapes ===" << endl;
    for(int i = 0; i < 2; i++)
    {
        processShape(shapes[i]);        // Line 117: Polymorphism!
        cout << "---" << endl;
    }
    
    cout << "\n=== Cleanup ===" << endl;
    for(int i = 0; i < 2; i++)
    {
        delete shapes[i];               // Line 124: Virtual destructor at work
    }
    
    return 0;
}
```

**Output:**
```
=== Creating shapes ===
Shape created: Circle
Circle created, radius: 5
Shape created: Rectangle
Rectangle created, w: 4, h: 6

=== Processing shapes ===
Drawing a circle with radius 5
Shape: Circle
  Area: 78.5398
  Perimeter: 31.4159
---
Drawing a rectangle 4 x 6
Shape: Rectangle
  Area: 24
  Perimeter: 20
---

=== Cleanup ===
Circle destroyed
Shape destroyed: Circle
Rectangle destroyed
Shape destroyed: Rectangle
```

---

## Rules for Abstract Classes

### Rule 1: One Pure Virtual = Abstract

```
┌─────────────────────────────────────────────────────────────────┐
│   RULE: A class with AT LEAST ONE pure virtual function        │
│         is automatically ABSTRACT                               │
│                                                                  │
│   class A { virtual void f() = 0; };  // Abstract               │
│   class B { virtual void f(); };      // NOT abstract           │
│   class C { void f() = 0; };          // ERROR! = 0 needs virtual│
└─────────────────────────────────────────────────────────────────┘
```

### Rule 2: Derived Must Implement All

```cpp
class Animal
{
public:
    virtual void speak() = 0;
    virtual void move() = 0;
};

class Bird : public Animal
{
public:
    void speak() override { cout << "Chirp"; }
    // move() NOT implemented!
};

// Bird is STILL ABSTRACT because move() is not implemented!
// Bird b;  // ERROR!

class Sparrow : public Bird
{
public:
    void move() override { cout << "Fly"; }
};

// Sparrow is CONCRETE because ALL pure virtuals implemented
Sparrow s;  // OK!
```

### Rule 3: Can Have Implementation

```cpp
class Base
{
public:
    virtual void func() = 0;  // Pure virtual but CAN have implementation!
};

// Providing implementation OUTSIDE the class
void Base::func()
{
    cout << "Base implementation" << endl;
}

class Derived : public Base
{
public:
    void func() override
    {
        Base::func();  // Can call base implementation!
        cout << "Derived addition" << endl;
    }
};
```

### Rule 4: Abstract Can Have Data and Regular Functions

```cpp
class AbstractBase
{
protected:
    int data;                           // Data members OK
    
public:
    AbstractBase() : data(0) { }        // Constructor OK
    
    void regularFunc()                  // Regular function OK
    {
        cout << "Regular function" << endl;
    }
    
    virtual void pureFunc() = 0;        // Makes class abstract
};
```

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────────────┐
│          ABSTRACT CLASSES - KEY POINTS                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. ABSTRACT CLASS:                                              │
│     • Has at least one pure virtual function                    │
│     • Cannot be instantiated directly                           │
│     • Used as base class for polymorphism                       │
│     • Can have data, constructors, regular functions            │
│                                                                  │
│  2. PURE VIRTUAL FUNCTION:                                       │
│     • Syntax: virtual void func() = 0;                          │
│     • No implementation required (but allowed)                  │
│     • Derived class MUST implement OR remain abstract           │
│     • Creates a "contract" for derived classes                  │
│                                                                  │
│  3. PURPOSE:                                                     │
│     • Define interface/contract                                 │
│     • Enable polymorphism with upcasting                        │
│     • Share common code among related classes                   │
│     • Prevent direct instantiation of base classes             │
│                                                                  │
│  4. RULES:                                                       │
│     • One pure virtual = class is abstract                      │
│     • Must implement ALL pure virtuals to be concrete           │
│     • Pure virtual can have implementation (outside class)     │
│     • Abstract can have regular functions and data              │
│                                                                  │
│  5. vs INTERFACE (Java/C#):                                      │
│     • C++ abstract class = Java abstract class                  │
│     • C++ abstract class with ONLY pure virtuals ≈ Java interface│
│     • C++ has no separate "interface" keyword                   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Previous: [Virtual Destructor](42_Virtual_Destructor.md)*
*Next: [Object Slicing](44_Object_Slicing.md)*
