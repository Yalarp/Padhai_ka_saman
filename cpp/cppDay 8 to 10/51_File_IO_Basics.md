# C++ File I/O Basics

## Table of Contents
- [Introduction](#introduction)
- [File Stream Classes](#file-stream-classes)
- [Opening and Closing Files](#opening-and-closing-files)
- [File Opening Methods](#file-opening-methods)
- [Checking File Status](#checking-file-status)
- [Basic File Operations](#basic-file-operations)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Common Mistakes](#common-mistakes)
- [Interview Questions](#interview-questions)

---

## Introduction

File I/O (Input/Output) in C++ allows programs to store data permanently and retrieve it later. Unlike variables that lose their data when the program ends, files persist on the storage device, making them essential for applications that need to save and load data.

> **Why File Handling?**
> - Store data permanently beyond program execution
> - Share data between different program runs
> - Process large amounts of data that won't fit in memory
> - Exchange data between different applications

---

## File Stream Classes

C++ provides three main classes for file operations in the `<fstream>` header:

| Class | Purpose | Default Mode |
|-------|---------|--------------|
| `ifstream` | Input from file (reading) | `ios::in` |
| `ofstream` | Output to file (writing) | `ios::out \| ios::trunc` |
| `fstream` | Both input and output | `ios::in \| ios::out` |

### Header Requirements

```cpp
#include <fstream>   // Required for ifstream, ofstream, fstream
#include <iostream>  // Required for cout, cin
```

### Class Hierarchy for File Streams

```
           ┌─────────────┐
           │    ios      │
           └──────┬──────┘
                  │
      ┌───────────┴───────────┐
      ▼                       ▼
┌──────────┐             ┌──────────┐
│ istream  │             │ ostream  │
└────┬─────┘             └────┬─────┘
     │                        │
     ▼                        ▼
┌──────────┐             ┌──────────┐
│ ifstream │             │ ofstream │
└──────────┘             └──────────┘
                  │
           ┌──────────┐
           │ fstream  │ (inherits both)
           └──────────┘
```

---

## Opening and Closing Files

### Method 1: Using Constructor

```cpp
// Opening file during object creation
ifstream inFile("c:\\temp\\data.txt");   // Open for reading
ofstream outFile("c:\\temp\\output.txt"); // Open for writing
fstream file("c:\\temp\\data.txt");       // Open for read/write
```

### Method 2: Using open() Function

```cpp
ifstream inFile;
inFile.open("c:\\temp\\data.txt");

ofstream outFile;
outFile.open("c:\\temp\\output.txt");

fstream file;
file.open("c:\\temp\\data.txt", ios::in | ios::out);
```

### Closing Files

```cpp
inFile.close();   // Close input file
outFile.close();  // Close output file
file.close();     // Close fstream file
```

> **Important**: Always close files when done to:
> - Free system resources
> - Flush remaining buffered data to disk
> - Allow other programs to access the file

---

## File Opening Methods

### Syntax Options

```cpp
// Option 1: Constructor with filename only
ifstream file("filename.txt");

// Option 2: Constructor with filename and mode
ofstream file("filename.txt", ios::app);

// Option 3: open() function
fstream file;
file.open("filename.txt", ios::in | ios::out);
```

---

## Checking File Status

There are multiple ways to check if a file was opened successfully:

### Method 1: Using operator!

```cpp
ifstream in("c:\\temp\\one.txt");
if (!in)  // operator! is overloaded
{
    cout << "Cannot open file" << endl;
    return 0;
}
```

**Explanation**: The `!` operator is overloaded in stream classes to return `true` when the stream is in a failed state.

### Method 2: Using fail() Function

```cpp
ofstream out("c:\\temp\\one.txt");
if (out.fail())
{
    cout << "Cannot open file" << endl;
    return 0;
}
```

### Method 3: Using is_open() Function

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main() {
    string filename = "c:\\temp\\example.txt";
    ifstream file(filename);
    
    if (file.is_open()) {        // Check if file opened successfully
        cout << "The file " << filename << " exists." << endl;
    }
    else {
        cout << "The file " << filename << " does not exist." << endl;
    }
    
    return 0;
}
```

### Comparison of Methods

| Method | Best For | Notes |
|--------|----------|-------|
| `!stream` | Quick checks | Uses overloaded operator |
| `fail()` | Checking after operations | Checks failbit |
| `is_open()` | Checking file existence | Most descriptive |

---

## Basic File Operations

### Writing to Files (ofstream)

| Operator/Function | Purpose |
|-------------------|---------|
| `<<` | Write formatted data |
| `put(char)` | Write single character |
| `write(buffer, n)` | Write n bytes from buffer |

### Reading from Files (ifstream)

| Operator/Function | Purpose |
|-------------------|---------|
| `>>` | Read formatted data (stops at whitespace) |
| `get(char&)` | Read single character |
| `getline(buffer, n)` | Read line (up to n chars or newline) |
| `read(buffer, n)` | Read n bytes into buffer |

---

## Code Examples

### Example 1: Basic Write and Read

```cpp
#include<fstream>      // Line 1: Include fstream for file classes
#include<iostream>     // Line 2: Include iostream for cout
using namespace std;   // Line 3: Use standard namespace

int main()             // Line 4: Main function entry
{
    char str[30];      // Line 6: Buffer to store read data
    
    // Writing to file
    ofstream out("c:\\temp\\one.txt");  // Line 8: Create ofstream, open file
    if (!out)                            // Line 9: Check if file opened
    {
        cout << endl << "can not open\n"; // Line 11: Error message
        return 0;                          // Line 12: Exit on failure
    }
    out << "helloworld";    // Line 14: Write string to file
    out.close();            // Line 15: Close output file
    
    // Reading from file
    ifstream in("c:\\temp\\one.txt");  // Line 17: Create ifstream, open file
    if (!in)                            // Line 18: Check if file opened
    {
        cout << endl << "can not open\n"; // Line 20: Error message
        return 0;                          // Line 21: Exit on failure
    }
    in >> str;              // Line 23: Read string from file
    in.close();             // Line 24: Close input file
    
    cout << endl << str << endl;  // Line 26: Display read data
    return 0;               // Line 27: Return success
}
```

### Line-by-Line Explanation:

| Line | Code | Explanation |
|------|------|-------------|
| 1 | `#include<fstream>` | Includes file stream classes (ifstream, ofstream) |
| 2 | `#include<iostream>` | Includes cout for console output |
| 6 | `char str[30];` | Declares character array to hold 30 characters |
| 8 | `ofstream out("c:\\temp\\one.txt");` | Creates ofstream object, opens file for writing |
| 9 | `if (!out)` | Checks if file failed to open (operator! overloaded) |
| 14 | `out << "helloworld";` | Uses insertion operator to write to file |
| 15 | `out.close();` | Closes the output file |
| 17 | `ifstream in("c:\\temp\\one.txt");` | Creates ifstream object, opens file for reading |
| 23 | `in >> str;` | Uses extraction operator to read from file |
| 24 | `in.close();` | Closes the input file |

### Execution Flow:

```
Program Starts
      │
      ▼
┌─────────────────────────────────┐
│ Create ofstream object 'out'    │
│ Open "c:\temp\one.txt" for      │
│ writing                         │
└─────────────┬───────────────────┘
              │
              ▼
        ┌─────┴─────┐
        │ File OK?  │
        └─────┬─────┘
         Yes  │  No → Print error, exit
              ▼
┌─────────────────────────────────┐
│ Write "helloworld" to file      │
│ Close the file                  │
└─────────────┬───────────────────┘
              │
              ▼
┌─────────────────────────────────┐
│ Create ifstream object 'in'     │
│ Open "c:\temp\one.txt" for      │
│ reading                         │
└─────────────┬───────────────────┘
              │
              ▼
        ┌─────┴─────┐
        │ File OK?  │
        └─────┬─────┘
         Yes  │  No → Print error, exit
              ▼
┌─────────────────────────────────┐
│ Read string from file into str  │
│ Close the file                  │
│ Display str on console          │
└─────────────┬───────────────────┘
              │
              ▼
       Program Ends
```

### Example 2: Reading/Writing Multiple Lines with getline

```cpp
#include<fstream>      // Line 1: Include for file operations
#include<iostream>     // Line 2: Include for console I/O
using namespace std;   // Line 3: Standard namespace

int main()             // Line 4: Main function
{
    char str[80];      // Line 6: Buffer for line input
    
    // Writing multiple lines to file
    ofstream of("c:\\temp\\two.txt");  // Line 8: Open file for writing
    cout << endl << "enter lines, press enter to stop\n";  // Line 9: Prompt
    do
    {
        cin.getline(str, 80);      // Line 12: Read line from console
        if (strlen(str) == 0)       // Line 13: Check for empty line
        {
            break;                  // Line 15: Exit loop if empty
        }
        of << str << endl;          // Line 17: Write line to file
    } while (true);                 // Line 18: Infinite loop until break
    of.close();                     // Line 19: Close output file
    
    // Reading multiple lines from file
    ifstream iff("c:\\temp\\two.txt");  // Line 21: Open for reading
    char ptr[80];                        // Line 22: Buffer for reading
    while (iff.getline(ptr, 80))        // Line 23: Read line by line
    {
        cout << ptr << endl;            // Line 25: Display each line
    }
    iff.close();                        // Line 27: Close input file
    return 0;                           // Line 28: Return success
}
```

### Key Points About getline:

| Aspect | `getline(buffer, n)` |
|--------|---------------------|
| Purpose | Reads up to n-1 characters or until newline |
| Delimiter | Stops at newline (and discards it) |
| Return | Returns stream reference (false-y when EOF) |
| Buffer | Must have space for n characters |

### Example 3: Character-by-Character with get() and put()

```cpp
#include<fstream>      // Line 1: Include fstream
#include<iostream>     // Line 2: Include iostream
using namespace std;   // Line 3: Namespace

/*
istream& get(char &ch);
- Reads a single character from the invoking stream
- Puts that value in ch
- Returns a reference to the stream

ostream& put(char ch);
- Writes ch to the stream
- Returns a reference to the stream
*/

int main()             // Line 11: Main function
{
    char ch;           // Line 13: Character variable
    
    // Writing characters using put()
    ofstream o("c:\\temp\\three.txt");  // Line 15: Open for writing
    if (!o)
    {
        cout << endl << "cannot open a file\n";
        return 0;
    }
    int i = 0;
    for (i = 65; i <= 150; i++)  // Line 22: Loop from ASCII 65 (A) to 150
    {
        o.put((char)i);          // Line 24: Write character to file
    }
    o.close();                   // Line 26: Close file
    
    // Reading characters using get()
    ifstream in("c:\\temp\\three.txt");  // Line 28: Open for reading
    if (!in)
    {
        cout << endl << "can not open file\n";
        return 0;
    }
    while (in.get(ch))           // Line 33: Read character by character
    {
        cout << endl << ch << endl;  // Line 35: Display each character
    }
    in.close();                  // Line 37: Close file
    return 0;                    // Line 38: Return success
}
```

### Comparison: get() vs >> for Character Reading

| Aspect | `get(ch)` | `>> ch` |
|--------|-----------|---------|
| Whitespace | Reads whitespace | Skips whitespace |
| Newlines | Reads newlines | Skips newlines |
| Use Case | Read every character | Read non-whitespace only |

---

## Key Points

1. **Three File Classes**:
   - `ifstream` - Input (reading) only
   - `ofstream` - Output (writing) only
   - `fstream` - Both input and output

2. **Always Check File Status**: Use `!file`, `file.fail()`, or `file.is_open()` to verify successful opening.

3. **Always Close Files**: Call `close()` when done to free resources and flush buffers.

4. **Escape Path Separators**: In Windows paths, use `\\` (double backslash) in strings.

5. **Default Behaviors**:
   - `ofstream` truncates (clears) existing file by default
   - `ifstream` only reads, cannot write
   - `fstream` can do both but needs appropriate modes

6. **Extraction Operator (`>>`)**: Stops reading at whitespace, not suitable for strings with spaces.

7. **getline()**: Better for reading full lines including spaces.

---

## Common Mistakes

### 1. Not Checking if File Opened
```cpp
// Wrong
ofstream out("file.txt");
out << "data";  // May fail silently if file didn't open

// Correct
ofstream out("file.txt");
if (!out) {
    cerr << "Failed to open file" << endl;
    return 1;
}
out << "data";
```

### 2. Forgetting to Close Files
```cpp
// Wrong - file never closed
void saveData() {
    ofstream out("data.txt");
    out << "data";
    // File handle leaked!
}

// Correct
void saveData() {
    ofstream out("data.txt");
    out << "data";
    out.close();  // Always close
}
```

### 3. Using Wrong Slash in Paths
```cpp
// Wrong on Windows
ifstream in("c:\temp\file.txt");  // \t and \f are escape sequences!

// Correct
ifstream in("c:\\temp\\file.txt");  // Escaped backslashes
// OR
ifstream in("c:/temp/file.txt");    // Forward slashes work too
```

### 4. Reading with >> When Spaces Needed
```cpp
// Wrong - only reads "Hello"
string line;
in >> line;  // Input: "Hello World"  →  line = "Hello"

// Correct - reads entire line
string line;
getline(in, line);  // Input: "Hello World"  →  line = "Hello World"
```

---

## Interview Questions

### Q1: What are the three main file stream classes in C++?
**Answer**: 
- `ifstream` - Input file stream for reading from files
- `ofstream` - Output file stream for writing to files
- `fstream` - File stream for both reading and writing

All are defined in the `<fstream>` header.

### Q2: What happens if you try to open a file that doesn't exist with ifstream?
**Answer**: The file stream will be in a failed state. You can detect this by:
- `if (!inFile)` returns true
- `inFile.fail()` returns true
- `inFile.is_open()` returns false

No exception is thrown; you must check the state explicitly.

### Q3: How does ofstream behave by default when opening an existing file?
**Answer**: By default, `ofstream` uses `ios::out | ios::trunc` mode, which means:
- The file is opened for writing
- If the file exists, its contents are deleted (truncated)
- If the file doesn't exist, a new file is created

### Q4: What is the difference between put() and <<?
**Answer**:
| `put(char)` | `<<` |
|-------------|------|
| Writes a single character | Writes formatted data |
| Takes one character parameter | Can output any streamable type |
| Returns ostream& | Returns ostream& |
| Raw character output | May format output |

### Q5: What is the difference between get() and >>?
**Answer**:
| `get(char&)` | `>>` |
|--------------|------|
| Reads all characters including whitespace | Skips whitespace |
| Reads exactly one character | Reads until whitespace |
| Includes newlines and spaces | Excludes newlines and spaces |

### Q6: Why should you always close files?
**Answer**:
1. Frees system file handles (limited resource)
2. Flushes buffered data to disk
3. Allows other programs/processes to access the file
4. Prevents data loss from unflushed buffers
5. Good practice for resource management

### Q7: How can you check if a file exists in C++?
**Answer**:
```cpp
#include <fstream>
bool fileExists(const string& filename) {
    ifstream file(filename);
    return file.is_open();
}
```
Or from C++17:
```cpp
#include <filesystem>
bool fileExists = std::filesystem::exists("filename.txt");
```

### Q8: What does the operator! do when used with file streams?
**Answer**: The `!` operator is overloaded in stream classes to return `true` when the stream is in a failed state (either fail or bad bit is set). This allows the idiom:
```cpp
if (!fileStream) {
    // Handle error
}
```
