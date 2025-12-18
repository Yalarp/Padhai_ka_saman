# Doubly Linked List Implementation

## Introduction

A **Doubly Linked List** allows traversal in both forward and backward directions. Each node has two pointers: `left` (previous) and `right` (next).

### Singly vs Doubly Linked List

```
Singly:
  [10|→] → [20|→] → [30|null]
  (Can only move forward)

Doubly:
  null ← [←|10|→] ↔ [←|20|→] ↔ [←|30|→] → null
  (Can move both directions)
```

---

## Dnode Structure

```java
package Linked_List_Examples;

public class Dnode {
    int data;
    Dnode left, right;  // Two pointers!
    
    Dnode(int data) {
        this.data = data;
        this.left = this.right = null;  // Both initially null
    }
}
```

**Key Difference:** Two self-referential pointers instead of one

---

## Complete Implementation (Doubly_Linked_List.java)

### 1. Insert Left

```java
void insert_left(int data) {
    Dnode n = new Dnode(data);
    if (root == null)
        root = n;
    else {
        n.right = root;     // New node's right points to root
        root.left = n;      // Root's left points to new node
        root = n;           // New node becomes root
    }
}
```

**Execution:**
```
Empty: root = null
insert_left(10): root → [null|10|null]

insert_left(20):
  Create [null|20|null]
  n.right = root → [null|20|→10]
  root.left = n  → [20←|10|null]
  root = n       → root points to [20]
  
Result: root → [null|20|↔|10|null]
```

---

### 2. Insert Right

```java
void insert_right(int data) {
    Dnode n = new Dnode(data);
    if (root == null)
        root = n;
    else {
        Dnode t = root;
        while (t.right != null)  // Find last node
            t = t.right;
        t.right = n;             // Link last to new
        n.left = t;              // Link new to last
    }
}
```

---

### 3. Delete Left

```java
void delete_left() {
    if (root == null)
        System.out.println("List is empty");
    else {
        Dnode t = root;
        root = root.right;       // Move root
        if (root != null)         // If list not empty now
            root.left = null;     // Break backward link
        System.out.println("Deleted:" + t.data);
    }
}
```

**Critical:** Must update `root.left` to null!

```
Before: root → [null|10|↔|20|↔|30|null]
After:  root → [null|20|↔|30|null]
        [10] garbage collected
```

---

### 4. Delete Right

```java
void delete_right() {
    if (root == null)
        System.out.println("List is empty");
    else {
        Dnode t = root;
        while (t.right != null)
            t = t.right;          // Find last node
        
        if (t == root)             // Single node
            root = null;
        else {
            Dnode t2 = t.left;     // Get previous node
            t2.right = null;       // Break forward link
        }
        System.out.println("Deleted:" + t.data);
    }
}
```

---

### 5. Print Forward

```java
void print_list() {
    if (root == null)
        System.out.println("List is empty");
    else {
        Dnode t = root;
        while (t != null) {
            System.out.print("<-| " + t.data + " |->");
            t = t.right;  // Move forward
        }
    }
}
```

---

### 6. Print Reverse (New Capability!)

```java
void print_list_rev() {
    if (root == null)
        System.out.println("List is empty");
    else {
        Dnode t = root;
        // Go to last node
        while (t.right != null)
            t = t.right;
        
        // Print backward
        while (t != null) {
            System.out.print("<-| " + t.data + " |->");
            t = t.left;  // Move backward!
        }
    }
}
```

**Output Example:**
```
Forward:  <-|10|-> <-|20|-> <-|30|->
Reverse:  <-|30|-> <-|20|-> <-|10|->
```

---

## Advantages

✅ **Bidirectional Traversal**: Move forward and backward
✅ **Easier Deletion**: Can access previous node directly
✅ **Reverse Printing**: No recursion needed

---

## Disadvantages

❌ **Extra Memory**: Two pointers per node vs one
❌ **Complex Logic**: Must update both links
❌ **More Error-Prone**: Forget to update one pointer → bugs

---

## Time Complexity

| Operation | Time |
|-----------|------|
| Insert Left | O(1) |
| Insert Right | O(n) |
| Delete Left | O(1) |
| Delete Right | O(n) |
| Print Forward | O(n) |
| Print Reverse | O(n) |

---

## Summary

**Key Concepts:**
- **Two Pointers**: `left` and `right`
- **Bidirectional**: Traverse both ways
- **Link Maintenance**: Update both pointers during insert/delete

**Use Cases:**
- Browser history (back/forward)
- Music playlists (previous/next)
- Undo/redo with bidirectional access

---

**Next:** [Circular Linked List](file:///c:/Users/2706p/Desktop/mcq/java/13_Circular_Linked_List_Implementation.md)
