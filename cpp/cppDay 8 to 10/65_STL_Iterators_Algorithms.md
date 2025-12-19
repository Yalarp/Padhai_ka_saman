# C++ STL Iterators and Algorithms

## Table of Contents
- [Introduction to Iterators](#introduction-to-iterators)
- [Iterator Types](#iterator-types)
- [Iterator Operations](#iterator-operations)
- [STL Algorithms](#stl-algorithms)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction to Iterators

Iterators are objects that act like pointers, allowing you to traverse through container elements. They provide a uniform interface for accessing elements regardless of the container type.

### Basic Concept:

```cpp
vector<int>::iterator it;    // Declare iterator for int vector
it = vec.begin();            // Point to first element
cout << *it;                 // Dereference to get value
it++;                        // Move to next element
```

---

## Iterator Types

| Type | Capabilities | Containers |
|------|--------------|------------|
| **Input** | Read forward only | istream |
| **Output** | Write forward only | ostream |
| **Forward** | Read/write forward | forward_list |
| **Bidirectional** | Forward + backward | list, set, map |
| **Random Access** | Any position access | vector, deque, array |

### Capability Hierarchy:
```
Random Access  ⊃  Bidirectional  ⊃  Forward  ⊃  Input/Output
```

---

## Iterator Operations

### Common Member Functions:

| Function | Description |
|----------|-------------|
| `begin()` | Iterator to first element |
| `end()` | Iterator past last element |
| `cbegin()` | Const iterator to first |
| `cend()` | Const iterator past last |
| `rbegin()` | Reverse iterator to last |
| `rend()` | Reverse iterator before first |

### Iterator Operators:

| Operator | Description | Type Required |
|----------|-------------|---------------|
| `*it` | Dereference | All |
| `++it` | Move forward | All |
| `--it` | Move backward | Bidirectional+ |
| `it + n` | Jump n positions | Random Access |
| `it - n` | Jump back n positions | Random Access |
| `it[n]` | Access nth element | Random Access |
| `it1 - it2` | Distance between | Random Access |

---

## STL Algorithms

### Header Required:
```cpp
#include <algorithm>
#include <numeric>  // For accumulate
```

### Sorting Algorithms:

```cpp
sort(vec.begin(), vec.end());           // Ascending
sort(vec.begin(), vec.end(), greater<int>()); // Descending
stable_sort(vec.begin(), vec.end());    // Preserves equal element order
```

### Searching Algorithms:

```cpp
auto it = find(vec.begin(), vec.end(), value);     // Linear search
bool found = binary_search(vec.begin(), vec.end(), value);  // Sorted only
```

### Numeric Algorithms:

```cpp
int sum = accumulate(vec.begin(), vec.end(), 0);   // Sum all
int cnt = count(vec.begin(), vec.end(), value);    // Count occurrences
auto maxIt = max_element(vec.begin(), vec.end());  // Iterator to max
auto minIt = min_element(vec.begin(), vec.end());  // Iterator to min
```

### Modifying Algorithms:

```cpp
reverse(vec.begin(), vec.end());                   // Reverse order
transform(vec.begin(), vec.end(), vec.begin(), func); // Apply function
fill(vec.begin(), vec.end(), value);              // Fill with value
copy(src.begin(), src.end(), dst.begin());        // Copy elements
```

---

## Code Examples

### Example 1: Iterator Traversal

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> v = {10, 20, 30, 40, 50};
    
    // Forward iteration
    cout << "Forward: ";
    vector<int>::iterator it;
    for (it = v.begin(); it != v.end(); ++it) {
        cout << *it << " ";
    }
    
    // Reverse iteration
    cout << "\nReverse: ";
    vector<int>::reverse_iterator rit;
    for (rit = v.rbegin(); rit != v.rend(); ++rit) {
        cout << *rit << " ";
    }
    
    // Modern range-based (uses iterators internally)
    cout << "\nRange-based: ";
    for (int x : v) {
        cout << x << " ";
    }
    
    return 0;
}
```

### Output:
```
Forward: 10 20 30 40 50
Reverse: 50 40 30 20 10
Range-based: 10 20 30 40 50
```

### Example 2: Algorithm Usage

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

int main() {
    vector<int> v = {5, 2, 8, 1, 9, 3};
    
    // Sort
    sort(v.begin(), v.end());
    // v = {1, 2, 3, 5, 8, 9}
    
    // Find
    auto it = find(v.begin(), v.end(), 5);
    if (it != v.end()) {
        cout << "Found 5 at position: " << (it - v.begin()) << endl;
    }
    
    // Count
    v.push_back(3);  // Add duplicate
    int cnt = count(v.begin(), v.end(), 3);
    cout << "Count of 3: " << cnt << endl;
    
    // Sum
    int sum = accumulate(v.begin(), v.end(), 0);
    cout << "Sum: " << sum << endl;
    
    // Max/Min
    auto maxIt = max_element(v.begin(), v.end());
    auto minIt = min_element(v.begin(), v.end());
    cout << "Max: " << *maxIt << ", Min: " << *minIt << endl;
    
    return 0;
}
```

### Example 3: Transform Algorithm

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int square(int x) {
    return x * x;
}

int main() {
    vector<int> v = {1, 2, 3, 4, 5};
    vector<int> result(5);
    
    // Transform each element
    transform(v.begin(), v.end(), result.begin(), square);
    
    cout << "Original: ";
    for (int x : v) cout << x << " ";
    
    cout << "\nSquared: ";
    for (int x : result) cout << x << " ";
    
    return 0;
}
```

### Output:
```
Original: 1 2 3 4 5
Squared: 1 4 9 16 25
```

---

## Key Points

1. **end() Returns Past-the-End**: Never dereference `end()`.

2. **Iterator Invalidation**: Modifying container may invalidate iterators.

3. **Range-Based For**: Modern C++ hides iterator complexity.

4. **Algorithm Flexibility**: Work with any container via iterators.

5. **const_iterator**: Use when you don't need to modify elements.

6. **Binary Search**: Container must be sorted first.

---

## Interview Questions

### Q1: What is an iterator?
**Answer**: An iterator is an object that allows traversal through container elements. It provides a uniform interface for accessing elements regardless of the underlying container type, similar to a pointer.

### Q2: What is the difference between begin() and end()?
**Answer**:
- `begin()`: Returns iterator pointing to the **first** element
- `end()`: Returns iterator pointing **past the last** element (not to it)
- Range: `[begin(), end())` - begin is inclusive, end is exclusive

### Q3: Why should you prefer algorithms over manual loops?
**Answer**:
- **Cleaner code**: More readable and maintainable
- **Optimization**: Library implementations are highly optimized
- **Safety**: Less chance of off-by-one errors
- **Abstraction**: Works with any compatible container

### Q4: What happens if you dereference end()?
**Answer**: Undefined behavior. The `end()` iterator points past the last element and should only be used for comparison, never dereferenced.

### Q5: When is an iterator invalidated?
**Answer**: Iterators can become invalid when:
- Container is resized (vector reallocation)
- Element is erased
- Container is cleared
- For vector: insertion may reallocate

Always check container documentation for specific rules.
