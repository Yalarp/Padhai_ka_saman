# 📘 Spring MVC Controller - Complete Guide

## Table of Contents
1. [Introduction to Spring MVC](#introduction-to-spring-mvc)
2. [Front Controller Design Pattern](#front-controller-design-pattern)
3. [DispatcherServlet](#dispatcherservlet)
4. [@Controller Annotation](#controller-annotation)
5. [View Resolver Configuration](#view-resolver-configuration)
6. [Complete Controller Examples](#complete-controller-examples)
7. [Execution Flow](#execution-flow)
8. [Quick Reference](#quick-reference)

---

## Introduction to Spring MVC

### What is Spring MVC?

Spring MVC is a web framework built on the Servlet API that uses the Model-View-Controller architecture pattern.

```
┌─────────────────────────────────────────────────────────────────┐
│                    SPRING MVC ARCHITECTURE                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│                      ┌─────────────┐                            │
│                      │   BROWSER   │                            │
│                      └──────┬──────┘                            │
│                             │ HTTP Request                      │
│                             ▼                                   │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │              DISPATCHER SERVLET                         │   │
│   │            (Front Controller)                           │   │
│   └─────────────────────────┬───────────────────────────────┘   │
│                             │                                   │
│            ┌────────────────┼────────────────┐                  │
│            ▼                ▼                ▼                  │
│     ┌──────────┐     ┌──────────┐     ┌──────────┐             │
│     │Controller│     │Controller│     │Controller│             │
│     └────┬─────┘     └────┬─────┘     └────┬─────┘             │
│          │                │                │                    │
│          └────────────────┴────────────────┘                    │
│                           │                                     │
│                           ▼                                     │
│                    ┌──────────────┐                             │
│                    │ VIEW RESOLVER│                             │
│                    └──────┬───────┘                             │
│                           ▼                                     │
│                    ┌──────────────┐                             │
│                    │    VIEW      │                             │
│                    │ (Thymeleaf)  │                             │
│                    └──────────────┘                             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### MVC Components

| Component | Role | Example |
|-----------|------|---------|
| **Model** | Data and business logic | Book.java |
| **View** | User interface | bookNew.html |
| **Controller** | Request handling | BookNewController.java |

---

## Front Controller Design Pattern

### Definition

```
┌─────────────────────────────────────────────────────────────────┐
│            FRONT CONTROLLER DESIGN PATTERN                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   "Front Controller Design Pattern is a STRUCTURAL              │
│    design pattern."                                             │
│                                                                 │
│   Definition:                                                   │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ "This design pattern means that ALL requests that come  │   │
│   │  for a resource in an application will be handled by    │   │
│   │  a SINGLE HANDLER and then dispatched to the            │   │
│   │  appropriate handler [specific controller which          │   │
│   │  programmer writes] for that type of request."          │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Visual Representation

```
┌─────────────────────────────────────────────────────────────────┐
│              FRONT CONTROLLER PATTERN                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Without Front Controller:                                     │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │                                                         │   │
│   │   Request 1 ──────────────────────▶ Servlet 1           │   │
│   │   Request 2 ──────────────────────▶ Servlet 2           │   │
│   │   Request 3 ──────────────────────▶ Servlet 3           │   │
│   │                                                         │   │
│   │   Problem: Each request directly goes to its servlet    │   │
│   │           No central control, duplicate code            │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   With Front Controller (Spring MVC):                           │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │                                                         │   │
│   │   Request 1 ──┐                                         │   │
│   │               │     ┌──────────────────┐                │   │
│   │   Request 2 ──┼────▶│ DispatcherServlet│────▶ Controller│   │
│   │               │     │ (Front Controller)│                │   │
│   │   Request 3 ──┘     └──────────────────┘                │   │
│   │                                                         │   │
│   │   Benefit: Single entry point, centralized control      │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## DispatcherServlet

### What is DispatcherServlet?

DispatcherServlet is Spring's implementation of the Front Controller pattern.

```
┌─────────────────────────────────────────────────────────────────┐
│                    DISPATCHER SERVLET                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Role: Front Controller in Spring MVC                         │
│                                                                 │
│   Responsibilities:                                             │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ 1. Receives ALL incoming HTTP requests                  │   │
│   │ 2. Consults Handler Mapping to find correct controller  │   │
│   │ 3. Delegates request to appropriate Controller          │   │
│   │ 4. Receives model and view name from Controller         │   │
│   │ 5. Consults View Resolver to find actual view           │   │
│   │ 6. Renders view with model data                         │   │
│   │ 7. Sends response back to client                        │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### DispatcherServlet in Traditional vs Spring Boot

```
┌─────────────────────────────────────────────────────────────────┐
│       DISPATCHERSERVLET CONFIGURATION                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   TRADITIONAL SPRING MVC:                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ Must configure manually in web.xml:                     │   │
│   │                                                         │   │
│   │ <servlet>                                               │   │
│   │   <servlet-name>dispatcher</servlet-name>               │   │
│   │   <servlet-class>                                       │   │
│   │     org.springframework.web.servlet.DispatcherServlet   │   │
│   │   </servlet-class>                                      │   │
│   │ </servlet>                                              │   │
│   │ <servlet-mapping>                                       │   │
│   │   <servlet-name>dispatcher</servlet-name>               │   │
│   │   <url-pattern>/</url-pattern>                          │   │
│   │ </servlet-mapping>                                      │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   SPRING BOOT:                                                  │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ Automatically configured!                               │   │
│   │                                                         │   │
│   │ No web.xml needed                                       │   │
│   │ No manual servlet configuration                         │   │
│   │ Just add @SpringBootApplication and it works!           │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## @Controller Annotation

### Understanding @Controller

```
┌─────────────────────────────────────────────────────────────────┐
│                    @CONTROLLER ANNOTATION                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   @Controller marks a class as a Spring MVC Controller          │
│                                                                 │
│   Key Characteristics:                                          │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ ✓ Specialization of @Component                          │   │
│   │ ✓ Auto-detected by component scanning                   │   │
│   │ ✓ Methods return VIEW NAMES (not data)                  │   │
│   │ ✓ Uses View Resolver to find actual view                │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   vs @RestController:                                           │
│   ┌────────────────────┬───────────────────────────────────┐    │
│   │ @Controller        │ Returns view name for rendering   │    │
│   │ @RestController    │ Returns data directly (JSON/XML)  │    │
│   └────────────────────┴───────────────────────────────────┘    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### @Controller Example with Line-by-Line Explanation

```java
/**
 * BookNewController.java - MVC Controller Example
 * 
 * This controller handles book-related web requests
 * and returns view names for Thymeleaf rendering.
 */

package com.example.demo;                              // Line 1: Package declaration
                                                       // Line 2: Empty line

import org.springframework.stereotype.Controller;      // Line 3: Import @Controller annotation
import org.springframework.ui.Model;                   // Line 4: Import Model interface
import org.springframework.web.bind.annotation.GetMapping;   // Line 5: Import @GetMapping
import org.springframework.web.bind.annotation.PostMapping;  // Line 6: Import @PostMapping
import org.springframework.web.bind.annotation.RequestParam; // Line 7: Import @RequestParam

@Controller                                            // Line 8: Marks class as MVC Controller
public class BookNewController                         // Line 9: Controller class declaration
{                                                      // Line 10: Class body start
    @GetMapping("book")                                // Line 11: Handle GET requests to /book
    public String before()                             // Line 12: Method returns String (view name)
    {                                                  // Line 13: Method body start
        return "bookNew";                              // Line 14: Return view name (bookNew.html)
    }                                                  // Line 15: Method body end
    
    @PostMapping("book")                               // Line 16: Handle POST requests to /book
    public String afterSubmit(                         // Line 17: Method with parameters
        @RequestParam("bookname") String name,         // Line 18: Read "bookname" from form
        @RequestParam("price") long bookprice,         // Line 19: Read "price" from form
        Model model)                                   // Line 20: Model to pass data to view
    {                                                  // Line 21: Method body start
        // new way to read request parameter           // Line 22: Comment
        
        Book book = new Book();                        // Line 23: Create new Book object
        book.setBookName(name);                        // Line 24: Set book name from form
        book.setPrice(bookprice);                      // Line 25: Set price from form
        model.addAttribute("mybook", book);            // Line 26: Add book to model for view
        return "success";                              // Line 27: Return view name (success.html)
    }                                                  // Line 28: Method body end
}                                                      // Line 29: Class body end
```

### Line-by-Line Explanation Table

| Line | Code | Explanation |
|------|------|-------------|
| 3-7 | Imports | Required Spring MVC classes |
| 8 | `@Controller` | Marks this class as a web controller |
| 11 | `@GetMapping("book")` | Maps HTTP GET requests to /book |
| 12 | `public String before()` | Returns String = view name |
| 14 | `return "bookNew"` | View resolver finds bookNew.html |
| 16 | `@PostMapping("book")` | Maps HTTP POST requests to /book |
| 18-19 | `@RequestParam` | Extracts form field values |
| 20 | `Model model` | Spring injects Model automatically |
| 26 | `model.addAttribute()` | Adds data accessible in view |
| 27 | `return "success"` | View resolver finds success.html |

---

## View Resolver Configuration

### What is View Resolver?

```
┌─────────────────────────────────────────────────────────────────┐
│                    VIEW RESOLVER                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   View Resolver translates view names to actual view files      │
│                                                                 │
│   Controller returns: "bookNew"                                 │
│                 │                                               │
│                 ▼                                               │
│   View Resolver applies: prefix + viewName + suffix             │
│                 │                                               │
│                 ▼                                               │
│   Result: "/" + "bookNew" + ".html" = "/bookNew.html"           │
│                 │                                               │
│                 ▼                                               │
│   File located: templates/bookNew.html                          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Configuration in application.properties

```properties
# application.properties

spring.mvc.view.prefix="/"
# Prefix added before view name
# "/" means root of templates folder

spring.mvc.view.suffix=".html"
# Suffix added after view name
# ".html" for Thymeleaf templates
# ".jsp" for JSP views
```

### View Resolution Flow

```
┌─────────────────────────────────────────────────────────────────┐
│              VIEW RESOLUTION FLOW                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Step 1: Controller method returns "bookNew"                   │
│           │                                                     │
│           ▼                                                     │
│   Step 2: DispatcherServlet receives view name                  │
│           │                                                     │
│           ▼                                                     │
│   Step 3: View Resolver applies configuration:                  │
│           ┌───────────────────────────────────────────────┐     │
│           │ prefix: "/"                                   │     │
│           │ view name: "bookNew"                          │     │
│           │ suffix: ".html"                               │     │
│           │ ────────────────────                          │     │
│           │ Result: /bookNew.html                         │     │
│           └───────────────────────────────────────────────┘     │
│           │                                                     │
│           ▼                                                     │
│   Step 4: Thymeleaf locates: templates/bookNew.html             │
│           │                                                     │
│           ▼                                                     │
│   Step 5: Thymeleaf engine renders HTML                         │
│           │                                                     │
│           ▼                                                     │
│   Step 6: HTML response sent to browser                         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Complete Controller Examples

### Example 1: Controller with ModelAndView

```java
/**
 * BookNewController using ModelAndView
 * 
 * ModelAndView combines both the model data and view name
 * in a single return object.
 */

package com.example.demo;

import java.util.ArrayList;
import java.util.List;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller                                           // Marks as MVC Controller
public class BookNewController
{
    @GetMapping("book")                               // Handle GET /book
    public ModelAndView before()                      // Returns ModelAndView instead of String
    {
        // Create book objects
        Book mybook1 = new Book();
        mybook1.setBookName("Java Black Book"); 
        mybook1.setPrice(900);
        
        Book mybook2 = new Book();
        mybook2.setBookName("Understanding Pointers in C"); 
        mybook2.setPrice(400);
        
        Book mybook3 = new Book();
        mybook3.setBookName("The complete JavaEE Guide"); 
        mybook3.setPrice(800);
        
        // Create list and add books
        List<Book> booklist = new ArrayList<Book>();
        booklist.add(mybook1);
        booklist.add(mybook2);
        booklist.add(mybook3);
        
        // Return ModelAndView with:
        // - "Final" = view name (Final.html)
        // - "mylist" = attribute name
        // - booklist = attribute value
        return new ModelAndView("Final", "mylist", booklist);
    }
}
```

### ModelAndView Explained

```
┌─────────────────────────────────────────────────────────────────┐
│                     MODEL AND VIEW                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   new ModelAndView("Final", "mylist", booklist)                 │
│                                                                 │
│   Three parameters:                                             │
│   ┌─────────────────┬───────────────────────────────────────┐   │
│   │ "Final"         │ View name - resolves to Final.html    │   │
│   │ "mylist"        │ Attribute name - accessible in view   │   │
│   │ booklist        │ Attribute value - the List<Book>      │   │
│   └─────────────────┴───────────────────────────────────────┘   │
│                                                                 │
│   In Thymeleaf view:                                            │
│   <tr th:each="ref : ${mylist}">                                │
│       <td th:text="${ref.bookName}" />                          │
│       <td th:text="${ref.price}" />                             │
│   </tr>                                                         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Example 2: Controller with Welcome Page

```java
/**
 * Controller with home page and navigation
 */

package com.example.demo;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.servlet.ModelAndView;

import jakarta.servlet.http.HttpSession;

@Controller
public class BookNewController
{
    // Home page handler
    @GetMapping("/")                                  // Root URL mapping
    public String home()                              // Returns view name
    {
        return "Home";                                // Home.html
    }
    
    // Show book form
    @GetMapping("book")
    public ModelAndView before()
    {
        Book defaultBook = new Book();                // Create empty book for form binding
        return new ModelAndView("bookNew", "mybook", defaultBook);
    }
    
    // Process form submission
    @PostMapping("book")
    public String afterSubmit(
        @ModelAttribute("mb") Book book,              // Bind form data to Book object
        HttpSession session)                          // Access HTTP session
    {
        session.setAttribute("val", 2000);            // Store value in session
        return "success";
    }
}
```

---

## Execution Flow

### Complete Request-Response Cycle

```
┌─────────────────────────────────────────────────────────────────┐
│            COMPLETE REQUEST-RESPONSE CYCLE                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. User types: http://localhost:8080/book                     │
│      │                                                          │
│      ▼                                                          │
│   2. Browser sends HTTP GET request to /book                    │
│      │                                                          │
│      ▼                                                          │
│   3. Embedded Tomcat receives request                           │
│      │                                                          │
│      ▼                                                          │
│   4. DispatcherServlet intercepts request                       │
│      │                                                          │
│      ▼                                                          │
│   5. Handler Mapping finds: BookNewController.before()          │
│      │                                                          │
│      ▼                                                          │
│   6. Controller method executes                                 │
│      └── Creates Book object                                    │
│      └── Returns "bookNew"                                      │
│      │                                                          │
│      ▼                                                          │
│   7. View Resolver resolves: bookNew → templates/bookNew.html   │
│      │                                                          │
│      ▼                                                          │
│   8. Thymeleaf Engine processes template                        │
│      └── Replaces th:* attributes with values                   │
│      │                                                          │
│      ▼                                                          │
│   9. Pure HTML sent to browser                                  │
│      │                                                          │
│      ▼                                                          │
│   10. Browser renders HTML page                                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

### Key Annotations

| Annotation | Purpose | Returns |
|------------|---------|---------|
| `@Controller` | Mark class as MVC controller | View name |
| `@RestController` | Mark class as REST API controller | Data (JSON/XML) |
| `@GetMapping` | Handle HTTP GET requests | - |
| `@PostMapping` | Handle HTTP POST requests | - |
| `@RequestMapping` | General request mapping | - |

### Controller Method Return Types

| Return Type | Purpose |
|-------------|---------|
| `String` | View name only |
| `ModelAndView` | View name + model data |
| `void` | No return (uses response directly) |

### Configuration Summary

```properties
# application.properties
spring.mvc.view.prefix="/"
spring.mvc.view.suffix=".html"
```

---

## Next Steps

After understanding Controllers, proceed to:
- **Note 05**: Request Handling - Learn about @GetMapping, @PostMapping, @RequestParam in detail

---

*This note is part of the Advanced Java - Spring Boot & Spring MVC series*
