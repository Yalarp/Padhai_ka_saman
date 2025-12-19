# C++ Smart Pointers (C++11)

## Table of Contents
- [Introduction](#introduction)
- [Problems with Raw Pointers](#problems-with-raw-pointers)
- [Types of Smart Pointers](#types-of-smart-pointers)
- [unique_ptr](#unique_ptr)
- [shared_ptr](#shared_ptr)
- [weak_ptr](#weak_ptr)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

Smart pointers are wrapper classes that manage dynamically allocated memory automatically. They implement RAII (Resource Acquisition Is Initialization), ensuring proper cleanup when the pointer goes out of scope.

> **Header Required**: `#include <memory>`

---

## Problems with Raw Pointers

| Problem | Description |
|---------|-------------|
| Memory Leaks | Forgetting to `delete` |
| Dangling Pointers | Using after `delete` |
| Double Deletion | Deleting twice |
| Exception Safety | Leak if exception thrown |
| Ownership Ambiguity | Who should delete? |

---

## Types of Smart Pointers

| Type | Ownership | Use Case |
|------|-----------|----------|
| `unique_ptr` | Exclusive | Single owner scenarios |
| `shared_ptr` | Shared | Multiple owners |
| `weak_ptr` | Non-owning | Breaking circular references |

---

## unique_ptr

Exclusive ownership - only one unique_ptr can own an object.

### Creation:
```cpp
unique_ptr<int> p1(new int(10));       // Direct
auto p2 = make_unique<int>(20);         // Preferred (C++14)
```

### Key Features:
- Cannot be copied
- Can be moved
- Automatically deletes on destruction

### Example:
```cpp
unique_ptr<int> p1 = make_unique<int>(100);
cout << *p1;  // 100

// unique_ptr<int> p2 = p1;  // Error: cannot copy
unique_ptr<int> p2 = move(p1);  // OK: transfer ownership
// p1 is now nullptr
```

---

## shared_ptr

Shared ownership - multiple shared_ptrs can own the same object.

### Creation:
```cpp
shared_ptr<int> p1(new int(10));
auto p2 = make_shared<int>(20);  // Preferred
```

### Key Features:
- Reference counted
- Object deleted when count reaches 0
- Can be copied and moved
- `use_count()` returns current reference count

### Example:
```cpp
auto p1 = make_shared<int>(100);
cout << p1.use_count();  // 1

auto p2 = p1;  // Share ownership
cout << p1.use_count();  // 2

p1.reset();  // p1 releases, count = 1
cout << p2.use_count();  // 1
```

---

## weak_ptr

Non-owning reference to shared_ptr managed object.

### Purpose:
- Breaks circular references
- Observes without affecting lifetime
- Must convert to shared_ptr before use

### Example:
```cpp
shared_ptr<int> sp = make_shared<int>(42);
weak_ptr<int> wp = sp;  // Doesn't increase count

cout << sp.use_count();  // 1 (weak doesn't count)

// Access through lock()
if (auto locked = wp.lock()) {
    cout << *locked;  // 42
}
```

---

## Code Examples

### Example 1: unique_ptr Basic Usage

```cpp
#include <iostream>
#include <memory>
using namespace std;

class Person {
public:
    string name;
    Person(string n) : name(n) {
        cout << "Created: " << name << endl;
    }
    ~Person() {
        cout << "Destroyed: " << name << endl;
    }
};

int main() {
    {
        unique_ptr<Person> p1 = make_unique<Person>("Alice");
        cout << "Name: " << p1->name << endl;
        
        // Transfer ownership
        unique_ptr<Person> p2 = move(p1);
        
        if (!p1) cout << "p1 is null after move" << endl;
        cout << "p2 has: " << p2->name << endl;
    }  // p2 goes out of scope, Person destroyed
    
    cout << "End of main" << endl;
    return 0;
}
```

### Output:
```
Created: Alice
Name: Alice
p1 is null after move
p2 has: Alice
Destroyed: Alice
End of main
```

### Example 2: shared_ptr Reference Counting

```cpp
#include <iostream>
#include <memory>
using namespace std;

int main() {
    shared_ptr<int> p1 = make_shared<int>(100);
    cout << "p1 count: " << p1.use_count() << endl;  // 1
    
    {
        shared_ptr<int> p2 = p1;  // Share
        cout << "After p2 = p1" << endl;
        cout << "p1 count: " << p1.use_count() << endl;  // 2
        cout << "p2 count: " << p2.use_count() << endl;  // 2
        
        shared_ptr<int> p3 = p1;  // Another share
        cout << "After p3 = p1" << endl;
        cout << "Count: " << p1.use_count() << endl;     // 3
    }  // p2 and p3 destroyed
    
    cout << "After block" << endl;
    cout << "p1 count: " << p1.use_count() << endl;  // 1
    
    return 0;
}  // p1 destroyed, memory freed
```

### Example 3: weak_ptr Breaking Circular Reference

```cpp
#include <iostream>
#include <memory>
using namespace std;

class B;  // Forward declaration

class A {
public:
    shared_ptr<B> b_ptr;
    ~A() { cout << "A destroyed" << endl; }
};

class B {
public:
    weak_ptr<A> a_ptr;  // Use weak_ptr to break cycle
    ~B() { cout << "B destroyed" << endl; }
};

int main() {
    auto a = make_shared<A>();
    auto b = make_shared<B>();
    
    a->b_ptr = b;
    b->a_ptr = a;  // weak_ptr doesn't increase count
    
    cout << "a count: " << a.use_count() << endl;  // 1
    cout << "b count: " << b.use_count() << endl;  // 2
    
    return 0;
}  // Both properly destroyed
```

---

## Key Points

1. **unique_ptr**: Exclusive ownership, no copy, only move.
2. **shared_ptr**: Shared ownership, reference counted.
3. **weak_ptr**: Non-owning, breaks cycles, needs lock().
4. **make_unique/make_shared**: Preferred creation methods.
5. **RAII**: Automatic cleanup when out of scope.
6. **No Manual Delete**: Never use delete with smart pointers.

---

## Interview Questions

### Q1: What is the difference between unique_ptr and shared_ptr?
**Answer**:
| unique_ptr | shared_ptr |
|------------|------------|
| Exclusive ownership | Shared ownership |
| Cannot be copied | Can be copied |
| No overhead | Reference counting overhead |
| Size: 1 pointer | Size: 2 pointers (data + control) |

### Q2: When would you use weak_ptr?
**Answer**: Use `weak_ptr` when you need to:
- Break circular references between shared_ptrs
- Observe an object without affecting its lifetime
- Cache objects that may be deleted

### Q3: What happens when shared_ptr count reaches 0?
**Answer**: When the last `shared_ptr` owning an object is destroyed or reset, the managed object is automatically deleted.

### Q4: Can you copy a unique_ptr?
**Answer**: No. `unique_ptr` cannot be copied (copy constructor is deleted). You can only transfer ownership using `std::move()`.

### Q5: Why use make_shared instead of new?
**Answer**:
- Exception safe (single allocation)
- More efficient (one allocation for object + control block)
- Cleaner syntax
- No explicit `new` keyword
