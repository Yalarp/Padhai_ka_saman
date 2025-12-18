# Binary Search Tree Fundamentals

## Tree Terminology

**Tree**: Hierarchical data structure with nodes connected by edges

**Key Terms:**
- **Root**: Top node (no parent)
- **Leaf**: Node with no children
- **Parent**: Node with children below
- **Child**: Node connected below parent
- **Subtree**: Tree formed by node and its descendants
- **Height**: Longest path from root to leaf
- **Depth**: Distance from root to node
- **Level**: Depth + 1

---

## Binary Tree vs Binary Search Tree

**Binary Tree**: Each node has at most 2 children

**Binary Search Tree (BST)**:
- Left child < Parent
- Right child > Parent
- Both subtrees are also BSTs

```
Binary Tree (not BST):
        10
       /  \
     20    5

BST:
        10
       /  \
      5    20
     / \   / \
    3   7 15 25
```

---

## Dnode for Tree (Reusing from Doubly LL)

```java
package Tree_Examples;

public class Dnode {
    int data;
    Dnode left, right;  // Left and right children
    
    Dnode(int data) {
        this.data = data;
        this.left = this.right = null;
    }
}
```

**Note:** Same structure as doubly linked list node, but `left`/`right` represent children, not previous/next!

---

## Tree Implementation (Tree_Class.java)

```java
package Tree_Examples;

public class Tree_Class {
    static Dnode root = null;  // Root of tree
    
    static void insert_node(Dnode r, Dnode n) {
        if (root == null) {
            root = n;
            System.out.println("\n" + n.data + " inserted");
        }
        else {
            if (n.data < r.data) {           // Go left
                if (r.left == null) {
                    r.left = n;
                    System.out.println("\n" + n.data + " inserted");
                }
                else
                    insert_node(r.left, n);   // Recurse left
            }
            else {                            // Go right
                if (r.right == null) {
                    r.right = n;
                    System.out.println("\n" + n.data + " inserted");
                }
                else
                    insert_node(r.right, n);  // Recurse right
            }
        }
    }
    
    static void inorder(Dnode r) {
        if (r != null) {
            inorder(r.left);          // L: Left subtree
            System.out.print(r.data + ",");  // P: Process node
            inorder(r.right);         // R: Right subtree
        }
    }
    
    static Dnode get_root() {
        return Tree_Class.root;
    }
    
    public static void main(String[] args) {
        Tree_Class.insert_node(Tree_Class.get_root(), new Dnode(10));
        Tree_Class.insert_node(Tree_Class.get_root(), new Dnode(5));
        Tree_Class.insert_node(Tree_Class.get_root(), new Dnode(20));
        Tree_Class.insert_node(Tree_Class.get_root(), new Dnode(15));
        Tree_Class.inorder(Tree_Class.get_root());
    }
}
```

---

## Insert Operation Analysis

```java
static void insert_node(Dnode r, Dnode n) {
    if (root == null) {
        root = n;  // First node becomes root
    }
    else {
        if (n.data < r.data) {        // New node is smaller
            if (r.left == null)        // Left is empty
                r.left = n;
            else
                insert_node(r.left, n); // Recurse left
        }
        else {                         // New node is larger
            if (r.right == null)
                r.right = n;
            else
                insert_node(r.right, n);
        }
    }
}
```

**Recursion Pattern:**
1. Compare new node with current node
2. Go left if smaller, right if larger
3. If spot empty, insert; else recurse

---

## Execution Trace

```
Insert 10: root = [10]
           10

Insert 5 (5 < 10, go left):
           10
          /
         5

Insert 20 (20 > 10, go right):
           10
          /  \
         5    20

Insert 15 (15 > 10, go right; 15 < 20, go left):
           10
          /  \
         5    20
             /
            15

Inorder traversal (LPR): 5, 10, 15, 20 (sorted!)
```

---

## Inorder Traversal (LPR)

```java
static void inorder(Dnode r) {
    if (r != null) {
        inorder(r.left);           // L: Process left subtree
        System.out.print(r.data);  // P: Process current node
        inorder(r.right);          // R: Process right subtree
    }
}
```

**Result:** Prints nodes in ascending order!

---

## Tree Traversals

1. **Inorder (LPR)**: Left → Process → Right → **Sorted output**
2. **Preorder (PLR)**: Process → Left → Right
3. **Postorder (LPR)**: Left → Right → Process

---

## BST Properties

✅ **Sorted Output**: Inorder traversal gives sorted sequence
✅ **Fast Search**: O(log n) average case
✅ **Dynamic**: Easily insert/delete nodes
✅ **No Duplicates**: (typically)

❌ **Can Degenerate**: Worst case O(n) if unbalanced

---

## Time Complexity

| Operation | Average | Worst (skewed) |
|-----------|---------|----------------|
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Delete | O(log n) | O(n) |

---

## Summary

**Key Concepts:**
- **BST Property**: Left < Parent < Right
- **Recursive Insert**: Compare and recurse
- **Inorder Traversal**: Gives sorted output
- **Static root**: Shared across all instances

---

**Next:** [Binary Search Tree Operations](file:///c:/Users/2706p/Desktop/mcq/java/15_Binary_Search_Tree_Operations.md)
