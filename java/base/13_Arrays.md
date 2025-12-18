# 📦 Arrays in Java

## Table of Contents
1. [Introduction to Arrays](#introduction-to-arrays)
2. [Array Declaration](#array-declaration)
3. [Array Initialization](#array-initialization)
4. [Accessing Array Elements](#accessing-array-elements)
5. [Array of Objects](#array-of-objects)
6. [Multidimensional Arrays](#multidimensional-arrays)
7. [Enhanced For Loop (for-each)](#enhanced-for-loop-for-each)
8. [Final Arrays](#final-arrays)
9. [Code Examples](#code-examples)
10. [Interview Questions](#interview-questions)

---

## Introduction to Arrays

An **array** is a collection of similar data types stored in contiguous memory locations.

### Characteristics:
- Fixed size (cannot grow or shrink)
- Zero-indexed (first element at index 0)
- Homogeneous (same data type elements)
- Object in Java (arrays are objects)

### Visual Representation:

```
Array: int[] arr = {10, 20, 30, 40, 50};

Index:    [0]   [1]   [2]   [3]   [4]
         ┌─────┬─────┬─────┬─────┬─────┐
Values:  │ 10  │ 20  │ 30  │ 40  │ 50  │
         └─────┴─────┴─────┴─────┴─────┘
            ▲
            │
         arr (reference points here)

arr.length = 5
```

---

## Array Declaration

### Syntax Options:

```java
// Style 1: Square brackets after type (preferred)
int[] arr;
String[] names;

// Style 2: Square brackets after variable name
int arr[];
String names[];

// Both styles are equivalent
```

### Key Point:
- Declaration only creates a **reference variable**
- No memory for elements yet
- Reference is `null` until initialized

---

## Array Initialization

### Method 1: Declaration + Allocation + Assignment (Separate)

```java
int arr[];           // Step 1: Declare reference
arr = new int[3];    // Step 2: Allocate memory for 3 elements
arr[0] = 10;         // Step 3: Assign values
arr[1] = 20;
arr[2] = 30;
```

### Method 2: Declaration + Allocation (Combined)

```java
int arr[] = new int[3];   // Declare and allocate
arr[0] = 10;              // Assign values
arr[1] = 20;
arr[2] = 30;
```

### Method 3: Declaration + Initialization (Shorthand)

```java
int arr[] = {10, 20, 30};  // All in one line
// Size is determined from number of elements (3)
```

### Code Example with Explanations:

```java
public class ArrayDemo2 {                           // Line 1: Class declaration
    public static void main(String args[]) {        // Line 2: Main method
        
        int arr[] = new int[3];                     // Line 3: Create array of 3 ints
        // Memory: [0, 0, 0] - default values
        
        arr[0] = 10;                                // Line 4: Set first element
        arr[1] = 20;                                // Line 5: Set second element
        arr[2] = 30;                                // Line 6: Set third element
        // Memory: [10, 20, 30]
        
        for (int i = 0; i < arr.length; i++) {      // Line 7: Loop through array
            System.out.println(arr[i]);             // Line 8: Print each element
        }
    }
}
```

### Output:
```
10
20
30
```

### Memory Diagram:

```
Stack:                     Heap:
┌────────────┐            ┌───────────────────────┐
│ arr: 0x100 │───────────►│ int[3] object         │
└────────────┘            │ ┌─────┬─────┬─────┐   │
                          │ │ 10  │ 20  │ 30  │   │
                          │ └─────┴─────┴─────┘   │
                          │ length: 3             │
                          └───────────────────────┘
```

---

## Accessing Array Elements

```java
int[] arr = {10, 20, 30, 40, 50};

// Access using index
int first = arr[0];   // 10
int third = arr[2];   // 30
int last = arr[arr.length - 1];  // 50

// Modify element
arr[1] = 25;  // arr is now {10, 25, 30, 40, 50}

// arr[5] = 60;  // ERROR! ArrayIndexOutOfBoundsException
```

### array.length Property:

```java
int[] numbers = {1, 2, 3, 4, 5};
System.out.println(numbers.length);  // 5

// Note: length is a property (no parentheses), not a method
// This is different from String.length() which is a method
```

---

## Array of Objects

Arrays can hold references to objects.

```java
public class ArrayOfInstance {                      // Line 1
    public static void main(String args[]) {        // Line 2
        
        // Create array of REFERENCES (not objects yet!)
        MyClass arr[] = new MyClass[3];             // Line 3
        // arr = [null, null, null]
        
        // Create objects and assign to array
        for (int i = 0, j = 10; i < arr.length; i++, j += 10) {  // Line 4
            arr[i] = new MyClass(j);                // Line 5: Create object
        }
        // arr = [MyClass(10), MyClass(20), MyClass(30)]
        
        // Access objects
        for (int i = 0; i < arr.length; i++) {      // Line 6
            System.out.println(arr[i].getNum());    // Line 7
        }
    }
}

class MyClass {                                     // Line 8
    int num;
    
    MyClass(int num) {                              // Line 9
        this.num = num;
    }
    
    int getNum() {                                  // Line 10
        return num;
    }
}
```

### Output:
```
10
20
30
```

### Memory Diagram for Object Array:

```
Step 1: MyClass[] arr = new MyClass[3];

Stack:                     Heap:
┌────────────┐            ┌──────────────────────┐
│ arr: 0x100 │───────────►│ MyClass[3]           │
└────────────┘            │ [null, null, null]   │
                          └──────────────────────┘

Step 2: After creating objects in loop

Stack:                     Heap:
┌────────────┐            ┌──────────────────────┐
│ arr: 0x100 │───────────►│ MyClass[3]           │
└────────────┘            │ ┌───────┬───────┬───────┐
                          │ │ 0x200 │ 0x300 │ 0x400 │
                          │ └───┬───┴───┬───┴───┬───┘
                          └─────┼───────┼───────┼────┘
                                │       │       │
                                ▼       ▼       ▼
                          ┌─────────┐ ┌─────────┐ ┌─────────┐
                          │num: 10  │ │num: 20  │ │num: 30  │
                          └─────────┘ └─────────┘ └─────────┘
```

---

## Multidimensional Arrays

### 2D Array (Matrix):

```java
// Declaration and initialization
int[][] matrix = new int[3][4];  // 3 rows, 4 columns

// Or with values
int[][] matrix = {
    {1, 2, 3, 4},
    {5, 6, 7, 8},
    {9, 10, 11, 12}
};

// Access element
int value = matrix[1][2];  // 7 (row 1, column 2)

// Iterate
for (int i = 0; i < matrix.length; i++) {           // Rows
    for (int j = 0; j < matrix[i].length; j++) {    // Columns
        System.out.print(matrix[i][j] + " ");
    }
    System.out.println();
}
```

### Visual Representation:

```
int[][] matrix = {{1,2,3}, {4,5,6}};

        Column 0  Column 1  Column 2
        ┌─────────┬─────────┬─────────┐
Row 0   │    1    │    2    │    3    │
        ├─────────┼─────────┼─────────┤
Row 1   │    4    │    5    │    6    │
        └─────────┴─────────┴─────────┘

matrix[0][0] = 1
matrix[1][2] = 6
```

### Jagged Array (Different Row Sizes):

```java
int[][] jagged = new int[3][];  // Only rows defined

jagged[0] = new int[2];   // Row 0 has 2 elements
jagged[1] = new int[4];   // Row 1 has 4 elements
jagged[2] = new int[3];   // Row 2 has 3 elements

// Visual:
// Row 0: [ ][ ]
// Row 1: [ ][ ][ ][ ]
// Row 2: [ ][ ][ ]
```

---

## Enhanced For Loop (for-each)

Simplified syntax for iterating arrays and collections.

### Syntax:
```java
for (dataType element : arrayName) {
    // Use element
}
```

### Example:

```java
int[] numbers = {10, 20, 30, 40, 50};

// Traditional for loop
for (int i = 0; i < numbers.length; i++) {
    System.out.println(numbers[i]);
}

// Enhanced for loop (for-each)
for (int num : numbers) {
    System.out.println(num);
}
```

### With Object Array:

```java
String[] names = {"Alice", "Bob", "Charlie"};

for (String name : names) {
    System.out.println("Hello, " + name);
}
```

### Limitations:
- Cannot modify elements
- Cannot get index
- Cannot iterate backwards

---

## Final Arrays

### Final Reference vs Final Elements:

```java
final int[] arr = {10, 20, 30};

// CANNOT reassign the reference
// arr = new int[5];  // ERROR! arr is final

// CAN modify elements
arr[0] = 100;  // OK! Elements are not final
arr[1] = 200;  // OK!

System.out.println(arr[0]);  // 100
```

### Visual Explanation:

```
final int[] arr = {10, 20, 30};

Stack:                     Heap:
┌────────────┐            ┌───────────────────┐
│ arr (final)│───────────►│ {10, 20, 30}     │
└────────────┘     ✗      │   can be modified │
       │          Cannot  └───────────────────┘
       │          change
       ▼          arrow
    New array 
    NOT allowed
```

---

## Code Examples

### Array Declaration Methods:

```java
public class ArrayDemo1 {                           // Line 1
    public static void main(String args[]) {        // Line 2
        
        // Method 1: Separate declaration and allocation
        /*int arr[];                                // Declare reference
        arr = new int[3];                           // Allocate memory */
        
        // Method 2: Combined declaration and allocation
        // int arr[] = new int[3];
        
        // Method 3: Shorthand initialization
        int arr[] = {10, 20, 30};                   // Line 3
        
        arr[0] = 10;                                // Line 4
        arr[1] = 20;                                // Line 5
        arr[2] = 30;                                // Line 6
        
        for (int i = 0; i < arr.length; i++) {      // Line 7
            System.out.println(arr[i]);             // Line 8
        }
    }
}
```

### Default Values in Arrays:

```java
public class ArrayDefaults {
    public static void main(String[] args) {
        // Numeric arrays default to 0
        int[] nums = new int[3];
        System.out.println(nums[0]);  // 0
        
        // Boolean arrays default to false
        boolean[] flags = new boolean[3];
        System.out.println(flags[0]);  // false
        
        // Object arrays default to null
        String[] strs = new String[3];
        System.out.println(strs[0]);  // null
    }
}
```

---

## Interview Questions

### Q1: What is an array?
**Answer**: An array is a collection of similar data types stored in contiguous memory locations with a fixed size.

### Q2: Are arrays objects in Java?
**Answer**: Yes, arrays in Java are objects. They inherit from `java.lang.Object` and have properties like `length`.

### Q3: What is the difference between length and length()?
**Answer**:
- `length`: Property of arrays (no parentheses)
- `length()`: Method of String class (with parentheses)

### Q4: What is ArrayIndexOutOfBoundsException?
**Answer**: Exception thrown when trying to access an array element with an index outside the valid range (0 to length-1).

### Q5: Can we change the size of an array after creation?
**Answer**: No. Array size is fixed. For dynamic sizing, use `ArrayList` or create a new larger array and copy elements.

### Q6: What is the default value of array elements?
**Answer**:
- Numeric types: 0
- boolean: false
- char: '\u0000'
- Reference types: null

### Q7: What is a jagged array?
**Answer**: A 2D array where different rows can have different number of columns.

### Q8: What is the difference between int[] arr and int arr[]?
**Answer**: No difference. Both are valid. `int[] arr` is preferred as it keeps type information together.

### Q9: Can we have an array of size 0?
**Answer**: Yes. `int[] arr = new int[0];` is valid. It's an empty array with `arr.length = 0`.

### Q10: What happens when final is applied to an array?
**Answer**: The reference cannot be reassigned, but array elements can still be modified.

---

## Quick Reference

### Array Syntax Summary

```java
// Declaration
int[] arr;
String[] names;

// Allocation
arr = new int[5];

// Initialization
int[] arr = {1, 2, 3, 4, 5};

// Access
arr[0] = 10;
int val = arr[2];

// Length
int size = arr.length;

// Iterate
for (int i = 0; i < arr.length; i++) { }
for (int num : arr) { }

// 2D Array
int[][] matrix = new int[3][4];
int[][] matrix = {{1,2}, {3,4}};
```

---

*Previous: [09_Memory_Management.md](./09_Memory_Management.md)*  
*Next: [14_Packages_and_JARs.md](./14_Packages_and_JARs.md)*
