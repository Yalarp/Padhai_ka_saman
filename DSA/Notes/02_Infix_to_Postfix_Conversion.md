# Infix to Postfix Conversion using Stack

## Table of Contents
1. [Expression Notations](#expression-notations)
2. [Why Convert Infix to Postfix?](#why-convert-infix-to-postfix)
3. [Operator Precedence and Associativity](#operator-precedence-and-associativity)
4. [Conversion Algorithm](#conversion-algorithm)
5. [Detailed Code Implementation](#detailed-code-implementation)
6. [Step-by-Step Examples](#step-by-step-examples)
7. [Complete Working Example](#complete-working-example)
8. [Practice Problems](#practice-problems)

---

## Expression Notations

### 1. **Infix Notation** (Human-Readable)
- Operator is placed **between** operands
- Natural way humans write expressions
- **Requires parentheses** for precedence

**Examples:**
```
A + B
A * B + C
(A + B) * C
A + B * C - D / E
```

### 2. **Postfix Notation** (Reverse Polish Notation - RPN)
- Operator is placed **after** operands
- **No parentheses needed**
- Easy for computers to evaluate

**Examples:**
```
Infix: A + B       → Postfix: A B +
Infix: A * B + C   → Postfix: A B * C +
Infix: (A + B) * C → Postfix: A B + C *
```

### 3. **Prefix Notation** (Polish Notation)
- Operator is placed **before** operands
- Also no parentheses needed

**Examples:**
```
Infix: A + B       → Prefix: + A B
Infix: A * B + C   → Prefix: + * A B C
```

### Notation Comparison Table

| Infix | Postfix | Prefix | Description |
|-------|---------|--------|-------------|
| `A + B` | `A B +` | `+ A B` | Simple addition |
| `A - B` | `A B -` | `- A B` | Simple subtraction |
| `A * B + C` | `A B * C +` | `+ * A B C` | Multiplication then addition |
| `(A + B) * C` | `A B + C *` | `* + A B C` | Addition then multiplication |
| `A + B * C` | `A B C * +` | `+ A * B C` | Precedence matters |

---

## Why Convert Infix to Postfix?

### ❌ Problems with Infix Notation

1. **Ambiguity without Parentheses**
   ```
   A + B * C
   Could mean: (A + B) * C  or  A + (B * C)  ?
   Need to know operator precedence
   ```

2. **Complex Parsing**
   - Must handle operator precedence
   - Must handle parentheses
   - Must handle associativity
   - Requires lookahead

3. **Stack-Based Evaluation is Difficult**
   - Cannot directly evaluate left to right
   - Need complex algorithms

### ✅ Benefits of Postfix Notation

1. **No Ambiguity**
   ```
   A B C * +  is unambiguous
   Always means: A + (B * C)
   ```

2. **No Parentheses Needed**
   - Precedence is implicit in position
   - Reduces expression length

3. **Easy Evaluation**
   ```
   Read left to right:
   - Operand? Push to stack
   - Operator? Pop two operands, compute, push result
   ```

4. **Efficient for Computers**
   - Simple algorithm
   - Single pass
   - Natural stack usage

### Real-World Usage

- **Calculators**: Many scientific calculators use RPN
- **Compilers**: Convert infix to postfix internally
- **Expression Evaluation**: Stack-based virtual machines
- **Programming Languages**: Forth, PostScript use postfix

---

## Operator Precedence and Associativity

### Operator Precedence Table

| Priority | Operators | Name | Example |
|----------|-----------|------|---------|
| **Highest (3)** | `^` | Exponentiation | `2 ^ 3 = 8` |
| **Medium (2)** | `*`, `/`, `%` | Multiplication, Division, Modulo | `4 * 5 = 20` |
| **Lowest (1)** | `+`, `-` | Addition, Subtraction | `3 + 4 = 7` |

### Associativity Rules

**Left-to-Right Associativity** (Most operators)
```
A + B + C  ≡  (A + B) + C
A - B - C  ≡  (A - B) - C
A * B * C  ≡  (A * B) * C
```

**Right-to-Left Associativity** (Exponentiation)
```
A ^ B ^ C  ≡  A ^ (B ^ C)
2 ^ 3 ^ 2  ≡  2 ^ (3 ^ 2) = 2 ^ 9 = 512
NOT (2 ^ 3) ^ 2 = 8 ^ 2 = 64
```

### Precedence Examples

```
Expression: A + B * C
  * has higher precedence than +
  Equivalent: A + (B * C)
  Postfix: A B C * +

Expression: A * B + C
  * has higher precedence than +
  Equivalent: (A * B) + C
  Postfix: A B * C +

Expression: A + B + C
  Both are +, same precedence
  Left-to-right: (A + B) + C
  Postfix: A B + C +
```

---

## Conversion Algorithm

### High-Level Strategy

1. **Scan infix expression left to right**
2. **For operands (A, B, C, etc.)**: Add directly to output
3. **For operators (+, -, *, /, etc.)**: 
   - Pop operators from stack with higher/equal precedence
   - Push current operator to stack
4. **For left parenthesis '('**: Push to stack
5. **For right parenthesis ')'**: 
   - Pop operators until '(' is found
   - Discard both parentheses
6. **At end**: Pop remaining operators to output

### Detailed Algorithm Steps

```
Algorithm InfixToPostfix(infix):
  1. Create empty stack for operators
  2. Create empty string for postfix result
  3. 
  4. For each character 'ch' in infix expression:
       a. If ch is operand (letter/digit):
            Append ch to postfix result
       
       b. If ch is '(':
            Push '(' onto stack
       
       c. If ch is ')':
            While stack top is not '(':
              Pop operator from stack
              Append to postfix result
            Pop '(' from stack (discard it)
       
       d. If ch is operator (+, -, *, /, ^):
            While (stack not empty) AND 
                  (top of stack is not '(') AND
                  (precedence of top >= precedence of ch):
              Pop operator from stack
              Append to postfix result
            Push ch onto stack
  
  5. While stack is not empty:
       Pop operator from stack
       Append to postfix result
  
  6. Return postfix result
```

---

## Detailed Code Implementation

### Helper Function: Operator Precedence

```java
int precedence(char operator) {
    switch(operator) {
        case '+':
        case '-':
            return 1;    // Lowest precedence
        
        case '*':
        case '/':
        case '%':
            return 2;    // Medium precedence
        
        case '^':
            return 3;    // Highest precedence
        
        default:
            return 0;    // For '(' or invalid characters
    }
}
```

#### **Line-by-Line Explanation:**

- **`switch(operator)`**: Checks which operator we have
- **`case '+': case '-': return 1;`**: Addition and subtraction have priority 1
- **`case '*': case '/': case '%': return 2;`**: Multiplication, division, modulo have priority 2
- **`case '^': return 3;`**: Exponentiation has highest priority 3
- **`default: return 0;`**: Parentheses or unknown characters get priority 0

**Usage:**
```
precedence('+') → 1
precedence('*') → 2
precedence('^') → 3
precedence('(') → 0
```

---

### Helper Function: Check if Character is Operator

```java
boolean isOperator(char ch) {
    return (ch == '+' || ch == '-' || ch == '*' || 
            ch == '/' || ch == '%' || ch == '^');
}
```

#### **Line-by-Line Explanation:**

- **Checks if character is one of the valid operators**
- **Returns true** if ch is an operator
- **Returns false** otherwise

**Usage:**
```
isOperator('+') → true
isOperator('A') → false
isOperator('5') → false
```

---

### Main Conversion Function

```java
String infixToPostfix(String infix) {
    // Step 1: Create a stack to store operators
    Stack<Character> stack = new Stack<>();
    
    // Step 2: Create StringBuilder for result (more efficient than String concatenation)
    StringBuilder postfix = new StringBuilder();
    
    // Step 3: Process each character in the infix expression
    for (int i = 0; i < infix.length(); i++) {
        char ch = infix.charAt(i);
        
        // Case 1: If character is alphanumeric (operand), add to output
        if (Character.isLetterOrDigit(ch)) {
            postfix.append(ch);
        }
        
        // Case 2: If character is '(', push to stack
        else if (ch == '(') {
            stack.push(ch);
        }
        
        // Case 3: If character is ')', pop until '(' is found
        else if (ch == ')') {
            while (!stack.isEmpty() && stack.peek() != '(') {
                postfix.append(stack.pop());
            }
            stack.pop(); // Remove '(' from stack
        }
        
        // Case 4: If character is an operator
        else if (isOperator(ch)) {
            while (!stack.isEmpty() && 
                   stack.peek() != '(' &&
                   precedence(stack.peek()) >= precedence(ch)) {
                postfix.append(stack.pop());
            }
            stack.push(ch);
        }
    }
    
    // Step 4: Pop all remaining operators from stack
    while (!stack.isEmpty()) {
        postfix.append(stack.pop());
    }
    
    // Step 5: Return the postfix expression
    return postfix.toString();
}
```

#### **Detailed Line-by-Line Explanation:**

**Line 2-3: Initialization**
```java
Stack<Character> stack = new Stack<>();
StringBuilder postfix = new StringBuilder();
```
- Creates a stack to temporarily hold operators
- Creates a StringBuilder for efficient string building
- **Why StringBuilder?** String concatenation in loop is inefficient (O(n²))

**Line 6-7: Loop through each character**
```java
for (int i = 0; i < infix.length(); i++) {
    char ch = infix.charAt(i);
```
- Iterate through infix string character by character
- Extract current character into `ch` variable

**Line 10-12: Handle Operands**
```java
if (Character.isLetterOrDigit(ch)) {
    postfix.append(ch);
}
```
- **`Character.isLetterOrDigit(ch)`**: Checks if ch is A-Z, a-z, or 0-9
- **Operands go directly to output** without processing
- No precedence needed for operands

**Example:**
```
Input: 'A' → Append 'A' to postfix
Input: '5' → Append '5' to postfix
Input: '+' → Process as operator (next case)
```

**Line 15-17: Handle Left Parenthesis**
```java
else if (ch == '(') {
    stack.push(ch);
}
```
- **Push '(' to stack** to mark beginning of subexpression
- Will be popped when ')' is encountered
- Acts as a boundary marker

**Line 20-24: Handle Right Parenthesis**
```java
else if (ch == ')') {
    while (!stack.isEmpty() && stack.peek() != '(') {
        postfix.append(stack.pop());
    }
    stack.pop(); // Remove '(' from stack
}
```

**Detailed breakdown:**
- **Line 21: Condition check**
  - `!stack.isEmpty()`: Ensure stack has elements
  - `stack.peek() != '('`: Keep popping until we find '('
  
- **Line 22: Pop and append**
  - Pops operator from stack
  - Appends it to postfix output
  
- **Line 24: Remove '('**
  - Pops the '(' but doesn't add to output
  - Parentheses are not part of postfix

**Example Flow:**
```
Infix: (A+B)
Stack during processing:
  Read '(': stack = ['(']
  Read 'A': postfix = "A", stack = ['(']
  Read '+': stack = ['(', '+']
  Read 'B': postfix = "AB", stack = ['(', '+']
  Read ')': 
    Pop '+': postfix = "AB+", stack = ['(']
    Pop '(': stack = []
Final: "AB+"
```

**Line 27-33: Handle Operators**
```java
else if (isOperator(ch)) {
    while (!stack.isEmpty() && 
           stack.peek() != '(' &&
           precedence(stack.peek()) >= precedence(ch)) {
        postfix.append(stack.pop());
    }
    stack.push(ch);
}
```

**Detailed breakdown:**

- **Line 28-30: While loop conditions**
  - `!stack.isEmpty()`: Stack must have operators
  - `stack.peek() != '('`: Don't pop past '('
  - `precedence(stack.peek()) >= precedence(ch)`: Higher or equal precedence operators must be popped

- **Line 31: Pop higher precedence operators**
  - Pop operator with higher/equal precedence
  - Add to postfix output
  - **Why?** Higher precedence must be evaluated first

- **Line 33: Push current operator**
  - After popping all higher precedence operators
  - Push current operator to stack

**Example Flow:**
```
Infix: A + B * C
Process '+':
  Stack empty, push '+'
  Stack = ['+']

Process '*':
  Top = '+' (precedence 1)
  * has precedence 2 (higher than +)
  Don't pop '+'
  Push '*'
  Stack = ['+', '*']

At end:
  Pop '*': postfix includes "BC*"
  Pop '+': postfix becomes "ABC*+"
```

**Line 37-39: Pop Remaining Operators**
```java
while (!stack.isEmpty()) {
    postfix.append(stack.pop());
}
```
- After processing all characters
- Pop all remaining operators from stack
- Add them to postfix in reverse order

**Line 42: Return Result**
```java
return postfix.toString();
```
- Convert StringBuilder to String
- Return final postfix expression

---

## Step-by-Step Examples

### Example 1: Simple Expression `A + B`

| Step | Input | Stack | Postfix | Action |
|------|-------|-------|---------|--------|
| 0 | `A + B` | `[]` | `""` | Start |
| 1 | `+ B` | `[]` | `"A"` | 'A' is operand → append |
| 2 | `B` | `[+]` | `"A"` | '+' is operator → push |
| 3 | - | `[+]` | `"AB"` | 'B' is operand → append |
| 4 | - | `[]` | `"AB+"` | End: pop '+' |

**Result:** `AB+`

---

### Example 2: Precedence `A + B * C`

| Step | Input | Stack | Postfix | Action |
|------|-------|-------|---------|--------|
| 0 | `A + B * C` | `[]` | `""` | Start |
| 1 | `+ B * C` | `[]` | `"A"` | 'A' → append |
| 2 | `B * C` | `[+]` | `"A"` | '+' → push |
| 3 | `* C` | `[+]` | `"AB"` | 'B' → append |
| 4 | `C` | `[+, *]` | `"AB"` | '*' has higher precedence → push |
| 5 | - | `[+, *]` | `"ABC"` | 'C' → append |
| 6 | - | `[+]` | `"ABC*"` | Pop '*' |
| 7 | - | `[]` | `"ABC*+"` | Pop '+' |

**Result:** `ABC*+`

**Verification:**
```
Original: A + B * C
Precedence: A + (B * C)  [* before +]
Postfix: A B C * +
Evaluation: Push A, Push B, Push C, 
            Pop C and B, compute B*C, Push result,
            Pop result and A, compute A+result
```

---

### Example 3: Parentheses `(A + B) * C`

| Step | Input | Stack | Postfix | Action |
|------|-------|-------|---------|--------|
| 0 | `(A + B) * C` | `[]` | `""` | Start |
| 1 | `A + B) * C` | `[(]` | `""` | '(' → push |
| 2 | `+ B) * C` | `[(]` | `"A"` | 'A' → append |
| 3 | `B) * C` | `[(, +]` | `"A"` | '+' → push |
| 4 | `) * C` | `[(, +]` | `"AB"` | 'B' → append |
| 5 | `* C` | `[]` | `"AB+"` | ')' → pop until '(', discard '(' |
| 6 | `C` | `[*]` | `"AB+"` | '*' → push |
| 7 | - | `[*]` | `"AB+C"` | 'C' → append |
| 8 | - | `[]` | `"AB+C*"` | End: pop '*' |

**Result:** `AB+C*`

**Verification:**
```
Original: (A + B) * C
Parentheses force: Must do A+B first
Postfix: A B + C *
Evaluation: Push A, Push B, Pop and add (A+B), Push result,
            Push C, Pop C and result, multiply, Push final
```

---

### Example 4: Complex Expression `A + B * C - D / E`

| Step | Input | Stack | Postfix | Explanation |
|------|-------|-------|---------|-------------|
| 1 | Process A | `[]` | `A` | Operand → append |
| 2 | Process + | `[+]` | `A` | Push + |
| 3 | Process B | `[+]` | `AB` | Operand → append |
| 4 | Process * | `[+,*]` | `AB` | * > +, push * |
| 5 | Process C | `[+,*]` | `ABC` | Operand → append |
| 6 | Process - | `[+]` | `ABC*` | Pop *, check +, + == -, pop + |
| 7 | | `[-]` | `ABC*+` | Push - |
| 8 | Process D | `[-]` | `ABC*+D` | Operand → append |
| 9 | Process / | `[-,/]` | `ABC*+D` | / > -, push / |
| 10 | Process E | `[-,/]` | `ABC*+DE` | Operand → append |
| 11 | End | `[-]` | `ABC*+DE/` | Pop / |
| 12 | | `[]` | `ABC*+DE/-` | Pop - |

**Result:** `ABC*+DE/-`

**Verification:**
```
Original: A + B * C - D / E
Precedence: A + (B * C) - (D / E)
Grouping: ((A + (B * C)) - (D / E))

Step-by-step:
1. B * C
2. A + result1
3. D / E
4. result2 - result3
```

---

## Complete Working Example

### Full Implementation with Main Method

```java
package Stack_Examples;

import java.util.Stack;
import java.util.Scanner;

public class InfixToPostfix {
    
    // Method to check if character is an operator
    boolean isOperator(char ch) {
        return (ch == '+' || ch == '-' || ch == '*' || 
                ch == '/' || ch == '%' || ch == '^');
    }
    
    // Method to return precedence of operators
    int precedence(char operator) {
        switch(operator) {
            case '+':
            case '-':
                return 1;
            
            case '*':
            case '/':
            case '%':
                return 2;
            
            case '^':
                return 3;
            
            default:
                return 0;
        }
    }
    
    // Main conversion method
    String infixToPostfix(String infix) {
        Stack<Character> stack = new Stack<>();
        StringBuilder postfix = new StringBuilder();
        
        for (int i = 0; i < infix.length(); i++) {
            char ch = infix.charAt(i);
            
            // Skip spaces
            if (ch == ' ') {
                continue;
            }
            
            // If operand, add to output
            if (Character.isLetterOrDigit(ch)) {
                postfix.append(ch);
            }
            
            // If '(', push to stack
            else if (ch == '(') {
                stack.push(ch);
            }
            
            // If ')', pop until '('
            else if (ch == ')') {
                while (!stack.isEmpty() && stack.peek() != '(') {
                    postfix.append(stack.pop());
                }
                if (!stack.isEmpty()) {
                    stack.pop(); // Remove '('
                }
            }
            
            // If operator
            else if (isOperator(ch)) {
                while (!stack.isEmpty() && 
                       stack.peek() != '(' &&
                       precedence(stack.peek()) >= precedence(ch)) {
                    postfix.append(stack.pop());
                }
                stack.push(ch);
            }
        }
        
        // Pop remaining operators
        while (!stack.isEmpty()) {
            postfix.append(stack.pop());
        }
        
        return postfix.toString();
    }
    
    // Main method for testing
    public static void main(String[] args) {
        InfixToPostfix converter = new InfixToPostfix();
        Scanner scanner = new Scanner(System.in);
        
        System.out.println("=== Infix to Postfix Converter ===");
        System.out.print("Enter infix expression: ");
        String infix = scanner.nextLine();
        
        String postfix = converter.infixToPostfix(infix);
        
        System.out.println("Infix Expression:   " + infix);
        System.out.println("Postfix Expression: " + postfix);
        
        scanner.close();
    }
}
```

### Sample Runs

**Run 1:**
```
=== Infix to Postfix Converter ===
Enter infix expression: A+B*C
Infix Expression:   A+B*C
Postfix Expression: ABC*+
```

**Run 2:**
```
=== Infix to Postfix Converter ===
Enter infix expression: (A+B)*(C-D)
Infix Expression:   (A+B)*(C-D)
Postfix Expression: AB+CD-*
```

**Run 3:**
```
=== Infix to Postfix Converter ===
Enter infix expression: A+B*C-D/E
Infix Expression:   A+B*C-D/E
Postfix Expression: ABC*+DE/-
```

---

## Practice Problems

### Problem 1: Convert the following

| Infix | Postfix (Your Answer) | Correct Answer |
|-------|----------------------|----------------|
| `A + B - C` | ? | `AB+C-` |
| `A * B + C * D` | ? | `AB*CD*+` |
| `A + B * C + D` | ? | `ABC*+D+` |
| `(A + B) * (C + D)` | ? | `AB+CD+*` |
| `A ^ B ^ C` | ? | `ABC^^` |

### Problem 2: Add Associativity

Modify the algorithm to handle right-to-left associativity for `^`:

**Hint:** Change the condition to `precedence(top) > precedence(ch)` for right-associative operators.

```java
// Modified condition for ^
if (ch == '^') {
    while (!stack.isEmpty() && 
           stack.peek() != '(' &&
           precedence(stack.peek()) > precedence(ch)) {  // > instead of >=
        postfix.append(stack.pop());
    }
    stack.push(ch);
}
```

### Problem 3: Complex Expression

Convert: `((A + B) * C - (D - E)) ^ (F + G)`

**Solution:**
```
Step-by-step conversion:
1. (A + B) → AB+
2. AB+ * C → AB+C*
3. (D - E) → DE-
4. AB+C* - DE- → AB+C*DE--
5. (F + G) → FG+
6. AB+C*DE-- ^ FG+ → AB+C*DE--FG+^

Answer: AB+C*DE--FG+^
```

---

## Summary

### Key Concepts

1. **Postfix has no parentheses** - Precedence is implicit

2. **Operands always go directly to output**

3. **Operators are pushed to stack after popping higher/equal precedence operators**

4. **Parentheses control evaluation order:**
   - `(` marks start of subexpression
   - `)` triggers popping until `(`

5. **Precedence levels:**
   - `^` = 3 (highest)
   - `*`, `/`, `%` = 2
   - `+`, `-` = 1 (lowest)

6. **Algorithm is single-pass** - O(n) time complexity

### Common Mistakes to Avoid

❌ **Forgetting to pop remaining operators at end**
❌ **Not handling parentheses correctly**
❌ **Wrong precedence comparisons (≥ vs >)**
❌ **Not checking for empty stack before peek/pop**

### Next Steps

- Learn [Postfix Expression Evaluation](file:///c:/Users/2706p/Desktop/mcq/java/03_Stack_Applications.md)
- Practice with complex expressions
- Implement infix to prefix conversion
- Build a calculator using these concepts

---

**Mastery Checklist:**
- [ ] Understand why postfix is better for computers
- [ ] Memorize operator precedence
- [ ] Trace algorithm with examples
- [ ] Handle parentheses correctly
- [ ] Write code from scratch
- [ ] Solve practice problems

