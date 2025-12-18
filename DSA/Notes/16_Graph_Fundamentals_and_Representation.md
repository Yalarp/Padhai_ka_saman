# Graph Fundamentals and Representation

## Graph Terminology

**Graph**: Collection of vertices (nodes) connected by edges

**Key Terms:**
- **Vertex**: Node in graph
- **Edge**: Connection between vertices
- **Degree**: Number of edges connected to vertex
- **Path**: Sequence of vertices connected by edges
- **Cycle**: Path that starts and ends at same vertex
- **Connected**: Path exists between all vertex pairs

---

## Graph Types

### 1. Directed vs Undirected

```
Directed (Digraph):
  A → B (one-way only)

Undirected:
  A ← → B (both ways)
```

### 2. Weighted vs Unweighted

```
Weighted:
  A —5— B (edge has weight/cost)

Unweighted:
  A —— B (no weight)
```

---

## Graph Representations

### Adjacency Matrix

2D array where `g[i][j]` indicates edge from vertex i to vertex j

```
Graph:     Adjacency Matrix:
  0—1          0  1  2  3
  |\          ┌─────────┐
  | \      0 │ 0  1  1  0 │
  2—3      1 │ 1  0  0  1 │
           2 │ 1  0  0  1 │
           3 │ 0  1  1  0 │
              └─────────┘

g[0][1] = 1 means edge from V0 to V1
g[0][3] = 0 means no edge
```

---

## Implementation (Graph_Class.java)

```java
package Graph_Examples;

import java.util.Scanner;

public class Graph_Class {
    int v, visited[], g[][];
    // v: number of vertices
    // g: adjacency matrix
    // visited: track visited vertices
    
    void createGraph(int nodes) {
        v = nodes;
        Scanner in = new Scanner(System.in);
        g = new int[v][v];
        visited = new int[v];
        
        for (int i = 0; i < v; i++) {
            for (int j = 0; j < v; j++) {
                System.out.println("Enter value for v" + i + 
                                 " to v" + j + " (999 for infinity):");
                g[i][j] = in.nextInt();
            }
        }
    }
    
    void printG() {
        for (int i = 0; i < v; i++) {
            for (int j = 0; j < v; j++) {
                System.out.print(g[i][j] + "\t");
            }
            System.out.println();
        }
    }
    
    void resetvisited() {
        for (int i = 0; i < v; i++) {
            visited[i] = 0;  // 0 = unvisited, 1 = visited
        }
    }
}
```

---

## Data Members Analysis

**`int v`**: Number of vertices in graph

**`int visited[]`**: 
- Array to mark vertices as visited
- `visited[i] = 0` → vertex i not visited
- `visited[i] = 1` → vertex i visited
- **Why needed?** Prevent revisiting in traversals

**`int g[][]`**: 
- Adjacency matrix
- `g[i][j] = 1` → edge exists from i to j
- `g[i][j] = 0` → no edge
- `g[i][j] = 999` → infinity (no connection)

---

## Example Usage

```java
public static void main(String args[]) {
    Graph_Class obj = new Graph_Class();
    obj.createGraph(4);
    obj.printG();
}
```

**Input for 4-vertex graph:**
```
v0 to v0: 0 (no self-loop)
v0 to v1: 1 (edge exists)
v0 to v2: 1
v0 to v3: 0
... (continue for all pairs)
```

---

## Advantages of Adjacency Matrix

✅ **Fast Edge Check**: O(1) to check if edge exists
✅ **Simple Implementation**: 2D array
✅ **Easy to Understand**: Visual representation

---

## Disadvantages

❌ **Space**: O(V²) even for sparse graphs
❌ **Add Vertex**: Requires creating new matrix
❌ **Iteration**: Must check all V vertices to find neighbors

---

## Summary

**Key Concepts:**
- **Adjacency Matrix**: 2D array representation
- **visited[] array**: Track visited vertices
- **Weighted graphs**: Can store weights instead of 0/1

---

**Next:** [Graph Traversal Algorithms](file:///c:/Users/2706p/Desktop/mcq/java/17_Graph_Traversal_Algorithms.md)
