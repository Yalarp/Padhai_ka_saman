# Graph Traversal Algorithms

## Overview

Two fundamental graph traversal algorithms:
1. **DFS (Depth-First Search)**: Go deep first
2. **BFS (Breadth-First Search)**: Visit level by level

---

## Depth-First Search (DFS)

### Algorithm
1. Visit current vertex, mark as visited
2. Recursively visit all unvisited neighbors
3. Backtrack when no unvisited neighbors

### Implementation

```java
public void DFS(int source) {
    visited[source] = 1;             // Mark as visited
    System.out.println("V" + source);
    
    for (int i = 0; i < v; i++) {
        if (g[source][i] == 1 && visited[i] != 1) {
            // Neighbor and unvisited
            DFS(i);                   // Recursive call
        }
    }
}
```

### Line-by-Line Explanation

**Line 1:** `visited[source] = 1;`
- Mark current vertex as visited
- Prevents revisiting in cycles

**Line 2:** `System.out.println("V" + source);`
- Process current vertex (print it)

**Line 4-7:** `for (int i = 0; i < v; i++)`
- Check all vertices as potential neighbors

**Line 5:** `if (g[source][i] == 1 && visited[i] != 1)`
- `g[source][i] == 1`: Edge exists (neighbor)
- `visited[i] != 1`: Not yet visited
- **Both conditions must be true**

**Line 7:** `DFS(i);`
- Recursively visit neighbor
- **Depth-first**: Go deep before going wide

---

### Execution Trace

```
Graph:
  0—1—3
  |
  2

Adjacency Matrix:
  0 1 2 3
0[0,1,1,0]
1[1,0,0,1]
2[1,0,0,0]
3[0,1,0,0]

DFS(0):
  Mark 0 visited, print V0
  Check neighbors: 1 and 2
  DFS(1):
    Mark 1 visited, print V1
    Check neighbors: 0 (visited), 3
    DFS(3):
      Mark 3 visited, print V3
      No unvisited neighbors, backtrack
    Back to 1, backtrack
  DFS(2):
    Mark 2 visited, print V2
    No unvisited neighbors, backtrack

Output: V0, V1, V3, V2
```

---

## DFS with Search

```java
public boolean DFS_search(int source, int key) {
    boolean flag = false;
    
    if (key == source) {
        flag = true;  // Found!
    }
    
    visited[source] = 1;
    System.out.println("V" + source);
    
    for (int i = 0; i < v; i++) {
        if (g[source][i] == 1 && visited[i] != 1) {
            DFS_search(i, key);
        }
    }
    
    return flag;
}
```

**Note:** Returns true if key found during traversal

---

## Breadth-First Search (BFS)

### Algorithm
1. Use queue to track vertices to visit
2. Visit vertex, mark as visited, enqueue
3. Dequeue, visit all unvisited neighbors
4. Enqueue neighbors, repeat

### Implementation

```java
public void BFS(int source) {
    int q[] = new int[v];        // Queue array
    int front = 0, rear = -1;
    
    visited[source] = 1;         // Mark start as visited
    q[++rear] = source;          // Enqueue start
    
    while (front <= rear) {      // While queue not empty
        int element = q[front++]; // Dequeue
        System.out.print("V" + element + "-");
        
        for (int i = 0; i < v; i++) {
            if (g[element][i] == 1 && visited[i] != 1) {
                visited[i] = 1;   // Mark as visited
                q[++rear] = i;    // Enqueue neighbor
            }
        }
    }
}
```

### Line-by-Line Explanation

**Line 2-3:** Queue initialization
- `q[]`: Array-based queue
- `front = 0, rear = -1`: Empty queue

**Line 5-6:** Initialize
- Mark start vertex as visited
- Enqueue start vertex

**Line 8:** `while (front <= rear)`
- Queue not empty condition
- Continue while vertices to process

**Line 9:** `int element = q[front++];`
- Dequeue front element
- Process it

**Line 12-18:** Process neighbors
- Check all vertices
- If neighbor and unvisited:
  - Mark visited immediately (prevent duplicates in queue)
  - Enqueue

---

### Execution Trace

```
Same Graph:
  0—1—3
  |
  2

BFS(0):
  Mark 0, enqueue 0
  Queue: [0]
  
  Dequeue 0, print V0
  Check neighbors 1, 2: mark, enqueue
  Queue: [1, 2]
  
  Dequeue 1, print V1
  Check neighbors 0 (visited), 3: mark, enqueue 3
  Queue: [2, 3]
  
  Dequeue 2, print V2
  No unvisited neighbors
  Queue: [3]
  
  Dequeue 3, print V3
  No unvisited neighbors
  Queue: []
  
Output: V0-V1-V2-V3-
```

---

## DFS vs BFS Comparison

| Feature | DFS | BFS |
|---------|-----|-----|
| **Data Structure** | Stack (recursion) | Queue |
| **Order** | Depth-first | Level-by-level |
| **Memory** | O(H) height | O(W) width |
| **Path** | May not be shortest | **Shortest path** |
| **Implementation** | Recursive (simple) | Iterative (queue) |

---

## Applications

### DFS Applications
- **Cycle Detection**: Check for cycles in graph
- **Path Finding**: Find if path exists
- **Topological Sort**: Order vertices for dependencies
- **Maze Solving**: Explore all paths

### BFS Applications
- **Shortest Path**: Unweighted graphs
- **Level-Order**: Process by distance from source
- **Connected Components**: Find all connected vertices
- **Web Crawling**: Crawl level by level

---

## Time Complexity

**Both DFS and BFS:**
- **Adjacency Matrix**: O(V²)
  - Check all V vertices for each of V vertices
- **Adjacency List**: O(V + E)
  - Visit V vertices + explore E edges

---

## Summary

**DFS:**
- Recursive, uses call stack
- Goes deep before wide
- Good for: cycle detection, paths

**BFS:**
- Iterative, uses queue
- Level-by-level traversal
- Good for: shortest path, levels

**visited[] array**: Critical to prevent infinite loops in cycles

---

**Next:** [Searching Algorithms](file:///c:/Users/2706p/Desktop/mcq/java/18_Searching_Algorithms.md)
