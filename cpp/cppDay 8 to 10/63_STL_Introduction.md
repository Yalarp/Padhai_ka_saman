# C++ Standard Template Library (STL) Introduction

## Table of Contents
- [Introduction](#introduction)
- [STL Components](#stl-components)
- [Containers](#containers)
- [Iterators](#iterators)
- [Algorithms](#algorithms)
- [STL vs Raw Arrays](#stl-vs-raw-arrays)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

The Standard Template Library (STL) is a powerful set of C++ template classes that provide general-purpose classes and functions with templates that implement many popular and commonly used algorithms and data structures.

> **Definition**: STL consists of inbuilt class templates for various data structures like vector, list, map, etc. It also includes iterators (for traversing) and algorithms (for specific tasks like sort, count, max, min).

---

## STL Components

```
┌───────────────────────────────────────────────────────────────┐
│                    STANDARD TEMPLATE LIBRARY                  │
├─────────────────┬───────────────────┬────────────────────────┤
│   CONTAINERS    │    ITERATORS      │     ALGORITHMS         │
├─────────────────┼───────────────────┼────────────────────────┤
│ vector          │ Input Iterator    │ sort()                 │
│ list            │ Output Iterator   │ find()                 │
│ deque           │ Forward Iterator  │ count()                │
│ set/multiset    │ Bidirectional     │ max()/min()            │
│ map/multimap    │ Random Access     │ transform()            │
│ stack           │                   │ accumulate()           │
│ queue           │                   │ reverse()              │
│ priority_queue  │                   │ binary_search()        │
└─────────────────┴───────────────────┴────────────────────────┘
```

---

## Containers

Containers are data structures that store collections of objects:

| Container | Description | Header |
|-----------|-------------|--------|
| `vector` | Dynamic array | `<vector>` |
| `list` | Doubly linked list | `<list>` |
| `deque` | Double-ended queue | `<deque>` |
| `set` | Sorted unique elements | `<set>` |
| `map` | Key-value pairs | `<map>` |
| `stack` | LIFO structure | `<stack>` |
| `queue` | FIFO structure | `<queue>` |

### Container Categories:

| Category | Examples | Characteristics |
|----------|----------|-----------------|
| **Sequence** | vector, list, deque | Elements in linear sequence |
| **Associative** | set, map, multiset, multimap | Sorted by keys |
| **Adapter** | stack, queue, priority_queue | Modified interface |

---

## Iterators

Iterators are objects that allow traversal through container elements:

```cpp
vector<int>::iterator it;  // Declare iterator
it = vec.begin();          // Point to first element
*it                        // Access element value
it++                       // Move to next element
```

### Iterator Functions:

| Function | Description |
|----------|-------------|
| `begin()` | Returns iterator to first element |
| `end()` | Returns iterator past last element |
| `cbegin()` | Const iterator to first element |
| `cend()` | Const iterator past last |

---

## Algorithms

STL provides functions that operate on containers:

```cpp
#include <algorithm>

sort(vec.begin(), vec.end());           // Sort container
find(vec.begin(), vec.end(), value);    // Find element
count(vec.begin(), vec.end(), value);   // Count occurrences
max_element(vec.begin(), vec.end());    // Find maximum
```

---

## STL vs Raw Arrays

| Aspect | Raw Array | STL Container |
|--------|-----------|---------------|
| **Size** | Fixed at compile time | Dynamic, can grow |
| **Bounds Checking** | None | Optional (at()) |
| **Memory Management** | Manual | Automatic |
| **Size Query** | Calculate yourself | size() method |
| **Algorithms** | Write your own | STL provides |

---

## Code Examples

### Example 1: Basic Vector with Iterator

```cpp
#include<iostream>      // Line 1
#include<vector>        // Line 2: Include vector header
using namespace std;    // Line 3

int main()
{
    vector<int> myvect(10, 5);         // Line 7: 10 ints, all initialized to 5
    vector<int>::iterator myiter;       // Line 8: Declare iterator
    
    for(myiter = myvect.begin(); myiter != myvect.end(); myiter++)  // Line 10
    {
        cout << endl << *myiter << endl;  // Line 12: Dereference iterator
    }
    
    return 0;
}
```

### Line-by-Line Explanation:

| Line | Code | Explanation |
|------|------|-------------|
| 2 | `#include<vector>` | Includes vector container |
| 7 | `vector<int> myvect(10, 5)` | Creates vector of 10 integers, each = 5 |
| 8 | `vector<int>::iterator myiter` | Declares iterator for int vector |
| 10 | `myvect.begin()` | Returns iterator to first element |
| 10 | `myvect.end()` | Returns iterator past last element |
| 12 | `*myiter` | Dereferences iterator to get value |

### Output:
```
5
5
5
5
5
5
5
5
5
5
```

### Example 2: Dynamic Vector Operations

```cpp
#include<iostream>      // Line 1
#include<vector>        // Line 2
using namespace std;    // Line 3

int main()
{
    vector<char> v(10);              // Line 7: Vector with 10 default chars
    int i;
    
    cout << endl << v.size() << endl;  // Line 10: Output: 10
    
    // Initialize with lowercase letters
    for (i = 0; i < 10; i++)         // Line 13
    {
        v[i] = i + 'a';               // Line 15: a, b, c, d...
    }
    
    // Display elements
    for (i = 0; i < v.size(); i++)   // Line 19
    {
        cout << endl << v[i] << endl;
    }
    
    // Add more elements using push_back
    for (i = 0; i < 10; i++)         // Line 25
    {
        v.push_back(i + 10 + 'a');   // Line 27: Appends to vector
    }
    
    cout << endl << v.size() << endl;  // Line 30: Output: 20
    
    // Display all elements
    for (i = 0; i < v.size(); i++)
    {
        cout << v[i] << endl;
    }
    
    // Convert to uppercase
    for (i = 0; i < v.size(); i++)   // Line 38
    {
        v[i] = toupper(v[i]);
    }
    
    // Display uppercase
    for (i = 0; i < v.size(); i++)
    {
        cout << v[i] << endl;
    }
    
    return 0;
}
```

### Execution Flow:

```
┌─────────────────────────────────────┐
│ vector<char> v(10)                  │
│ Creates vector with 10 elements     │
│ v.size() = 10                       │
└─────────────┬───────────────────────┘
              │
              ▼
┌─────────────────────────────────────┐
│ Fill with: a, b, c, d, e, f, g, h,  │
│           i, j                       │
└─────────────┬───────────────────────┘
              │
              ▼
┌─────────────────────────────────────┐
│ push_back() 10 more elements        │
│ k, l, m, n, o, p, q, r, s, t       │
│ v.size() = 20 (dynamically grew!)   │
└─────────────┬───────────────────────┘
              │
              ▼
┌─────────────────────────────────────┐
│ Convert all to uppercase            │
│ A, B, C, D...T                      │
└─────────────────────────────────────┘
```

---

## Key Points

1. **Dynamic Size**: STL containers can grow and shrink dynamically.

2. **Type Safety**: Templates ensure type-safe containers.

3. **Three Components**: Containers + Iterators + Algorithms.

4. **Memory Management**: STL handles memory allocation automatically.

5. **Headers**: Each container has its own header file.

6. **Iterators**: Provide uniform way to traverse any container.

7. **Algorithms**: Work with any container through iterators.

---

## Interview Questions

### Q1: What is STL?
**Answer**: Standard Template Library is a collection of C++ template classes providing container classes (vector, list, map), iterators for traversing containers, and algorithms (sort, find, count) that work with iterators.

### Q2: What is the difference between STL and raw arrays?
**Answer**:
| Raw Arrays | STL Containers |
|------------|----------------|
| Fixed size | Dynamic size |
| No bounds checking | at() provides checking |
| Manual memory | Automatic memory |
| Need sizeof for size | size() method |

### Q3: What are the three components of STL?
**Answer**:
1. **Containers**: Data structures (vector, list, map)
2. **Iterators**: Objects for traversing containers
3. **Algorithms**: Functions that operate on containers (sort, find)

### Q4: What is the relationship between iterators and algorithms?
**Answer**: Algorithms use iterators to access container elements. This separation allows algorithms to work with any container that provides compatible iterators.

### Q5: What is push_back()?
**Answer**: A vector method that adds an element to the end of the vector, automatically resizing if needed. Example: `vec.push_back(10);`
