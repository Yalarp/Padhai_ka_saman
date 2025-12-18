# Multi-Threading Fundamentals

Multi-tasking is the ability of an operating system to execute multiple tasks simultaneously. In Java, this is achieved through **Thread-based multi-tasking**.

## 1. Process-based vs Thread-based Multi-tasking

| Feature | Process-based | Thread-based |
| :--- | :--- | :--- |
| **Definition** | Running multiple programs (processes) simultaneously. | Running multiple independent parts (threads) of the same program. |
| **Example** | Using Word and Excel at the same time. | Typing text (Thread 1) and checking spelling (Thread 2) inside Word. |
| **Memory** | Processes have separate memory address spaces. | Threads **share** the same memory area. |
| **Context Switching** | Switching between processes is "Heavyweight" (expensive). | Switching between threads is "Lightweight" (cheap). |
| **Communication** | IPC (Inter-Process Communication) is costly. | Inter-thread communication is fast and easy. |

**Key Concept**: A URL or CPU can essentially handle one task at a time (unless multi-core). The OS uses **Context Switching** (jumping very fast between tasks) to give the illusion of parallelism.

---

## 2. Thread Scheduler

The component of the JVM responsible for deciding which thread runs is the **Thread Scheduler**.

-   **Pre-emptive Scheduling**: A higher priority thread can snatch the CPU from a lower priority thread immediately.
-   **Time-Slicing**: Each thread gets a fixed time slot (e.g., 100ms) to execute, round-robin style.
-   *Note*: The scheduling algorithm depends on the underlying OS. Java does not guarantee the order of execution.

---

## 3. Creating Threads in Java

There are two ways to create a thread in Java:
1.  Extending `java.lang.Thread` class.
2.  Implementing `java.lang.Runnable` interface.

### Method 1: Extending Thread Class

```java
// 1. Extend Thread
class Th1 extends Thread {
    // 2. Override run() method (Execution Body)
    public void run() {
        for(int i=0; i<5; i++) {
            System.out.println("Hello " + i);
        }
    }
    
    public static void main(String args[]) {
        Th1 t1 = new Th1();
        
        // 3. Call start() to register with Scheduler
        t1.start(); 
        
        // Note: Main thread continues its own execution
    }
}
```

**What happens if you call `run()` directly?**
If you call `t1.run()` instead of `t1.start()`, **no new thread is created**. The code runs in the current (main) thread as a normal method call. The call stack remains single.

### Method 2: Implementing Runnable Interface (Preferred)

This method is preferred because Java supports multiple interface inheritance but only single class inheritance. If you extend `Thread`, you cannot extend any other class.

```java
// 1. Implement Runnable
class Th5 implements Runnable {
    public void run() {
        for(int i=0; i<5; i++) {
            System.out.println("Hello " + i);
        }
    }
    
    public static void main(String args[]) {
        Th5 task = new Th5(); // Create task
        
        // 2. Create Thread object and pass the task
        Thread t1 = new Thread(task); 
        Thread t2 = new Thread(task);
        
        t1.start();
        t2.start();
    }
}
```

---

## 4. Key Thread Methods

-   `start()`: Registers the thread with the Thread Scheduler. It allocates a new stack for the thread.
-   `run()`: The entry point for the thread. When `run()` finishes, the thread dies.
-   `setName(String)` / `getName()`: Identification of threads.
-   `setPriority(int)`: Range 1 (Min) to 10 (Max). Default is 5 (Normal).
    -   `Thread.MIN_PRIORITY` (1)
    -   `Thread.NORM_PRIORITY` (5)
    -   `Thread.MAX_PRIORITY` (10)
-   `currentThread()`: Static method returning the reference of the thread executing the current line.
-   `sleep(long ms)`: Pauses the execution of the current thread for specified milliseconds. Throws `InterruptedException`.
-   `join()`: Waits for a thread to die. If `t1.join()` is called in `main`, the `main` thread waits until `t1` completes.

#### Multiple Threads Example (`Th4_a.java` Logic)

```java
class MyThread extends Thread {
    public void run() {
        for(int i=0; i<5; i++) {
            // Displaying Thread Name
            System.out.println("Hello " + Thread.currentThread().getName() + " " + i);
            try {
                Thread.sleep(100); // Pause for 100ms
            } catch(InterruptedException ie) {
                ie.printStackTrace();
            }
        }
    }
    
    public static void main(String args[]) {
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();
        
        t1.setName("first");
        t2.setName("second");
        
        t1.start();
        t2.start();
    }
}
```

---

## 5. Daemon Threads

A **Daemon Thread** is a low-priority background thread that provides services to user threads. It automatically dies when all user threads (non-daemon threads) have finished execution.

-   **Example**: Garbage Collector, Finalizer.
-   **Usage**: `t1.setDaemon(true)` before starting the thread.

```java
Thread t1 = new Thread(new Task());
t1.setDaemon(true); // Now this is a daemon thread
t1.start();
```

---

## 6. Thread Lifecycle (States)

1.  **New (Born)**: Thread object created (`new Thread()`) but `start()` not called.
2.  **Runnable (Ready)**: `start()` is called. Thread is registered with scheduler and waiting for CPU.
3.  **Running**: Scheduler assigns CPU. `run()` method is executing.
4.  **Blocked/Waiting**: Thread is paused due to `sleep()`, `wait()`, `join()`, or waiting for I/O.
5.  **Terminated (Dead)**: `run()` method execution completes.
