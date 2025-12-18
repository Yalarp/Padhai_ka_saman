# ReentrantLock and Condition Objects

Java 5 introduced the `java.util.concurrent.locks` package to provide more flexible and powerful locking mechanisms than the implicit `synchronized` keyword.

## 1. ReentrantLock vs Synchronized

| Feature | Synchronized (Implicit Lock) | ReentrantLock (Explicit Lock) |
| :--- | :--- | :--- |
| **Flexibility** | Rigid. Must release lock in same block. | Flexible. Can lock in one method and unlock in another. |
| **Fairness** | No fairness guarantee (Threads acquire arbitrarily). | Supports Fairness (FIFO order of waiting threads). |
| **Waiting** | Thread waits forever to acquire lock. | Can try to acquire lock with timeout (`tryLock`). |
| **API** | Simple (Language keyword). | Rich API (`getQueueLength`, `isLocked`, etc.). |

---

## 2. Basic Usage Pattern

You must **manually** release the lock, preferably in a `finally` block to ensure it is released even if an exception occurs.

#### Code Example: `Demo1.java`

```java
import java.util.concurrent.locks.ReentrantLock;

public class Demo1 implements Runnable {
    ReentrantLock mylock = new ReentrantLock(); // The Lock Object

    public void run() {
        // 1. Acquire Lock
        mylock.lock(); 
        try {
            // Critical Section
            for(int i=0; i<5; i++) {
                System.out.println("Hello " + i);
                Thread.sleep(100);
            }
        } catch(InterruptedException ie) {
             System.out.println(ie);
        } finally {
            // 2. Release Lock (Crucial!)
            mylock.unlock(); 
        }
    }
}
```

---

## 3. Advanced Features

### A. Polling for Lock (`tryLock`)

This method allows a thread to try acquiring a lock without getting blocked forever. It prevents Deadlocks.

#### Code Example: `TryLockDemo1.java`

```java
public void run() {
    while(true) {
        // Try to acquire lock immediately. Returns true if successful.
        if (mylock.tryLock()) {
            try {
                // Critical Section
                System.out.println("Got lock!");
            } finally {
                mylock.unlock();
            }
            break; // Exit loop after work is done
        } else {
            // Lock not available, do alternative work instead of blocking
            System.out.println("Doing something else...");
        }
    }
}
```

### B. Timed Lock Wait

```java
// Wait for up to 2 seconds to acquire lock
if (mylock.tryLock(2, TimeUnit.SECONDS)) { ... }
```

---

## 4. Condition Objects (Replacement for wait/notify)

When using `ReentrantLock`, we cannot use `wait()`/`notify()` because we don't hold the Object Monitor. Instead, we use `Condition` objects.

-   `wait()`  --->  `condition.await()`
-   `notify()` ---> `condition.signal()`
-   `notifyAll()` ---> `condition.signalAll()`

#### Producer-Consumer with ReentrantLock (`Transaction.java`)

```java
import java.util.concurrent.locks.*;

class Monitor {
    ReentrantLock mylock = new ReentrantLock();
    // Create a Condition bound to this lock
    Condition value = mylock.newCondition(); 
    
    int token;
    boolean value_set = false;

    public void set(int k) {
        // 1. Lock
        mylock.lock();
        try {
            while(value_set) {
                try {
                    // 2. Await (Releases lock)
                    value.await(); 
                } catch(InterruptedException ie) { }
            }
            token = k;
            System.out.println("Set  " + token);
            value_set = true;
            
            // 3. Signal (Notify)
            value.signalAll(); 
        } finally {
            // 4. Unlock
            mylock.unlock(); 
        }
    }

    public int get() {
        mylock.lock();
        try {
            while(!value_set) {
                try {
                    value.await();
                } catch(InterruptedException ie) { }
            }
            value_set = false;
            System.out.println("Get  " + token);
            value.signalAll();
            return token;
        } finally {
            mylock.unlock();
        }
    }
}
```

**Key Benefit**: You can have **multiple conditions** for a single lock (e.g., `bufferNotFull` and `bufferNotEmpty`), allowing finer control than a single `wait()` queue.
