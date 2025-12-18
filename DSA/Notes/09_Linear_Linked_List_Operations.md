# Linear Linked List Operations

## Complete Implementation Analysis (Linear_Linked_List.java)

###Class Structure
```java
public class Linear_Linked_List {
    Node root;  // Only data member - reference to first node
}
```

---

## 1. Insert Left (At Beginning)

```java
void insert_left(int data) {
    Node n = new Node(data);  // Create new node
    if (root == null)          // Empty list
        root = n;
    else {
        n.next = root;         // New node points to current root
        root = n;              // New node becomes root
    }
}
```

**Execution Flow:**
```
Empty List:
  root = null
  insert_left(10): root → [10|null]

Non-empty List:
  root → [20|null]
  insert_left(10):
    Step 1: Create n → [10|null]
    Step 2: n.next = root → [10]→[20|null]
    Step 3: root = n → root points to [10]
    Result: root → [10]→[20|null]
```

---

## 2. Insert Right (At End)

```java
void insert_right(int data) {
    Node n = new Node(data);
    if (root == null)
        root = n;
    else {
        Node t = root;           // Start from root
        while (t.next != null)   // Traverse to last node
            t = t.next;
        t.next = n;              // Link last node to new node
    }
}
```

**Time Complexity:** O(n) - Must traverse entire list

---

## 3. Delete Left

```java
void delete_left() {
    if (root == null)
        System.out.println("List is empty");
    else {
        Node t = root;         // Save reference to first node
        root = root.next;      // Move root to second node
        System.out.println("Deleted:" + t.data);  // t becomes garbage
    }
}
```

**Execution:**
```
Before: root → [10]→[20]→[30|null]
After:  root → [20]→[30|null]
        [10] is garbage collected
```

---

## 4. Delete Right

```java
void delete_right() {
    if (root == null)
        System.out.println("List is empty");
    else {
        Node t, t2;
        t = t2 = root;
        while (t.next != null) {  // Find last node
            t2 = t;               // t2 trails behind t
            t = t.next;
        }
        if (t == root)            // Single node
            root = null;
        else
            t2.next = null;       // Break link to last node
        System.out.println("Deleted:" + t.data);
    }
}
```

**Two-Pointer Technique:**
```
List: [10]→[20]→[30|null]
      t2,t

Loop iteration 1:
      [10]→[20]→[30|null]
       t2   t
      
Loop iteration 2:
      [10]→[20]→[30|null]
            t2   t (t.next==null, exit loop)
      
Delete: t2.next = null
Result: [10]→[20|null]
```

---

## 5. Search Element

```java
void search_list(int key) {
    if (root == null)
        System.out.println("List is empty");
    else {
        Node t = root;
        while (t != null) {
            if (t.data == key) {
                System.out.println("\n" + key + " Found");
                return;
            }
            t = t.next;
        }
        System.out.println("\n" + key + " Not Found");
    }
}
```

**Time Complexity:** O(n) - Linear search

---

## 6. Delete Specific Element

```java
void delete_element(int key) {
    if (root == null)
        System.out.println("List is empty");
    else {
        Node t, t2;
        t = t2 = root;
        while (t != null) {
            if (t.data == key)
                break;
            t2 = t;
            t = t.next;
        }
        if (t == null)
            System.out.println("\n" + key + " Not Found");
        else {
            if (t == root)           // Delete left case
                root = root.next;
            else if (t.next == null)  // Delete right case
                t2.next = null;
            else                      // Middle deletion
                t2.next = t.next;
            System.out.println("\n" + t.data + " deleted.");
        }
    }
}
```

**Three Cases:**
1. **Delete first node:** `root = root.next`
2. **Delete last node:** `t2.next = null`
3. **Delete middle:** `t2.next = t.next` (bypass t)

---

## 7. Insert After Key

```java
void insert_after(int key, int data) {
    if (root == null)
        System.out.println("List is empty");
    else {
        Node t = root;
        while (t != null) {
            if (t.data == key)
                break;
            t = t.next;
        }
        if (t == null)
            System.out.println("\n" + key + " Not Found");
        else {
            Node n = new Node(data);
            n.next = t.next;    // New node points to t's next
            t.next = n;         // t points to new node
        }
    }
}
```

**Execution:**
```
List: [10]→[20]→[30|null]
Insert 25 after 20:

Step 1: Find 20, t → [20]
Step 2: Create n → [25|null]
Step 3: n.next = t.next → [25]→[30|null]
Step 4: t.next = n → [20]→[25]→[30|null]
```

---

## 8. Sort List (Bubble Sort)

```java
void sort_list() {
    if (root == null)
        System.out.println("Empty list");
    else {
        for (Node i = root; i.next != null; i = i.next) {      // Passes
            for (Node t = root, t2 = t.next; t2 != null; t = t.next, t2 = t2.next) {
                if (t.data > t2.data) {                          // Swap data
                    int temp = t.data;
                    t.data = t2.data;
                    t2.data = temp;
                }
            }
        }
    }
    System.out.println("\nSorting done..");
}
```

**Time Complexity:** O(n²)
**Note:** Swaps data, not node references

---

## 9. Print List

```java
void print_list() {
    if (root == null)
        System.out.println("List is empty");
    else {
        Node t = root;
        while (t != null) {
            System.out.print("| " + t.data + " |→");
            t = t.next;
        }
    }
}
```

---

## Menu-Driven Program (Linked_List_Main.java)

```java
public class Linked_List_Main {
    public static void main(String[] args) {
        Linear_Linked_List list = new Linear_Linked_List();
        Scanner sc = new Scanner(System.in);
        
        do {
            // Display menu
            System.out.println("\n======== LINKED LIST MENU ========");
            System.out.println("1. Insert Left");
            System.out.println("2. Insert Right");
            System.out.println("3. Delete Left");
            System.out.println("4. Delete Right");
            System.out.println("5. Print List");
            System.out.println("6. Search Element");
            System.out.println("7. Delete Element");
            System.out.println("8. Insert After");
            System.out.println("9. Sort List");
            System.out.println("0. Exit");
            
            int ch = sc.nextInt();
            // Switch cases handle each operation
        } while (ch != 0);
    }
}
```

---

## Time Complexity Summary

| Operation | Time Complexity |
|-----------|-----------------|
| Insert Left | O(1) |
| Insert Right | O(n) |
| Delete Left | O(1) |
| Delete Right | O(n) |
| Search | O(n) |
| Delete Element | O(n) |
| Insert After | O(n) |
| Sort | O(n²) |
| Print | O(n) |

---

## Key Patterns

### Two-Pointer Technique
Used in: Delete Right, Delete Element
- `t` advances through list
- `t2` trails one position behind
- Allows modifying previous node's next pointer

### Sentinel Node Check
Always check `if (root == null)` before operations

### Loop Termination
- `while (t != null)` - Traverse entire list
- `while (t.next != null)` - Stop at last node

---

**Next:** [Dynamic Stack and Queue](file:///c:/Users/2706p/Desktop/mcq/java/10_Dynamic_Stack_and_Queue_with_LinkedList.md)
