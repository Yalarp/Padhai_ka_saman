# Thread Safety in Servlets

## Table of Contents
1. [Introduction](#1-introduction)
2. [The Thread Problem](#2-the-thread-problem)
3. [SingleThreadModel (Deprecated)](#3-singlethreadmodel-deprecated)
4. [Synchronized Blocks](#4-synchronized-blocks)
5. [Thread-Safe Coding Practices](#5-thread-safe-coding-practices)
6. [Complete Code Examples](#6-complete-code-examples)
7. [Key Points to Remember](#7-key-points-to-remember)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

Servlets are inherently multi-threaded. A single servlet instance handles multiple requests simultaneously through multiple threads. This can lead to race conditions and data corruption if not handled properly.

---

## 2. The Thread Problem

### How Servlets Handle Requests

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    SERVLET THREADING MODEL                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Container creates ONE servlet instance                                     │
│  ┌─────────────────────────────────────────────────────┐                   │
│  │              MyServlet Instance                      │                   │
│  │                                                      │                   │
│  │  Instance Variables: int count = 0;                 │ ← SHARED!         │
│  │                                                      │                   │
│  └─────────────────────────────────────────────────────┘                   │
│                          ▲                                                  │
│                          │                                                  │
│         ┌────────────────┼────────────────┐                                │
│         │                │                │                                │
│    ┌────┴────┐     ┌────┴────┐     ┌────┴────┐                            │
│    │ Thread 1│     │ Thread 2│     │ Thread 3│                            │
│    │ User A  │     │ User B  │     │ User C  │                            │
│    └─────────┘     └─────────┘     └─────────┘                            │
│                                                                             │
│  Problem: All threads access the SAME instance variable!                   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Race Condition Example

```java
public class CounterServlet extends HttpServlet {
    private int count = 0;  // SHARED instance variable
    
    public void doGet(HttpServletRequest request, HttpServletResponse response) {
        count++;  // NOT THREAD-SAFE!
        response.getWriter().println("Count: " + count);
    }
}
```

**What could go wrong:**

```
Thread 1: reads count (0)
Thread 2: reads count (0)
Thread 1: increments to 1
Thread 2: increments to 1  ← Should be 2!
Thread 1: writes 1
Thread 2: writes 1  ← Lost update!
```

---

## 3. SingleThreadModel (Deprecated)

### What Was SingleThreadModel?

An interface that guaranteed only ONE thread would execute the service method at a time.

```java
// DEPRECATED - DO NOT USE
public class SafeServlet extends HttpServlet implements SingleThreadModel {
    private int count = 0;
    
    public void doGet(HttpServletRequest request, HttpServletResponse response) {
        count++;  // Safe because only one thread at a time
    }
}
```

### Why Deprecated?

| Problem | Explanation |
|---------|-------------|
| Performance | Only one request at a time OR multiple instances |
| Memory | Container might create multiple servlet instances |
| Not truly safe | Static variables still shared |
| Better alternatives | Synchronized blocks, local variables |

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    SINGLETHREADMODEL BEHAVIOR                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Option 1: Serialize requests (slow)                                        │
│  ┌─────────────────────────────────────────────────────┐                   │
│  │              MyServlet Instance                      │                   │
│  └─────────────────────────────────────────────────────┘                   │
│         ▲                                                                   │
│         │                                                                   │
│         │ (one at a time)                                                   │
│         │                                                                   │
│    Thread 1 ─► Thread 2 ─► Thread 3                                        │
│    (waiting)   (waiting)                                                    │
│                                                                             │
│  Option 2: Multiple instances (memory waste)                                │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐                                 │
│  │Instance 1│  │Instance 2│  │Instance 3│                                 │
│  └────▲─────┘  └────▲─────┘  └────▲─────┘                                 │
│       │             │             │                                        │
│  Thread 1      Thread 2      Thread 3                                      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 4. Synchronized Blocks

### The Recommended Solution

Use synchronized blocks to protect critical sections:

```java
public class SafeCounterServlet extends HttpServlet {
    private int count = 0;
    
    public void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws IOException {
        
        // Synchronized block - only one thread at a time
        synchronized(this) {
            count++;
        }
        
        response.getWriter().println("Count: " + count);
    }
}
```

### How Synchronized Works

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    SYNCHRONIZED BLOCK EXECUTION                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Thread 1: Acquires lock on 'this'                                          │
│       │                                                                     │
│       ▼                                                                     │
│  ┌─────────────────────────────────────┐                                   │
│  │ synchronized(this) {                │ ← Thread 1 inside                 │
│  │     count++;                        │                                   │
│  │ }                                   │                                   │
│  └─────────────────────────────────────┘                                   │
│       │                                                                     │
│       ▼                                                                     │
│  Thread 1: Releases lock                                                    │
│                                                                             │
│  Thread 2: Was waiting, now acquires lock                                   │
│       │                                                                     │
│       ▼                                                                     │
│  ┌─────────────────────────────────────┐                                   │
│  │ synchronized(this) {                │ ← Thread 2 inside                 │
│  │     count++;                        │                                   │
│  │ }                                   │                                   │
│  └─────────────────────────────────────┘                                   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Thread-Safe Coding Practices

### Practice 1: Use Local Variables

Local variables are thread-safe because each thread has its own stack.

```java
public void doGet(HttpServletRequest request, HttpServletResponse response) {
    // SAFE: local variable, each thread has its own copy
    int localCount = 0;
    localCount++;
    
    // SAFE: local reference
    String name = request.getParameter("name");
}
```

### Practice 2: Use Request/Session Attributes

```java
public void doGet(HttpServletRequest request, HttpServletResponse response) {
    // SAFE: Each request has its own request object
    request.setAttribute("data", userData);
    
    // SAFE: Each user has their own session
    HttpSession session = request.getSession();
    session.setAttribute("user", userObject);
}
```

### Practice 3: Synchronize Shared Resources

```java
public class SharedResourceServlet extends HttpServlet {
    private List<String> sharedList = new ArrayList<>();
    
    public void doGet(HttpServletRequest request, HttpServletResponse response) {
        String item = request.getParameter("item");
        
        // Synchronize when modifying shared collection
        synchronized(sharedList) {
            sharedList.add(item);
        }
    }
}
```

### Practice 4: Use Thread-Safe Collections

```java
public class SafeCollectionServlet extends HttpServlet {
    // Thread-safe collections
    private ConcurrentHashMap<String, Object> safeMap = new ConcurrentHashMap<>();
    private CopyOnWriteArrayList<String> safeList = new CopyOnWriteArrayList<>();
    private AtomicInteger atomicCount = new AtomicInteger(0);
    
    public void doGet(HttpServletRequest request, HttpServletResponse response) {
        // These operations are thread-safe
        safeMap.put("key", "value");
        safeList.add("item");
        atomicCount.incrementAndGet();
    }
}
```

### Summary Table

| Variable Type | Thread Safe? | Recommendation |
|---------------|--------------|----------------|
| Local variables | ✅ Yes | Always safe |
| Request attributes | ✅ Yes | Safe for request-specific data |
| Session attributes | ⚠️ Mostly | Watch for concurrent AJAX |
| Instance variables | ❌ No | Synchronize or avoid |
| Static variables | ❌ No | Avoid or synchronize carefully |

---

## 6. Complete Code Examples

### UnsafeServlet.java - The Problem

```java
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class UnsafeServlet extends HttpServlet {
    
    // DANGER: Instance variable shared by all threads
    private int visitCount = 0;
    
    public void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // RACE CONDITION: Multiple threads can cause lost updates
        visitCount++;
        
        PrintWriter out = response.getWriter();
        out.println("Visit count: " + visitCount);
    }
}
```

### SafeServlet.java - The Solution

```java
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;
import java.util.concurrent.atomic.AtomicInteger;

public class SafeServlet extends HttpServlet {
    
    // SOLUTION 1: AtomicInteger for counters
    private AtomicInteger visitCount = new AtomicInteger(0);
    
    // SOLUTION 2: Regular variable with sync
    private int anotherCount = 0;
    
    public void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // SAFE: AtomicInteger is thread-safe
        int count = visitCount.incrementAndGet();
        
        // SAFE: Synchronized access
        synchronized(this) {
            anotherCount++;
        }
        
        // SAFE: Local variables
        String name = request.getParameter("name");
        int localVar = 10;
        
        PrintWriter out = response.getWriter();
        out.println("Atomic count: " + count);
        out.println("Synced count: " + anotherCount);
    }
}
```

---

## 7. Key Points to Remember

1. **One instance, multiple threads**: Container creates one servlet instance shared by all

2. **Instance variables are dangerous**: Shared across all requests/threads

3. **Local variables are safe**: Each thread has its own stack

4. **SingleThreadModel is deprecated**: Don't use it

5. **Synchronized blocks work**: But reduce performance

6. **Use AtomicInteger for counters**: Thread-safe without explicit sync

7. **Request/Session are safe**: Each has its own scope

8. **Prefer stateless servlets**: No instance variables = no problems

9. **Static variables same problem**: Shared even across instances

10. **init() is single-threaded**: Container ensures thread-safe init

---

## 8. Interview Questions

### Basic Level

**Q1: Are servlets thread-safe by default?**
> No. Servlets are multi-threaded by default. A single servlet instance handles multiple requests through different threads, making instance variables unsafe.

**Q2: What is the problem with instance variables in servlets?**
> Instance variables are shared by all threads handling different requests. This can lead to race conditions and data corruption.

**Q3: What is SingleThreadModel?**
> SingleThreadModel was an interface that ensured only one thread executed the service method at a time. It's deprecated because of poor performance and memory issues.

### Intermediate Level

**Q4: How do you make servlet code thread-safe?**
> 1. Use local variables instead of instance variables
> 2. Use synchronized blocks for shared resources
> 3. Use thread-safe collections (ConcurrentHashMap)
> 4. Use atomic classes (AtomicInteger)
> 5. Store per-user data in session, per-request in request

**Q5: Why is SingleThreadModel deprecated?**
> - Poor performance (serialized requests)
> - Memory waste (multiple instances)
> - Static variables still shared
> - Better alternatives exist

**Q6: What are local variables thread-safe?**
> Local variables are stored on the thread's stack, not on the heap. Each thread has its own stack, so local variables are not shared between threads.

### Advanced Level

**Q7: How would you implement a thread-safe hit counter?**
```java
public class HitCounterServlet extends HttpServlet {
    private final AtomicLong hitCount = new AtomicLong(0);
    
    public void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws IOException {
        long count = hitCount.incrementAndGet();
        response.getWriter().println("Hits: " + count);
    }
}
```

**Q8: Can session.getAttribute() cause thread-safety issues?**
> Yes, in scenarios like:
> - Multiple browser tabs accessing same session
> - Multiple AJAX requests from single page
> 
> Solution: Synchronize on session object when modifying shared session data.

**Q9: What is the difference between synchronized method and synchronized block?**
| Synchronized Method | Synchronized Block |
|---------------------|-------------------|
| Locks entire method | Locks specific code |
| `synchronized void doGet()` | `synchronized(obj) { }` |
| Coarse-grained | Fine-grained |
| Easier to write | Better performance |

---

## Next Topic: [Load On Startup →](./16_Load_On_Startup.md)
