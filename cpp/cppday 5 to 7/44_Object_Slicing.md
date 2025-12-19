# ✂️ Object Slicing in C++

## Table of Contents
1. [What is Object Slicing?](#what-is-object-slicing)
2. [How Object Slicing Occurs](#how-object-slicing-occurs)
3. [Solutions to Avoid Object Slicing](#solutions-to-avoid-object-slicing)
4. [Copy Constructors and Inheritance](#copy-constructors-and-inheritance)
5. [Key Takeaways](#key-takeaways)

---

## What is Object Slicing?

> **Object Slicing**: When a derived class object is assigned to a base class object, only the base class part is copied, and the derived class part is "sliced off" (lost).

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        OBJECT SLICING CONCEPT                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   Derived Object (complete):          Base Object (sliced copy):            │
│   ┌────────────────────────────┐      ┌────────────────────────────┐        │
│   │  Base Class Members        │      │  Base Class Members        │        │
│   │  ┌──────────────────────┐  │      │  ┌──────────────────────┐  │        │
│   │  │  x = 10              │  │  ──> │  │  x = 10              │  │        │
│   │  │  baseMethod()        │  │      │  │  baseMethod()        │  │        │
│   │  └──────────────────────┘  │      │  └──────────────────────┘  │        │
│   │                            │      │                            │        │
│   │  Derived Class Members     │      │                            │        │
│   │  ┌──────────────────────┐  │      │   ← SLICED OFF!            │        │
│   │  │  y = 20   ← LOST!    │  │      │                            │        │
│   │  │  derivedMethod()     │  │      │                            │        │
│   │  └──────────────────────┘  │      └────────────────────────────┘        │
│   └────────────────────────────┘                                            │
│                                                                              │
│   base b = derived_obj;  ← Object Slicing!                                  │
│   • Only base portion is copied                                             │
│   • Derived portion is lost                                                 │
│   • Virtual functions won't work as expected                                │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## How Object Slicing Occurs

### Case 1: Assignment

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class Animal                            // Line 4: Base class
{
public:
    string name;                        // Line 7: Base member
    
    Animal(string n) : name(n)          // Line 9: Constructor
    {
        cout << "Animal created: " << name << endl;
    }
    
    virtual void speak()                // Line 14: Virtual function
    {
        cout << name << " makes a sound" << endl;
    }
};

class Dog : public Animal               // Line 20: Derived class
{
public:
    string breed;                       // Line 23: Derived member (will be sliced!)
    
    Dog(string n, string b)             // Line 25: Constructor
        : Animal(n), breed(b)
    {
        cout << "Dog created: " << name << ", breed: " << breed << endl;
    }
    
    void speak() override               // Line 31: Override
    {
        cout << name << " (a " << breed << ") barks: Woof!" << endl;
    }
};

int main()                              // Line 37: Main function
{
    Dog d("Buddy", "Golden Retriever"); // Line 39: Create Dog
    
    Animal a = d;                       // Line 41: OBJECT SLICING!
                                        // Only Animal part copied to 'a'
                                        // breed is LOST!
    
    cout << "\n--- After slicing ---" << endl;
    
    d.speak();                          // Line 47: "Buddy (a Golden Retriever) barks: Woof!"
    a.speak();                          // Line 48: "Buddy makes a sound" ← NOT Dog::speak!
    
    // a.breed;                         // ERROR! 'breed' doesn't exist in 'a'
    
    return 0;
}
```

**Output:**
```
Animal created: Buddy
Dog created: Buddy, breed: Golden Retriever

--- After slicing ---
Buddy (a Golden Retriever) barks: Woof!
Buddy makes a sound         ← Sliced! Base version called!
```

### Case 2: Pass by Value

```cpp
#include<iostream>
using namespace std;

class base
{
public:
    int x;
    base(int x) : x(x) { }
    virtual void disp() { cout << "base: " << x << endl; }
};

class sub : public base
{
public:
    int y;
    sub(int x, int y) : base(x), y(y) { }
    void disp() override { cout << "sub: " << x << ", " << y << endl; }
};

void process(base b)                    // PASS BY VALUE - causes slicing!
{
    b.disp();                           // Always calls base::disp()
}

int main()
{
    sub s(10, 20);
    
    cout << "Direct call: ";
    s.disp();                           // sub: 10, 20
    
    cout << "After passing by value: ";
    process(s);                         // base: 10 ← SLICED!
    
    return 0;
}
```

**Output:**
```
Direct call: sub: 10, 20
After passing by value: base: 10       ← Sliced! y is lost!
```

### What Happens Internally

```
┌────────────────────────────────────────────────────────────────────────────┐
│                   OBJECT SLICING INTERNALS                                  │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   sub s(10, 20);                                                            │
│   ┌──────────────────────────────────┐                                      │
│   │ s (sub object)                   │                                      │
│   │ ┌────────────────────────────┐   │                                      │
│   │ │ vptr → sub's VTABLE        │   │                                      │
│   │ │ x = 10                     │   │                                      │
│   │ │ y = 20                     │   │                                      │
│   │ └────────────────────────────┘   │                                      │
│   └──────────────────────────────────┘                                      │
│                                                                             │
│   process(s);  // void process(base b)                                      │
│   │                                                                         │
│   └── Creates NEW base object 'b' by copying only base part of 's'         │
│                                                                             │
│   ┌──────────────────────────────────┐                                      │
│   │ b (base object - inside process) │                                      │
│   │ ┌────────────────────────────┐   │                                      │
│   │ │ vptr → base's VTABLE  ← !!! │   │ ← VPTR is for BASE, not sub!        │
│   │ │ x = 10                     │   │                                      │
│   │ │                            │   │ ← y is NOT here!                     │
│   │ └────────────────────────────┘   │                                      │
│   └──────────────────────────────────┘                                      │
│                                                                             │
│   b.disp() calls base::disp() because b's VPTR points to base's VTABLE!    │
│                                                                             │
└────────────────────────────────────────────────────────────────────────────┘
```

---

## Solutions to Avoid Object Slicing

### Solution 1: Use Pointers

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
    void disp() override { cout << "sub disp" << endl; }
};

void process(base *ptr)                 // Pass POINTER - no slicing!
{
    ptr->disp();                        // Late binding works correctly
}

int main()
{
    sub s;
    process(&s);                        // sub disp ← Correct!
    
    base *ptr = new sub;
    process(ptr);                       // sub disp ← Correct!
    delete ptr;
    
    return 0;
}
```

**Output:**
```
sub disp
sub disp
```

### Solution 2: Use References

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
    void disp() override { cout << "sub disp" << endl; }
};

void process(base &ref)                 // Pass REFERENCE - no slicing!
{
    ref.disp();                         // Late binding works correctly
}

int main()
{
    sub s;
    process(s);                         // sub disp ← Correct!
    return 0;
}
```

**Output:**
```
sub disp
```

### Comparison

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    AVOIDING OBJECT SLICING                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   PASS BY VALUE (SLICING!):           PASS BY POINTER/REFERENCE (NO SLICE): │
│   ─────────────────────────           ──────────────────────────────────────│
│                                                                              │
│   void process(base b)                void process(base *ptr)               │
│   {                                   void process(base &ref)               │
│       b.disp();  // base::disp()      {                                     │
│   }                                       ptr->disp();  // sub::disp()       │
│                                           ref.disp();   // sub::disp()       │
│   sub s;                              }                                      │
│   process(s);  // SLICING!                                                   │
│                                       sub s;                                 │
│   What happens:                       process(&s);  // NO slicing            │
│   1. Copy of s created               process(s);   // NO slicing            │
│   2. Only base part copied                                                   │
│   3. VPTR points to base's VTABLE    What happens:                           │
│   4. base::disp() called              1. NO copy created                     │
│                                       2. Original object accessed            │
│                                       3. VPTR points to sub's VTABLE         │
│                                       4. sub::disp() called                  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Copy Constructors and Inheritance

### Default Behavior

```cpp
#include<iostream>
using namespace std;

class base
{
public:
    int x;
    
    base(int x = 0) : x(x)
    {
        cout << "base parameterized: " << x << endl;
    }
    
    base(const base& other) : x(other.x)     // Copy constructor
    {
        cout << "base copy constructor: " << x << endl;
    }
    
    virtual void disp() { cout << "base: " << x << endl; }
};

class sub : public base
{
public:
    int y;
    
    sub(int x = 0, int y = 0) : base(x), y(y)
    {
        cout << "sub parameterized: " << x << ", " << y << endl;
    }
    
    sub(const sub& other) : base(other), y(other.y)  // Copy constructor
    {                                                // MUST call base's copy constructor!
        cout << "sub copy constructor: " << x << ", " << y << endl;
    }
    
    void disp() override { cout << "sub: " << x << ", " << y << endl; }
};

int main()
{
    sub s1(10, 20);
    cout << "\n--- Copying sub to sub (no slicing) ---" << endl;
    sub s2 = s1;                         // Calls sub's copy constructor
    s2.disp();
    
    cout << "\n--- Copying sub to base (SLICING) ---" << endl;
    base b = s1;                         // Calls base's copy constructor
    b.disp();                            // base::disp() - SLICED!
    
    return 0;
}
```

**Output:**
```
base parameterized: 10
sub parameterized: 10, 20

--- Copying sub to sub (no slicing) ---
base copy constructor: 10
sub copy constructor: 10, 20
sub: 10, 20

--- Copying sub to base (SLICING) ---
base copy constructor: 10
base: 10                    ← SLICED! y is lost, base::disp() called
```

### Copy Constructor Chain

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                COPY CONSTRUCTOR IN INHERITANCE                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   CORRECT derived class copy constructor:                                    │
│                                                                              │
│   sub(const sub& other)                                                      │
│       : base(other),                 ← MUST call base's copy constructor!   │
│         y(other.y)                   ← Copy derived members                 │
│   {                                                                          │
│   }                                                                          │
│                                                                              │
│   If you DON'T call base(other):                                            │
│   sub(const sub& other)                                                      │
│       : y(other.y)                   ← base() default constructor called!   │
│   {                                  ← x would be 0 instead of other.x!     │
│   }                                                                          │
│                                                                              │
│   IMPORTANT: base(other) works because:                                      │
│   • 'other' is a sub&                                                       │
│   • sub IS-A base (upcasting)                                               │
│   • base copy constructor accepts base& ← sub& implicitly converts         │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Complete Example: Avoiding Slicing

```cpp
#include<iostream>                      // Line 1: Include iostream
#include<vector>                        // Line 2: Include vector
using namespace std;                    // Line 3: Use standard namespace

class Shape                             // Line 5: Abstract base class
{
public:
    virtual void draw() const = 0;      // Line 8: Pure virtual
    virtual Shape* clone() const = 0;   // Line 9: Clone pattern!
    virtual ~Shape() { }                // Line 10: Virtual destructor
};

class Circle : public Shape             // Line 13: Concrete class
{
    double radius;
public:
    Circle(double r) : radius(r) { }
    
    void draw() const override
    {
        cout << "Drawing Circle with radius " << radius << endl;
    }
    
    Circle* clone() const override      // Returns Circle* (covariant return)
    {
        return new Circle(*this);       // Create copy of this Circle
    }
};

class Rectangle : public Shape         // Line 30: Concrete class
{
    double width, height;
public:
    Rectangle(double w, double h) : width(w), height(h) { }
    
    void draw() const override
    {
        cout << "Drawing Rectangle " << width << " x " << height << endl;
    }
    
    Rectangle* clone() const override   // Returns Rectangle* (covariant return)
    {
        return new Rectangle(*this);    // Create copy of this Rectangle
    }
};

// Store shapes BY POINTER to avoid slicing
class ShapeCollection
{
    vector<Shape*> shapes;
public:
    void add(Shape* s)
    {
        shapes.push_back(s->clone());   // Clone to avoid ownership issues
    }
    
    void drawAll() const
    {
        for(const Shape* s : shapes)
            s->draw();
    }
    
    ~ShapeCollection()
    {
        for(Shape* s : shapes)
            delete s;
    }
};

int main()
{
    Circle c(5);
    Rectangle r(4, 6);
    
    ShapeCollection collection;
    collection.add(&c);                 // Add clones, not originals
    collection.add(&r);
    
    cout << "--- Drawing all shapes ---" << endl;
    collection.drawAll();               // Polymorphism works!
    
    return 0;
}
```

**Output:**
```
--- Drawing all shapes ---
Drawing Circle with radius 5
Drawing Rectangle 4 x 6
```

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────────────┐
│              OBJECT SLICING - KEY POINTS                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. WHAT IS SLICING:                                             │
│     • Derived object assigned to base object (by value)         │
│     • Only base part copied, derived part LOST                  │
│     • Virtual functions don't work as expected                  │
│                                                                  │
│  2. WHEN SLICING OCCURS:                                         │
│     • base b = derived_obj;         (assignment)                │
│     • void func(base b);            (pass by value)             │
│     • base b(derived_obj);          (copy construction)         │
│     • vector<base> v; v.push_back(derived_obj);                 │
│                                                                  │
│  3. HOW TO AVOID:                                                │
│     • Use POINTERS: base* ptr = &derived_obj;                   │
│     • Use REFERENCES: base& ref = derived_obj;                  │
│     • Store pointers in containers: vector<base*>               │
│     • Use smart pointers: vector<unique_ptr<base>>              │
│                                                                  │
│  4. COPY CONSTRUCTOR IN INHERITANCE:                             │
│     • Derived copy ctor MUST call base copy ctor               │
│     • sub(const sub& o) : base(o), y(o.y) { }                  │
│     • If not called, base gets DEFAULT constructed             │
│                                                                  │
│  5. BEST PRACTICE:                                               │
│     • For polymorphic classes, work with pointers/references   │
│     • Clone pattern for deep copies                             │
│     • Use smart pointers for automatic memory management       │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Previous: [Abstract Classes and Pure Virtual Functions](43_Abstract_Classes_Pure_Virtual.md)*
*Next: [RTTI and typeid Operator](45_RTTI_typeid_Operator.md)*
