# C++ File Pointers: seekg, seekp, tellg, tellp

## Table of Contents
- [Introduction](#introduction)
- [Get Pointer vs Put Pointer](#get-pointer-vs-put-pointer)
- [Positioning Functions](#positioning-functions)
- [Position Reference Points](#position-reference-points)
- [The clear() Function](#the-clear-function)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Common Mistakes](#common-mistakes)
- [Interview Questions](#interview-questions)

---

## Introduction

When working with files in C++, the system maintains internal pointers that track the current read and write positions within the file. These pointers determine where the next read or write operation will occur. C++ provides functions to manipulate and query these positions, enabling **random access** file operations.

---

## Get Pointer vs Put Pointer

C++ file streams maintain two types of pointers:

| Pointer | Also Called | Purpose | Used With |
|---------|-------------|---------|-----------|
| **Get Pointer** | Read Pointer | Tracks position for next input operation | `>>`, `get()`, `getline()`, `read()` |
| **Put Pointer** | Write Pointer | Tracks position for next output operation | `<<`, `put()`, `write()` |

### Visual Representation

```
File Content: [ H | e | l | l | o | W | o | r | l | d ]
Position:       0   1   2   3   4   5   6   7   8   9

Get Pointer (g): ↓ (position 0 - next character to read)
Put Pointer (p): ↓ (position 0 - where next character will be written)
```

---

## Positioning Functions

### seekg() - Seek Get Pointer (for Reading)

```cpp
istream& seekg(streampos position);              // Absolute position
istream& seekg(streamoff offset, ios::seekdir direction);  // Relative position
```

**Purpose**: Moves the get (read) pointer to a specified position.

### seekp() - Seek Put Pointer (for Writing)

```cpp
ostream& seekp(streampos position);              // Absolute position
ostream& seekp(streamoff offset, ios::seekdir direction);  // Relative position
```

**Purpose**: Moves the put (write) pointer to a specified position.

### tellg() - Tell Get Pointer Position

```cpp
streampos tellg();
```

**Purpose**: Returns the current position of the get pointer.

### tellp() - Tell Put Pointer Position

```cpp
streampos tellp();
```

**Purpose**: Returns the current position of the put pointer.

---

## Position Reference Points

The direction parameter in `seekg()` and `seekp()` can be:

| Direction | Meaning | Starting Point |
|-----------|---------|----------------|
| `ios::beg` | Beginning | Start of file (position 0) |
| `ios::cur` | Current | Current pointer position |
| `ios::end` | End | End of file |

### Offset Examples

```cpp
// Move to absolute position 10 from beginning
file.seekg(10, ios::beg);

// Move 5 bytes forward from current position
file.seekg(5, ios::cur);

// Move 10 bytes backward from end
file.seekg(-10, ios::end);

// Move to the very end
file.seekg(0, ios::end);

// Move to the very beginning
file.seekg(0, ios::beg);
```

### Visual Examples

```
File: [ A | B | C | D | E | F | G | H | I | J ]
Pos:    0   1   2   3   4   5   6   7   8   9

seekg(0, ios::beg);     →  Position 0 (at 'A')
seekg(3, ios::beg);     →  Position 3 (at 'D')
seekg(0, ios::end);     →  Position 10 (after 'J')
seekg(-2, ios::end);    →  Position 8 (at 'I')

Current at position 5:
seekg(2, ios::cur);     →  Position 7 (at 'H')
seekg(-3, ios::cur);    →  Position 2 (at 'C')
```

---

## The clear() Function

### Why clear() is Needed

When you read a file and reach the end, the **EOF flag** is set in the stream. After EOF is set:
- The stream is in a "failed" state
- Subsequent read operations return immediately without reading
- `seekg()` alone won't work!

### Solution: Use clear() Before seekg()

```cpp
ff.clear();      // Reset stream flags (clear EOF)
ff.seekg(0, ios::beg);  // Now seek works
```

### What clear() Does

```cpp
stream.clear();
```
- Resets all error state flags (eofbit, failbit, badbit)
- Stream returns to "good" state
- Subsequent operations can now proceed

---

## Code Examples

### Example 1: Random Access with fstream

```cpp
// Random Access File
#include<fstream>       // Line 1: Include fstream
#include<iostream>      // Line 2: Include iostream
using namespace std;    // Line 3: Standard namespace

int main()
{
    /*
    ios::in    for reading
    ios::out   for writing
    ios::trunc will truncate the file if it exists
               and create a new file if it doesn't exist.
    */
    
    fstream ff("c:\\temp\\fourth.txt", ios::in | ios::out | ios::trunc);  // Line 15
    
    int i = 65;
    char ch;
    
    // Write characters (ASCII 65-99: A to c)
    for (i = 65; i < 100; i++)       // Line 20
    {
        ff.put((char)i);             // Line 22: Write character
    }
    
    ff.seekg(0, ios::beg);           // Line 25: Move GET pointer to beginning
    
    while (ff.get(ch))               // Line 27: Read each character
    {
        cout << endl << ch << endl;  // Line 29: Display character
    }
    
    ff.close();                      // Line 32: Close file
    return 0;
}
```

### Line-by-Line Explanation:

| Line | Code | Explanation |
|------|------|-------------|
| 15 | `fstream ff(...)` | Opens file for both read and write, truncates if exists |
| 20-22 | `for` loop with `put()` | Writes 35 characters (A-Z, then more) to file |
| 25 | `ff.seekg(0, ios::beg)` | Moves get pointer to position 0 (beginning) |
| 27 | `while (ff.get(ch))` | Reads characters one by one until EOF |

### Execution Flow:

```
Start
  │
  ▼
Open file (creates new or truncates existing)
  │
  ▼
Write loop: i = 65 to 99
  │ Write 'A'(65), 'B'(66)... until character 99
  │ After loop: put pointer at position 35
  │             get pointer also at position 35
  ▼
seekg(0, ios::beg)
  │ Moves GET pointer to position 0
  │ PUT pointer remains at position 35
  ▼
Read loop: get(ch) until EOF
  │ Reads from position 0 to 34
  │ Displays each character
  ▼
Close file
  │
  ▼
End
```

### Example 2: Reading File Multiple Times (clear() Required)

```cpp
// Random Access File with clear()
#include<fstream>       // Line 1
#include<iostream>      // Line 2
using namespace std;    // Line 3

int main()
{
    fstream ff("c:\\temp\\fourth.txt", ios::in | ios::out | ios::trunc);  // Line 7
    
    struct emp                        // Line 9: Structure definition
    {
        char name[20];
        int age;
    } e[3] = { {"abc",30}, {"xyz",25}, {"lmn",10} };  // Line 13
    
    struct emp e1;                    // Line 15: For reading
    
    cout << sizeof(e) << endl;        // Line 17: Display array size
    ff.write((char*)e, sizeof(e));    // Line 18: Write array to file
    
    // FIRST READ
    ff.seekg(0, ios::beg);            // Line 21: Move to beginning
    while (ff.read((char*)&e1, sizeof(e1)))  // Line 22: Read each struct
    {
        cout << e1.name << "\t" << e1.age << endl;  // Line 24
    }
    
    // SECOND READ - without clear()
    cout << "lets read again!" << endl;  // Line 28
    // ff.clear();  // This is commented - MUST be uncommented!
    ff.seekg(0, ios::beg);            // Line 30
    
    while (ff.read((char*)&e1, sizeof(e1)))  // Line 32: Won't execute!
    {
        cout << e1.name << "\t" << e1.age << endl;
    }
    
    ff.close();                       // Line 37
    return 0;
}
```

### Why Second Read Fails Without clear():

```
FIRST READ:
┌─────────────────────────────────────┐
│ Start: Get pointer at 0             │
│ Read struct 1: pointer → 24         │
│ Read struct 2: pointer → 48         │
│ Read struct 3: pointer → 72         │
│ Attempt read: No more data          │
│ EOF FLAG SET! Stream in failed state│
└─────────────────────────────────────┘
            │
            ▼
SECOND READ (without clear):
┌─────────────────────────────────────┐
│ seekg(0, ios::beg) executes BUT...  │
│ EOF flag is STILL SET              │
│ ff.read() returns immediately       │
│ No data is read!                    │
└─────────────────────────────────────┘

SECOND READ (with clear):
┌─────────────────────────────────────┐
│ ff.clear() - Resets all flags       │
│ EOF flag is now CLEARED            │
│ seekg(0, ios::beg) - Move to start  │
│ ff.read() now works normally        │
│ All 3 structs read successfully     │
└─────────────────────────────────────┘
```

### Example 3: Using tellg() to Get File Size

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main() {
    ifstream file("c:\\temp\\data.txt", ios::binary);  // Line 6
    
    if (!file) {
        cerr << "Cannot open file" << endl;
        return 1;
    }
    
    // Get current position (beginning)
    streampos startPos = file.tellg();     // Line 14: Returns 0
    cout << "Start position: " << startPos << endl;
    
    // Seek to end
    file.seekg(0, ios::end);               // Line 18: Move to end
    
    // Get file size
    streampos fileSize = file.tellg();     // Line 21: Position = file size
    cout << "File size: " << fileSize << " bytes" << endl;
    
    // Go back to beginning
    file.seekg(0, ios::beg);               // Line 25
    
    file.close();
    return 0;
}
```

### Example 4: Modifying Specific Position in File

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main() {
    // Create initial file
    fstream file("c:\\temp\\modify.txt", ios::out | ios::trunc);
    file << "AAAAA";  // Write 5 A's
    file.close();
    
    // Open for modification
    file.open("c:\\temp\\modify.txt", ios::in | ios::out);  // Line 11
    
    // Move to position 2
    file.seekp(2, ios::beg);   // Line 14: Move PUT pointer to position 2
    file.put('X');             // Line 15: Write 'X' at position 2
    
    // Read entire file to verify
    file.seekg(0, ios::beg);   // Line 18: Move GET pointer to start
    string content;
    file >> content;
    cout << "Modified content: " << content << endl;  // Outputs: AAXAA
    
    file.close();
    return 0;
}
```

---

## Key Points

1. **Two Pointers**: Files have separate get (read) and put (write) pointers.

2. **seekg vs seekp**:
   - `seekg` = Seek **G**et pointer (for reading)
   - `seekp` = Seek **P**ut pointer (for writing)

3. **tellg vs tellp**:
   - `tellg` = **T**ell **G**et pointer position
   - `tellp` = **T**ell **P**ut pointer position

4. **Three Reference Points**:
   - `ios::beg` - From beginning
   - `ios::cur` - From current position
   - `ios::end` - From end (usually negative offset)

5. **clear() is Essential**: Call `clear()` before `seekg()` if EOF was reached.

6. **Random Access**: These functions enable reading/writing at any position in file.

---

## Common Mistakes

### 1. Forgetting clear() After EOF
```cpp
// Wrong
while (file.read(...)) { }  // EOF reached
file.seekg(0, ios::beg);    // Won't work!
file.read(...);              // Fails silently

// Correct
while (file.read(...)) { }  // EOF reached
file.clear();                // Reset flags first!
file.seekg(0, ios::beg);    // Now works
file.read(...);              // Success
```

### 2. Confusing seekg and seekp
```cpp
// Wrong - using seekg when you want to write
file.seekg(0, ios::beg);
file.write(data, size);  // May not write where expected

// Correct - use seekp for writing
file.seekp(0, ios::beg);
file.write(data, size);
```

### 3. Wrong Direction for seekg from ios::end
```cpp
// Wrong - positive offset from end goes beyond file
file.seekg(10, ios::end);  // Invalid position!

// Correct - negative offset from end
file.seekg(-10, ios::end);  // 10 bytes before end
```

---

## Interview Questions

### Q1: What is the difference between seekg() and seekp()?
**Answer**:
| seekg() | seekp() |
|---------|---------|
| Seeks the **get** (read) pointer | Seeks the **put** (write) pointer |
| Used before read operations | Used before write operations |
| Affects `>>`, `get()`, `read()` | Affects `<<`, `put()`, `write()` |

### Q2: What are ios::beg, ios::cur, and ios::end?
**Answer**: These are direction specifiers for seek operations:
- `ios::beg` - Offset from the beginning of file (position 0)
- `ios::cur` - Offset from current pointer position
- `ios::end` - Offset from end of file (usually negative)

### Q3: Why do we need clear() before seekg() sometimes?
**Answer**: When EOF is reached during reading, the stream's eofbit flag is set. The stream enters a "failed" state where further read operations won't work. `clear()` resets all error flags, returning the stream to a "good" state so that `seekg()` and subsequent reads can proceed normally.

### Q4: How can you get the size of a file using stream functions?
**Answer**:
```cpp
ifstream file("data.txt", ios::binary);
file.seekg(0, ios::end);        // Move to end
streampos size = file.tellg();  // Get position = file size
```

### Q5: What is the difference between tellg() and tellp()?
**Answer**:
- `tellg()` returns the current position of the **get** (read) pointer
- `tellp()` returns the current position of the **put** (write) pointer

Both return a `streampos` value representing the byte offset from the beginning of the file.

### Q6: Can you seek beyond the end of a file?
**Answer**: Yes, you can seek beyond the end with `seekp()`. If you write data at that position, the file will be extended and the gap filled with undefined content (often null bytes). However, seeking beyond end with `seekg()` for reading will typically fail.
