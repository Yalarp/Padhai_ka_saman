# C++ Multithreading Basics (C++11)

## Table of Contents
- [Introduction](#introduction)
- [Creating Threads](#creating-threads)
- [Thread Management](#thread-management)
- [Thread Synchronization](#thread-synchronization)
- [Mutex and Lock](#mutex-and-lock)
- [Code Examples](#code-examples)
- [Key Points](#key-points)
- [Interview Questions](#interview-questions)

---

## Introduction

C++11 introduced native thread support with the `<thread>` header. Threads allow concurrent execution of code, improving performance on multi-core systems.

> **Header Required**: `#include <thread>`

### Key Concepts:
- **Thread**: Independent execution path
- **Main Thread**: Runs `main()` function
- **Child Threads**: Created from main thread
- **Concurrency**: Multiple threads running simultaneously

---

## Creating Threads

### Basic Syntax:
```cpp
#include <thread>

void myFunction() {
    // Thread code
}

int main() {
    thread t1(myFunction);  // Create and start thread
    t1.join();              // Wait for thread to finish
    return 0;
}
```

### Thread Creation Options:

| Method | Example |
|--------|---------|
| Function pointer | `thread t1(func);` |
| Lambda | `thread t2([]() { ... });` |
| Functor | `thread t3(MyFunctor());` |
| Member function | `thread t4(&Class::method, &obj);` |

---

## Thread Management

### join() - Wait for Thread
```cpp
thread t1(myFunc);
t1.join();  // Block until t1 completes
```

### detach() - Run Independently
```cpp
thread t1(myFunc);
t1.detach();  // Thread runs independently
// Don't access t1 after detach
```

### joinable() - Check Status
```cpp
if (t1.joinable()) {
    t1.join();
}
```

---

## Thread Synchronization

### Race Condition:
When multiple threads access shared data simultaneously without synchronization.

### Mutex (Mutual Exclusion):
Prevents simultaneous access to shared resources.

```cpp
#include <mutex>

mutex mtx;
int sharedData = 0;

void increment() {
    mtx.lock();
    sharedData++;  // Critical section
    mtx.unlock();
}
```

---

## Mutex and Lock

### lock_guard (RAII):
```cpp
void safeIncrement() {
    lock_guard<mutex> lock(mtx);  // Automatically locks
    sharedData++;
    // Automatically unlocks when lock goes out of scope
}
```

### unique_lock (More Flexible):
```cpp
void flexibleLock() {
    unique_lock<mutex> lock(mtx);
    sharedData++;
    lock.unlock();  // Can unlock manually
    // ... other work
    lock.lock();    // Can relock
}
```

---

## Code Examples

### Example 1: Basic Thread Creation

```cpp
#include <iostream>
#include <thread>
using namespace std;

void myfun1() {
    cout << "inside myfun1 function of thread" << endl;
}

int main() {
    cout << "in main function before" << endl;
    
    thread t1(myfun1);  // Thread created and started
    
    t1.join();  // Wait for t1 to complete
    
    cout << "in main function after" << endl;
    return 0;
}
```

### Execution Flow:
```
Main Thread                    Child Thread (t1)
    │                               │
    ├── print "before"              │
    │                               │
    ├── create t1 ─────────────────►│
    │                               ├── print "inside myfun1"
    │                               │
    ├── join() [waits] ◄────────────│
    │                               │
    ├── print "after"               │
    │                               │
    ▼                               ▼
```

### Example 2: Multiple Threads

```cpp
#include <iostream>
#include <thread>
using namespace std;

void printNumbers(int start) {
    for (int i = start; i < start + 5; i++) {
        cout << "Thread " << start << ": " << i << endl;
    }
}

int main() {
    thread t1(printNumbers, 0);
    thread t2(printNumbers, 100);
    thread t3(printNumbers, 200);
    
    t1.join();
    t2.join();
    t3.join();
    
    cout << "All threads completed" << endl;
    return 0;
}
```

### Example 3: Thread with Lambda

```cpp
#include <iostream>
#include <thread>
using namespace std;

int main() {
    int value = 42;
    
    thread t1([value]() {
        cout << "Lambda thread: " << value << endl;
    });
    
    t1.join();
    return 0;
}
```

### Example 4: Mutex for Thread Safety

```cpp
#include <iostream>
#include <thread>
#include <mutex>
using namespace std;

mutex mtx;
int counter = 0;

void incrementCounter() {
    for (int i = 0; i < 10000; i++) {
        lock_guard<mutex> lock(mtx);
        counter++;  // Protected by mutex
    }
}

int main() {
    thread t1(incrementCounter);
    thread t2(incrementCounter);
    
    t1.join();
    t2.join();
    
    cout << "Final counter: " << counter << endl;  // Always 20000
    return 0;
}
```

---

## Key Points

1. **join()**: Main thread waits for child to complete.
2. **detach()**: Thread runs independently (daemon).
3. **Must join/detach**: Before thread object destroyed.
4. **Mutex**: Prevents race conditions.
5. **lock_guard**: RAII-based automatic locking.
6. **Thread-safe**: Use mutex for shared data access.

---

## Interview Questions

### Q1: What is the difference between join() and detach()?
**Answer**:
| join() | detach() |
|--------|----------|
| Blocks until thread completes | Thread runs independently |
| Main thread waits | Main thread continues |
| Thread resources cleaned after | Background execution |

### Q2: What is a race condition?
**Answer**: A race condition occurs when multiple threads access shared data concurrently, and the final result depends on the timing/order of execution. It leads to unpredictable behavior.

### Q3: What is a mutex?
**Answer**: A mutex (mutual exclusion) is a synchronization primitive that prevents multiple threads from accessing a shared resource simultaneously. Only one thread can hold the lock at a time.

### Q4: Why use lock_guard instead of lock()/unlock()?
**Answer**: `lock_guard` is RAII-based, so it automatically unlocks when it goes out of scope, even if an exception occurs. This prevents deadlocks from forgotten unlocks.

### Q5: What happens if you don't call join() or detach()?
**Answer**: The program will terminate with `std::terminate()` when the thread object is destroyed while still joinable.
