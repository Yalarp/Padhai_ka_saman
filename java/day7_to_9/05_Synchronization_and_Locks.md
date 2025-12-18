# Synchronization and Locks in Java

When multiple threads share the same memory (object), there is a risk of **Data Corruption** due to **Race Conditions**. Synchronization is the mechanism to control access to shared resources.

## 1. The Race Condition Problem

A race condition occurs when two or more threads access a shared resource (like a variable or file) concurrently, and at least one of them modifies it. The final result depends on the timing of execution, which is unpredictable.

**Example Scenario**:
- Thread A reads a file.
- Thread B writes to the same file.
- If they interleave, data becomes corrupted.

---

## 2. Synchronization Solution

To avoid race conditions, we must ensure **Mutual Exclusion**. Only one thread should exact the critical section of code at a time. In Java, this is achieved using the `synchronized` keyword.

### The Concept of "LOCK" (Monitor)

Every object in Java has a built-in **Lock** (or Monitor).
-   To execute a `synchronized` block/method, a thread must first **acquire the lock** of the object.
-   Once acquired, other threads attempting to enter any synchronized block/method of the **same object** are blocked (put in "Seeking Lock" state).
-   When the thread finishes the block, it **releases the lock** automatically.

---

## 3. Synchronized Types

### A. Synchronized Method

The entire method is protected. The lock is obtained on the instance (`this`) executing the method.

```java
class Counter {
    private int count = 0;

    // Only one thread can execute this at a time for a given Counter object
    public synchronized void increment() {
        count++;
    }
}
```

### B. Synchronized Block

Used when you want to protect only a specific part of the code (Critical Section) to improve performance. You must specify which object's lock to acquire.

```java
public void run() {
    System.out.println("Non-critical code");
    
    // Acquire lock on 'this' object
    synchronized(this) {
        for(int i=0; i<5; i++) {
            System.out.println("Critical Section: " + i);
        }
    }
}
```

---

## 4. Object Lock Rules (Detailed)

1.  **Per Object**: Locks belong to objects, not methods. If you have two synchronized methods `m1()` and `m2()` in an object, and Thread A is executing `m1()`, Thread B **cannot** execute `m2()` because Thread A holds the lock of the object.
2.  **Different Objects**: If Thread A has a lock on Object 1, Thread B is free to acquire a lock on Object 2.

#### Code Analysis: `Th8.java` Logic

```java
// Logic from Th8 example
class Th8 implements Runnable {
    public void run() {
        synchronized(this) {
            // ... logic ...
        }
    }
    
    public static void main(String args[]) {
        Th8 ob1 = new Th8();
        Th8 ob2 = new Th8(); // Different object
        
        Thread t1 = new Thread(ob1);
        Thread t2 = new Thread(ob2);
        
        t1.start();
        t2.start();
    }
}
```
**Outcome**: Properties `t1` and `t2` run **simultaneously** because they are locking on **different objects** (`ob1` vs `ob2`). There is no contention.

---

## 5. Class Lock (Static Synchronization)

What if the shared resource is `static`? A static variable is common to all objects of the class. An object lock (`this`) is not sufficient because multiple objects could exist.

**Solution**: **Class Lock**.
-   Every class in Java corresponds to a `java.lang.Class` instance.
-   A `static synchronized` method acquires the lock on the `Class` object (e.g., `Th9.class`), not instances.
-   This ensures mutual exclusion across **all** instances of that class for that static method.

```java
class Shared {
    // Acquires lock on Shared.class
    public static synchronized void print() {
        // Critical static code
    }
}
```

---

## 6. Deadlock

While synchronization solves race conditions, it introduces the risk of **Deadlock**.
-   **Deadlock**: Two threads are waiting for each other to release a lock forever.
-   **Scenario**:
    -   Thread A holds Lock 1 and waits for Lock 2.
    -   Thread B holds Lock 2 and waits for Lock 1.
-   Java does not resolve deadlocks automatically; the programmer must design carefully to avoid circular dependencies.

---

## Summary Table

| Type | Keyword Usage | Lock Acquired On | Scope |
| :--- | :--- | :--- | :--- |
| **Object Lock** | `synchronized void m()` | Current Object (`this`) | Specific Instance |
| **Block Lock** | `synchronized(obj) { }` | Specified Object (`obj`) | Specific Block |
| **Class Lock** | `static synchronized void m()` | Class Object (`MyClass.class`) | All Instances |
