# C++ string_view (C++17)

## Table of Contents
- [Introduction](#introduction)
- [Why string_view](#why-string_view)
- [Syntax and Usage](#syntax-and-usage)
- [Limitations](#limitations)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

`std::string_view` is a lightweight, non-owning view of a string. It provides read-only access to a sequence of characters without copying or allocating memory.

> **Header**: `#include <string_view>`

---

## Why string_view

| Problem | Solution |
|---------|----------|
| Copying strings is expensive | string_view never copies |
| Multiple string types | Works with string, char*, etc. |
| Substring extraction | Creates view without copy |

### Traditional Approach:
```cpp
void print(const string& s) { }  // May force temporary
void print(const char* s) { }    // Need overload
```

### With string_view:
```cpp
void print(string_view s) { }    // Works with both!
```

---

## Syntax and Usage

```cpp
#include <string_view>

string_view sv1 = "Hello";           // From literal
string s = "World";
string_view sv2 = s;                 // From string
string_view sv3 = sv1.substr(0, 3);  // Cheap substring
```

### Common Operations:

| Method | Description |
|--------|-------------|
| `size()` | Number of characters |
| `empty()` | Check if empty |
| `substr()` | Get substring view |
| `find()` | Find substring |
| `remove_prefix()` | Skip first n chars |
| `remove_suffix()` | Skip last n chars |

---

## Limitations

> **Warning**: `string_view` is non-owning. The underlying data must outlive the view!

```cpp
string_view bad() {
    string s = "temporary";
    return s;  // DANGER: s destroyed, view becomes dangling!
}
```

### Not Null-Terminated:
```cpp
string_view sv = "Hello";
// Don't use sv.data() with C functions expecting null-terminated strings
```

---

## Code Examples

### Example 1: Basic Usage

```cpp
#include <iostream>
#include <string>
#include <string_view>
using namespace std;

void print(string_view sv) {
    cout << sv << " (length: " << sv.size() << ")" << endl;
}

int main() {
    print("Hello");           // From literal
    
    string s = "World";
    print(s);                 // From string
    
    char arr[] = "Array";
    print(arr);               // From char array
    
    return 0;
}
```

### Example 2: Efficient Substring

```cpp
#include <iostream>
#include <string_view>
using namespace std;

int main() {
    string_view text = "Hello World Programming";
    
    // Cheap substring (no copy!)
    string_view hello = text.substr(0, 5);   // "Hello"
    string_view world = text.substr(6, 5);   // "World"
    
    cout << hello << " - " << world << endl;
    
    // Modify view (not the data)
    string_view sv = text;
    sv.remove_prefix(6);   // Now: "World Programming"
    sv.remove_suffix(12);  // Now: "World"
    
    cout << sv << endl;
    
    return 0;
}
```

---

## Key Points

1. **Non-Owning**: Doesn't own the data, just a view.
2. **No Copy**: Very cheap to create and pass.
3. **Read-Only**: Cannot modify underlying data.
4. **Lifetime**: Data must outlive the view.
5. **Universal**: Works with string, char*, char array.

---

## Interview Questions

### Q1: What is string_view?
**Answer**: `string_view` is a non-owning, read-only view of a contiguous sequence of characters. It avoids copying strings.

### Q2: When would you use string_view?
**Answer**:
- Function parameters that only read strings
- When you need cheap substring operations
- When interfacing with different string types

### Q3: What is the danger with string_view?
**Answer**: Dangling reference if the underlying string is destroyed while the view still exists.
