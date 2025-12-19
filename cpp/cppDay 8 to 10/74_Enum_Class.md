# C++ Enum Class (C++11)

## Table of Contents
- [Introduction](#introduction)
- [Traditional enum vs enum class](#traditional-enum-vs-enum-class)
- [Syntax](#syntax)
- [Underlying Type](#underlying-type)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

`enum class` (scoped enumeration) was introduced in C++11 to address problems with traditional enums. It provides type safety and prevents name collisions.

---

## Traditional enum vs enum class

| Feature | Traditional `enum` | `enum class` |
|---------|-------------------|--------------|
| Scope | Global | Scoped to enum |
| Implicit conversion | To int | No implicit conversion |
| Name collision | Possible | Prevented |
| Forward declaration | No | Yes |
| Type safety | Weak | Strong |

### Problem with Traditional enum:
```cpp
enum Color { Red, Green, Blue };
enum TrafficLight { Red, Yellow, Green };  // Error: Red, Green already defined!

Color c = Red;
int i = c;  // Implicit conversion - may cause bugs
```

### Solution with enum class:
```cpp
enum class Color { Red, Green, Blue };
enum class TrafficLight { Red, Yellow, Green };  // OK: different scopes

Color c = Color::Red;
// int i = c;  // Error: no implicit conversion
int i = static_cast<int>(c);  // Explicit conversion required
```

---

## Syntax

### Declaration:
```cpp
enum class EnumName { Value1, Value2, Value3 };
```

### Usage:
```cpp
EnumName variable = EnumName::Value1;
```

### With Underlying Type:
```cpp
enum class Color : unsigned char { Red, Green, Blue };
enum class BigEnum : long long { Large = 1000000000000LL };
```

---

## Underlying Type

By default, `enum class` uses `int`. You can specify a different type:

```cpp
enum class SmallColor : uint8_t {
    Red = 0,
    Green = 1,
    Blue = 2
};

enum class Permissions : unsigned int {
    None = 0,
    Read = 1,
    Write = 2,
    Execute = 4
};
```

---

## Code Examples

### Example 1: Basic enum class Usage

```cpp
#include<iostream>
using namespace std;

enum class Color { Red, Green, Blue };
enum class Size { Small, Medium, Large };

int main() {
    Color c = Color::Red;
    Size s = Size::Medium;
    
    // Type safe - can't mix types
    // if (c == s) { }  // Error!
    
    // Compare within same type - OK
    if (c == Color::Red) {
        cout << "Color is Red" << endl;
    }
    
    // Explicit conversion to int
    int colorNum = static_cast<int>(c);
    cout << "Color value: " << colorNum << endl;
    
    return 0;
}
```

### Example 2: Switch with enum class

```cpp
#include<iostream>
using namespace std;

enum class Direction { North, South, East, West };

void move(Direction d) {
    switch (d) {
        case Direction::North:
            cout << "Moving North" << endl;
            break;
        case Direction::South:
            cout << "Moving South" << endl;
            break;
        case Direction::East:
            cout << "Moving East" << endl;
            break;
        case Direction::West:
            cout << "Moving West" << endl;
            break;
    }
}

int main() {
    move(Direction::North);
    move(Direction::West);
    return 0;
}
```

### Example 3: enum class with Explicit Values

```cpp
#include<iostream>
using namespace std;

enum class HttpStatus : int {
    OK = 200,
    Created = 201,
    BadRequest = 400,
    NotFound = 404,
    ServerError = 500
};

void handleResponse(HttpStatus status) {
    int code = static_cast<int>(status);
    
    if (status == HttpStatus::OK) {
        cout << "Success: " << code << endl;
    } else if (code >= 400 && code < 500) {
        cout << "Client Error: " << code << endl;
    } else if (code >= 500) {
        cout << "Server Error: " << code << endl;
    }
}

int main() {
    handleResponse(HttpStatus::OK);
    handleResponse(HttpStatus::NotFound);
    handleResponse(HttpStatus::ServerError);
    return 0;
}
```

---

## Key Points

1. **Scoped**: Values accessed via `EnumName::Value`.
2. **No Implicit Conversion**: Must use `static_cast` for int.
3. **No Name Collision**: Different enums can have same value names.
4. **Type Safe**: Can't compare different enum types.
5. **Underlying Type**: Default int, can specify others.
6. **Forward Declaration**: Possible with enum class.

---

## Interview Questions

### Q1: What is the difference between enum and enum class?
**Answer**:
| enum | enum class |
|------|------------|
| Unscoped | Scoped |
| Implicit int conversion | No implicit conversion |
| Can have name collisions | No name collisions |
| Weak type safety | Strong type safety |

### Q2: How do you convert enum class to int?
**Answer**: Use `static_cast`:
```cpp
enum class Color { Red, Green, Blue };
Color c = Color::Red;
int value = static_cast<int>(c);
```

### Q3: Can two enum classes have the same value names?
**Answer**: Yes, because enum class values are scoped:
```cpp
enum class Fruit { Apple, Orange };
enum class Company { Apple, Microsoft };  // OK - different scopes
```

### Q4: What is the default underlying type of enum class?
**Answer**: The default underlying type is `int`. You can specify a different type:
```cpp
enum class SmallEnum : uint8_t { A, B, C };
```

### Q5: Why is enum class preferred over traditional enum?
**Answer**:
- Prevents naming conflicts
- No implicit conversion (safer)
- Better type checking
- Enables forward declaration
