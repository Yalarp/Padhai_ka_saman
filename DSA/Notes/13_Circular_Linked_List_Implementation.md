# Circular Linked List Implementation

## Introduction

A **Circular Linked List** has the last node's `next` pointing back to the first node, forming a circle. No node has `next = null`.

### Visual Representation

```
Linear:
  root → [10|→] → [20|→] → [30|null]

Circular:
     ┌─────────────────┐
     ↓                 │
  root → [10|→] → [20|→] → [30|┘
         ↑___________________|
```

---

## Implementation (Circular_Linked_List.java)

### Data Members

```java
public class Circular_Linked_List {
    Node root, last;  // TWO pointers: root and last
}
```

**Why `last`?** To maintain circular link efficiently

---

### 1. Insert Left

```java
void insert_left(int data) {
    Node n = new Node(data);
    if (root == null) {
        root = last = n;   // Single node
        last.next = root;  // Point to itself!
    }
    else {
        n.next = root;     // New node points to root
        root = n;          // New node becomes root
        last.next = root;  // Last still points to root
    }
}
```

**Key:** `last.next` always points to `root` (circular link)

---

### 2. Insert Right

```java
void insert_right(int data) {
    Node n = new Node(data);
    if (root == null) {
        root = last = n;
        last.next = root;
    }
    else {
        last.next = n;     // Last points to new
        last = n;          // Update last
        last.next = root;  // Maintain circle
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
        Node t = root;
        if (root == last)        // Single node
            root = last = null;
        else {
            root = root.next;     // Move root
            last.next = root;     // Maintain circle
        }
        System.out.println("Deleted:" + t.data);
    }
}
```

---

### 4. Delete Right

```java
void delete_right() {
    if (root == null)
        System.out.println("List is empty");
    else {
        Node t, t2;
        t = t2 = root;
        if (root == last)
            root = last = null;
        else {
            while (t != last) {   // Find second-last
                t2 = t;
                t = t.next;
            }
            last = t2;            // Update last
            last.next = root;     // Maintain circle
        }
        System.out.println("Deleted:" + t.data);
    }
}
```

---

### 5. Print List

```java
void print_list() {
    if (root == null)
        System.out.println("List is empty");
    else {
        Node t = root;
        do {
            System.out.print("| " + t.data + " |->");
            t = t.next;
        } while (t != root);  // Stop when back to root
    }
}
```

**Critical:** Use `do-while` instead of `while` to ensure at least one iteration

---

## Applications

1. **Round-Robin Scheduling**: CPU task scheduling
2. **Circular Buffer**: Audio/video streaming
3. **Game Turn System**: Multiplayer games
4. **Navigate Playlist**: Loop through songs

---

## Summary

**Key Concepts:**
- **last.next = root**: Maintains circular link
- **No null pointers**: Every node points somewhere
- **do-while for print**: Start from root, stop when back to root
- **root and last pointers**: Efficient operations at both ends

---

**Next:** [Binary Search Tree Fundamentals](file:///c:/Users/2706p/Desktop/mcq/java/14_Binary_Search_Tree_Fundamentals.md)
