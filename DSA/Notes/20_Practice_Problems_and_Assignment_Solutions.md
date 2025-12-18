# Practice Problems and Assignment Solutions

## Overview

Collection of practice problems, assignments, and interview questions covering all data structures and algorithms.

---

## Stack Problems

### Problem 1: Balanced Parentheses
```java
// Check if string has balanced brackets
boolean isBalanced(String expr) {
    Stack<Character> stack = new Stack<>();
    for (char ch : expr.toCharArray()) {
        if (ch == '(' || ch == '[' || ch == '{') {
            stack.push(ch);
        }
        else if (ch == ')' || ch == ']' || ch == '}') {
            if (stack.isEmpty()) return false;
            char top = stack.pop();
            if ((ch == ')' && top != '(') ||
                (ch == ']' && top != '[') ||
                (ch == '}' && top != '{'))
                return false;
        }
    }
    return stack.isEmpty();
}
```

### Problem 2: Next Greater Element
```java
// For each element, find next greater element
void nextGreater(int[] arr) {
    Stack<Integer> stack = new Stack<>();
    int[] result = new int[arr.length];
    
    for (int i = arr.length - 1; i >= 0; i--) {
        while (!stack.isEmpty() && stack.peek() <= arr[i]) {
            stack.pop();
        }
        result[i] = stack.isEmpty() ? -1 : stack.peek();
        stack.push(arr[i]);
    }
}
```

---

## Queue Problems

### Problem 3: Generate Binary Numbers
```java
// Generate first n binary numbers
void generateBinary(int n) {
    Queue<String> q = new LinkedList<>();
    q.add("1");
    
    for (int i = 0; i < n; i++) {
        String curr = q.poll();
        System.out.println(curr);
        q.add(curr + "0");
        q.add(curr + "1");
    }
}
```

### Problem 4: Implement Stack using Two Queues
```java
class StackUsingQueues {
    Queue<Integer> q1 = new LinkedList<>();
    Queue<Integer> q2 = new LinkedList<>();
    
    void push(int x) {
        q2.add(x);
        while (!q1.isEmpty()) {
            q2.add(q1.poll());
        }
        Queue<Integer> temp = q1;
        q1 = q2;
        q2 = temp;
    }
    
    int pop() {
        return q1.poll();
    }
}
```

---

## Linked List Problems

### Problem 5: Reverse Linked List
```java
Node reverse(Node root) {
    Node prev = null, curr = root, next;
    
    while (curr != null) {
        next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
    }
    
    return prev;  // New root
}
```

### Problem 6: Detect Cycle in Linked List
```java
boolean hasCycle(Node root) {
    if (root == null) return false;
    
    Node slow = root, fast = root;
    
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        
        if (slow == fast)
            return true;  // Cycle detected
    }
    
    return false;
}
```

### Problem 7: Find Middle Element
```java
int findMiddle(Node root) {
    Node slow = root, fast = root;
    
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    
    return slow.data;  // Middle element
}
```

---

## Tree Problems

### Problem 8: Level-Order Traversal (BFS)
```java
void levelOrder(Dnode root) {
    Queue<Dnode> q = new LinkedList<>();
    q.add(root);
    
    while (!q.isEmpty()) {
        Dnode curr = q.poll();
        System.out.print(curr.data + " ");
        
        if (curr.left != null)
            q.add(curr.left);
        if (curr.right != null)
            q.add(curr.right);
    }
}
```

### Problem 9: Check if BST is Valid
```java
boolean isValidBST(Dnode root, int min, int max) {
    if (root == null)
        return true;
    
    if (root.data <= min || root.data >= max)
        return false;
    
    return isValidBST(root.left, min, root.data) &&
           isValidBST(root.right, root.data, max);
}
```

---

## Graph Problems

