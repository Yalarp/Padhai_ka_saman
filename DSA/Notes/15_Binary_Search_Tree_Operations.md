# Binary Search Tree Operations

## Tree Traversals (Complete Reference)

### 1. Inorder Traversal (LPR) - Left, Process, Right

```java
void inorder(Dnode r) {
    if (r != null) {
        inorder(r.left);           // L
        System.out.print(r.data);  // P
        inorder(r.right);          // R
    }
}
```
**Result:** Sorted order (ascending)

### 2. Preorder Traversal (PLR) - Process, Left, Right

```java
void preorder(Dnode r) {
    if (r != null) {
        System.out.print(r.data);  // P
        preorder(r.left);          // L
        preorder(r.right);         // R
    }
}
```
**Use:** Copy tree, create prefix expression

### 3. Postorder Traversal (LRP) - Left, Right, Process

```java
void postorder(Dnode r) {
    if (r != null) {
        postorder(r.left);         // L
        postorder(r.right);        // R
        System.out.print(r.data);  // P
    }
}
```
**Use:** Delete tree, create postfix expression

---

## Example Tree

```
        10
       /  \
      5    20
     / \   /
    3   7 15

Inorder:   3, 5, 7, 10, 15, 20
Preorder:  10, 5, 3, 7, 20, 15
Postorder: 3, 7, 5, 15, 20, 10
```

---

## Search Operation

```java
boolean search(Dnode r, int key) {
    if (r == null)
        return false;  // Not found
    
    if (r.data == key)
        return true;   // Found!
    
    if (key < r.data)
        return search(r.left, key);   // Search left
    else
        return search(r.right, key);  // Search right
}
```

**Time:** O(log n) average, O(n) worst

---

## Find Min/Max

```java
int findMin(Dnode r) {
    if (r.left == null)
        return r.data;  // Leftmost node is minimum
    return findMin(r.left);
}

int findMax(Dnode r) {
    if (r.right == null)
        return r.data;  // Rightmost node is maximum
    return findMax(r.right);
}
```

---

## Height Calculation

```java
int height(Dnode r) {
    if (r == null)
        return 0;
    
    int leftHeight = height(r.left);
    int rightHeight = height(r.right);
    
    return 1 + Math.max(leftHeight, rightHeight);
}
```

---

## Count Nodes

```java
int countNodes(Dnode r) {
    if (r == null)
        return 0;
    
    return 1 + countNodes(r.left) + countNodes(r.right);
}
```

---

## Applications

1. **Databases**: Index structures
2. **File Systems**: Directory structures
3. **Expression Trees**: Compilers
4. **Priority Queues**: Heap (special tree)

---

## Summary

**Traversals:** Inorder (sorted), Preorder, Postorder
**Operations:** Search, Min/Max, Height, Count
**Complexity:** O(log n) average for balanced tree

---

**Next:** [Graph Fundamentals](file:///c:/Users/2706p/Desktop/mcq/java/16_Graph_Fundamentals_and_Representation.md)
