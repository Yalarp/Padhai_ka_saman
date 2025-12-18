# Queue Fundamentals and Linear Queue Implementation

## Table of Contents
1. [Introduction to Queue](#introduction-to-queue)
2. [Queue Characteristics](#queue-characteristics)
3. [Queue Operations](#queue-operations)
4. [Array-Based Linear Queue Implementation](#array-based-linear-queue-implementation)
5. [Detailed Code Analysis](#detailed-code-analysis)
6. [Menu-Driven Queue Program](#menu-driven-queue-program)
7. [Limitations of Linear Queue](#limitations-of-linear-queue)
8. [Time and Space Complexity](#time-and-space-complexity)
9. [Applications](#applications)

---

## Introduction to Queue

A **Queue** is a linear data structure that follows the **FIFO (First In, First Out)** principle. The first element added to the queue will be the first one to be removed.

### Real-World Analogies

1. **Line at a ticket counter**
   - First person in line gets served first
   - New people join at the back

2. **Print queue**
   - First document sent gets printed first
   - New documents added to end

3. **Call center queue**
   - First caller in queue gets answered first

### Key Concept: FIFO Principle

```mermaid
graph LR
    A[NEW Elements<br/>Join Here] -->|Enqueue<br/>REAR| B[Queue]
    B -->|Dequeue<br/>FRONT| C[Elements<br/>Leave Here]
    
    style A fill:#4ecdc4
    style C fill:#ff6b6b
```

---

## Queue Characteristics

### 1. **Two Access Points**
- **FRONT**: Remove elements (Dequeue)
- **REAR**: Add elements (Enqueue)

### 2. **FIFO Discipline**
- First element added is first to be removed
- Maintains order of insertion

### 3. **Restricted Access**
- Cannot access middle elements directly
- Must dequeue to reach inner elements

### 4. **Size Constraints**
- Array-based: Fixed size
- Linked-list based: Dynamic size

---

## Queue Operations

### Primary Operations

| Operation | Description | Position | Time Complexity |
|-----------|-------------|----------|-----------------|
| **Enqueue** | Add element to queue | REAR (back) | O(1) |
| **Dequeue** | Remove element from queue | FRONT (front) | O(1) |
| **is_empty** | Check if queue is empty | - | O(1) |
| **is_full** | Check if queue is full | - | O(1) |
| **print_queue** | Display all elements | - | O(n) |

---

## Array-Based Linear Queue Implementation

### Conceptual Structure

```
Initial State (Empty):
Queue: [ ] [ ] [ ] [ ] [ ]
Index:  0   1   2   3   4
Front = 0 (points to first element position)
Rear = -1 (no elements yet)

After Enqueue(10), Enqueue(20), Enqueue(30):
Queue: [10][20][30][ ][ ]
Index:  0   1   2   3   4
        ↑       ↑
      Front   Rear
```

### Implementation Components

1. **queue[]** - Integer array for storage
2. **front** - Index pointer to first element
3. **rear** - Index pointer to last element  
4. **MaxSize** - Maximum capacity

---

## Detailed Code Analysis

### Queue Class Structure (Queue_Class.java)

```java
package Queue_Examples;
import java.util.Scanner;

public class Queue_Class {
    int queue[];           // Array to store elements
    int front, rear, MaxSize;  // Pointers and size
    
    // Methods explained below
}
```

#### **Data Members Explanation:**

- **`int queue[]`**: Array to hold queue elements
- **`int front`**: Index of the first element to be dequeued
- **`int rear`**: Index of the last enqueued element
- **`int MaxSize`**: Maximum capacity of the queue

---

### 1. Create Queue Method

```java
void create_Queue(int size) {
    rear = -1;              // No elements initially
    front = 0;              // Front always starts at 0
    MaxSize = size;         // Set capacity
    queue = new int[MaxSize];  // Allocate array
}
```

#### **Line-by-Line Explanation:**

**Line 1:** `rear = -1;`
- Initialize rear to -1 indicating empty queue
- **Why -1?** When we enqueue first element, we do `queue[++rear]`, which becomes `queue[0]`

**Line 2:** `front = 0;`
- Front is always initialized to 0
- Points to where we'll dequeue from
- **Does NOT move** during enqueue, only during dequeue

**Line 3:** `MaxSize = size;`
- Stores the maximum number of elements queue can hold

**Line 4:** `queue = new int[MaxSize];`
- Creates array in heap memory
- All elements initialized to 0

#### **Visual Representation:**

```
After create_Queue(5):

front = 0, rear = -1, MaxSize = 5

Queue: [ ][ ][ ][ ][ ]
Index:  0  1  2  3  4
        ↑
      front
      (rear is still -1, before first element)
```

---

### 2. Enqueue Operation

```java
void enqueue(int e) {
    queue[++rear] = e;
}
```

#### **Line-by-Line Explanation:**

- **`queue[++rear] = e;`**
  - **Pre-increment `++rear`**: Increment rear first, then use value
  - Adds element at new rear position

#### **Dequeued Execution Flow:**

```
Initial: front=0, rear=-1
Queue: [ ][ ][ ][ ][ ]

Enqueue(10):
  ++rear → rear becomes 0
  queue[0] = 10
  State: front=0, rear=0
  Queue: [10][ ][ ][ ][ ]
          ↑   ↑
        front rear

Enqueue(20):
  ++rear → rear becomes 1
  queue[1] = 20
  State: front=0, rear=1
  Queue: [10][20][ ][ ][ ]
          ↑      ↑
        front  rear

Enqueue(30):
  ++rear → rear becomes 2
  queue[2] = 30
  State: front=0, rear=2
  Queue: [10][20][30][ ][ ]
          ↑          ↑
        front      rear
```

---

### 3. Dequeue Operation

```java
int dequeue() {
    int temp = queue[front];  // Get element at front
    front++;                  // Move front forward
    return temp;              // Return removed element
}
```

#### **Line-by-Line Explanation:**

**Line 1:** `int temp = queue[front];`
- Read the element at front position
- Store in temporary variable
- **Doesn't actually delete** from array

**Line 2:** `front++;`
- Increment front pointer
- Makes next element the new front
- **Logical removal** - element still in array but inaccessible

**Line 3:** `return temp;`
- Return the dequeued element

#### **Execution Flow:**

```
Initial: front=0, rear=2
Queue: [10][20][30][ ][ ]
        ↑          ↑
      front      rear

Dequeue():
  temp = queue[0] = 10
  front = 0 + 1 = 1
  return 10
  
Result: front=1, rear=2
Queue: [10][20][30][ ][ ]
             ↑      ↑
           front  rear
           (10 still in array but ignored)

Dequeue():
  temp = queue[1] = 20
  front = 1 + 1 = 2
  return 20
  
Result: front=2, rear=2
Queue: [10][20][30][ ][ ]
                 ↑
              front
              rear (both pointing to same element)
```

---

### 4. is_full Check

```java
boolean is_full() {
    return (rear == MaxSize - 1);
}
```

#### **Line-by-Line Explanation:**

- **`return (rear == MaxSize - 1);`**
  - Queue is full when rear reaches last valid index
  - **MaxSize - 1** because array is 0-indexed

**Example:**
```
MaxSize = 5
Valid indices: 0, 1, 2, 3, 4
Full when rear = 4 = (5 - 1)

Queue: [10][20][30][40][50]
Index:  0   1   2   3   4
                        ↑
                      rear=4
is_full() = true (4 == 4)
```

---

### 5. is_empty Check

```java
boolean is_empty() {
    if (front > rear)
        return true;
    else
        return false;
}
```

#### **Line-by-Line Explanation:**

**Condition:** `front > rear`
- Queue is empty when front has moved past rear
- All elements have been dequeued

**Simplified version:**
```java
boolean is_empty() {
    return (front > rear);
}
```

#### **Truth Table:**

| front | rear | front > rear | Result | Meaning |
|-------|------|--------------|--------|---------|
| 0 | -1 | true | **Empty** | Initial state |
| 0 | 2 | false | Has elements | 3 elements |
| 3 | 2 | true | **Empty** | All dequeued |
| 2 | 2 | false | 1 element | Last element |

---

### 6. print_queue Operation

```java
void print_queue() {
    for (int i = front; i <= rear; i++)
        System.out.print(queue[i] + " - ");
}
```

#### **Line-by-Line Explanation:**

**Line 1:** `for (int i = front; i <= rear; i++)`
- Start from front (first valid element)
- Go until rear (last valid element)
- Increment to next element

**Line 2:** `System.out.print(queue[i] + " - ");`
- Print each element with separator

#### **Example:**

```
State: front=1, rear=3
Queue: [10][20][30][40][ ]
             ↑          ↑
           front      rear

Output: 20 - 30 - 40 - 
(Element at index 0 is ignored because front=1)
```

---

## Menu-Driven Queue Program

### Complete Queue_Main.java Analysis

```java
package Queue_Examples;

import java.util.Scanner;

public class Queue_Main {
    public static void main(String[] args) {
        Queue_Class obj = new Queue_Class();  // Create queue object
        int choice;
        Scanner in = new Scanner(System.in);
        
        // Get queue size from user
        System.out.println("Enter size of queue:");
        int size = in.nextInt();
        obj.create_Queue(size);  // Initialize queue
        
        do {
            // Display menu
            System.out.println("\nQueue Menu");
            System.out.println("==========");
            System.out.println("1.Enqueue");
            System.out.println("2.Dequeue");
            System.out.println("3.Print");
            System.out.println("0.Exit");
            System.out.println("--------");
            System.out.print(":");
            choice = in.nextInt();
            
            switch(choice) {
                case 1:  // Enqueue
                    if (!obj.is_full()) {  // Check if not full
                        System.out.println("Enter element to enter:");
                        int e = in.nextInt();
                        obj.enqueue(e);
                        System.out.println("Element Enqueued");
                    } else {
                        System.out.println("Queue Full");
                    }
                    break;
                    
                case 2:  // Dequeue
                    if (!obj.is_empty()) {  // Check if not empty
                        System.out.println("Element Dequeued:" + obj.dequeue());
                    } else {
                        System.out.println("Queue Empty");
                    }
                    break;
                    
                case 3:  // Print
                    if (!obj.is_empty()) {
                        System.out.println("Queue has:");
                        obj.print_queue();
                    } else {
                        System.out.println("Queue Empty");
                    }
                    break;
                    
                case 0:
                    System.out.println("Thanks for using the code: amar.career");
                    break;
                    
                default:
                    System.out.println("check the option selected.");
                    break;
            }
        } while (choice != 0);
    }
}
```

### Sample Execution

```
Enter size of queue:
5

Queue Menu
==========
1.Enqueue
2.Dequeue
3.Print
0.Exit
--------
:1
Enter element to enter:
10
Element Enqueued

Queue Menu
==========
:1
Enter element to enter:
20
Element Enqueued

Queue Menu
==========
:3
Queue has:
10 - 20 - 

Queue Menu
==========
:2
Element Dequeued:10

Queue Menu
==========
:3
Queue has:
20 - 

Queue Menu
==========
:0
Thanks for using the code: amar.career
```

---

## Limitations of Linear Queue

### Problem: Wasted Space

```
Initial: Create queue of size 5
Queue: [ ][ ][ ][ ][ ]
       front=0, rear=-1

After Enqueue(10), Enqueue(20), Enqueue(30):
Queue: [10][20][30][ ][ ]
        ↑          ↑
      front      rear=2

After Dequeue(), Dequeue():
Queue: [10][20][30][ ][ ]
                 ↑      
              front=2
              rear=2

After Dequeue():
Queue: [10][20][30][ ][ ]
                       
              front=3
              rear=2
is_empty() = true (front > rear)

Now Try Enqueue(40):
  rear++ → rear=3
  Queue: [10][20][30][40][ ]
                      ↑
                    rear=3

Enqueue(50):
  rear++ → rear=4
  Queue: [10][20][30][40][50]
                           ↑
                         rear=4
                         
is_full() = true ✓

Enqueue(60):
  ❌ QUEUE FULL!
  But indices 0, 1, 2 are EMPTY!
  Space is WASTED!
```

### Why This Happens

1. **front only moves forward**
2. **rear only moves forward**
3. **No wrap-around**
4. **Cannot reuse freed space at beginning**

### Visualization of Problem

```
Timeline of operations on queue of size 5:

Step 1: Enqueue 10, 20, 30, 40, 50
[10][20][30][40][50]  ← FULL
  ↑              ↑
front=0      rear=4

Step 2: Dequeue 3 times (remove 10, 20, 30)
[10][20][30][40][50]
              ↑   ↑
          front=3 rear=4
          
Step 3: Try to Enqueue 60
❌ ERROR: Queue Full (rear = MaxSize-1)
But [0][1][2] are FREE! 

Solution → Use Circular Queue!
```

---

## Time and Space Complexity

### Time Complexity

| Operation | Complexity | Reason |
|-----------|------------|--------|
| create_Queue | O(n) | Array allocation |
| enqueue | O(1) | Direct index access |
| dequeue | O(1) | Direct index access |
| is_full | O(1) | Single comparison |
| is_empty | O(1) | Single comparison |
| print_queue | O(n) | Traverse all elements |

### Space Complexity

- **Array Storage**: O(n) where n = MaxSize
- **Additional Variables**: O(1) for front, rear, MaxSize
- **Total**: O(n)

---

## Applications

### 1. **CPU Scheduling**
- Ready queue for processes
- First-come-first-served scheduling

### 2. **Printer Queue**
- Documents printed in order received
- Spooling system

### 3. **BFS in Graphs**
- Breadth-First Search uses queue
- Level-order tree traversal

### 4. **IO Buffers**
- Keyboard buffer
- Mouse event queue

### 5. **Customer Service**
- Call center queues
- Ticket systems

---

## Summary

### Key Concepts

1. **FIFO Principle**: First In, First Out

2. **Two Pointers**:
   - `front` - Points to first element (dequeue position)
   - `rear` - Points to last element (enqueue position)

3. **Important Checks**:
   - `is_full()` before enqueue → Check `rear == MaxSize-1`
   - `is_empty()` before dequeue → Check `front > rear`

4. **Critical Limitation**: 
   - Linear queue wastes space
   - Solution → **Circular Queue**

### Comparison: Stack vs Queue

| Feature | Stack | Queue |
|---------|-------|-------|
| Principle | LIFO | FIFO |
| Operations | Push/Pop | Enqueue/Dequeue |
| Access Point | One end (top) | Two ends (front & rear) |
| Use Case | Function calls | Task scheduling |

### Next Topics

- [Circular Queue](file:///c:/Users/2706p/Desktop/mcq/java/05_Circular_Queue_Implementation.md) - Solves space wastage problem
- [Priority Queue](file:///c:/Users/2706p/Desktop/mcq/java/06_Priority_Queue_Implementation.md) - Elements have priority
- [Dynamic Queue](file:///c:/Users/2706p/Desktop/mcq/java/10_Dynamic_Stack_and_Queue_with_LinkedList.md) - Using linked list

---

**Practice:** Implement queue operations and observe the limitation firsthand!
