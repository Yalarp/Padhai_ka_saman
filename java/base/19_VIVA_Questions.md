# ❓ VIVA Questions Compilation - JavaSE Days 1-4

## Table of Contents
1. [Java Basics](#java-basics)
2. [Data Types and Variables](#data-types-and-variables)
3. [Operators and Control Flow](#operators-and-control-flow)
4. [OOP Fundamentals](#oop-fundamentals)
5. [Constructors and Static](#constructors-and-static)
6. [Memory Management](#memory-management)
7. [Arrays and Packages](#arrays-and-packages)
8. [Inheritance](#inheritance)
9. [Polymorphism](#polymorphism)
10. [Abstract Classes and Interfaces](#abstract-classes-and-interfaces)
11. [Design Patterns](#design-patterns)

---

## Java Basics

### Q1: What is Java?
**Answer**: Java is a high-level, class-based, object-oriented programming language designed to be platform-independent following "Write Once, Run Anywhere" (WORA) principle.

### Q2: What is the difference between JDK, JRE, and JVM?
**Answer**:
- **JVM**: Executes bytecode, platform-specific
- **JRE**: JVM + Libraries, for running programs
- **JDK**: JRE + Development tools, for developing programs

### Q3: Why is Java platform independent?
**Answer**: Java source code compiles to bytecode (.class) which runs on JVM. The same bytecode runs on any platform that has JVM.

### Q4: What is bytecode?
**Answer**: Intermediate code generated after compilation. Platform-independent, executed by JVM.

### Q5: Why is main() method static?
**Answer**: So JVM can call it without creating an object. If not static, JVM would need to instantiate the class first.

### Q6: What are the features of Java?
**Answer**: Simple, Object-Oriented, Platform Independent, Secure, Robust, Multithreaded, Portable, High Performance.

### Q7: What is the role of String[] args in main?
**Answer**: Receives command-line arguments passed to the program.

---

## Data Types and Variables

### Q8: What are primitive data types in Java?
**Answer**: byte, short, int, long (integers); float, double (decimals); char (character); boolean (true/false).

### Q9: What is the default value of int and boolean?
**Answer**: int = 0, boolean = false (for instance/static variables only).

### Q10: Why is char 2 bytes in Java?
**Answer**: Java uses Unicode (UTF-16) which requires 2 bytes to represent characters from all languages.

### Q11: What is type casting?
**Answer**: Converting one data type to another. Implicit (widening, automatic) or explicit (narrowing, manual).

### Q12: What happens when you assign 130 to byte?
**Answer**: Overflow occurs. 130 becomes -126 because byte range is -128 to 127.

### Q13: Why does "float f = 3.5;" give error?
**Answer**: 3.5 is double by default. Use 3.5f or (float)3.5 for float.

### Q14: Can we convert boolean to int?
**Answer**: No. Boolean is not compatible with any numeric type in Java.

### Q15: What is the difference between local and instance variables?
**Answer**: Local variables are in methods (no default), instance variables are in class (have defaults).

---

## Operators and Control Flow

### Q16: What is the difference between / and %?
**Answer**: `/` gives quotient, `%` gives remainder.

### Q17: What is short-circuit evaluation?
**Answer**: In `&&` and `||`, second operand may not be evaluated if result is determined by first.

### Q18: What is the difference between & and &&?
**Answer**: `&` is bitwise AND (evaluates both). `&&` is logical AND (short-circuits).

### Q19: What is the output of ~5?
**Answer**: -6 (inverts all bits including sign).

### Q20: What is difference between while and do-while?
**Answer**: while checks condition first (may execute 0 times). do-while executes at least once.

### Q21: What happens if break is missing in switch?
**Answer**: Fall-through occurs - execution continues to next case.

### Q22: What is a labeled loop?
**Answer**: A named loop that allows breaking/continuing from nested loops.

---

## OOP Fundamentals

### Q23: What is Object-Oriented Programming?
**Answer**: Programming paradigm based on objects containing data and behavior, with principles: Encapsulation, Abstraction, Inheritance, Polymorphism.

### Q24: What is the difference between class and object?
**Answer**: Class is blueprint/template. Object is actual instance created from class.

### Q25: What is encapsulation?
**Answer**: Bundling data and methods, hiding data via private access, providing controlled access via getters/setters.

### Q26: What is abstraction?
**Answer**: Hiding implementation complexity, showing only relevant details to user.

### Q27: What is the purpose of 'this' keyword?
**Answer**: Refers to current object. Used to differentiate instance variables from parameters.

### Q28: Can 'this' be used in static method?
**Answer**: No. Static methods don't belong to any object.

### Q29: What is method overloading?
**Answer**: Multiple methods with same name but different parameters (number, type, or order).

### Q30: Is overloading based on return type?
**Answer**: No. Return type alone cannot differentiate overloaded methods.

---

## Constructors and Static

### Q31: What is a constructor?
**Answer**: Special method to initialize objects. Same name as class, no return type.

### Q32: What is default constructor?
**Answer**: No-argument constructor. Compiler provides if no constructor defined.

### Q33: Can constructor be private?
**Answer**: Yes. Used for Singleton pattern, utility classes.

### Q34: What is constructor overloading?
**Answer**: Multiple constructors with different parameters.

### Q35: What is a static variable?
**Answer**: Class variable shared by all objects. One copy, allocated when class loads.

### Q36: Can we access non-static from static method?
**Answer**: Not directly. Need object reference.

### Q37: What is static block?
**Answer**: Block that executes when class loads, before main. Used for static initialization.

### Q38: What is the difference between static and instance block?
**Answer**: Static block runs once (class loading). Instance block runs for each object creation.

### Q39: What is constructor chaining?
**Answer**: One constructor calling another using this() or super().

### Q40: When is static block executed?
**Answer**: When class is loaded, before any objects are created.

---

## Memory Management

### Q41: What is Garbage Collection?
**Answer**: Automatic process of reclaiming memory by deleting unreferenced objects.

### Q42: Can we force Garbage Collection?
**Answer**: No. We can only request via System.gc(). JVM decides when to run.

### Q43: What is finalize() method?
**Answer**: Called by GC before destroying object. Not guaranteed to execute.

### Q44: What is the difference between final, finally, finalize?
**Answer**: final = constant/prevent override; finally = always-execute block; finalize = GC callback.

### Q45: When is object eligible for GC?
**Answer**: When no live reference points to it (nullified, reassigned, out of scope).

### Q46: What is the final keyword?
**Answer**: For constants (variables), prevent overriding (methods), prevent inheritance (classes).

### Q47: Can final variable be changed?
**Answer**: No. Once initialized, value cannot be reassigned.

---

## Arrays and Packages

### Q48: What is an array?
**Answer**: Collection of similar data types in contiguous memory, fixed size, zero-indexed.

### Q49: Are arrays objects in Java?
**Answer**: Yes. They inherit from Object and have properties like length.

### Q50: What is difference between length and length()?
**Answer**: length is array property. length() is String method.

### Q51: What is ArrayIndexOutOfBoundsException?
**Answer**: Exception when accessing index outside valid range (0 to length-1).

### Q52: What is a package?
**Answer**: Collection of related classes. Used for organization, naming conflicts, access control.

### Q53: What is java.lang package?
**Answer**: Default package auto-imported. Contains String, Object, Math, System.

### Q54: What is a JAR file?
**Answer**: Java Archive - compressed file bundling class files for distribution.

### Q55: Does import affect performance?
**Answer**: No. Import is compile-time convenience only.

---

## Inheritance

### Q56: What is inheritance?
**Answer**: Mechanism where child class acquires properties/behaviors of parent class. IS-A relationship.

### Q57: Why doesn't Java support multiple inheritance?
**Answer**: To avoid Diamond Problem (ambiguity when same method in multiple parents).

### Q58: What is super keyword?
**Answer**: Refers to parent class. Access parent's variables, methods, constructors.

### Q59: Can constructors be inherited?
**Answer**: No. But child constructor always calls parent constructor (explicitly or implicitly).

### Q60: What is java.lang.Object?
**Answer**: Root class of all Java classes. Every class inherits from Object.

### Q61: What is constructor chaining in inheritance?
**Answer**: When child object created, constructors called in chain: Object → Parent → Child.

### Q62: Can private members be inherited?
**Answer**: Inherited in memory but not accessible. Access through parent's public methods.

---

## Polymorphism

### Q63: What is polymorphism?
**Answer**: "Many forms" - objects behaving differently based on actual type while referenced by parent type.

### Q64: What is compile-time vs runtime polymorphism?
**Answer**: Compile-time = overloading (resolved at compile). Runtime = overriding (resolved at execution).

### Q65: What is method overriding?
**Answer**: Redefining parent's method in child with same signature.

### Q66: What is dynamic method dispatch?
**Answer**: Mechanism where overridden method is called based on actual object type at runtime.

### Q67: What is upcasting?
**Answer**: Assigning child object to parent reference. Implicit, always safe.

### Q68: What is downcasting?
**Answer**: Casting parent reference to child type. Explicit, may throw ClassCastException.

### Q69: What is instanceof operator?
**Answer**: Checks if object is instance of specific class. Returns boolean.

### Q70: When does ClassCastException occur?
**Answer**: When casting object to class it's not actually an instance of.

---

## Abstract Classes and Interfaces

### Q71: What is an abstract class?
**Answer**: Class with abstract keyword that cannot be instantiated. May have abstract and concrete methods.

### Q72: What is an interface?
**Answer**: Completely abstract type defining contract. All methods abstract (before Java 8).

### Q73: Can abstract class have constructor?
**Answer**: Yes. Called when child object is created via super().

### Q74: What is difference between abstract class and interface?
**Answer**: Abstract = partial abstraction, can have state. Interface = full abstraction, only constants.

### Q75: Can interface have variables?
**Answer**: Only constants (public static final).

### Q76: What are default methods in interface?
**Answer**: Methods with implementation (Java 8+). Allow adding methods without breaking implementations.

### Q77: Can we have static methods in interface?
**Answer**: Yes (Java 8+). Called using InterfaceName.methodName().

### Q78: Can class extend abstract and implement interface?
**Answer**: Yes. `class Child extends Abstract implements Interface`.

---

## Design Patterns

### Q79: What is Singleton pattern?
**Answer**: Design pattern ensuring class has only one instance with global access point.

### Q80: How to implement Singleton?
**Answer**: Private static instance, private constructor, public static getInstance() method.

### Q81: What is lazy initialization?
**Answer**: Creating instance only when first requested, not at class loading.

### Q82: Why is simple lazy singleton not thread-safe?
**Answer**: Multiple threads may each create instance before any assignment completes.

### Q83: What is double-checked locking?
**Answer**: Pattern checking null twice - once without sync, once with sync for safe creation.

### Q84: What is Bill Pugh Singleton?
**Answer**: Uses static inner class. Lazy, thread-safe without synchronization.

### Q85: How can Singleton be broken?
**Answer**: Through reflection, serialization, or cloning.

---

## Quick Comparison Tables

### Access Modifiers

| Modifier | Class | Package | Subclass | World |
|----------|-------|---------|----------|-------|
| public | ✓ | ✓ | ✓ | ✓ |
| protected | ✓ | ✓ | ✓ | ✗ |
| default | ✓ | ✓ | ✗ | ✗ |
| private | ✓ | ✗ | ✗ | ✗ |

### Overloading vs Overriding

| Aspect | Overloading | Overriding |
|--------|-------------|------------|
| Methods | Same name, different params | Same signature |
| Classes | Same class | Parent-child |
| Binding | Compile-time | Runtime |
| Return | Can differ | Same or covariant |

### Abstract Class vs Interface

| Feature | Abstract Class | Interface |
|---------|----------------|-----------|
| Methods | Abstract + Concrete | Abstract only* |
| Variables | Any | Constants only |
| Constructor | Can have | Cannot have |
| Inheritance | Single | Multiple |

*Before Java 8

---

*This compilation covers all major VIVA questions from JavaSE Days 1-4.*
