# 📘 AOP with Spring Boot - Complete Guide

## Table of Contents
1. [What is AOP](#what-is-aop)
2. [Why Use AOP](#why-use-aop)
3. [AOP Terminology](#aop-terminology)
4. [Setting Up AOP in Spring Boot](#setting-up-aop-in-spring-boot)
5. [@Aspect and @Component](#aspect-and-component)
6. [@Pointcut Expressions](#pointcut-expressions)
7. [Advice Types](#advice-types)
8. [JoinPoint Interface](#joinpoint-interface)
9. [Complete Code Examples](#complete-code-examples)
10. [Execution Flow](#execution-flow)
11. [Quick Reference](#quick-reference)

---

## What is AOP

### Aspect Oriented Programming

```
┌─────────────────────────────────────────────────────────────────┐
│                    WHAT IS AOP?                                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   AOP = Aspect Oriented Programming                             │
│                                                                 │
│   Definition: A programming paradigm that allows separating     │
│   CROSS-CUTTING CONCERNS from the main business logic.          │
│                                                                 │
│   Cross-cutting concerns are functionality that affects         │
│   multiple parts of an application:                             │
│                                                                 │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ ✓ Logging         - Log method entry/exit               │   │
│   │ ✓ Security        - Check authorization                 │   │
│   │ ✓ Transactions    - Begin/commit/rollback               │   │
│   │ ✓ Performance     - Measure execution time              │   │
│   │ ✓ Caching         - Cache method results                │   │
│   │ ✓ Error Handling  - Catch and handle exceptions         │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Why Use AOP

### The Problem Without AOP

```
┌─────────────────────────────────────────────────────────────────┐
│              WITHOUT AOP (Code Tangling)                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   public class BookController {                                 │
│                                                                 │
│       public Book saveBook(Book book) {                         │
│           log.info("Entering saveBook");      // ← Logging      │
│           checkSecurity();                     // ← Security    │
│           startTransaction();                  // ← Transaction │
│           long start = System.currentTimeMillis(); // ← Perf    │
│                                                                 │
│           // ─────── ACTUAL BUSINESS LOGIC ───────              │
│           repository.save(book);                                │
│           // ─────────────────────────────────────              │
│                                                                 │
│           long end = System.currentTimeMillis();  // ← Perf     │
│           commitTransaction();                 // ← Transaction │
│           log.info("Exiting saveBook, time: "+(end-start));     │
│           return book;                                          │
│       }                                                         │
│   }                                                             │
│                                                                 │
│   Problems:                                                     │
│   ✗ Business logic buried in cross-cutting code                 │
│   ✗ Same code repeated in every method                          │
│   ✗ Hard to maintain and modify                                 │
│   ✗ Single Responsibility Principle violated                    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### The Solution With AOP

```
┌─────────────────────────────────────────────────────────────────┐
│              WITH AOP (Clean Separation)                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   // Controller - ONLY business logic                           │
│   public class BookController {                                 │
│       public Book saveBook(Book book) {                         │
│           return repository.save(book);  // That's it!          │
│       }                                                         │
│   }                                                             │
│                                                                 │
│   // Separate Aspect Classes handle cross-cutting concerns      │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @Aspect LoggingAspect      → Handles all logging        │   │
│   │ @Aspect SecurityAspect     → Handles all security       │   │
│   │ @Aspect TransactionAspect  → Handles all transactions   │   │
│   │ @Aspect PerformanceAspect  → Handles all timing         │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Benefits:                                                     │
│   ✓ Clean business logic                                        │
│   ✓ Reusable aspects                                            │
│   ✓ Easy to modify (change in one place)                        │
│   ✓ Separation of concerns                                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## AOP Terminology

### Key Terms Explained

```
┌─────────────────────────────────────────────────────────────────┐
│              AOP TERMINOLOGY                                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. ASPECT                                                     │
│   ──────────                                                    │
│   A class containing cross-cutting logic. Marked with @Aspect.  │
│   Example: LoggingAspect, SecurityAspect                        │
│                                                                 │
│   2. ADVICE                                                     │
│   ──────────                                                    │
│   The action taken by an aspect. The actual code that runs.     │
│   Types: @Before, @After, @AfterReturning, @AfterThrowing,      │
│          @Around                                                │
│                                                                 │
│   3. POINTCUT                                                   │
│   ──────────                                                    │
│   An expression that defines WHERE advice should apply.         │
│   Example: execution(* BookController.*(..))                    │
│   Means: All methods in BookController                          │
│                                                                 │
│   4. JOINPOINT                                                  │
│   ──────────                                                    │
│   A point in program execution where advice CAN be applied.     │
│   In Spring AOP: method execution                               │
│                                                                 │
│   5. TARGET                                                     │
│   ──────────                                                    │
│   The object being advised (the actual controller/service).     │
│                                                                 │
│   6. WEAVING                                                    │
│   ──────────                                                    │
│   The process of applying aspects to target objects.            │
│   Spring uses runtime weaving via proxies.                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Visual Representation

```
┌─────────────────────────────────────────────────────────────────┐
│              HOW AOP WORKS                                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Request                                                       │
│      │                                                          │
│      ▼                                                          │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │                    PROXY                                │   │
│   ├─────────────────────────────────────────────────────────┤   │
│   │  @Before advice runs ──────────────────────▶            │   │
│   │        │                                                │   │
│   │        ▼                                                │   │
│   │  ┌───────────────────────────────────────────────────┐  │   │
│   │  │           TARGET METHOD (Business Logic)          │  │   │
│   │  └───────────────────────────────────────────────────┘  │   │
│   │        │                                                │   │
│   │        ▼                                                │   │
│   │  @After advice runs ───────────────────────▶            │   │
│   └─────────────────────────────────────────────────────────┘   │
│      │                                                          │
│      ▼                                                          │
│   Response                                                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Setting Up AOP in Spring Boot

### Required Dependencies

Add to pom.xml:

```xml
<!-- AspectJ Runtime -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
</dependency>

<!-- Spring Boot AOP Starter -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

### Enable AOP in Application Class

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@SpringBootApplication
@EnableAspectJAutoProxy(proxyTargetClass=true)  // Enable AOP
public class DemoApplication 
{
    public static void main(String[] args) 
    {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

### Annotation Explained

| Parameter | Description |
|-----------|-------------|
| `@EnableAspectJAutoProxy` | Enables Spring AOP with AspectJ support |
| `proxyTargetClass=true` | Use CGLIB proxies (allows proxying classes without interfaces) |

---

## @Aspect and @Component

### Creating an Aspect Class

```java
package com.example.demo;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Aspect      // Marks class as an Aspect
@Component   // Required for Spring to detect and manage
public class SampleAspect 
{
    // Pointcut and Advice methods go here
}
```

### Important Notes

```
┌─────────────────────────────────────────────────────────────────┐
│              ASPECT CLASS REQUIREMENTS                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. @Aspect Annotation                                         │
│      └── Marks class as containing AOP advice                   │
│                                                                 │
│   2. @Component Annotation                                      │
│      └── Required for Spring to auto-detect the aspect          │
│      └── Without it, Spring won't know about the aspect!        │
│                                                                 │
│   3. Placement                                                  │
│      └── Place in same package as Spring Boot Application class │
│      └── Or in a sub-package that gets component-scanned        │
│                                                                 │
│   4. Import Statements (from org.aspectj.lang.annotation)       │
│      └── @Aspect, @Before, @After, @Pointcut, etc.              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## @Pointcut Expressions

### Defining Pointcuts

```java
@Pointcut("execution(* BookNewController.*(..))")  
public void bookControllerMethods() {
    // Method body is empty - just used as identifier
}
```

### Pointcut Expression Syntax

```
┌─────────────────────────────────────────────────────────────────┐
│              POINTCUT EXPRESSION BREAKDOWN                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   execution(* BookNewController.*(..))                          │
│   │         │ │                  │ │                            │
│   │         │ │                  │ └── (..) = any arguments     │
│   │         │ │                  └── * = any method name        │
│   │         │ └── BookNewController = class name                │
│   │         └── * = any return type                             │
│   └── execution = method execution                              │
│                                                                 │
│   Full syntax:                                                  │
│   execution(modifiers? return-type declaring-type.method(args)) │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Common Pointcut Patterns

| Pattern | Description |
|---------|-------------|
| `execution(* *.*(..))` | All methods in all classes |
| `execution(* com.example..*.*(..))` | All methods in com.example package and subpackages |
| `execution(* BookController.*(..))` | All methods in BookController |
| `execution(* save*(..))` | All methods starting with "save" |
| `execution(public * *(..))` | All public methods |
| `execution(* *(..) throws Exception)` | Methods that throw Exception |
| `execution(String *(..))` | Methods returning String |

### Combining Pointcuts

```java
@Pointcut("execution(* BookController.*(..))")
public void bookControllerMethods() {}

@Pointcut("execution(* OrderController.*(..))")
public void orderControllerMethods() {}

// Combine with OR (||)
@Pointcut("bookControllerMethods() || orderControllerMethods()")
public void allControllerMethods() {}

// Combine with AND (&&)
@Pointcut("execution(* save*(..)) && execution(public * *(..))")
public void publicSaveMethods() {}
```

---

## Advice Types

### All Advice Types Explained

```
┌─────────────────────────────────────────────────────────────────┐
│              ADVICE TYPES                                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   @Before                                                       │
│   ────────                                                      │
│   Runs BEFORE the target method                                 │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @Before("pointcut()")                                   │   │
│   │ public void beforeAdvice() {                            │   │
│   │     System.out.println("Before");                       │   │
│   │ }                                                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   @After ("finally")                                            │
│   ──────────────────                                            │
│   Runs AFTER the target method (success or exception)           │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @After("pointcut()")                                    │   │
│   │ public void afterAdvice() {                             │   │
│   │     System.out.println("After (always)");               │   │
│   │ }                                                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   @AfterReturning                                               │
│   ────────────────                                              │
│   Runs AFTER successful completion (no exception)               │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @AfterReturning(pointcut="pointcut()", returning="res") │   │
│   │ public void afterReturning(Object res) {                │   │
│   │     System.out.println("Returned: " + res);             │   │
│   │ }                                                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   @AfterThrowing                                                │
│   ──────────────                                                │
│   Runs AFTER an exception is thrown                             │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @AfterThrowing(pointcut="pointcut()", throwing="ex")    │   │
│   │ public void afterThrowing(Exception ex) {               │   │
│   │     System.out.println("Exception: " + ex);             │   │
│   │ }                                                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   @Around                                                       │
│   ────────                                                      │
│   Wraps the method - runs before AND after                      │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @Around("pointcut()")                                   │   │
│   │ public Object around(ProceedingJoinPoint pjp) {         │   │
│   │     System.out.println("Before");                       │   │
│   │     Object result = pjp.proceed(); // Call target       │   │
│   │     System.out.println("After");                        │   │
│   │     return result;                                      │   │
│   │ }                                                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Advice Execution Order

```
┌─────────────────────────────────────────────────────────────────┐
│              ADVICE EXECUTION ORDER                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Request                                                       │
│      │                                                          │
│      ▼                                                          │
│   @Before advice                                                │
│      │                                                          │
│      ▼                                                          │
│   Target Method Execution                                       │
│      │                                                          │
│      ├──────────────────────────┐                               │
│      │                          │                               │
│      ▼ (Success)                ▼ (Exception)                   │
│   @AfterReturning          @AfterThrowing                       │
│      │                          │                               │
│      ├──────────────────────────┘                               │
│      ▼                                                          │
│   @After (always runs)                                          │
│      │                                                          │
│      ▼                                                          │
│   Response                                                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## JoinPoint Interface

### Accessing Method Information

```java
@Before("beforepointcut()")
public void myadvice(JoinPoint jp) {
    // Get method signature
    String methodName = jp.getSignature().getName();
    String className = jp.getSignature().getDeclaringTypeName();
    
    // Get all method arguments
    Object[] args = jp.getArgs();
    
    // Get target object (the actual controller/service)
    Object target = jp.getTarget();
    
    System.out.println("Class: " + className);
    System.out.println("Method: " + methodName);
    System.out.println("Arguments: " + Arrays.toString(args));
}
```

### JoinPoint Methods

| Method | Description |
|--------|-------------|
| `getSignature()` | Returns method signature |
| `getArgs()` | Returns method arguments as Object[] |
| `getTarget()` | Returns target object |
| `getThis()` | Returns proxy object |
| `toString()` | String representation |

---

## Complete Code Examples

### SampleAspect.java

```java
package com.example.demo;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Aspect      // This class is an aspect
@Component   // Spring will detect this
public class SampleAspect 
{
    /**
     * Pointcut for before advice
     * Matches all methods in BookNewController
     */
    @Pointcut("execution(* BookNewController.*(..))")  
    public void beforepointcut() {
        // Empty method - just an identifier
    }
    
    /**
     * Pointcut for after advice
     * Matches all methods in BookNewController
     */
    @Pointcut("execution(* BookNewController.*(..))")  
    public void afterpointcut() {
        // Empty method - just an identifier
    }
    
    /**
     * Before Advice
     * Runs BEFORE any matched method
     */
    @Before("beforepointcut()")
    public void myadvice1(JoinPoint jp) {  
        System.out.println("═══════════════════════════════════════");
        System.out.println("BEFORE Advice Executing");
        System.out.println("Method: " + jp.getSignature().getName());
        System.out.println("Full Signature: " + jp.getSignature());
        System.out.println("═══════════════════════════════════════");
    }  
    
    /**
     * After Advice
     * Runs AFTER any matched method (success or exception)
     */
    @After("afterpointcut()")
    public void myadvice2(JoinPoint jp) {  
        System.out.println("═══════════════════════════════════════");
        System.out.println("AFTER Advice Executing");
        System.out.println("Method: " + jp.getSignature().getName());
        System.out.println("═══════════════════════════════════════");
    }  
}
```

### DemoApplication.java

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@SpringBootApplication
@EnableAspectJAutoProxy(proxyTargetClass=true)  
public class DemoApplication 
{
    public static void main(String[] args) 
    {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

### BookNewController.java (Target)

```java
package com.example.demo;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class BookNewController
{
    @GetMapping("book")
    public ModelAndView before() {
        System.out.println(">>> Controller: before() executing");
        Book book = new Book();
        return new ModelAndView("bookNew", "mybook", book);
    }
    
    @PostMapping("book")
    public String afterSubmit(@ModelAttribute("mb") Book book) {
        System.out.println(">>> Controller: afterSubmit() executing");
        return "success";
    }
}
```

---

## Execution Flow

### Console Output When Accessing /book

```
═══════════════════════════════════════
BEFORE Advice Executing
Method: before
Full Signature: ModelAndView com.example.demo.BookNewController.before()
═══════════════════════════════════════
>>> Controller: before() executing
═══════════════════════════════════════
AFTER Advice Executing
Method: before
═══════════════════════════════════════
```

### Visual Flow

```
┌─────────────────────────────────────────────────────────────────┐
│              AOP EXECUTION FLOW                                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. Request: GET /book                                         │
│      │                                                          │
│      ▼                                                          │
│   2. Spring finds BookNewController.before() matches            │
│      │                                                          │
│      ▼                                                          │
│   3. AOP Proxy intercepts                                       │
│      │                                                          │
│      ▼                                                          │
│   4. @Before("beforepointcut()") runs                           │
│      └── Prints "BEFORE Advice Executing"                       │
│      └── Prints method signature                                │
│      │                                                          │
│      ▼                                                          │
│   5. Target method before() executes                            │
│      └── Prints "Controller: before() executing"                │
│      └── Creates Book and ModelAndView                          │
│      │                                                          │
│      ▼                                                          │
│   6. @After("afterpointcut()") runs                             │
│      └── Prints "AFTER Advice Executing"                        │
│      │                                                          │
│      ▼                                                          │
│   7. Response returned to client                                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

### Annotations Summary

| Annotation | Purpose |
|------------|---------|
| `@Aspect` | Mark class as Aspect |
| `@Component` | Spring manages the aspect |
| `@Pointcut` | Define where advice applies |
| `@Before` | Run before method |
| `@After` | Run after method (always) |
| `@AfterReturning` | Run after successful return |
| `@AfterThrowing` | Run after exception |
| `@Around` | Run before and after |
| `@EnableAspectJAutoProxy` | Enable AOP in application |

### Required Dependencies (pom.xml)

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

### Pointcut Syntax Cheatsheet

| Element | Meaning |
|---------|---------|
| `*` | Any (return type, method name) |
| `..` | Any arguments |
| `execution()` | Method execution |
| `within()` | Within certain types |
| `@annotation()` | Methods with annotation |

---

*This note is part of the Advanced Java - Spring Boot & Spring MVC series*
