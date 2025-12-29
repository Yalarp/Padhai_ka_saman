# 📘 Spring Initializer & Project Setup - Complete Guide

## Table of Contents
1. [Introduction to Spring Initializer](#introduction-to-spring-initializer)
2. [Using start.spring.io](#using-startspringio)
3. [Project Configuration Options](#project-configuration-options)
4. [Importing Project into IDE](#importing-project-into-ide)
5. [@SpringBootApplication Annotation](#springbootapplication-annotation)
6. [Creating Your First RestController](#creating-your-first-restcontroller)
7. [Running the Application](#running-the-application)
8. [Complete Code Examples with Explanations](#complete-code-examples-with-explanations)
9. [Quick Reference](#quick-reference)

---

## Introduction to Spring Initializer

### What is Spring Initializer?

Spring Initializer is a **web-based tool** that generates Spring Boot project structures with all the required configurations and dependencies.

```
┌─────────────────────────────────────────────────────────────────┐
│                    SPRING INITIALIZER                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   URL: https://start.spring.io/                                 │
│                                                                 │
│   Purpose:                                                      │
│   • Generate Spring Boot projects instantly                     │
│   • Select required dependencies                                │
│   • Configure project metadata                                  │
│   • Download ready-to-use project ZIP                           │
│                                                                 │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ "This is Spring Initializer's home page"                │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Using start.spring.io

### Step-by-Step Process

```
┌─────────────────────────────────────────────────────────────────┐
│                SPRING INITIALIZR WORKFLOW                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Step 1: Go to https://start.spring.io/                        │
│              │                                                  │
│              ▼                                                  │
│   Step 2: Configure Project Settings                            │
│              │                                                  │
│              ▼                                                  │
│   Step 3: Add Dependencies                                      │
│              │                                                  │
│              ▼                                                  │
│   Step 4: Click "Generate"                                      │
│              │                                                  │
│              ▼                                                  │
│   Step 5: Download ZIP file (demo.zip)                          │
│              │                                                  │
│              ▼                                                  │
│   Step 6: Unzip and Import to IDE                               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Project Configuration Options

### Configuration Settings Table

| Setting | Value | Description |
|---------|-------|-------------|
| **Project** | Maven | Build tool (Maven recommended for beginners) |
| **Language** | Java | Programming language |
| **Spring Boot Version** | 3.0.8 snapshot / 3.1 | Framework version |
| **Group** | com.example | Organization identifier |
| **Artifact** | demo | Project name |
| **Package Name** | com.example.demo | Base package |
| **Packaging** | jar | Output format |
| **Java Version** | 17 (for 3.x) / 8 (for 2.x) | JDK version |

### Version-Specific Requirements

```
┌─────────────────────────────────────────────────────────────────┐
│              JAVA VERSION REQUIREMENTS                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Spring Boot 3.x (e.g., 3.0.8, 3.1)                            │
│   ├── Requires: Java 17 or higher                               │
│   ├── Compiler: javac 17                                        │
│   └── JRE: JRE 17                                               │
│                                                                 │
│   Spring Boot 2.x (e.g., 2.7.8)                                 │
│   ├── Requires: Java 8 or higher                                │
│   ├── Compiler: javac 8                                         │
│   └── JRE: JRE 8                                                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Adding Dependencies

```
┌─────────────────────────────────────────────────────────────────┐
│                ADDING DEPENDENCIES                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. On the right side, click "Add Dependencies" button         │
│                                                                 │
│   2. Search and select required dependencies:                   │
│                                                                 │
│      ┌──────────────────┬───────────────────────────────────┐   │
│      │ Dependency       │ Purpose                           │   │
│      ├──────────────────┼───────────────────────────────────┤   │
│      │ Spring Web       │ REST APIs, MVC features           │   │
│      │ Thymeleaf        │ Template engine for views         │   │
│      │ Spring Boot      │ Auto-restart on code changes      │   │
│      │ DevTools         │                                   │   │
│      └──────────────────┴───────────────────────────────────┘   │
│                                                                 │
│   3. Click "Generate" button from the bottom panel              │
│                                                                 │
│   4. "demo.zip" will be downloaded                              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Importing Project into IDE

### Unzipping the Downloaded File

```
┌─────────────────────────────────────────────────────────────────┐
│                 UNZIPPING THE PROJECT                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Downloaded: demo.zip                                          │
│                                                                 │
│   After unzipping:                                              │
│                                                                 │
│   demo.zip ──extract──▶ demo/                                   │
│                           └── demo/   ◀── This is the project   │
│                               ├── src/                          │
│                               ├── pom.xml                       │
│                               └── ...                           │
│                                                                 │
│   IMPORTANT NOTE:                                               │
│   "When you unzip demo.zip, it gives you 'demo' folder          │
│    inside 'demo' folder"                                        │
│                                                                 │
│   Select the INNER demo folder when importing!                  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Import Steps in Eclipse/STS

```
┌─────────────────────────────────────────────────────────────────┐
│              IMPORTING INTO ECLIPSE/STS                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Step 1: Start Eclipse/STS                                     │
│                                                                 │
│   Step 2: Navigate to:                                          │
│           File → Import → Existing Maven Project                │
│                                                                 │
│   Step 3: Select root directory:                                │
│           Example: C:\Users\Sriram\Downloads\demo\demo          │
│                                                                 │
│   Step 4: Verify pom.xml is selected automatically              │
│                                                                 │
│   Step 5: Do NOT select "Add project to working set"            │
│                                                                 │
│   Step 6: [Internet must be ON]                                 │
│           Click "Finish"                                        │
│                                                                 │
│   Step 7: Wait for Maven to download all dependencies           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Verify Project Settings

```
┌─────────────────────────────────────────────────────────────────┐
│              VERIFY PROJECT SETTINGS                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   After import, verify the following:                           │
│                                                                 │
│   ┌────────────────────┬────────────────────────────────────┐   │
│   │ Setting            │ Required Value                     │   │
│   ├────────────────────┼────────────────────────────────────┤   │
│   │ Compiler           │ Java 17 (for Spring Boot 3.x)      │   │
│   │ JRE                │ JRE 17                             │   │
│   │ Project Facet      │ Java 17                            │   │
│   └────────────────────┴────────────────────────────────────┘   │
│                                                                 │
│   For Spring Boot 2.x:                                          │
│   All above should be Java 8 instead of Java 17                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Project Structure After Import

```
┌─────────────────────────────────────────────────────────────────┐
│                  PROJECT STRUCTURE                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   demo/                                                         │
│   ├── src/                                                      │
│   │   ├── main/                                                 │
│   │   │   ├── java/                                             │
│   │   │   │   └── com/                                          │
│   │   │   │       └── example/                                  │
│   │   │   │           └── demo/                                 │
│   │   │   │               └── DemoApplication.java  ◀── Main    │
│   │   │   └── resources/                                        │
│   │   │       ├── templates/        ◀── Thymeleaf files go here │
│   │   │       ├── static/           ◀── CSS, JS, images         │
│   │   │       └── application.properties ◀── Configuration      │
│   │   └── test/                                                 │
│   │       └── java/                                             │
│   └── pom.xml                       ◀── Maven configuration     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## @SpringBootApplication Annotation

### Understanding the Main Class

The main class generated by Spring Initializer contains the `@SpringBootApplication` annotation.

```java
/**
 * DemoApplication.java - Main entry point
 * 
 * This is the Spring Boot Application class that serves as the
 * entry point for the entire application.
 */
package com.example.demo;                    // Line 1: Package declaration

import org.springframework.boot.SpringApplication;           // Line 2: Import SpringApplication class
import org.springframework.boot.autoconfigure.SpringBootApplication;  // Line 3: Import annotation

@SpringBootApplication                       // Line 4: Key annotation (explained below)
public class DemoApplication {               // Line 5: Main class declaration

    public static void main(String[] args) { // Line 6: Main method - entry point
        SpringApplication.run(DemoApplication.class, args);  // Line 7: Starts Spring Boot
    }

}
```

### Line-by-Line Explanation

| Line | Code | Explanation |
|------|------|-------------|
| 1 | `package com.example.demo;` | Declares the package where this class belongs |
| 2-3 | `import ...` | Imports required Spring Boot classes |
| 4 | `@SpringBootApplication` | **CRITICAL**: Meta-annotation combining three annotations |
| 5 | `public class DemoApplication` | Declares the main application class |
| 6 | `public static void main(String[] args)` | Standard Java main method - program entry point |
| 7 | `SpringApplication.run(...)` | Bootstraps and launches the Spring application |

### What Does @SpringBootApplication Do?

```
┌─────────────────────────────────────────────────────────────────┐
│           @SpringBootApplication BREAKDOWN                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   @SpringBootApplication = @Configuration                       │
│                          + @EnableAutoConfiguration             │
│                          + @ComponentScan                       │
│                                                                 │
│   ┌───────────────────────────────────────────────────────────┐ │
│   │ @Configuration                                            │ │
│   │ • Marks this class as a source of bean definitions        │ │
│   │ • Allows @Bean methods within this class                  │ │
│   └───────────────────────────────────────────────────────────┘ │
│                                                                 │
│   ┌───────────────────────────────────────────────────────────┐ │
│   │ @EnableAutoConfiguration                                  │ │
│   │ • Enables Spring Boot's auto-configuration                │ │
│   │ • Automatically configures beans based on dependencies    │ │
│   │ • "If you have spring-web, I'll configure DispatcherServlet" │
│   └───────────────────────────────────────────────────────────┘ │
│                                                                 │
│   ┌───────────────────────────────────────────────────────────┐ │
│   │ @ComponentScan                                            │ │
│   │ • Scans for @Component, @Controller, @Service, @Repository│ │
│   │ • Default: scans current package and sub-packages         │ │
│   └───────────────────────────────────────────────────────────┘ │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Creating Your First RestController

### Step-by-Step Controller Creation

```
┌─────────────────────────────────────────────────────────────────┐
│            CREATING A REST CONTROLLER                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   "Now you can add RestController inside it, so that it can     │
│    be run on the browser also."                                 │
│                                                                 │
│   Steps:                                                        │
│   1. Right-click on "com.example.demo" package                  │
│   2. Select New → Java Class                                    │
│   3. Name it "FirstController"                                  │
│   4. Add the following code                                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Complete RestController Code

```java
/**
 * FirstController.java - REST Controller Example
 * 
 * This controller handles HTTP requests and returns responses
 * directly to the client (no view resolution).
 */
package com.example.demo;                              // Line 1: Package declaration
                                                       // Line 2: Empty line
import org.springframework.web.bind.annotation.GetMapping;     // Line 3: Import GetMapping
import org.springframework.web.bind.annotation.RestController; // Line 4: Import RestController
                                                       // Line 5: Empty line
@RestController                                        // Line 6: Marks class as REST API controller
public class FirstController                           // Line 7: Class declaration
{                                                      // Line 8: Class body start
    @GetMapping("/hello")                              // Line 9: Maps GET request to /hello URL
    public String sayHello()                           // Line 10: Method that handles the request
    {                                                  // Line 11: Method body start
        return "welcome to spring boot with spring initializer";  // Line 12: Response body
    }                                                  // Line 13: Method body end
}                                                      // Line 14: Class body end
```

### Line-by-Line Explanation

| Line | Code | Explanation |
|------|------|-------------|
| 1 | `package com.example.demo;` | Must be in the same package or sub-package of main class |
| 3 | `import GetMapping` | Annotation to map HTTP GET requests |
| 4 | `import RestController` | Marks class as REST API controller |
| 6 | `@RestController` | Combines @Controller + @ResponseBody |
| 7 | `public class FirstController` | Controller class name |
| 9 | `@GetMapping("/hello")` | When someone visits /hello, this method runs |
| 10 | `public String sayHello()` | Method returns String directly as response |
| 12 | `return "welcome to..."` | This string is sent as HTTP response body |

### @RestController vs @Controller

```
┌─────────────────────────────────────────────────────────────────┐
│          @RestController vs @Controller                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   @RestController                    @Controller                │
│   ├── Returns data directly          ├── Returns view name      │
│   ├── Response is JSON/XML/String    ├── Uses View Resolver     │
│   ├── For REST APIs                  ├── For web pages          │
│   └── = @Controller + @ResponseBody  └── Needs @ResponseBody    │
│                                           for data response     │
│                                                                 │
│   Example:                           Example:                   │
│   @RestController                    @Controller                │
│   public String getData() {          public String getPage() {  │
│     return "Hello";                    return "index";          │
│   }                                  }                          │
│   // Returns "Hello" as text         // Looks for index.html    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Running the Application

### Starting the Application

```
┌─────────────────────────────────────────────────────────────────┐
│                RUNNING THE APPLICATION                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Method 1: From IDE                                            │
│   ──────────────────                                            │
│   1. Right-click on "DemoApplication.java"                      │
│   2. Select: Run As → Java Application                          │
│   3. Wait for console to show:                                  │
│      "Tomcat started on port(s): 8080"                          │
│                                                                 │
│   Method 2: From Command Line                                   │
│   ─────────────────────────────                                 │
│   $ mvn spring-boot:run                                         │
│                                                                 │
│   Method 3: From JAR                                            │
│   ───────────────────                                           │
│   $ java -jar demo-0.0.1-SNAPSHOT.jar                           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Execution Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                 EXECUTION FLOW                                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. Run DemoApplication.java                                   │
│      │                                                          │
│      ▼                                                          │
│   2. SpringApplication.run() executes                           │
│      │                                                          │
│      ▼                                                          │
│   3. Auto-configuration kicks in                                │
│      ├── Embedded Tomcat server starts                          │
│      ├── DispatcherServlet is configured                        │
│      └── Component scanning finds FirstController               │
│      │                                                          │
│      ▼                                                          │
│   4. Console shows: "Tomcat started on port(s): 8080"           │
│      │                                                          │
│      ▼                                                          │
│   5. Application is ready to receive requests                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Testing in Browser

```
┌─────────────────────────────────────────────────────────────────┐
│                 TESTING IN BROWSER                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. Open web browser                                           │
│                                                                 │
│   2. Type URL: http://localhost:8080/hello                      │
│                                                                 │
│   3. Expected output:                                           │
│      ┌───────────────────────────────────────────────────────┐  │
│      │ welcome to spring boot with spring initializer        │  │
│      └───────────────────────────────────────────────────────┘  │
│                                                                 │
│   What happens when you hit this URL:                           │
│                                                                 │
│   Browser ──GET /hello──▶ Tomcat ──▶ DispatcherServlet          │
│                                          │                      │
│                                          ▼                      │
│                                    FirstController.sayHello()   │
│                                          │                      │
│                                          ▼                      │
│   Browser ◀──Response──── "welcome to spring boot..."           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Complete Code Examples with Explanations

### Creating Project Using STS (Alternative Method)

```
┌─────────────────────────────────────────────────────────────────┐
│          CREATING PROJECT IN STS                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   File → New → Spring Starter Project                           │
│                                                                 │
│   Project Settings:                                             │
│   ┌────────────────────┬────────────────────────────────────┐   │
│   │ Name               │ MVC (or any name)                  │   │
│   │ Type               │ Maven                              │   │
│   │ Java Version       │ 17                                 │   │
│   │ Spring Boot        │ 3.0.8 snapshot                     │   │
│   └────────────────────┴────────────────────────────────────┘   │
│                                                                 │
│   Click "Next"                                                  │
│                                                                 │
│   Dependencies:                                                 │
│   ☑ Spring Web                                                  │
│   ☑ Thymeleaf                                                   │
│   ☑ Spring Boot DevTools (under Developer Tools)               │
│                                                                 │
│   Click "Finish"                                                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Spring Boot DevTools

```
┌─────────────────────────────────────────────────────────────────┐
│              SPRING BOOT DEVTOOLS                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   What it does:                                                 │
│   "This ensures that server gets restarted automatically        │
│    when you make changes (in java files) to your application"   │
│                                                                 │
│   Benefits:                                                     │
│   ✓ Auto-restart on code changes                                │
│   ✓ LiveReload for browser refresh                              │
│   ✓ Faster development cycle                                    │
│                                                                 │
│   Without DevTools:                                             │
│   Change code → Stop server → Start server → Test               │
│                                                                 │
│   With DevTools:                                                │
│   Change code → Server auto-restarts → Test                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

### Project Setup Checklist

- [ ] Go to start.spring.io
- [ ] Select Maven project
- [ ] Choose Java language
- [ ] Set Spring Boot version
- [ ] Configure Group, Artifact, Package
- [ ] Select Java version (17 for Spring Boot 3.x)
- [ ] Add dependencies (Spring Web, Thymeleaf, DevTools)
- [ ] Click Generate
- [ ] Unzip and import to IDE
- [ ] Verify Java version settings
- [ ] Run DemoApplication.java
- [ ] Test in browser

### Important URLs

| Purpose | URL |
|---------|-----|
| Spring Initializer | https://start.spring.io/ |
| Default App | http://localhost:8080 |
| Test Endpoint | http://localhost:8080/hello |

### Key Annotations Summary

| Annotation | Purpose |
|------------|---------|
| `@SpringBootApplication` | Main application class marker |
| `@RestController` | REST API controller |
| `@GetMapping("/path")` | Handle GET requests at /path |

---

## Next Steps

After setting up your project, proceed to:
- **Note 03**: Thymeleaf Template Engine - Learn to create dynamic HTML views

---

*This note is part of the Advanced Java - Spring Boot & Spring MVC series*
