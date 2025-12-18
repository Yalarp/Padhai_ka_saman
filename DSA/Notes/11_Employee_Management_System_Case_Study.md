# Employee Management System Case Study

## Overview

Real-world application using Java's LinkedList to manage employee records with CRUD operations.

---

## E_Node Structure

```java
package Linked_List_Examples;

public class E_Node {
    // Employee data fields
    String name, gender;
    int eid;
    float salary;
    
    // Self-referential pointer
    E_Node next;
    
    // Constructor
    E_Node(String name, String gender, int eid, float salary) {
        this.name = name;
        this.gender = gender;
        this.eid = eid;
        this.salary = salary;
        this.next = null;
    }
    
    // Display method
    void print_Node() {
        System.out.println("\nID:" + eid + "\tName:" + name + 
                         "\tGender:" + gender + "\tSalary:" + salary);
    }
}
```

**Key Points:**
- Extends basic Node concept to custom objects
- Multiple data fields (not just int)
- `print_Node()` method for display

---

## Complete Implementation

```java
package Linked_List_Examples;

import java.util.LinkedList;
import java.util.Scanner;

public class Employee_Management_System {
    public static void main(String[] args) {
        LinkedList<E_Node> list = new LinkedList<>();
        Scanner in = new Scanner(System.in);
        
        // INSERT: Add 2 employee records
        for (int i = 0; i < 2; i++) {
            System.out.println("\nEnter name,gender,id,salary for record " + (i+1));
            String name = in.next();
            String gender = in.next();
            int eid = in.nextInt();
            float salary = in.nextFloat();
            
            E_Node n = new E_Node(name, gender, eid, salary);
            list.add(n);  // Add to LinkedList
        }
        
        // DISPLAY: Print all records
        for (E_Node i : list) {
            i.print_Node();
        }
        
        // SEARCH: Find by ID
        System.out.println("Enter id to search:");
        int id = in.nextInt();
        boolean found = false;
        
        for (E_Node i : list) {
            if (i.eid == id) {
                System.out.println("found");
                i.print_Node();
                found = true;
                break;
            }
        }
        
        if (!found)
            System.out.println("Not Found");
    }
}
```

---

## Line-by-Line Analysis

**LinkedList Declaration:**
```java
LinkedList<E_Node> list = new LinkedList<>();
```
- Generic type `<E_Node>` specifies node type
- Java Collections Framework provides LinkedList implementation
- No manual pointer management needed!

**Enhanced For Loop (For-Each):**
```java
for (E_Node i : list) {
    i.print_Node();
}
```
- Iterates through all nodes automatically
- `i` represents each E_Node in the list
- Cleaner than while loop with temporary pointers

**Search Logic:**
```java
for (E_Node i : list) {
    if (i.eid == id) {
        // Found!
        break;  // Exit loop early
    }
}
```
- Linear search through linked list
- O(n) complexity
- `break` stops searching once found

---

## Sample Execution

```
Enter name,gender,id,salary for record 1
John M 101 50000.50

Enter name,gender,id,salary for record 2
Alice F 102 60000.75

ID:101 Name:John  Gender:M Salary:50000.5
ID:102 Name:Alice Gender:F Salary:60000.75

Enter id to search:
101
found
ID:101 Name:John  Gender:M Salary:50000.5
```

---

## CRUD Operations

### Create (Insert)
```java
E_Node n = new E_Node(name, gender, eid, salary);
list.add(n);
```

### Read (Display/Search)
```java
// Display all
for (E_Node i : list) {
    i.print_Node();
}

// Search by ID
for (E_Node i : list) {
    if (i.eid == id) { /* found */ }
}
```

### Update
```java
// Find employee, update fields
for (E_Node i : list) {
    if (i.eid == id) {
        i.salary = newSalary;  // Update
        break;
    }
}
```

### Delete
```java
list.removeIf(node -> node.eid == idToDelete);
// Or manually:
for (E_Node i : list) {
    if (i.eid == id) {
        list.remove(i);
        break;
    }
}
```

---

## Java Collections Framework Benefits

1. **Built-in Methods**: add(), remove(), contains(), size()
2. **No Manual Memory**: Automatic garbage collection
3. **Type Safety**: Generics prevent type errors
4. **Iterators**: Enhanced for-loop support

---

## Summary

**Custom Node for Real Data:**
- E_Node holds employee information
- Multiple fields beyond simple int
- Methods for display and comparison

**Using Java LinkedList:**
- Production-ready implementation
- No manual pointer manipulation
- Rich API for operations

**Practical Application:**
- Employee database
- Student records
- Product inventory

---

**Next:** [Doubly Linked List](file:///c:/Users/2706p/Desktop/mcq/java/12_Doubly_Linked_List_Implementation.md)
