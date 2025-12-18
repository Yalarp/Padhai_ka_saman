# Inter-Thread Communication

Thread communication allows synchronized threads to communicate with each other. It is a mechanism where a thread is paused running in its critical section and another thread is allowed to enter (or lock) in the same critical section to be executed.

## 1. Why do we need Communication?

By default, threads run independently. Synchronization prevents data corruption (race condition), but it can lead to **inefficiency** or **Deadlocks**.

**Example Problem**: Polling (Busy Waiting).
If a Consumer thread needs to process data produced by a Producer, it might keep checking "Is data ready?" in a loop. This wastes CPU cycles.

**Better Solution**:
- Consumer: "I will go to sleep (WAIT). Wake me up when data is ready."
- Producer: "I have produced data. Wake up (NOTIFY) the consumer."

---

## 2. Key Methods in `java.lang.Object`

Java provides three final methods for communication. They **must** be called from within a `synchronized` context.

| Method | Description |
| :--- | :--- |
| `wait()` | Causes the current thread to release the lock and go to the **Wait Pool** until another thread notifies it. |
| `notify()` | Wakes up a single thread waiting on this object's monitor. The woken thread enters the **Seeking Lock** state. |
| `notifyAll()` | Wakes up **all** the threads waiting on this object's monitor. |

### Important Interview Questions:
1.  **Why behave these methods in `Object` class instead of `Thread`?**
    -   Because these methods work on **Locks**, and locks belong to **Objects**, not threads.
2.  **Difference between `sleep()` and `wait()`?**
    -   `sleep()`: Thread pauses but **keeps the lock**.
    -   `wait()`: Thread pauses and **releases the lock**.

---

## 3. Producer-Consumer Example

The classic problem where a Producer generates data and a Consumer uses it. They must work in coordination: Producer shouldn't produce if buffer is full; Consumer shouldn't consume if buffer is empty.

#### Code Analysis: `Transaction.java`

```java
// 1. SHARED RESOURCE (Monitor)
class Monitor {
    int token;
    boolean value_set = false;

    // PRODUCE METHOD
    synchronized public void set(int k) {
        // While data is already present, wait for consumer to take it
        while(value_set) {
            try {
                wait(); // Releases lock, waits for notify
            } catch(InterruptedException ie) { }
        }
        
        // Critical Section: Produce Data
        token = k;
        System.out.println("Set  " + token);
        
        value_set = true; // Mark as full
        notifyAll(); // Wake up Consumer
    }

    // CONSUME METHOD
    synchronized public int get() {
        // While no data is present, wait for producer
        while(!value_set) {
            try {
                wait(); // Releases lock, waits for notify
            } catch(InterruptedException ie) { }
        }
        
        // Critical Section: Consume Data
        value_set = false; // Mark as empty
        System.out.println("Get  " + token);
        
        notifyAll(); // Wake up Producer
        return token;
    }
}

// 2. PRODUCER THREAD
class Producer implements Runnable {
    Monitor obj;
    Producer(Monitor obj) { this.obj = obj; }
    
    public void run() {
        int i = 0;
        while(true) {
            obj.set(i++); // Infinite production
            try { Thread.sleep(200); } catch(InterruptedException ie) { }
        }
    }
}

// 3. CONSUMER THREAD
class Consumer implements Runnable {
    Monitor obj;
    Consumer(Monitor obj) { this.obj = obj; }
    
    public void run() {
        while(true) {
            obj.get(); // Infinite consumption
            try { Thread.sleep(200); } catch(InterruptedException ie) { }
        }
    }
}

// 4. MAIN EXECUTION
public class Transaction {
    public static void main(String args[]) {
        Monitor m = new Monitor(); // Shared Object
        Producer p = new Producer(m);
        Consumer c = new Consumer(m);
        
        Thread t1 = new Thread(p);
        Thread t2 = new Thread(c);
        
        t1.start();
        t2.start();
    }
}
```

**Execution Flow**:
1.  **Consumer** starts. Buffer is empty (`value_set = false`). Calls `wait()`. Releases lock.
2.  **Producer** acquires lock. Checks loop (buffer not full), proceeds.
3.  Producer sets token (0), sets `value_set = true`, calls `notifyAll()`. Releases lock (after method end).
4.  **Consumer** wakes up, acquires lock. Checks loop (buffer IS full now). Exits loop.
5.  Consumer gets token (0), sets `value_set = false`, calls `notifyAll()`.
6.  Loop repeats.

This strict alternation ("Set 0", "Get 0", "Set 1", "Get 1") proves the communication works. Without `wait/notify`, you might see "Set 0", "Set 1", "Set 2" (Overwriting) or "Get 0", "Get 0" (Duplicate reads).
