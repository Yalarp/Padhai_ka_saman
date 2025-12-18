# 🔢 Operators in Java

## Table of Contents
1. [Introduction to Operators](#introduction-to-operators)
2. [Arithmetic Operators](#arithmetic-operators)
3. [Relational Operators](#relational-operators)
4. [Logical Operators](#logical-operators)
5. [Assignment Operators](#assignment-operators)
6. [Unary Operators](#unary-operators)
7. [Ternary Operator](#ternary-operator)
8. [Bitwise Operators](#bitwise-operators)
9. [Shift Operators](#shift-operators)
10. [Number System Basics](#number-system-basics)
11. [Operator Precedence](#operator-precedence)
12. [Interview Questions](#interview-questions)

---

## Introduction to Operators

**Operators** are special symbols that perform operations on operands (variables/values).

### Classification by Operands:

| Type | Operands | Example |
|------|----------|---------|
| Unary | 1 | `++a`, `!b` |
| Binary | 2 | `a + b`, `x > y` |
| Ternary | 3 | `a ? b : c` |

---

## Arithmetic Operators

Used for mathematical calculations.

| Operator | Name | Example | Result |
|----------|------|---------|--------|
| `+` | Addition | 10 + 3 | 13 |
| `-` | Subtraction | 10 - 3 | 7 |
| `*` | Multiplication | 10 * 3 | 30 |
| `/` | Division | 10 / 3 | 3 |
| `%` | Modulus (Remainder) | 10 % 3 | 1 |

### Important Notes:

```java
public class ArithmeticDemo {
    public static void main(String[] args) {
        int a = 10, b = 3;
        
        System.out.println("Addition: " + (a + b));       // 13
        System.out.println("Subtraction: " + (a - b));    // 7
        System.out.println("Multiplication: " + (a * b)); // 30
        System.out.println("Division: " + (a / b));       // 3 (integer division)
        System.out.println("Modulus: " + (a % b));        // 1
        
        // Integer division truncates decimal part
        int result = 10 / 3;     // Result: 3 (not 3.33)
        
        // To get decimal result, use double
        double result2 = 10.0 / 3;  // Result: 3.333...
        
        // Modulus gives remainder
        int remainder = 17 % 5;  // Result: 2 (17 = 5*3 + 2)
    }
}
```

---

## Relational Operators

Used to compare values. Always return `boolean` (true/false).

| Operator | Name | Example | Result |
|----------|------|---------|--------|
| `==` | Equal to | 5 == 5 | true |
| `!=` | Not equal to | 5 != 3 | true |
| `>` | Greater than | 5 > 3 | true |
| `<` | Less than | 5 < 3 | false |
| `>=` | Greater than or equal | 5 >= 5 | true |
| `<=` | Less than or equal | 5 <= 3 | false |

```java
public class RelationalDemo {
    public static void main(String[] args) {
        int x = 10, y = 20;
        
        System.out.println(x == y);  // false
        System.out.println(x != y);  // true
        System.out.println(x > y);   // false
        System.out.println(x < y);   // true
        System.out.println(x >= 10); // true
        System.out.println(y <= 15); // false
    }
}
```

---

## Logical Operators

Used to combine multiple conditions.

| Operator | Name | Description |
|----------|------|-------------|
| `&&` | Logical AND | true if BOTH conditions are true |
| `\|\|` | Logical OR | true if AT LEAST ONE condition is true |
| `!` | Logical NOT | Inverts the boolean value |

### Truth Tables:

**AND (&&)**
| A | B | A && B |
|---|---|--------|
| true | true | true |
| true | false | false |
| false | true | false |
| false | false | false |

**OR (||)**
| A | B | A \|\| B |
|---|---|----------|
| true | true | true |
| true | false | true |
| false | true | true |
| false | false | false |

**NOT (!)**
| A | !A |
|---|-----|
| true | false |
| false | true |

### Short-Circuit Evaluation:

```java
public class LogicalDemo {
    public static void main(String[] args) {
        int a = 5, b = 10;
        
        // AND: Both must be true
        System.out.println(a > 0 && b > 0);  // true
        System.out.println(a > 0 && b < 0);  // false
        
        // OR: At least one must be true
        System.out.println(a > 0 || b < 0);  // true
        System.out.println(a < 0 || b < 0);  // false
        
        // NOT: Inverts
        System.out.println(!true);   // false
        System.out.println(!(a > b)); // true
        
        // Short-circuit: Second condition not evaluated if result known
        int x = 0;
        // In &&, if first is false, second is skipped
        if (x != 0 && 10/x > 1) {  // 10/x never executes (no error)
            System.out.println("Won't reach here");
        }
    }
}
```

---

## Assignment Operators

Used to assign values to variables.

| Operator | Example | Equivalent To |
|----------|---------|---------------|
| `=` | a = 5 | a = 5 |
| `+=` | a += 5 | a = a + 5 |
| `-=` | a -= 5 | a = a - 5 |
| `*=` | a *= 5 | a = a * 5 |
| `/=` | a /= 5 | a = a / 5 |
| `%=` | a %= 5 | a = a % 5 |

```java
public class AssignmentDemo {
    public static void main(String[] args) {
        int a = 10;
        
        a += 5;   // a = 10 + 5 = 15
        System.out.println(a);  // 15
        
        a -= 3;   // a = 15 - 3 = 12
        System.out.println(a);  // 12
        
        a *= 2;   // a = 12 * 2 = 24
        System.out.println(a);  // 24
        
        a /= 4;   // a = 24 / 4 = 6
        System.out.println(a);  // 6
        
        a %= 4;   // a = 6 % 4 = 2
        System.out.println(a);  // 2
    }
}
```

---

## Unary Operators

Operate on a single operand.

| Operator | Name | Description |
|----------|------|-------------|
| `+` | Unary plus | Indicates positive value |
| `-` | Unary minus | Negates value |
| `++` | Increment | Increases by 1 |
| `--` | Decrement | Decreases by 1 |
| `!` | Logical NOT | Inverts boolean |

### Pre vs Post Increment/Decrement:

```java
public class UnaryDemo {
    public static void main(String[] args) {
        int a = 10;
        
        // Pre-increment: Increment FIRST, then use
        System.out.println(++a);  // Output: 11 (a becomes 11, then printed)
        
        // Post-increment: Use FIRST, then increment
        System.out.println(a++);  // Output: 11 (printed 11, then a becomes 12)
        System.out.println(a);    // Output: 12
        
        // Pre-decrement: Decrement FIRST, then use  
        System.out.println(--a);  // Output: 11
        
        // Post-decrement: Use FIRST, then decrement
        System.out.println(a--);  // Output: 11
        System.out.println(a);    // Output: 10
    }
}
```

### Flow Visualization:

```
Initial: a = 10

++a (Pre-increment):
  Step 1: a = a + 1 = 11
  Step 2: Use value (11)
  
a++ (Post-increment):
  Step 1: Use current value
  Step 2: a = a + 1
```

---

## Ternary Operator

The only operator with **three operands**. Also called conditional operator.

### Syntax:
```java
condition ? valueIfTrue : valueIfFalse
```

### Example:

```java
public class TernaryDemo {
    public static void main(String[] args) {
        int a = 10, b = 20;
        
        // Simple ternary
        int max = (a > b) ? a : b;
        System.out.println("Maximum: " + max);  // 20
        
        // Equivalent if-else:
        // if (a > b) max = a;
        // else max = b;
        
        // Ternary with String
        String result = (a % 2 == 0) ? "Even" : "Odd";
        System.out.println(a + " is " + result);  // 10 is Even
        
        // Nested ternary (use sparingly - can be confusing)
        int x = 5, y = 10, z = 15;
        int greatest = (x > y) ? (x > z ? x : z) : (y > z ? y : z);
        System.out.println("Greatest: " + greatest);  // 15
    }
}
```

### Nested Ternary Flow:

```
Given: x=5, y=10, z=15

(x > y) ? (x > z ? x : z) : (y > z ? y : z)

Step 1: Is x > y? (5 > 10) → FALSE
Step 2: Go to FALSE branch: (y > z ? y : z)
Step 3: Is y > z? (10 > 15) → FALSE  
Step 4: Return z → 15
```

---

## Bitwise Operators

Operate on individual bits of integers.

| Operator | Name | Description |
|----------|------|-------------|
| `&` | Bitwise AND | 1 if both bits are 1 |
| `\|` | Bitwise OR | 1 if at least one bit is 1 |
| `^` | Bitwise XOR | 1 if bits are different |
| `~` | Bitwise NOT | Inverts all bits |

### Truth Tables:

**AND (&)**
| Bit A | Bit B | A & B |
|-------|-------|-------|
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

**OR (|)**
| Bit A | Bit B | A \| B |
|-------|-------|--------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 1 |

**XOR (^)**
| Bit A | Bit B | A ^ B |
|-------|-------|-------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

### Bitwise Examples:

```java
public class BitwiseDemo {
    public static void main(String[] args) {
        int a = 5;   // Binary: 0101
        int b = 3;   // Binary: 0011
        
        // AND: Both bits must be 1
        System.out.println(a & b);  // 0101 & 0011 = 0001 = 1
        
        // OR: At least one bit must be 1
        System.out.println(a | b);  // 0101 | 0011 = 0111 = 7
        
        // XOR: Bits must be different
        System.out.println(a ^ b);  // 0101 ^ 0011 = 0110 = 6
        
        // NOT: Inverts all bits (includes sign bit)
        System.out.println(~a);     // ~0101 = ...11111010 = -6
    }
}
```

### Step-by-Step Bitwise AND:

```
a = 5 → Binary: 0101
b = 3 → Binary: 0011
          
  0 1 0 1
& 0 0 1 1
---------
  0 0 0 1 = 1

Explanation:
Position 3: 0 & 0 = 0
Position 2: 1 & 0 = 0
Position 1: 0 & 1 = 0
Position 0: 1 & 1 = 1
```

---

## Shift Operators

Move bits left or right.

| Operator | Name | Description |
|----------|------|-------------|
| `<<` | Left shift | Shifts bits left, fills with 0 |
| `>>` | Right shift (signed) | Shifts bits right, preserves sign |
| `>>>` | Right shift (unsigned) | Shifts bits right, fills with 0 |

### Left Shift (<<):

```java
// Left shift: Multiply by 2^n
int a = 5;        // Binary: 00000101
int result = a << 2;  // Shift left by 2 positions

// 00000101 << 2 = 00010100 = 20
// Formula: a * 2^n = 5 * 2^2 = 5 * 4 = 20
```

### Right Shift (>>):

```java
// Right shift: Divide by 2^n
int a = 20;       // Binary: 00010100
int result = a >> 2;  // Shift right by 2 positions

// 00010100 >> 2 = 00000101 = 5
// Formula: a / 2^n = 20 / 2^2 = 20 / 4 = 5
```

### Complete Shift Example:

```java
public class ShiftDemo {
    public static void main(String[] args) {
        int a = 8;  // Binary: 00001000
        
        // Left shift by 1 (multiply by 2)
        System.out.println(a << 1);  // 16 (00010000)
        
        // Left shift by 2 (multiply by 4)
        System.out.println(a << 2);  // 32 (00100000)
        
        // Right shift by 1 (divide by 2)
        System.out.println(a >> 1);  // 4 (00000100)
        
        // Right shift by 2 (divide by 4)
        System.out.println(a >> 2);  // 2 (00000010)
        
        // Negative number right shift
        int b = -8;
        System.out.println(b >> 1);   // -4 (signed, preserves negative)
        System.out.println(b >>> 1);  // Large positive (unsigned)
    }
}
```

### Visual Representation:

```
Left Shift (<<):
Original:  0 0 0 0 1 0 0 0  (8)
<< 1:      0 0 0 1 0 0 0 0  (16)
<< 2:      0 0 1 0 0 0 0 0  (32)

Right Shift (>>):
Original:  0 0 0 0 1 0 0 0  (8)
>> 1:      0 0 0 0 0 1 0 0  (4)
>> 2:      0 0 0 0 0 0 1 0  (2)
```

---

## Number System Basics

Understanding number systems is crucial for bitwise operations.

### Decimal to Binary Conversion

**Method: Repeated Division by 2**

```
Convert 35 to Binary:

35 ÷ 2 = 17 remainder 1
17 ÷ 2 = 8  remainder 1
8  ÷ 2 = 4  remainder 0
4  ÷ 2 = 2  remainder 0
2  ÷ 2 = 1  remainder 0
1  ÷ 2 = 0  remainder 1

Read remainders bottom-up: 100011
So, 35 = 100011 in binary
```

### Binary to Decimal Conversion

```
Convert 100011 to Decimal:

Position:  5  4  3  2  1  0
Binary:    1  0  0  0  1  1
Value:    32  0  0  0  2  1

= 1×2^5 + 0×2^4 + 0×2^3 + 0×2^2 + 1×2^1 + 1×2^0
= 32 + 0 + 0 + 0 + 2 + 1
= 35
```

### Negative Numbers (2's Complement)

For negative numbers, Java uses **2's complement** representation.

**Steps to find 2's complement:**
1. Find binary of positive number
2. Invert all bits (1's complement)
3. Add 1

```
Binary of -5:

Step 1: Binary of 5 = 00000101
Step 2: Invert bits = 11111010 (1's complement)
Step 3: Add 1       = 11111011 (2's complement)

So, -5 is represented as 11111011 in 8-bit binary
```

### Java Methods for Conversion:

```java
public class NumberSystemDemo {
    public static void main(String[] args) {
        int x = 35;
        
        // Decimal to other bases
        System.out.println(Integer.toBinaryString(x));  // 100011
        System.out.println(Integer.toOctalString(x));   // 43
        System.out.println(Integer.toHexString(x));     // 23
        
        // Other bases to decimal
        System.out.println(Integer.parseInt("100011", 2));  // 35
        System.out.println(Integer.parseInt("43", 8));      // 35
        System.out.println(Integer.parseInt("23", 16));     // 35
    }
}
```

### Number System Table:

| Decimal | Binary | Octal | Hexadecimal |
|---------|--------|-------|-------------|
| 0 | 0000 | 0 | 0 |
| 1 | 0001 | 1 | 1 |
| 2 | 0010 | 2 | 2 |
| 3 | 0011 | 3 | 3 |
| 4 | 0100 | 4 | 4 |
| 5 | 0101 | 5 | 5 |
| 6 | 0110 | 6 | 6 |
| 7 | 0111 | 7 | 7 |
| 8 | 1000 | 10 | 8 |
| 9 | 1001 | 11 | 9 |
| 10 | 1010 | 12 | A |
| 15 | 1111 | 17 | F |
| 16 | 10000 | 20 | 10 |

---

## Operator Precedence

Order in which operators are evaluated.

| Priority | Operators | Associativity |
|----------|-----------|---------------|
| 1 (Highest) | `()` `[]` `.` | Left to Right |
| 2 | `++` `--` `!` `~` (unary) | Right to Left |
| 3 | `*` `/` `%` | Left to Right |
| 4 | `+` `-` | Left to Right |
| 5 | `<<` `>>` `>>>` | Left to Right |
| 6 | `<` `<=` `>` `>=` | Left to Right |
| 7 | `==` `!=` | Left to Right |
| 8 | `&` | Left to Right |
| 9 | `^` | Left to Right |
| 10 | `\|` | Left to Right |
| 11 | `&&` | Left to Right |
| 12 | `\|\|` | Left to Right |
| 13 | `?:` | Right to Left |
| 14 (Lowest) | `=` `+=` `-=` etc. | Right to Left |

### Example:

```java
int result = 5 + 3 * 2;  // 11, not 16
// Multiplication first: 3 * 2 = 6
// Then addition: 5 + 6 = 11

int result2 = (5 + 3) * 2;  // 16
// Parentheses first: 5 + 3 = 8
// Then multiplication: 8 * 2 = 16
```

---

## Interview Questions

### Q1: What is the difference between / and % operators?
**Answer**: 
- `/` (Division) gives quotient
- `%` (Modulus) gives remainder

Example: 10/3 = 3, 10%3 = 1

### Q2: What is short-circuit evaluation?
**Answer**: In `&&` and `||`, if the result can be determined from the first operand, the second operand is not evaluated.
- `false && x`: x is not evaluated
- `true || x`: x is not evaluated

### Q3: What is the difference between & and &&?
**Answer**:
- `&`: Bitwise AND (evaluates both operands always)
- `&&`: Logical AND (short-circuit, may skip second operand)

### Q4: What is the output of ~5?
**Answer**: -6
- 5 in binary: 00000101
- ~5 inverts: 11111010 (2's complement of -6)

### Q5: What is left shift equivalent to?
**Answer**: `a << n` is equivalent to `a * 2^n`
Example: 5 << 2 = 5 * 4 = 20

### Q6: What is the difference between >> and >>>?
**Answer**:
- `>>`: Signed right shift, preserves sign bit
- `>>>`: Unsigned right shift, fills with 0

### Q7: What is the output of 5 ^ 5?
**Answer**: 0
- XOR of same numbers is always 0
- Useful property: `a ^ a = 0` and `a ^ 0 = a`

### Q8: Can we use ternary operator for statements?
**Answer**: No, ternary operator can only return values, not execute statements.
```java
// Valid
int x = (a > b) ? a : b;

// Invalid
(a > b) ? System.out.println(a) : System.out.println(b);  // Error
```

### Q9: What is 2's complement?
**Answer**: Method to represent negative numbers in binary.
Steps: Invert all bits (1's complement), then add 1.

### Q10: What is the precedence of arithmetic operators?
**Answer**: `*`, `/`, `%` have higher precedence than `+`, `-`

---

## Quick Reference

### All Operators at a Glance

```java
// Arithmetic
+ - * / %

// Relational
== != > < >= <=

// Logical
&& || !

// Assignment
= += -= *= /= %=

// Unary
++ -- + - !

// Ternary
condition ? value1 : value2

// Bitwise
& | ^ ~

// Shift
<< >> >>>
```

### Common Tricks

```java
// Check if even/odd
if ((n & 1) == 0) // even
if ((n & 1) == 1) // odd

// Multiply by 2^n
x << n

// Divide by 2^n
x >> n

// Swap without temp
a = a ^ b;
b = a ^ b;
a = a ^ b;
```

---

*Previous: [02_Data_Types_and_Variables.md](./02_Data_Types_and_Variables.md)*  
*Next: [04_Control_Flow.md](./04_Control_Flow.md)*
