# Priority Queue Implementation

## Table of Contents
1. [Introduction to Priority Queue](#introduction-to-priority-queue)
2. [Priority Queue Concept](#priority-queue-concept)
3. [Implementation Approach](#implementation-approach)
4. [Complete Code Analysis](#complete-code-analysis)
5. [Time Complexity Analysis](#time-complexity-analysis)
6. [Better Implementations](#better-implementations)
7. [Applications](#applications)

---

## Introduction to Priority Queue

A **Priority Queue** is a special type of queue where each element has a **priority value**. Elements with higher priority are dequeued before elements with lower priority, regardless of their insertion order.

### Regular Queue vs Priority Queue

```
Regular Queue (FIFO):
  Enqueue: 10, 20, 30, 40
  Dequeue order: 10, 20, 30, 40
  (First in, first out)

Priority Queue:
  Enqueue: 10, 20, 30, 40
  After sorting: 40, 30, 20, 10 (descending priority)
  Dequeue order: 40, 30, 20, 10
  (Highest priority first)
```

---

## Priority Queue Concept

### Real-World Examples

1. **Hospital Emergency Room**
   - Critical patients treated first (highest priority)
   - Minor injuries wait longer (lower priority)

2. **CPU Task Scheduling**
   - System tasks get higher priority
   - User tasks get lower priority

3. **Printer Queue**
   - Executive's documents print first
   - Regular documents wait

### Priority Rules

**Two Common Approaches:**

1. **Higher Number = Higher Priority**
   ```
   Elements: [10, 50, 30, 40, 20]
   After sorting (descending): [50, 40, 30, 20, 10]
   Dequeue: 50 first (highest value)
   ```

2. **Lower Number = Higher Priority**
   ```
   Elements: [10, 50, 30, 40, 20]
   After sorting (ascending): [10, 20, 30, 40, 50]
   Dequeue: 10 first (lowest value)
   ```

**Our implementation uses approach 1** (higher number = higher priority)

---

## Implementation Approach

### Strategy: Sort on Enqueue

```
Idea: Keep queue always sorted
  → Enqueue: Insert and sort immediately
  → Dequeue: Simply remove from front (already highest priority)
```

### Visual Example

```
Initial empty queue:
  [ ][ ][ ][ ][ ]

Enqueue(30):
  [30][ ][ ][ ][ ]

Enqueue(10):
  Insert at rear, then sort
  Before sort: [30][10][ ][ ][ ]
  After sort:  [30][10][ ][ ][ ]  (30 > 10, already sorted)

Enqueue(50):
  Insert at rear, then sort
  Before sort: [30][10][50][ ][ ]
  After sort:  [50][30][10][ ][ ]  (sorted descending)

Enqueue(20):
  Before sort: [50][30][10][20][ ]
  After sort:  [50][30][20][10][ ]

Dequeue:
  Remove from front: 50 (highest priority)
  Result: [30][20][10][ ][ ]
```

---

## Complete Code Analysis

### Priority_Queue.java

```java
package Queue_Examples;

public class Priority_Queue {
    int queue[];
    int front, rear, MaxSize;
    
    void create_Queue(int size) {
        rear = -1;
        front = 0;
        MaxSize = size;
        queue = new int[MaxSize];
    }
    
    // Enqueue: Insert element and sort for priority
    void enqueue(int e) {
        queue[++rear] = e;  // Insert at rear
        
        // Bubble sort to maintain descending order
        for (int i = front; i < rear; i++) {       // Outer loop: passes
            for (int j = front; j < rear; j++) {   // Inner loop: comparisons
                if (queue[j] < queue[j + 1]) {     // Swap if not in order
                    int temp = queue[j];
                    queue[j] = queue[j + 1];
                    queue[j + 1] = temp;
                }
            }
        }
    }
    
    boolean is_full() {
        return (rear == MaxSize - 1);
    }
    
    int dequeue() {
        int temp = queue[front];
        front++;
        return temp;
    }
    
    boolean is_empty() {
        if (front > rear)
            return true;
        else
            return false;
    }
    
    void print_queue() {
        for (int i = front; i <= rear; i++)
            System.out.print(queue[i] + " - ");
    }
}
```

---

### Detailed Code Breakdown

#### 1. Enqueue Method Analysis

```java
void enqueue(int e) {
    queue[++rear] = e;  // Line 1
    
    // Sorting code: Bubble sort
    for (int i = front; i < rear; i++)              // Line 2
    {
        for (int j = front; j < rear; j++)          // Line 3
        {
            if (queue[j] < queue[j + 1])            // Line 4
            {
                int temp = queue[j];                 // Line 5
                queue[j] = queue[j + 1];            // Line 6
                queue[j + 1] = temp;                // Line 7
            }
        }
    }
}
```

**Line 1:** `queue[++rear] = e;`
- Pre-increment rear
- Insert element at new rear position
- **Same as regular queue**

**Lines 2-3:** Bubble Sort Loops
```java
for (int i = front; i < rear; i++)        // Outer loop
    for (int j = front; j < rear; j++)    // Inner loop
```
- **Outer loop (i)**: Number of passes (n-1 passes for n elements)
- **Inner loop (j)**: Comparisons in each pass
- **Range**: `front` to `rear-1`

**Why `i < rear` and `j < rear`?**
```
If queue has 3 elements (front=0, rear=2):
  Valid indices: 0, 1, 2
  i should go: 0, 1 (2 passes for 3 elements)
  j should go: 0, 1 (compare j and j+1)
    j=0: compare queue[0] and queue[1]
    j=1: compare queue[1] and queue[2]
  If j=2: would compare queue[2] and queue[3] (out of bounds!)
```

**Line 4:** `if (queue[j] < queue[j + 1])`
- Compare adjacent elements
- If current element is **smaller** than next element
- We want **descending** order (larger elements first)

**Lines 5-7:** Swap Logic
```java
int temp = queue[j];      // Store current element
queue[j] = queue[j + 1];  // Move next element to current position
queue[j + 1] = temp;      // Move stored element to next position
```
- Standard swap using temporary variable
- Swaps `queue[j]` with `queue[j+1]`

---

### Step-by-Step Execution Trace

```
Initial state: front=0, rear=-1
Queue: [ ][ ][ ][ ][ ]

Operation 1: Enqueue(30)
  ++rear → rear=0
  queue[0] = 30
  Sorting: (Only 1 element, no swaps needed)
    i=0 to -1: No iterations (i < rear = 0 < 0 is false)
  Result: [30][ ][ ][ ][ ]

Operation 2: Enqueue(10)
  ++rear → rear=1
  queue[1] = 10
  Before sort: [30][10][ ][ ][ ]
  
  Sorting:
    Pass i=0 (i < 1):
      j=0 (j < 1):
        Compare queue[0]=30 with queue[1]=10
        30 < 10? No, skip swap
    
  After sort: [30][10][ ][ ][ ]  (already in descending order)

Operation 3: Enqueue(50)
  ++rear → rear=2
  queue[2] = 50
  Before sort: [30][10][50][ ][ ]
  
  Sorting:
    Pass i=0:
      j=0: Compare 30 < 10? No, skip
      j=1: Compare 10 < 50? Yes, SWAP
        Before: [30][10][50]
        After:  [30][50][10]
    
    Pass i=1:
      j=0: Compare 30 < 50? Yes, SWAP
        Before: [30][50][10]
        After:  [50][30][10]
      j=1: Compare 30 < 10? No, skip
    
  After sort: [50][30][10][ ][ ]  ✓

Operation 4: Enqueue(20)
  ++rear → rear=3
  queue[3] = 20
  Before sort: [50][30][10][20][ ]
  
  Sorting (3 passes):
    Pass i=0:
      j=0: 50 < 30? No
      j=1: 30 < 10? No
      j=2: 10 < 20? Yes, SWAP → [50][30][20][10]
    
    Pass i=1:
      j=0: 50 < 30? No
      j=1: 30 < 20? No
      j=2: 20 < 10? No
    
    Pass i=2:
      j=0: 50 < 30? No
      j=1: 30 < 20? No
      j=2: 20 < 10? No
    
  After sort: [50][30][20][10][ ]  ✓

Operation 5: Dequeue()
  temp = queue[0] = 50
  front = 1
  return 50
  
  Result: front=1, rear=3
  Effective queue: [30][20][10]
```

---

### Complete Bubble Sort Visualization

```
Example: Sort [30, 10, 50, 20] in descending order

Initial: [30][10][50][20]

Pass 1 (i=0):
  j=0: 30 < 10? No  → [30][10][50][20]
  j=1: 10 < 50? Yes → [30][50][10][20]  (swapped)
  j=2: 10 < 20? Yes → [30][50][20][10]  (swapped)
  After Pass 1: [30][50][20][10]

Pass 2 (i=1):
  j=0: 30 < 50? Yes → [50][30][20][10]  (swapped)
  j=1: 30 < 20? No  → [50][30][20][10]
  j=2: 20 < 10? No  → [50][30][20][10]
  After Pass 2: [50][30][20][10]

Pass 3 (i=2):
  j=0: 50 < 30? No  → [50][30][20][10]
  j=1: 30 < 20? No  → [50][30][20][10]
  j=2: 20 < 10? No  → [50][30][20][10]
  After Pass 3: [50][30][20][10]

Final sorted: [50][30][20][10]  ✓
```

---

## Time Complexity Analysis

### Operations Complexity

| Operation | Time Complexity | Explanation |
|-----------|-----------------|-------------|
| **create_Queue** | O(n) | Array allocation |
| **enqueue** | **O(n²)** | Bubble sort after each insert |
| **dequeue** | O(1) | Simple front increment |
| **is_full** | O(1) | Single comparison |
| **is_empty** | O(1) | Single comparison |
| **print_queue** | O(n) | Traverse all elements |

### Why Enqueue is O(n²)?

```
Bubble sort has:
  Outer loop: n-1 iterations
  Inner loop: n-1 iterations per outer iteration
  Total comparisons: (n-1) × (n-1) ≈ n²

For each enqueue:
  - Insert: O(1)
  - Sort: O(n²)
  - Total: O(n²)
```

### Inefficiency Example

```
Queue size: 1000 elements

Linear/Circular Queue:
  1000 enqueues × O(1) = 1000 operations
  
Priority Queue (this implementation):
  1000 enqueues × O(n²) where n grows from 1 to 1000
  ≈ 1000³ / 6 operations
  = 166,666,666 operations! ❌
```

---

## Better Implementations

### 1. Heap-Based Priority Queue

```
Binary Heap:
  - Complete binary tree
  - Parent >= Children (max heap) or Parent <= Children (min heap)
  - Operations:
    * Insert: O(log n)
    * Remove Max: O(log n)
    * Peek: O(1)
  
Much better than O(n²) sorting!
```

**Java's Built-in PriorityQueue uses heaps internally.**

### 2. Sorted Linked List

```
Maintain sorted linked list:
  - Insert in sorted position: O(n)
  - Remove from head: O(1)
  
Better than O(n²), worse than heap
```

### 3. Array with Insertion Sort

```
Use insertion sort instead of bubble sort:
  - Better for nearly-sorted data
  - Best case: O(n)
  - Average case: O(n²)
  - Still not optimal
```

### Comparison Table

| Implementation | Insert | Remove | Space |
|----------------|--------|--------|-------|
| **Unsorted Array** | O(1) | O(n) | O(n) |
| **Sorted Array (Bubble)** | O(n²) | O(1) | O(n) |
| **Sorted Array (Binary Insert)** | O(n) | O(1) | O(n) |
| **Binary Heap** | **O(log n)** | **O(log n)** | O(n) |
| **Fibonacci Heap** | O(1) | O(log n) | O(n) |

**Binary Heap is the best practical choice!**

---

## Applications

### 1. **CPU Scheduling**
```java
Class Task {
    int priority;
    String name;
}

Priority Queue of tasks:
  Higher priority tasks execute first
```

### 2. **Dijkstra's Shortest Path Algorithm**
```
Use min-heap priority queue:
  - Store vertices with distance as priority
  - Process vertex with smallest distance first
```

### 3. **Huffman Coding**
```
Build Huffman tree:
  - Use min-heap to combine two smallest-frequency nodes
  - Used in data compression
```

### 4. **Event-Driven Simulation**
```
Events sorted by timestamp:
  - Process events in chronological order
  - Example: Discrete event simulation
```

### 5. **Load Balancing**
```
Distribute tasks:
  - Assign task to least-loaded server
  - Use min priority queue of server loads
```

---

## Java's Built-in PriorityQueue

### Using Java Collections

```java
import java.util.PriorityQueue;

public class PriorityQueueExample {
    public static void main(String[] args) {
        // Min heap by default (smaller values have higher priority)
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        
        pq.add(30);
        pq.add(10);
        pq.add(50);
        pq.add(20);
        
        System.out.println("Dequeue order:");
        while (!pq.isEmpty()) {
            System.out.println(pq.poll());  // 10, 20, 30, 50
        }
    }
}
```

### For Max Heap (Higher values first)

```java
import java.util.PriorityQueue;
import java.util.Collections;

PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());

pq.add(30);
pq.add(10);
pq.add(50);
pq.add(20);

while (!pq.isEmpty()) {
    System.out.println(pq.poll());  // 50, 30, 20, 10
}
```

---

## Summary

### Key Concepts

1. **Priority Queue Definition**: Elements dequeued based on priority, not insertion order

2. **This Implementation**: Sorts on every enqueue
   - Simple to understand
   - Inefficient: O(n²) per enqueue
   - Good for learning, not for production

3. **Bubble Sort in Enqueue**:
   - Maintains descending order
   - Highest value = highest priority
   - Dequeues from front (already sorted)

4. **Better Alternatives**:
   - Binary Heap: O(log n) operations
   - Java's PriorityQueue uses heap
   - Always prefer heap for real applications

### When to Use

**This implementation:**
- Small queues (< 100 elements)
- Educational purposes
- Understanding priority concepts

**Heap-based:**
- Large queues
- Performance-critical applications
- Production code

### Next Topics

- [Advanced Queue Applications](file:///c:/Users/2706p/Desktop/mcq/java/07_Advanced_Queue_Applications.md)
- [Binary Heap Implementation](#)
- [Java Collections Framework](#)

---

**Remember:** This is a simple but inefficient implementation. Learn the concept, but use heap-based implementations in practice!
