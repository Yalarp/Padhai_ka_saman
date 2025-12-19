# C++ Manipulators

## Table of Contents
- [Introduction](#introduction)
- [Types of Manipulators](#types-of-manipulators)
- [Width and Fill Manipulators](#width-and-fill-manipulators)
- [Precision Manipulators](#precision-manipulators)
- [Number Base Manipulators](#number-base-manipulators)
- [Alignment Manipulators](#alignment-manipulators)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

Manipulators are special functions that can be used with insertion (`<<`) and extraction (`>>`) operators to format input and output. They modify the behavior of streams, such as setting field width, precision, fill character, and number base.

> **Header Files Required**:
> - `<iostream>` - For basic manipulators like `endl`, `hex`, `oct`
> - `<iomanip>` - For parameterized manipulators like `setw`, `setprecision`

---

## Types of Manipulators

| Category | Manipulators | Description |
|----------|--------------|-------------|
| **Width** | `setw()`, `width()` | Set field width |
| **Fill** | `setfill()`, `fill()` | Set fill character |
| **Precision** | `setprecision()`, `precision()` | Set decimal precision |
| **Base** | `hex`, `oct`, `dec`, `setbase()` | Set number base |
| **Alignment** | `left`, `right`, `internal` | Set alignment |
| **Float Format** | `fixed`, `scientific` | Set float display format |
| **Misc** | `endl`, `flush`, `ws` | Newline, flush, skip whitespace |

---

## Width and Fill Manipulators

### setw() and width()

These set the minimum field width for the next output operation.

```cpp
// Using setw() - from <iomanip>
cout << setw(10) << value;

// Using width() - member function
cout.width(10);
cout << value;
```

> **Important**: Width setting applies only to the **next** output operation!

### setfill() and fill()

These set the character used to fill empty spaces in a field.

```cpp
// Using setfill() - from <iomanip>
cout << setw(10) << setfill('*') << value;

// Using fill() - member function
cout.fill('*');
cout.width(10);
cout << value;
```

> **Note**: Unlike width, fill setting **persists** until changed.

---

## Precision Manipulators

### setprecision() and precision()

Control the number of significant digits or decimal places displayed.

```cpp
// Using setprecision() - from <iomanip>
cout << setprecision(3) << 3.14159;  // Output: 3.14

// Using precision() - member function
cout.precision(3);
cout << 3.14159;  // Output: 3.14
```

### fixed and scientific

These affect how precision is applied:

| Mode | Precision Meaning | Example |
|------|-------------------|---------|
| Default | Total significant digits | `3.14159` with precision 4 → `3.142` |
| `fixed` | Decimal places | `3.14159` with precision 4 → `3.1416` |
| `scientific` | Decimal places in mantissa | `3.14159` with precision 2 → `3.14e+00` |

```cpp
cout << fixed << setprecision(4) << 3.14159;      // 3.1416
cout << scientific << setprecision(2) << 3.14159; // 3.14e+00
```

---

## Number Base Manipulators

### hex, oct, dec

Change the base for integer output:

```cpp
int number = 100;
cout << hex << number << endl;  // Output: 64 (hexadecimal)
cout << oct << number << endl;  // Output: 144 (octal)
cout << dec << number << endl;  // Output: 100 (decimal)
```

### setbase()

Alternative way to set base (8, 10, or 16):

```cpp
cout << setbase(16) << 100 << endl;  // Output: 64
cout << setbase(8) << 100 << endl;   // Output: 144
cout << setbase(10) << 100 << endl;  // Output: 100
```

---

## Alignment Manipulators

### left, right, internal

Control alignment within the field width:

```cpp
// Right alignment (default)
cout << setw(10) << right << 42;     // "        42"

// Left alignment
cout << setw(10) << left << 42;      // "42        "

// Internal alignment (sign at left, value at right)
cout << setw(10) << internal << -42;  // "-       42"
```

### Using setf() for Alignment

```cpp
cout.setf(ios::left);   // Set left alignment
cout.setf(ios::right);  // Set right alignment
```

---

## Code Examples

### Example 1: Width and Fill

```cpp
#include<iostream>      // Line 1: Include iostream
using namespace std;    // Line 2: Standard namespace

int main()
{
    double arr[] = { 1.23, 35.45, 786.45, 1234.333 };  // Line 6: Array of doubles
    
    // Display with width of 10
    for (int i = 0; i < 4; i++)    // Line 9: Loop through array
    {
        cout.width(10);            // Line 11: Set width to 10 characters
        cout << arr[i] << endl;    // Line 12: Display right-aligned
    }
    
    // Width with fill character
    char str[] = "hello world";     // Line 15: String literal
    cout.width(40);                 // Line 16: Set width to 40
    cout.fill('*');                 // Line 17: Fill with asterisks
    cout << str << endl;            // Line 18: Display with padding
    
    // Left alignment with fill
    char str1[] = "hello world";    // Line 21
    cout.width(40);                 // Line 22
    cout.setf(ios::left);           // Line 23: Set left alignment
    cout.fill('*');                 // Line 24
    cout << str1 << endl;           // Line 25
    
    return 0;
}
```

### Expected Output:

```
      1.23
     35.45
    786.45
  1234.333
*****************************hello world
hello world*****************************
```

### Line-by-Line Explanation:

| Line | Code | Explanation |
|------|------|-------------|
| 11 | `cout.width(10)` | Sets minimum field width to 10 characters |
| 12 | `cout << arr[i]` | Number is right-aligned in 10-character field |
| 16 | `cout.width(40)` | Sets field width to 40 characters |
| 17 | `cout.fill('*')` | Uses '*' to fill empty spaces |
| 23 | `cout.setf(ios::left)` | Changes alignment to left |

### Example 2: Precision and Fixed

```cpp
#include<iostream>      // Line 1
using namespace std;    // Line 2

int main()
{
    float fl = 34.68f;              // Line 6: Float value
    cout.precision(4);              // Line 7: Set precision to 4
    cout.setf(ios::fixed);          // Line 8: Fixed-point notation
    
    cout << endl << fl << endl;     // Line 10: Output: 34.6800
    
    double dd = 34.567;             // Line 12
    cout << endl << dd << endl;     // Line 13: Output: 34.5670
    
    cout.precision(9);              // Line 15: Change precision to 9
    cout << endl << dd << endl;     // Line 16: Output: 34.567000000
    
    return 0;
}
```

### Execution Flow:

```
Start
  │
  ▼
precision(4) + fixed mode set
  │ "fixed" means precision = decimal places
  │
  ▼
Output fl (34.68f)
  │ Shows as: 34.6800 (4 decimal places)
  │
  ▼
Output dd (34.567)
  │ Shows as: 34.5670 (4 decimal places)
  │
  ▼
Change precision(9)
  │
  ▼
Output dd (34.567)
  │ Shows as: 34.567000000 (9 decimal places)
  │
  ▼
End
```

### Example 3: Tabular Display with setw()

```cpp
#include<iostream>      // Line 1
#include<iomanip>       // Line 2: Required for setw
using namespace std;    // Line 3

int main()
{
    double per[] = { 34.56, 78.67, 55.67, 86.34 };      // Line 7
    char names[4][20] = { "aaa", "bbb", "ccc", "ddd" }; // Line 8
    
    cout.precision(3);              // Line 10: Set precision
    cout.setf(ios::fixed);          // Line 11: Fixed notation
    
    for (int i = 0; i < 4; i++)     // Line 13: Loop through data
    {
        cout << setw(3) << names[i] << setw(20) << per[i] << endl;  // Line 15
    }
    
    return 0;
}
```

### Expected Output:

```
aaa              34.560
bbb              78.670
ccc              55.670
ddd              86.340
```

### Example 4: Number Base Conversion

```cpp
#include<iostream>      // Line 1
#include<iomanip>       // Line 2
using namespace std;    // Line 3

int main()
{
    cout << setbase(16) << 100 << endl;         // Line 7: Output: 64 (hex)
    
    float A = 1.34255f;                          // Line 9
    cout << "Value of A is\t" << A << endl;      // Line 10
    
    cout << fixed << setprecision(3) << A << endl;  // Line 12: Output: 1.343
    
    int number = 100;                            // Line 14
    
    cout << "Hex Value =" << " " << hex << number << endl;    // Line 16: 64
    cout << "Octal Value=" << " " << oct << number << endl;   // Line 17: 144
    cout << "decimal Value=" << " " << dec << number << endl; // Line 18: 100
    
    cout << "Setbase Value=" << " " << setbase(8) << number << endl;   // Line 20: 144
    cout << "Setbase Value=" << " " << setbase(16) << number << endl;  // Line 21: 64
    
    return 0;
}
```

### Example 5: Fill Character

```cpp
#include<iostream>      // Line 1
using namespace std;    // Line 2

int main()
{
    double per[] = { 1.23, 35.67, 744.56, 3452.789 };  // Line 6
    
    for (int i = 0; i < 4; i++)    // Line 8
    {
        cout.width(10);            // Line 10: Field width 10
        cout.fill('*');            // Line 11: Fill with asterisks
        cout << per[i] << endl;    // Line 12
    }
    
    return 0;
}
```

### Output:

```
******1.23
*****35.67
****744.56
**3452.789
```

---

## Key Points

1. **Header Requirements**:
   - Basic manipulators (`endl`, `hex`, `oct`, `dec`): `<iostream>`
   - Parameterized manipulators (`setw`, `setprecision`, `setfill`): `<iomanip>`

2. **Width is Temporary**: `setw()` applies only to the next output. Must be reset each time.

3. **Fill is Persistent**: `setfill()` remains in effect until changed.

4. **Precision with fixed**:
   - Without `fixed`: precision = total significant digits
   - With `fixed`: precision = decimal places

5. **Base Changes are Persistent**: `hex`, `oct`, `dec` remain until changed.

6. **Member Functions vs Manipulators**:
   - `cout.width(10)` ≡ `cout << setw(10)`
   - `cout.precision(3)` ≡ `cout << setprecision(3)`

---

## Interview Questions

### Q1: What is the difference between setw() and setfill()?
**Answer**:
| setw() | setfill() |
|--------|-----------|
| Sets field width | Sets fill character |
| Applies to next output only | Persists until changed |
| Default: 0 (no minimum) | Default: space |

### Q2: What is the difference between precision with and without `fixed`?
**Answer**:
- **Without `fixed`**: Precision specifies total significant digits
- **With `fixed`**: Precision specifies decimal places

```cpp
double d = 123.456789;
cout << setprecision(4) << d;          // 123.5 (4 significant digits)
cout << fixed << setprecision(4) << d; // 123.4568 (4 decimal places)
```

### Q3: Why is `<iomanip>` needed for setw() but not for endl?
**Answer**: 
- `endl` is a simple function pointer defined in `<iostream>`
- `setw()` and similar are template functions with parameters that require the `<iomanip>` header

### Q4: What is the default fill character?
**Answer**: The default fill character is a **space** (`' '`).

### Q5: How do you display a number in different bases?
**Answer**:
```cpp
int n = 255;
cout << hex << n;       // ff (hexadecimal)
cout << oct << n;       // 377 (octal)
cout << dec << n;       // 255 (decimal)
cout << setbase(16) << n; // ff (alternative)
```

### Q6: What does `internal` alignment do?
**Answer**: `internal` alignment places the sign/prefix at the left edge and the value at the right edge:
```cpp
cout << setw(10) << internal << -42;  // Output: "-       42"
```
