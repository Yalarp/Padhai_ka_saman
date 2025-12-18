# Linked List Fundamentals and Node Structure

## Introduction to Linked Lists

A **Linked List** is a linear data structure where elements are stored in nodes. Each node contains data and a reference (link) to the next node in the sequence.

### Array vs Linked List

| Feature | Array | Linked List |
|---------|-------|-------------|
| **Memory** | Contiguous | Scattered |
| **Size** | Fixed | Dynamic |
| **Access** | Direct O(1) | Sequential O(n) |
| **Insertion** | O(n) | O(1) at known position |
| **Deletion** | O(n) | O(1) at known position |

---

## Node Structure

### Node.java Analysis

```java
package Linked_List_Examples;

public class Node {
    int data;              // Stores the element value
    Node next;             // Reference to next node (self-referential)
    
    Node(int data) {       // Constructor
        this.data = data;  // Set the data value
        this.next = null;  // Initially points to nothing
    }
}
```

### Line-by-Line Explanation

**Line 4:** `int data;`
- Stores the actual value of the node
- Can be any data type (int, String, custom objects)

**Line 5:** `Node next;`
- **Self-referential pointer**: Node type variable inside Node class
- Points to the next Node in the list
- `null` indicates end of list

**Line 7-10:** Constructor
- `this.data = data` - Initialize data field
- `this.next = null` - Set next to null (not connected yet)

### Visual Representation

```
Single Node:
┌──────────┬──────┐
│ data: 10 │ next │──→ null
└──────────┴──────┘

Three Connected Nodes:
┌───┬────┐    ┌───┬────┐    ┌───┬────┐
│10 │next│───→│20 │next│───→│30 │null│
└───┴────┘    └───┴────┘    └───┴────┘
```

---

## Linked List Characteristics

### 1. Dynamic Size
```java
// No predefined size needed
Linear_Linked_List list = new Linear_Linked_List();
list.insert_right(10);  // Can keep adding as long as memory available
```

### 2. Efficient Insertion/Deletion
```java
// Insert at beginning: O(1)
// Insert in array at beginning: O(n)
```

### 3. Sequential Access Only
```java
// To reach 3rd element, must traverse through 1st and 2nd
// No direct indexing like array[2]
```

### 4. Extra Memory for Pointers
```java
// Each node stores: data + next reference
// Arrays store only data
```

---

## Types of Linked Lists

### 1. Singly Linked List (Linear)
```
root → [10|→] → [20|→] → [30|null]
```

### 2. Doubly Linked List
```
null ← [←|10|→] ↔ [←|20|→] ↔ [←|30|→] → null
```

### 3. Circular Linked List
```
     ┌────────────────────┐
     ↓                    │
   [10|→] → [20|→] → [30|─┘
```

---

## Basic Operations Overview

### Insertion Operations
- **Insert Left** (at beginning): Add before root
- **Insert Right** (at end): Add after last node
- **Insert After Key**: Add after specific element

### Deletion Operations
- **Delete Left**: Remove first node
- **Delete Right**: Remove last node
- **Delete Element**: Remove specific element

### Search Operation
- Traverse from root to find element

### Print Operation
- Traverse and display all elements

---

## Memory Representation

### Array (Contiguous Memory)
```
Memory: [10][20][30][40][50]
Address: 100 104 108 112 116
         (sequential, predictable)
```

### Linked List (Scattered Memory)
```
Node at address 100: data=10, next=300
Node at address 300: data=20, next=150
Node at address 150: data=30, next=null
         (non-contiguous, unpredictable)
```

---

## Advantages

✅ **Dynamic Size**: Grow/shrink as needed
✅ **Efficient Insert/Delete**: O(1) at known position
✅ **No Wasted Memory**: Use only what's needed
✅ **Easy to Implement**: Stack and Queue

---

## Disadvantages

❌ **No Random Access**: Must traverse to reach element
❌ **Extra Memory**: Pointers consume space
❌ **Not Cache Friendly**: Scattered memory locations
❌ **Cannot Traverse Backwards**: (in singly linked list)

---

## Summary

**Key Concepts:**
1. **Node Class**: Self-referential structure with data and next
2. **Root**: First node reference
3. **Null**: Indicates end of list
4. **Dynamic**: Size changes at runtime

**Next:** [Linear Linked List Operations](file:///c:/Users/2706p/Desktop/mcq/java/09_Linear_Linked_List_Operations.md)
