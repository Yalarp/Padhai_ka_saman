# JVM Architecture

The **Java Virtual Machine (JVM)** is an abstract machine that provides a runtime environment in which Java bytecode can be executed. It has three main subsystems:
1.  **Class Loader Subsystem**
2.  **Runtime Data Areas (Memory)**
3.  **Execution Engine**

---

## 1. Class Loader Subsystem

This subsystem is responsible for **Loading**, **Linking**, and **Initialization** of class files (`.class`).

### A. Loading Phase
The JVM finds the binary representation of a class (bytecode) and loads it into the Method Area.
-   **Bootstrap ClassLoader**: Loads core Java libraries (`rt.jar`, `java.lang.*`). Implemented in native languages (C/C++).
-   **Extension ClassLoader**: Loads classes from `lib/ext` directory.
-   **Application (System) ClassLoader**: Loads classes from the CLASSPATH (your application code).

### B. Linking Phase
1.  **Verification**: Ensures the bytecode is valid and doesn't violate security constraints (e.g., no stack overflow, no illegal pointers).
2.  **Preparation**: Allocates memory for **static** variables and assigns default values (e.g., `int = 0`, `obj = null`).
3.  **Resolution**: Replaces symbolic references (logical names) with direct references (memory addresses).

### C. Initialization Phase
This is the final phase of class loading.
-   Static blocks are executed.
-   Static variables are assigned their **original values** (defined in code).
-   Executed from top to bottom.

---

## 2. Runtime Data Areas (Memory Model)

The JVM allows specific memory areas during execution.

| Memory Area | Shared? | Description |
| :--- | :--- | :--- |
| **Method Area** | Yes | Stores class-level data: Class Name, Static Variables, Method Code, Runtime Constant Pool. (Known as "MetaSpace" in Java 8+). |
| **Heap Area** | Yes | **All Objects** and Arrays are stored here. It is the largest memory area and is managed by the Garbage Collector. |
| **Stack Area** | No (Per Thread) | Stores Frames. Each method call creates a **Stack Frame** containing: Local Variables, Operand Stack, and Reference to Runtime Constant Pool. |
| **PC Register** | No (Per Thread) | Stores the address of the currently executing JVM instruction. |
| **Native Method Stack** | No (Per Thread) | Stores native method information (for C/C++ code called via JNI). |

---

## 3. Execution Engine

(Covered in Detail in Note 13)
The Execution Engine reads the bytecode from the runtime data areas and executes it. It contains the **Interpreter**, **JIT Compiler**, and **Garbage Collector**.

### Important Config
-   **Magic Number**: Every valid class file starts with `0xCAFEBABE`.
-   **Major/Minor Version**: JVM checks if the class file version is supported. A lower version JVM cannot run a class compiled by a higher version compiler (`UnsupportedClassVersionError`).