### Problem 10: Find All Paths
```java
void findAllPaths(int source, int dest, boolean[] visited, String path) {
    visited[source] = true;
    path += source;
    
    if (source == dest) {
        System.out.println(path);
    }
    else {
        for (int i = 0; i < v; i++) {
            if (g[source][i] == 1 && !visited[i]) {
                findAllPaths(i, dest, visited, path + "->");
            }
        }
    }
    
    visited[source] = false;  // Backtrack
}
```

---

## Array/Sorting Problems

### Problem 11: Find Kth Largest Element
```java
int findKthLargest(int[] arr, int k) {
    PriorityQueue<Integer> pq = new PriorityQueue<>();
    
    for (int num : arr) {
        pq.add(num);
        if (pq.size() > k) {
            pq.poll();
        }
    }
    
    return pq.peek();
}
```

### Problem 12: Merge Two Sorted Arrays
```java
int[] merge(int[] arr1, int[] arr2) {
    int[] result = new int[arr1.length + arr2.length];
    int i = 0, j = 0, k = 0;
    
    while (i < arr1.length && j < arr2.length) {
        if (arr1[i] < arr2[j])
            result[k++] = arr1[i++];
        else
            result[k++] = arr2[j++];
    }
    
    while (i < arr1.length)
        result[k++] = arr1[i++];
    while (j < arr2.length)
        result[k++] = arr2[j++];
    
    return result;
}
```

---

## Interview Questions

### Conceptual Questions

1. **Explain LIFO and FIFO principles**
   - LIFO (Last In First Out): Stack - Last element added is first removed
   - FIFO (First In First Out): Queue - First element added is first removed

2. **When to use Stack vs Queue?**
   - Stack: Function calls, expression evaluation, backtracking
   - Queue: Task scheduling, BFS, buffering

3. **Array vs Linked List trade-offs?**
   - Array: Fast access O(1), fixed size, contiguous memory
   - Linked List: Dynamic size, slow access O(n), scattered memory

4. **Why Binary Search requires sorted array?**
   - Relies on comparing middle element to decide which half to search
   - Requires order to make correct decisions

5. **DFS vs BFS - when to use each?**
   - DFS: Finding paths, cycle detection, memory constrained
   - BFS: Shortest path, level-order processing

---

## Data Structure Selection Guide

| Requirement | Best Structure |
|-------------|----------------|
| LIFO order | Stack |
| FIFO order | Queue |
| Fast search (sorted) | BST |
| Fast search (unsorted) | Hash Table |
| Priority-based access | Priority Queue (Heap) |
| Frequent insert/delete | Linked List |
| Random access | Array |
| Graph traversal | Queue (BFS) or Stack (DFS) |

---

## Common Mistakes to Avoid

1. **Stack/Queue**: Forgetting to check empty before pop/dequeue
2. **Linked List**: Not handling null pointers
3. **Binary Search**: Integer overflow in mid calculation (use `left + (right-left)/2`)
4. **Recursion**: Missing base case → stack overflow
5. **Graph**: Not marking vertices as visited → infinite loop
6. **Sorting**: Using wrong algorithm for data characteristics

---

## Time Complexity Summary

| Operation | Time |
|-----------|------|
| Stack push/pop | O(1) |
| Queue enqueue/dequeue | O(1) |
| Linked List search | O(n) |
| BST search (balanced) | O(log n) |
| Binary Search | O(log n) |
| Bubble Sort | O(n²) |
| Merge Sort | O(n log n) |
| Hash Table access | O(1) |
| DFS/BFS | O(V + E) |

---

## Summary

**Practice Strategy:**
1. Start with basic problems
2. Understand patterns (two-pointer, recursion, etc.)
3. Implement from scratch
4. Analyze time/space complexity
5. Practice variations

**Resources:**
- LeetCode, HackerRank for practice
- Visualize with diagrams
- Code review with peers
- Time yourself for interviews

---

**Congratulations!** You've completed the comprehensive Data Structures & Algorithms study guide covering all 20 topics!
