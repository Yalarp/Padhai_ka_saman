# C++ STL Containers

## Table of Contents
- [Introduction](#introduction)
- [Container Categories](#container-categories)
- [Vector](#vector)
- [List](#list)
- [Map](#map)
- [Set](#set)
- [Deque](#deque)
- [Stack and Queue](#stack-and-queue)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

STL containers are template classes that manage collections of objects. Each container has different characteristics suitable for different use cases.

---

## Container Categories

| Category | Containers | Key Feature |
|----------|------------|-------------|
| **Sequence** | vector, list, deque, array | Linear element arrangement |
| **Associative** | set, map, multiset, multimap | Sorted by keys |
| **Unordered Associative** | unordered_set, unordered_map | Hash-based lookup |
| **Container Adapters** | stack, queue, priority_queue | Restricted interface |

---

## Vector

Dynamic array that can resize automatically.

### Declaration:
```cpp
#include <vector>
vector<int> v;                    // Empty vector
vector<int> v(10);               // 10 elements, default value
vector<int> v(10, 5);            // 10 elements, all = 5
vector<int> v = {1, 2, 3, 4};    // Initializer list
```

### Common Operations:

| Operation | Description | Complexity |
|-----------|-------------|------------|
| `push_back(val)` | Add to end | O(1) amortized |
| `pop_back()` | Remove from end | O(1) |
| `size()` | Number of elements | O(1) |
| `at(i)` or `[i]` | Access element | O(1) |
| `front()` | First element | O(1) |
| `back()` | Last element | O(1) |
| `clear()` | Remove all | O(n) |
| `empty()` | Check if empty | O(1) |

### Example:
```cpp
vector<int> v;
v.push_back(10);      // [10]
v.push_back(20);      // [10, 20]
v.push_back(30);      // [10, 20, 30]
cout << v.size();     // 3
cout << v[1];         // 20
cout << v.front();    // 10
cout << v.back();     // 30
v.pop_back();         // [10, 20]
```

---

## List

Doubly linked list allowing insertion/deletion at any position.

### Declaration:
```cpp
#include <list>
list<int> lst;
list<int> lst = {1, 2, 3};
```

### Common Operations:

| Operation | Description | Complexity |
|-----------|-------------|------------|
| `push_back()` | Add to end | O(1) |
| `push_front()` | Add to beginning | O(1) |
| `pop_back()` | Remove from end | O(1) |
| `pop_front()` | Remove from beginning | O(1) |
| `insert()` | Insert at position | O(1) |
| `remove(val)` | Remove all val | O(n) |

### Example:
```cpp
list<int> lst;
lst.push_back(10);    // [10]
lst.push_front(5);    // [5, 10]
lst.push_back(15);    // [5, 10, 15]
lst.pop_front();      // [10, 15]
```

---

## Map

Key-value pairs stored in sorted order by key.

### Declaration:
```cpp
#include <map>
map<string, int> ages;
map<int, string> names;
```

### Common Operations:

| Operation | Description |
|-----------|-------------|
| `insert({key, value})` | Add pair |
| `[key]` | Access/create value |
| `at(key)` | Access with bounds check |
| `find(key)` | Returns iterator |
| `erase(key)` | Remove by key |
| `count(key)` | 0 or 1 |

### Example:
```cpp
map<string, int> ages;
ages["Alice"] = 25;
ages["Bob"] = 30;
ages.insert({"Charlie", 35});

cout << ages["Alice"];  // 25
cout << ages.size();    // 3

for(auto& p : ages) {
    cout << p.first << ": " << p.second;
}
```

---

## Set

Collection of unique, sorted elements.

### Declaration:
```cpp
#include <set>
set<int> s;
set<int> s = {3, 1, 4, 1, 5};  // {1, 3, 4, 5} - duplicates removed
```

### Common Operations:

| Operation | Description |
|-----------|-------------|
| `insert(val)` | Add element |
| `erase(val)` | Remove element |
| `find(val)` | Returns iterator |
| `count(val)` | 0 or 1 |
| `size()` | Number of elements |

### Example:
```cpp
set<int> s;
s.insert(10);
s.insert(5);
s.insert(10);  // Duplicate, ignored
s.insert(15);
// s = {5, 10, 15}

if(s.count(10)) cout << "Found";
```

---

## Deque

Double-ended queue - efficient insertion/deletion at both ends.

### Declaration:
```cpp
#include <deque>
deque<int> dq;
deque<int> dq(5, 10);  // 5 elements, all 10
```

### Common Operations:

| Operation | Description |
|-----------|-------------|
| `push_front()` | Add to beginning |
| `push_back()` | Add to end |
| `pop_front()` | Remove from beginning |
| `pop_back()` | Remove from end |
| `[i]` | Random access |

---

## Stack and Queue

### Stack (LIFO):
```cpp
#include <stack>
stack<int> st;
st.push(10);          // Add to top
st.push(20);
cout << st.top();     // 20 (peek)
st.pop();             // Remove top
cout << st.top();     // 10
```

### Queue (FIFO):
```cpp
#include <queue>
queue<int> q;
q.push(10);           // Add to back
q.push(20);
cout << q.front();    // 10 (first in)
cout << q.back();     // 20 (last in)
q.pop();              // Remove front
cout << q.front();    // 20
```

---

## Key Points

1. **Vector**: Best for random access and back insertions.
2. **List**: Best for frequent insertions/deletions in middle.
3. **Map**: Key-value lookup, automatically sorted.
4. **Set**: Unique elements, automatically sorted.
5. **Deque**: Efficient both-ends operations.
6. **Stack**: Last-In-First-Out operations.
7. **Queue**: First-In-First-Out operations.

---

## Interview Questions

### Q1: When would you use vector vs list?
**Answer**:
| Use Vector When | Use List When |
|-----------------|---------------|
| Need random access [i] | Frequent mid-insertions |
| Mostly add/remove at end | Add/remove at beginning |
| Memory locality matters | Don't need random access |

### Q2: What is the difference between map and unordered_map?
**Answer**:
| map | unordered_map |
|-----|---------------|
| Sorted by keys | No ordering |
| O(log n) lookup | O(1) average lookup |
| Uses tree | Uses hash table |

### Q3: Can a set contain duplicates?
**Answer**: No. A `set` stores only unique elements. Use `multiset` for duplicates.

### Q4: What is the difference between push_back and insert in vector?
**Answer**: 
- `push_back()`: Adds element at the end (O(1) amortized)
- `insert()`: Adds element at any position (O(n) for shifting)
