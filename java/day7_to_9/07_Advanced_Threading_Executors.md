# Advanced Threading: Executors Framework

Creating a new thread for every task (`new Thread()`) is expensive and can lead to resource exhaustion if thousands of requests arrive. The **Executors Framework** (introduced in Java 5) manages a **Thread Pool** to reuse threads efficiently.

## 1. Thread Pool Concept

A **Thread Pool** is a collection of pre-created (and idle) threads.
-   When a task arrives, a thread from the pool picks it up, executes it, and then returns to the pool to wait for the next task.
-   **Benefit**: Eliminates the overhead of creating/destroying threads repeatedly. Better system stability.

**Interfaces**: `java.util.concurrent.ExecutorService` is the primary interface.
**Factory**: `java.util.concurrent.Executors` class provides factory methods to create thread pools.

---

## 2. Types of Thread Pools

### A. Cached Thread Pool (`newCachedThreadPool`)

-   Creates new threads as needed but reuses previously constructed threads when they are available.
-   Good for short-lived asynchronous tasks.
-   No limit on the number of threads (can expand indefinitely).

#### Code Example: `ExecutorDemo1.java`

```java
import java.util.concurrent.*;

class Task implements Runnable {
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println("Processing " + i + " by " + Thread.currentThread().getName());
            try { Thread.sleep(100); } catch (Exception e) {}
        }
    }
}

public class ExecutorDemo1 {
    public static void main(String args[]) {
        // Create Cached Thread Pool
        ExecutorService exec = Executors.newCachedThreadPool();

        // Submit tasks
        for (int i = 0; i < 3; i++) {
            exec.execute(new Task());
        }

        System.out.println("Tasks submitted");

        // Shutdown: No new tasks accepted. Existing tasks continue.
        exec.shutdown(); 
        
        // exec.execute(new Task()); // This would throw RejectedExecutionException
    }
}
```

### B. Fixed Thread Pool (`newFixedThreadPool`)

-   Reuses a fixed number of threads.
-   If all threads are active, additional tasks wait in a queue.
-   Good for controlling resource usage (CPU/Memory).

#### Code Example: `ExecutorDemo2.java`

```java
public class ExecutorDemo2 {
    public static void main(String args[]) {
        // Create pool with exactly 2 threads
        ExecutorService exec = Executors.newFixedThreadPool(2);

        // Submit 3 tasks
        for(int i=0; i<3; i++) {
            exec.execute(new Task());
        }
        
        // Result: 
        // Task 1 -> Thread-1
        // Task 2 -> Thread-2
        // Task 3 -> Waits until Thread-1 or Thread-2 is free
        
        exec.shutdown();
    }
}
```

---

## 3. Important ExecutorService Methods

1.  `execute(Runnable)`: Submits a Runnable task for execution.
2.  `shutdown()`: Initiates an orderly shutdown. Previously submitted tasks are executed, but no new tasks will be accepted.
3.  `shutdownNow()`: Attempts to stop all actively executing tasks and halts the processing of waiting tasks.

---

## 4. Executors with Synchronization

Using Executors doesn't negate the need for synchronization if tasks access shared resources.

#### Code Example: `ExecutorDemo3.java` (Class Lock)

```java
import java.util.concurrent.*;

class SharedTask implements Runnable {
    // Static Synchronized: Class Lock
    synchronized static void disp() {
        for(int i=0; i<5; i++) {
            System.out.println("Hello " + i + " " + Thread.currentThread());
        }
    }

    public void run() {
        disp();
    }
}

public class ExecutorDemo3 {
    public static void main(String args[]) {
        ExecutorService exec = Executors.newFixedThreadPool(2);
        
        // Even with multiple instances, they share the Class Lock
        for(int i=0; i<3; i++) {
            exec.execute(new SharedTask());
        }
        
        exec.shutdown();
    }
}
```
**Observation**: Even though 2 threads are available in the pool, the output will be **sequential** because `disp()` is `static synchronized`. The second thread must wait for the Class Lock.
