# Searching Algorithms

## Overview

Searching: Finding an element in a collection

Two fundamental algorithms:
1. **Linear Search**: Sequential checking
2. **Binary Search**: Divide and conquer (requires sorted data)

---

## Linear Search

### Algorithm
Check each element sequentially until found or end reached

### Implementation

```java
public class LinearSearch {
    static int linearSearch(int[] arr, int key) {
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == key) {
                return i;  // Found at index i
            }
        }
        return -1;  // Not found
    }
    
    public static void main(String[] args) {
        int[] arr = {64, 25, 12, 22, 11};
        int key = 22;
        int result = linearSearch(arr, key);
        
        if (result != -1)
            System.out.println("Found at index: " + result);
        else
            System.out.println("Not found");
    }
}
```

### Time Complexity
- **Best Case**: O(1) - First element
- **Average Case**: O(n/2) → O(n)
- **Worst Case**: O(n) - Last element or not present

### Space Complexity: O(1)

---

## Binary Search

### Prerequisites
**Array must be sorted!**

### Algorithm
1. Compare key with middle element
2. If equal, found
3. If key < middle, search left half
4. If key > middle, search right half
5. Repeat until found or search space exhausted

### Iterative Implementation

```java
public class BinarySearch {
    static int binarySearch(int[] arr, int key) {
        int left = 0, right = arr.length - 1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;  // Avoid overflow
            
            if (arr[mid] == key)
                return mid;  // Found
            
            if (arr[mid] < key)
                left = mid + 1;  // Search right half
            else
                right = mid - 1;  // Search left half
        }
        
        return -1;  // Not found
    }
}
```

### Recursive Implementation

```java
static int binarySearchRec(int[] arr, int left, int right, int key) {
    if (left > right)
        return -1;  // Base case: not found
    
    int mid = left + (right - left) / 2;
    
    if (arr[mid] == key)
        return mid;  // Found
    
    if (arr[mid] < key)
        return binarySearchRec(arr, mid + 1, right, key);  // Right
    else
        return binarySearchRec(arr, left, mid - 1, key);   // Left
}
```

---

## Execution Trace

```
Array: [11, 12, 22, 25, 64]
Key: 22

Iteration 1:
  left=0, right=4
  mid = 0 + (4-0)/2 = 2
  arr[2] = 22 == key
  FOUND at index 2!

Key: 25

Iteration 1:
  left=0, right=4, mid=2
  arr[2]=22 < 25
  Search right: left=3

Iteration 2:
  left=3, right=4, mid=3
  arr[3]=25 == key
  FOUND at index 3!
```

---

## Time Complexity

### Binary Search
- **Best Case**: O(1) - Middle element
- **Average/Worst Case**: O(log n)
  - Each iteration halves search space
  - n → n/2 → n/4 → ... → 1
  - Takes log₂(n) steps

### Space Complexity
- Iterative: O(1)
- Recursive: O(log n) - Call stack

---

## Comparison

| Feature | Linear Search | Binary Search |
|---------|---------------|---------------|
| **Prerequisite** | None | **Sorted array** |
| **Time** | O(n) | O(log n) |
| **Best For** | Small/unsorted | Large sorted |
| **Implementation** | Simple | Moderate |

---

## When to Use

**Linear Search:**
- Small arrays (< 100 elements)
- Unsorted data
- Single search operation

**Binary Search:**
- Large sorted arrays
- Multiple search operations
- Performance critical

---

## Summary

**Linear Search:** Simple, works on unsorted data, O(n)
**Binary Search:** Fast, requires sorted data, O(log n)

---

**Next:** [Sorting Algorithms](file:///c:/Users/2706p/Desktop/mcq/java/19_Sorting_Algorithms.md)
