# Advanced Queue Applications

## Table of Contents
1. [Two Queues in Single Array](#two-queues-in-single-array)
2. [Binary Sequence Generation](#binary-sequence-generation)
3. [Max Profit Algorithm](#max-profit-algorithm)
4. [Bidding Process Simulation](#bidding-process-simulation)

---

## Two Queues in Single Array

### Concept
Efficiently implement two queues in a single array by utilizing opposite ends.

```
Queue 1: Grows from left → right
Queue 2: Grows from right → left

Array: [Q1][Q1][Q1][ ][Q2][Q2][Q2]
        ←Q1 grows→    ←Q2 grows← 
```

### Implementation (Two_Queue_in_an_Array.java)

```java
package Queue_Examples;

public class Two_Queue_in_an_Array {
    int queue[];
    int front1, rear1, front2, rear2, MaxSize;
    
    void create_Queue(int size) {
        rear1 = -1;           // Queue1 rear starts at -1
        front1 = 0;           // Queue1 front at 0
        rear2 = MaxSize;      // Queue2 rear starts at MaxSize  
        front2 = MaxSize - 1; // Queue2 front at MaxSize-1
        MaxSize = size;
        queue = new int[MaxSize];
    }
    
    // Queue 1: Normal left-to-right
    void enqueue1(int e) {
        queue[++rear1] = e;  // Increment rear1, add element
    }
    
    // Queue 2: Reverse right-to-left
    void enqueue2(int e) {
        queue[--rear2] = e;  // Decrement rear2, add element
    }
    
    boolean is_full() {
        return ((rear2 - 1) == rear1);  // Full when they meet
    }
    
    int dequeue1() {
        int temp = queue[front1];
        front1++;
        return temp;
    }
    
    int dequeue2() {
        int temp = queue[front2];
        front2--;              // Decrement for Queue2
        return temp;
    }
    
    boolean is_empty1() {
        return (front1 > rear1);
    }
    
    boolean is_empty2() {
        return (front2 < rear2);  // Note: reversed logic
    }
    
    void print_queue1() {
        for (int i = front1; i <= rear1; i++)
            System.out.print(queue[i] + " - ");
    }
    
    void print_queue2() {
        for (int i = front2; i >= rear2; i--)  // Reverse iteration
            System.out.print(queue[i] + " - ");
    }
}
```

### Key Points

**Queue 1 (Left side):**
- `rear1` starts at -1, increments with `++`
- `front1` starts at 0, increments normally
- Grows left to right

**Queue 2 (Right side):**
- `rear2` starts at MaxSize, decrements with `--`
- `front2` starts at MaxSize-1, decrements
- Grows right to left

**Full Condition:**
```
Queue Full when: (rear2 - 1) == rear1
Example: MaxSize = 10
  Queue1: rear1 = 4
  Queue2: rear2 = 6
  (6 - 1) == 4? No, space available
  
  Queue1: rear1 = 5  
  Queue2: rear2 = 6
  (6 - 1) == 5? Yes, FULL!
```

---

## Binary Sequence Generation

### Problem
Generate binary numbers from 1 to n in sequence: 1, 10, 11, 100, 101, 110, 111, ...

### Algorithm
1. Start with "1" in queue
2. Dequeue, print, then enqueue: current+"0" and current+"1"
3. Repeat n times

### Implementation (Binary_sequence.java)

```java
package Queue_Examples;

import java.util.ArrayDeque;
import java.util.Scanner;
import java.util.Queue;

public class Binary_sequence {
    static void binary_sequence_printer(int n) {
        // Use ArrayDeque of String
        Queue<String> bin = new ArrayDeque<>();
        
        // Add "1" to start
        bin.add("1");
        
        // Generate n binary numbers
        for (int i = 1; i < n; i++) {
            // Print front and remove it
            String current = bin.poll();
            System.out.print("\n" + i + "-----" + current);
            
            // Add current+"0" to queue
            bin.add(current + "0");
            
            // Add current+"1" to queue
            bin.add(current + "1");
        }
    }
    
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        System.out.println("Enter n:");
        int n = in.nextInt();
        Binary_sequence.binary_sequence_printer(n);
    }
}
```

### Execution Trace (n=7)

```
Initial Queue: ["1"]

Iteration 1:
  Dequeue "1", Print: 1-----1
  Enqueue "10", "11"
  Queue: ["10", "11"]

Iteration 2:
  Dequeue "10", Print: 2-----10
  Enqueue "100", "101"
  Queue: ["11", "100", "101"]

Iteration 3:
  Dequeue "11", Print: 3-----11
  Enqueue "110", "111"
  Queue: ["100", "101", "110", "111"]

Iteration 4:
  Dequeue "100", Print: 4-----100
  Enqueue "1000", "1001"
  Queue: ["101", "110", "111", "1000", "1001"]

Iteration 5:
  Dequeue "101", Print: 5-----101
  Queue: ["110", "111", "1000", "1001", "1010", "1011"]

Iteration 6:
  Dequeue "110", Print: 6-----110

Output:
1-----1
2-----10
3-----11
4-----100
5-----101
6-----110
```

---

## Max Profit Algorithm

### Problem
Given stock prices array, find maximum profit by buying once and selling once.
Buy must occur before sell.

### Algorithm
```
For each buy day:
  For each sell day after buy:
    Calculate profit = price[sell] - price[buy]
    Track maximum profit
```

### Implementation (Max_Profit.java)

```java
package Queue_Examples;

public class Max_Profit {
    static void Profit_Maker(int n[]) {
        int Max_Profit = 0, Buy_on = 0, Sell_on = 0;
        
        // Try all buy days
        for (int buy = 0; buy < n.length - 1; buy++) {
            // Try all sell days after buy
            for (int sell = n.length - 1; sell > buy; sell--) {
                int profit = n[sell] - n[buy];
                
                // Update if better profit found
                if (profit > Max_Profit) {
                    Max_Profit = profit;
                    Buy_on = buy;
                    Sell_on = sell;
                }
            }
        }
        
        System.out.println("Max Profit " + Max_Profit + 
                         " by buy on " + Buy_on + 
                         " and sell on" + Sell_on);
    }
    
    public static void main(String[] args) {
        int a[] = {100, 180, 260, 310, 40, 535, 695, 30, 50, 70, 20, 40, 60, 90, 10};
        Max_Profit.Profit_Maker(a);
    }
}
```

### Line-by-Line Explanation

**Line 7:** `for (int buy = 0; buy < n.length - 1; buy++)`
- Iterate through all possible buy days
- `buy < n.length - 1` because must sell after buying

**Line 10:** `for (int sell = n.length - 1; sell > buy; sell--)`
- Iterate through all sell days after buy day
- Start from end (optimization: check highest prices first)
- `sell > buy` ensures sell happens after buy

**Line 12:** `int profit = n[sell] - n[buy];`
- Calculate profit for this buy-sell pair
- Profit = selling price - buying price

**Lines 15-19:** Update maximum
- If this profit > current Max_Profit
- Update Max_Profit, Buy_on, Sell_on

### Example Execution

```
Prices: [100, 180, 260, 310, 40, 535, 695]
Indices:  0    1    2    3   4    5    6

buy=0 (100):
  sell=6 (695): profit = 695-100 = 595 ✓ NEW MAX
  sell=5 (535): profit = 535-100 = 435
  sell=4 (40):  profit = 40-100 = -60
  ... (continue checking)

buy=1 (180):
  sell=6 (695): profit = 695-180 = 515
  ... (no improvement)

buy=4 (40):
  sell=6 (695): profit = 695-40 = 655 ✓ NEW MAX
  sell=5 (535): profit = 535-40 = 495

Result: Max Profit = 655 (buy at index 4, sell at index 6)
```

### Time Complexity: O(n²)
- Nested loops: n × n comparisons
- Better algorithm exists: O(n) using single pass

---

## Bidding Process Simulation

### Concept
Collect bids from users, store in list, find maximum bid.

### Implementation (Biding_Process.java)

```java
package Queue_Examples;

import java.util.*;

public class Biding_Process {
    public static void main(String[] args) {
        String bid;
        List<Integer> bid_list = new ArrayList<>();
        Scanner in = new Scanner(System.in);
        
        while (true) {  // Infinite loop
            System.out.println("Enter bid(blank to stop):");
            bid = in.nextLine().trim();
            
            // Blank check
            if (bid.isEmpty())  // Check if empty
                break;
            else {
                bid_list.add(Integer.parseInt(bid));  // Store in list
            }
        }
        
        System.out.println("Bids are:" + bid_list);
        System.out.println("Max is :" + Collections.max(bid_list));
    }
}
```

### Line-by-Line Explanation

**Line 9:** `List<Integer> bid_list = new ArrayList<>();`
- Create ArrayList to store integer bids
- Dynamic size (grows as needed)

**Line 12:** `while (true)`
- Infinite loop
- Continues until `break` is executed

**Line 14:** `bid = in.nextLine().trim();`
- Read entire line as string
- `trim()` removes leading/trailing whitespace

**Line 17:** `if (bid.isEmpty())`
- Check if string is empty (user pressed Enter without typing)
- `isEmpty()` better than `== ""` for string comparison

**Line 18:** `break;`
- Exit the while loop

**Line 21:** `bid_list.add(Integer.parseInt(bid));`
- `Integer.parseInt(bid)` converts String to int
- Add to ArrayList

**Line 25:** `Collections.max(bid_list)`
- utility method to find maximum value in collection

### Sample Run

```
Enter bid(blank to stop):
100
Enter bid(blank to stop):
250
Enter bid(blank to stop):
180
Enter bid(blank to stop):
320
Enter bid(blank to stop):
150
Enter bid(blank to stop):

Bids are:[100, 250, 180, 320, 150]
Max is :320
```

---

## Summary

### Applications Covered

1. **Two Queues in Array**: Space-efficient dual queue implementation
2. **Binary Sequence**: Clever queue usage for sequence generation
3. **Max Profit**: Algorithmic problem solving
4. **Bidding Process**: Practical simulation using collections

### Key Takeaways

- Queues aren't just for task scheduling
- Creative use solves various problems
- Java Collections Framework provides powerful tools
- Algorithm efficiency matters (O(n²) vs O(n))

---

**These applications demonstrate queue versatility in problem-solving!**
