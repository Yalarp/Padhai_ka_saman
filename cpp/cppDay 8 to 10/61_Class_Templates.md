# C++ Class Templates

## Table of Contents
- [Introduction](#introduction)
- [Class Template Syntax](#class-template-syntax)
- [Creating Objects from Class Templates](#creating-objects-from-class-templates)
- [Smart Pointer Example](#smart-pointer-example)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

Class templates allow you to create generic classes that can work with any data type. Instead of writing separate `IntArray`, `DoubleArray`, `CharArray` classes, you write one `Array<T>` template that works with all types.

> **Definition**: A class template is a blueprint for generating classes that differ only in the types they use.

---

## Class Template Syntax

### Declaration:

```cpp
template <class T>
class ClassName
{
private:
    T data;           // Member of type T
    T* pointer;       // Pointer to type T
    
public:
    ClassName(T value);         // Constructor
    T getData();                // Return type T
    void setData(T value);      // Parameter of type T
};
```

### Member Function Definition Outside Class:

```cpp
template <class T>
ClassName<T>::ClassName(T value)
{
    data = value;
}

template <class T>
T ClassName<T>::getData()
{
    return data;
}
```

---

## Creating Objects from Class Templates

When creating objects, you must specify the type:

```cpp
ClassName<int> obj1(10);        // T becomes int
ClassName<double> obj2(3.14);   // T becomes double
ClassName<string> obj3("Hi");   // T becomes string
```

### Syntax:
```cpp
TemplateName<ActualType> objectName(arguments);
```

---

## Smart Pointer Example

A practical use of class templates is implementing smart pointers that work with any class:

```cpp
template<class Type>
class SP  // Smart Pointer
{
private:
    Type* pData;  // Pointer to any type
    
public:
    SP(Type* pValue)
    {
        pData = new Type(*pValue);  // Copy construct
        delete pValue;               // Delete original
    }
    
    ~SP()
    {
        delete pData;  // Automatic cleanup
    }
    
    Type& operator*()
    {
        return *pData;  // Dereference
    }
    
    Type* operator->()
    {
        return pData;   // Arrow operator
    }
};
```

### Usage:
```cpp
SP<Person> personPtr(new Person("John", 25));
personPtr->display();  // Uses operator->
(*personPtr).display();  // Uses operator*
```

---

## Code Examples

### Example 1: Generic Smart Pointer

```cpp
#include<iostream>      // Line 1
#include<string.h>      // Line 2
using namespace std;    // Line 3

class Person            // Line 5: First class to use with template
{
    int age;
    char* pName;

public:
    Person(): pName(0), age(0) { }  // Line 11: Default constructor
    
    Person(char* pName, int age)    // Line 13: Parameterized
    {
        this->pName = new char[strlen(pName)+1];
        strcpy(this->pName, pName);
        this->age = age;
    }
    
    ~Person()                        // Line 20: Destructor
    {
        cout << "inside dest" << endl;
        delete pName;
    }
    
    Person(Person& ref)              // Line 26: Copy constructor
    {
        cout << "copy const" << endl;
        pName = new char[strlen(ref.pName)+1];
        strcpy(pName, ref.pName);
        age = ref.age;
    }
    
    void display()                   // Line 34
    {
        cout << pName << "\t" << age << endl;
    }
    
    void shout()                     // Line 39
    {
        cout << "aaaaaaaaaaaoooooooooo" << endl;
    }
};

class Vehicle           // Line 44: Second class to use with template
{
    int speed;
    char* vName;

public:
    Vehicle(): vName(0), speed(0) { }
    
    Vehicle(char* vName, int speed)
    {
        this->vName = new char[strlen(vName)+1];
        strcpy(this->vName, vName);
        this->speed = speed;
    }
    
    ~Vehicle()
    {
        cout << "inside dest" << endl;
        delete vName;
    }
    
    Vehicle(Vehicle& ref)
    {
        cout << "copy const" << endl;
        vName = new char[strlen(ref.vName)+1];
        strcpy(vName, ref.vName);
        speed = ref.speed;
    }
    
    void display()
    {
        cout << vName << "\t" << speed << endl;
    }
    
    void start()
    {
        cout << vName << "\t" << "started" << endl;
    }
};

template<class Type>    // Line 85: Class template declaration
class SP                // Line 86: Smart Pointer template
{
private:
    Type* pData;        // Line 88: Pointer to generic type
    
public:
    SP(Type* pValue)    // Line 91: Constructor
    {
        pData = new Type(*pValue);  // Line 93: Deep copy using copy constructor
        delete pValue;               // Line 94: Delete original heap allocation
    }
    
    ~SP()               // Line 97: Destructor
    {
        delete pData;   // Line 99: Automatic memory cleanup
    }

    Type& operator*()   // Line 102: Dereference operator
    {
        return *pData;
    }

    Type* operator->()  // Line 107: Arrow operator
    {
        return pData;
    }
};

int main()
{
    SP<Person> s(new Person("abc", 10));  // Line 114: Smart pointer to Person
    s->display();                          // Line 115: Uses operator->
    s->shout();
    (*s).display();                        // Line 117: Uses operator*
    (*s).shout();

    SP<Vehicle> s1(new Vehicle("Car", 100));  // Line 120: Smart pointer to Vehicle
    s1->display();
    s1->start();
    (*s1).display();
    (*s1).start();
    
    return 0;
}  // Line 126: s and s1 go out of scope, destructors called automatically
```

### Line-by-Line Explanation:

| Line | Code | Explanation |
|------|------|-------------|
| 85 | `template<class Type>` | Template parameter for any class |
| 88 | `Type* pData` | Pointer can point to Person, Vehicle, or any type |
| 93 | `new Type(*pValue)` | Creates copy using Type's copy constructor |
| 94 | `delete pValue` | Smart pointer takes ownership, deletes original |
| 99 | `delete pData` | Destructor ensures no memory leak |
| 102-104 | `operator*()` | Allows `(*sp)` syntax |
| 107-109 | `operator->()` | Allows `sp->method()` syntax |
| 114 | `SP<Person>` | Instantiates template with Type=Person |
| 120 | `SP<Vehicle>` | Instantiates template with Type=Vehicle |

### Execution Flow:

```
┌─────────────────────────────────────┐
│ SP<Person> s(new Person("abc",10)) │
├─────────────────────────────────────┤
│ 1. new Person("abc", 10) creates   │
│    heap object                      │
│ 2. SP constructor called           │
│    - Creates copy of Person        │
│    - Deletes original              │
│ 3. s now owns the Person copy      │
└─────────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────────┐
│ s->display()                        │
│ - operator->() returns pData       │
│ - display() called on *pData       │
│ Output: "abc    10"                │
└─────────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────────┐
│ (*s).shout()                        │
│ - operator*() returns *pData       │
│ - shout() called on dereferenced   │
│ Output: "aaaaaaaaaaaoooooooooo"    │
└─────────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────────┐
│ main() ends, s goes out of scope   │
│ - ~SP() called                     │
│ - delete pData called              │
│ - ~Person() called automatically   │
│ - Memory properly freed            │
└─────────────────────────────────────┘
```

### Example 2: Generic Array Container

```cpp
template<class T>
class Array
{
private:
    T* data;
    int size;
    
public:
    Array(int s) : size(s)
    {
        data = new T[size];
    }
    
    ~Array()
    {
        delete[] data;
    }
    
    T& operator[](int index)
    {
        return data[index];
    }
    
    int getSize() { return size; }
};

int main()
{
    Array<int> intArr(5);
    intArr[0] = 10;
    intArr[1] = 20;
    
    Array<string> strArr(3);
    strArr[0] = "Hello";
    strArr[1] = "World";
    
    return 0;
}
```

---

## Key Points

1. **Type Parameter**: `T` represents any type provided at instantiation.

2. **Explicit Type Specification**: Must specify type when creating objects: `ClassName<Type>`.

3. **Code Generation**: Compiler generates separate class for each type used.

4. **Member Functions**: Can use `T` in return types, parameters, and member variables.

5. **Outside Definitions**: Need `template <class T>` prefix and `ClassName<T>::` qualifier.

6. **Copy Constructor**: Essential for templates that copy objects.

7. **RAII**: Class templates are great for implementing RAII patterns (like smart pointers).

---

## Interview Questions

### Q1: What is a class template?
**Answer**: A class template is a blueprint for creating classes that work with any data type. The actual type is specified when creating objects, and the compiler generates specific class code for each type used.

### Q2: How do you create an object of a class template?
**Answer**: Specify the type in angle brackets:
```cpp
ClassName<int> obj1;
ClassName<double> obj2;
ClassName<MyClass> obj3;
```

### Q3: Why is the smart pointer template useful?
**Answer**: It provides automatic memory management for any class type. When the smart pointer goes out of scope, it automatically deletes the managed object, preventing memory leaks.

### Q4: How do you define member functions outside a class template?
**Answer**:
```cpp
template <class T>
ReturnType ClassName<T>::functionName(parameters)
{
    // implementation
}
```
Both the template header and class qualifier with `<T>` are required.

### Q5: Can a class template have multiple type parameters?
**Answer**: Yes:
```cpp
template <class Key, class Value>
class Map
{
    Key* keys;
    Value* values;
};

Map<string, int> ages;
```
