# Sorting Algorithms

## Overview

Sorting: Arranging elements in a specific order (ascending/descending)

Algorithms covered:
1. Bubble Sort
2. Selection Sort
3. Insertion Sort
4. Merge Sort
5. Quick Sort

---

## Bubble Sort

### Algorithm
Repeatedly swap adjacent elements if in wrong order. Largest element "bubbles" to end each pass.

### Implementation

```java
void bubbleSort(int[] arr) {
    int n = arr.length;
    for (int i = 0; i < n - 1; i++) {           // Passes
        for (int j = 0; j < n - i - 1; j++) {   // Comparisons
            if (arr[j] > arr[j + 1]) {          // Swap if wrong order
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
            }
        }
    }
}
```

**Used in:** Priority Queue (from our code!)

### Time Complexity
- Best: O(n) - Already sorted (with optimization)
- Average: O(n²)
- Worst: O(n²)

---

## Selection Sort

### Algorithm
Find minimum element, swap with first position. Repeat for remaining array.

### Implementation

```java
void selectionSort(int[] arr) {
    int n = arr.length;
    for (int i = 0; i < n - 1; i++) {
        int minIdx = i;
        for (int j = i + 1; j < n; j++) {
            if (arr[j] < arr[minIdx]) {
                minIdx = j;  // Find minimum
            }
        }
        // Swap
        int temp = arr[i];
        arr[i] = arr[minIdx];
        arr[minIdx] = temp;
    }
}
```

### Time Complexity
- Best/Average/Worst: O(n²)
- **No early termination possible**

---

## Insertion Sort

### Algorithm
Build sorted array one element at a time by inserting each element in correct position.

### Implementation

```java
void insertionSort(int[] arr) {
    int n = arr.length;
    for (int i = 1; i < n; i++) {
        int key = arr[i];
        int j = i - 1;
        
        // Shift elements greater than key
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;  // Insert key
    }
}
```

### Time Complexity
- Best: O(n) - Already sorted
- Average: O(n²)
- Worst: O(n²)

**Good for:** Nearly sorted data

---

## Merge Sort

### Algorithm (Divide and Conquer)
1. Divide array into two halves
2. Recursively sort both halves
3. Merge sorted halves

### Implementation

```java
void mergeSort(int[] arr, int left, int right) {
    if (left < right) {
        int mid = left + (right - left) / 2;
        
        mergeSort(arr, left, mid);      // Sort left half
        mergeSort(arr, mid + 1, right); // Sort right half
        merge(arr, left, mid, right);   // Merge
    }
}

void merge(int[] arr, int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;
    
    int[] L = new int[n1];
    int[] R = new int[n2];
    
    // Copy data
    for (int i = 0; i < n1; i++)
        L[i] = arr[left + i];
    for (int j = 0; j < n2; j++)
        R[j] = arr[mid + 1 + j];
    
    // Merge
    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        if (L[i] <= R[j])
            arr[k++] = L[i++];
        else
            arr[k++] = R[j++];
    }
    
    // Copy remaining
    while (i < n1)
        arr[k++] = L[i++];
    while (j < n2)
        arr[k++] = R[j++];
}
```

### Time Complexity
- Best/Average/Worst: O(n log n)
- **Stable**: Maintains relative order

### Space: O(n) - Not in-place

---

## Quick Sort

### Algorithm
1. Choose pivot element
2. Partition array: smaller left, larger right
3. Recursively sort partitions

### Implementation

```java
void quickSort(int[] arr, int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);   // Partition index
        quickSort(arr, low, pi - 1);          // Sort left
        quickSort(arr, pi + 1, high);         // Sort right
    }
}

int partition(int[] arr, int low, int high) {
    int pivot = arr[high];  // Last element as pivot
    int i = low - 1;
    
    for (int j = low; j < high; j++) {
        if (arr[j] < pivot) {
            i++;
            // Swap arr[i] and arr[j]
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
    }
    
    // Place pivot in correct position
    int temp = arr[i + 1];
    arr[i + 1] = arr[high];
    arr[high] = temp;
    
    return i + 1;
}
```

### Time Complexity
- Best/Average: O(n log n)
- Worst: O(n²) - Already sorted, poor pivot choice

### Space: O(log n) - In-place but recursive

---

## Comparison Table

| Algorithm | Best | Average | Worst | Space | Stable | In-Place |
|-----------|------|---------|-------|-------|--------|----------|
| **Bubble** | O(n) | O(n²) | O(n²) | O(1) | Yes | Yes |
| **Selection** | O(n²) | O(n²) | O(n²) | O(1) | No | Yes |
| **Insertion** | O(n) | O(n²) | O(n²) | O(1) | Yes | Yes |
| **Merge** | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes | No |
| **Quick** | O(n log n) | O(n log n) | O(n²) | O(log n) | No | Yes |

---

## Terminology

**Stable**: Maintains relative order of equal elements
**In-Place**: Uses O(1) extra space
**Adaptive**: Faster on nearly sorted data

---

## When to Use

**Bubble/Insertion**: Small arrays, nearly sorted data, educational
**Selection**: Minimize swaps
**Merge**: Guaranteed O(n log n), external sorting, stability needed
**Quick**: General purpose, average case performance

---

## Summary

**Simple Sorts (O(n²)):** Bubble, Selection, Insertion
**Efficient Sorts (O(n log n)):** Merge, Quick

**Python/Java default:** Timsort (hybrid of Merge + Insertion)

---

**Next:** [Practice Problems and Assignments](file:///c:/Users/2706p/Desktop/mcq/java/20_Practice_Problems_and_Assignment_Solutions.md)
