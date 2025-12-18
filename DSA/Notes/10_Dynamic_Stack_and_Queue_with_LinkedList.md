# Dynamic Stack and Queue with Linked List

## Advantages of Dynamic Implementation

**Static (Array-Based):**
- Fixed size at creation
- Stack overflow/Queue full errors
- Wasted memory if underutilized

**Dynamic (Linked List-Based):**
- Grows as needed
- No overflow (until system memory exhausted)
- Uses exact memory needed

---

## Dynamic Stack Implementation

### Complete Code Analysis (Dynamic_Stack.java)

```java
package Linked_List_Examples;

public class Dynamic_Stack {
    Node tos;  // Top of Stack reference
    
    void push(int data) {
        Node n = new Node(data);
        if (tos == null)
            tos = n;
        else {
            n.next = tos;    // New node points to current top
            tos = n;         // New node becomes top
        }
    }
    
    void pop() {
        if (tos == null)
            System.out.println("Stack is empty");
        else {
            Node t = tos;
            tos = tos.next;  // Move top to next node
            System.out.println("popped:" + t.data);
        }
    }
    
    void print_stack() {
        if (tos == null)
            System.out.println("Stack is empty");
        else {
            Node t = tos;
            while (t != null) {
                System.out.println(t.data);
                System.out.println("======");
                t = t.next;
            }
        }
    }
    
    int peek() {
        if (tos == null) {
            System.out.println("Stack is empty");
            return -1;
        }
        else {
            return tos.data;
        }
    }
}
```

### Push Operation Analysis

```java
void push(int data) {
    Node n = new Node(data);  // Create new node
    if (tos == null)          // Empty stack
        tos = n;
    else {
        n.next = tos;         // Link new node to current top
        tos = n;              // Update top reference
    }
}
```

**Execution:**
```
Empty Stack:
  tos = null
  push(10): tos → [10|null]

Push 20:
  Create [20|null]
  n.next = tos → [20]→[10|null]
  tos = n → tos points to [20]
  Result: tos → [20]→[10|null]
```

**Time Complexity:** O(1) - No traversal needed!

### Pop Operation

```java
void pop() {
    if (tos == null)
        System.out.println("Stack is empty");
    else {
        Node t = tos;           // Save current top
        tos = tos.next;         // Move top to next
        System.out.println("popped:" + t.data);
    }
}
```

**Execution:**
```
Before: tos → [30]→[20]→[10|null]
After:  tos → [20]→[10|null]
        [30] becomes garbage
```

**Time Complexity:** O(1)

---

## Dynamic Queue Implementation

### Complete Code Analysis (Dynamic_Queue.java)

```java
package Linked_List_Examples;

public class Dynamic_Queue {
    Node front, rear;  // Two pointers
    
    void enqueue(int data) {
        Node n = new Node(data);
        if (front == null) {      // Empty queue
            front = rear = n;      // Both point to first node
        }
        else {
            rear.next = n;         // Link current rear to new node
            rear = n;              // Update rear
        }
    }
    
    void dequeue() {
        if (front == null)
            System.out.println("Queue is empty");
        else {
            Node t = front;
            front = front.next;    // Move front forward
            if (front == null)     // If queue becomes empty
                rear = null;       // Update rear too
            System.out.println("Dequeued:" + t.data);
        }
    }
    
    void print_queue() {
        if (front == null)
            System.out.println("Queue is empty");
        else {
            Node t = front;
            while (t != null) {
                System.out.print(t.data + " - ");
                t = t.next;
            }
        }
    }
}
```

### Enqueue Operation

```java
void enqueue(int data) {
    Node n = new Node(data);
    if (front == null) {
        front = rear = n;    // First element
    }
    else {
        rear.next = n;       // Add at end
        rear = n;            // Update rear
    }
}
```

**Execution:**
```
Empty Queue:
  front = rear = null
  enqueue(10): front = rear → [10|null]

Enqueue(20):
  Create [20|null]
  rear.next = n → [10]→[20|null]
  rear = n → rear points to [20]
  Result: front → [10]→[20|null] ← rear
```

**Time Complexity:** O(1) - Direct access to rear!

### Dequeue Operation

```java
void dequeue() {
    if (front == null)
        System.out.println("Queue is empty");
    else {
        Node t = front;
        front = front.next;
        if (front == null)     // Queue became empty
            rear = null;       // Reset rear
        System.out.println("Dequeued:" + t.data);
    }
}
```

**Critical:** When last element is dequeued, both `front` and `rear` must be set to null!

---

## Comparison

### Dynamic Stack

| Operation | Time | vs Array Stack |
|-----------|------|----------------|
| Push | O(1) | Same |
| Pop | O(1) | Same |
| Peek | O(1) | Same |
| **Overflow** | **Never** | **Possible** |
| **Memory** | **Exact** | **May waste** |

### Dynamic Queue

| Operation | Time | vs Array Queue |
|-----------|------|----------------|
| Enqueue | O(1) | Same |
| Dequeue | O(1) | Same |
| **Circular Logic** | **Not Needed!** | **Required** |
| **Overflow** | **Never** | **Possible** |

---

## Complete Menu-Driven Stack Example

```java
public static void main(String[] args) {
    Dynamic_Stack obj = new Dynamic_Stack();
    Scanner in = new Scanner(System.in);
    int choice;
    
    do {
        System.out.println("\nStack Menu");
        System.out.println("1.Push");
        System.out.println("2.Pop");
        System.out.println("3.Peek");
        System.out.println("4.Print");
        System.out.println("0.Exit");
        choice = in.nextInt();
        
        switch(choice) {
            case 1:
                System.out.println("Enter element:");
                int e = in.nextInt();
                obj.push(e);
                break;
            case 2:
                obj.pop();
                break;
            case 3:
                int res = obj.peek();
                if (res != -1)
                    System.out.println("At peek:" + res);
                break;
            case 4:
                obj.print_stack();
                break;
        }
    } while(choice != 0);
}
```

---

## Summary

**Dynamic Stack:**
- Use linked list with `tos` pointer
- Push/pop at head: O(1)
- No size limit (until memory exhausted)

**Dynamic Queue:**
- Use linked list with `front` and `rear` pointers
- Enqueue at rear, dequeue at front: both O(1)
- No circular logic needed!

**When to Use:**
- Unknown maximum size
- Need to avoid overflow errors
- Memory efficiency important

---

**Next:** [Employee Management System](file:///c:/Users/2706p/Desktop/mcq/java/11_Employee_Management_System_Case_Study.md)
