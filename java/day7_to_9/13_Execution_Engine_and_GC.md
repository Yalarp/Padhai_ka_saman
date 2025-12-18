# Execution Engine and Garbage Collection

The **Execution Engine** is the component of the JVM that executes the bytecode loaded into the Runtime Data Areas. It reads the bytecode stream and executes the instructions.

## 1. Components of Execution Engine

### A. Interpreter
-   **Role**: Reads bytecode instructions one by one and executes them.
-   **Pros**: Fast start-up time (starts executing immediately).
-   **Cons**: Slow execution for repetitive code because it interprets the same line every time it is called.

### B. JIT (Just-In-Time) Compiler
-   **Role**: Improvements performance by compiling bytecode into **native machine code** at runtime.
-   **How it works**:
    1.  JVM identifies "Hot Spots" (frequently executed methods/loops) using a **HotSpot Profiler**.
    2.  JIT compiles these hot spots into native code.
    3.  The next time the method is called, the native code is executed directly (no interpretation needed).
-   **Pros**: Much faster execution for long-running applications.
-   **Cons**: Slower start-up (compilation takes time and memory).

**Conclusion**: Modern JVMs use a mixed approach. They start with the Interpreter for fast startup and switch to JIT for hot methods.

---

## 2. Garbage Collection (GC)

Garbage Collection is the process of reclaiming memory occupied by objects that are no longer reachable (referenced) by the application.

### A. Generations
The Heap is divided into generations to optimize GC performance based on the hypothesis that "most objects die young".

1.  **Young Generation**:
    -   **Eden Space**: Where all new objects are allocated.
    -   **Survivor Spaces (S0, S1)**: Holds objects that survived a few GC cycles.
2.  **Old (Tenured) Generation**:
    -   Holds long-lived objects that survived many GC cycles in the Young Generation.

### B. Types of GC

| Type | Target Area | Trigger | Speed |
| :--- | :--- | :--- | :--- |
| **Minor GC** | Young Generation | When Eden space is full. | Very Fast. |
| **Major GC** (Full GC) | Old Generation | When Old Gen is full. | Slow (Stop-the-world pause). |

### C. GC Process Flow
1.  New Object -> **Eden**.
2.  Eden Full? -> **Minor GC**.
    -   Unreachable objects in Eden are removed.
    -   Survivors move to **S0/S1**.
    -   Objects in S0/S1 that survive enough cycles (Threshold) move to **Old Generation**.
3.  Old Gen Full? -> **Major GC**.
    -   Cleans up the entire heap (Old + Young).
    -   Application pauses during this time.

### D. System.gc()
Calling `System.gc()` is a request to the JVM to run the Garbage Collector.
-   **Note**: It is **not guaranteed** to run. The JVM determines if it is necessary or valid to run GC at that moment.
