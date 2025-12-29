# 📘 Request Handling in Spring MVC - Complete Guide

## Table of Contents
1. [Introduction to Request Handling](#introduction-to-request-handling)
2. [@GetMapping Annotation](#getmapping-annotation)
3. [@PostMapping Annotation](#postmapping-annotation)
4. [@RequestMapping Annotation](#requestmapping-annotation)
5. [@RequestParam for Reading Form Data](#requestparam-for-reading-form-data)
6. [Traditional vs Modern Approach](#traditional-vs-modern-approach)
7. [Complete Code Examples](#complete-code-examples)
8. [Request Handling Flow](#request-handling-flow)
9. [Quick Reference](#quick-reference)

---

## Introduction to Request Handling

### HTTP Methods Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                    HTTP METHODS                                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌──────────────┬──────────────────────────────────────────┐   │
│   │ HTTP Method  │ Purpose                                  │   │
│   ├──────────────┼──────────────────────────────────────────┤   │
│   │ GET          │ Retrieve data (show form, display data)  │   │
│   │ POST         │ Submit data (form submission, create)    │   │
│   │ PUT          │ Update existing data                     │   │
│   │ DELETE       │ Remove data                              │   │
│   │ PATCH        │ Partial update                           │   │
│   └──────────────┴──────────────────────────────────────────┘   │
│                                                                 │
│   In Spring MVC forms, we primarily use:                        │
│   • GET  - Display the form                                     │
│   • POST - Submit the form                                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Spring MVC Annotations for Request Handling

| Annotation | HTTP Method | Use Case |
|------------|-------------|----------|
| `@GetMapping` | GET | Display forms, show data |
| `@PostMapping` | POST | Form submission |
| `@PutMapping` | PUT | Update resources |
| `@DeleteMapping` | DELETE | Delete resources |
| `@RequestMapping` | Any | General purpose |

---

## @GetMapping Annotation

### What is @GetMapping?

```
┌─────────────────────────────────────────────────────────────────┐
│                    @GETMAPPING                                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   @GetMapping is a shortcut for @RequestMapping(method = GET)   │
│                                                                 │
│   Purpose:                                                      │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ • Map HTTP GET requests to handler methods              │   │
│   │ • Used for viewing/displaying data                      │   │
│   │ • Used for showing forms before submission              │   │
│   │ • Retrieves information without modifying state         │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Syntax:                                                       │
│   @GetMapping("path")                                           │
│   @GetMapping("/path")    // Both work                          │
│   @GetMapping(value="/path")                                    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### @GetMapping Example with Explanation

```java
/**
 * Example showing @GetMapping usage
 */

@Controller
public class BookNewController
{
    @GetMapping("book")                    // When GET request comes to /book
    public String before()                 // This method runs
    {
        return "bookNew";                  // Returns view name: bookNew.html
    }
}
```

### Line-by-Line Breakdown

| Line | Code | What Happens |
|------|------|--------------|
| `@GetMapping("book")` | Maps GET /book | When user visits `http://localhost:8080/book` via browser |
| `public String before()` | Handler method | Spring calls this method |
| `return "bookNew"` | View name | View Resolver finds `templates/bookNew.html` |

### What Triggers @GetMapping?

```
┌─────────────────────────────────────────────────────────────────┐
│              WHAT TRIGGERS @GETMAPPING                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. Typing URL in browser:                                     │
│      http://localhost:8080/book                                 │
│                                                                 │
│   2. Clicking a hyperlink:                                      │
│      <a href="book">Click here</a>                              │
│                                                                 │
│   3. Thymeleaf link:                                            │
│      <a th:href="@{/book}">Click here to add Book</a>           │
│                                                                 │
│   4. Redirect from another controller                           │
│                                                                 │
│   All of these send HTTP GET requests!                          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## @PostMapping Annotation

### What is @PostMapping?

```
┌─────────────────────────────────────────────────────────────────┐
│                    @POSTMAPPING                                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   @PostMapping is a shortcut for @RequestMapping(method = POST) │
│                                                                 │
│   Purpose:                                                      │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ • Map HTTP POST requests to handler methods             │   │
│   │ • Used for form submissions                             │   │
│   │ • Used for creating new resources                       │   │
│   │ • Modifies data/state on server                         │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Syntax:                                                       │
│   @PostMapping("path")                                          │
│   @PostMapping("/path")                                         │
│   @PostMapping(value="/path")                                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### @PostMapping Example with Explanation

```java
/**
 * Example showing @PostMapping with @RequestParam
 */

@Controller
public class BookNewController
{
    @PostMapping("book")                   // When POST request comes to /book
    public String afterSubmit(
        @RequestParam("bookname") String name,      // Extract "bookname" field
        @RequestParam("price") long bookprice,      // Extract "price" field
        Model model)                                // Spring provides Model
    {
        // new way to read request parameter
        
        Book book = new Book();            // Create Book object
        book.setBookName(name);            // Set from form field
        book.setPrice(bookprice);          // Set from form field
        model.addAttribute("mybook", book); // Add to model for view
        return "success";                   // Return view name
    }
}
```

### Line-by-Line Breakdown

| Line | Code | What Happens |
|------|------|--------------|
| `@PostMapping("book")` | Maps POST /book | Form submission to /book triggers this |
| `@RequestParam("bookname") String name` | Extract form field | Gets value from `<input name="bookname">` |
| `@RequestParam("price") long bookprice` | Extract form field | Gets value from `<input name="price">` |
| `Model model` | Dependency Injection | Spring automatically injects Model |
| `new Book()` | Create POJO | Instantiate new Book object |
| `model.addAttribute("mybook", book)` | Add to model | Makes `mybook` available in view |
| `return "success"` | View name | View Resolver finds `success.html` |

### What Triggers @PostMapping?

```
┌─────────────────────────────────────────────────────────────────┐
│              WHAT TRIGGERS @POSTMAPPING                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   HTML Form with method="post":                                 │
│                                                                 │
│   <form th:action="@{/book}" method="post">                     │
│       <input type="text" name="bookname" />                     │
│       <input type="text" name="price" />                        │
│       <input type="submit" value="Submit" />                    │
│   </form>                                                       │
│                                                                 │
│   When user clicks "Submit":                                    │
│   1. Form data is collected                                     │
│   2. HTTP POST request sent to /book                            │
│   3. @PostMapping("book") method handles it                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## @RequestMapping Annotation

### What is @RequestMapping?

```
┌─────────────────────────────────────────────────────────────────┐
│                    @REQUESTMAPPING                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   @RequestMapping is the general-purpose annotation for         │
│   mapping HTTP requests to handler methods.                     │
│                                                                 │
│   It can handle ANY HTTP method:                                │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @RequestMapping(value="/book", method=RequestMethod.GET)│   │
│   │ @RequestMapping(value="/book", method=RequestMethod.POST)│  │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Evolution:                                                    │
│   @RequestMapping(method=GET)  →  @GetMapping (Spring 4.3+)     │
│   @RequestMapping(method=POST) →  @PostMapping (Spring 4.3+)    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### @RequestMapping vs @GetMapping/@PostMapping

```
┌─────────────────────────────────────────────────────────────────┐
│       @REQUESTMAPPING vs @GETMAPPING/@POSTMAPPING                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   OLDER APPROACH (still valid):                                 │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @RequestMapping(value = "/book", method = RequestMethod.GET)│
│   │ public String showForm() { ... }                        │   │
│   │                                                         │   │
│   │ @RequestMapping(value = "/book", method = RequestMethod.POST)│
│   │ public String submitForm() { ... }                      │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   MODERN APPROACH (preferred):                                  │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @GetMapping("/book")                                    │   │
│   │ public String showForm() { ... }                        │   │
│   │                                                         │   │
│   │ @PostMapping("/book")                                   │   │
│   │ public String submitForm() { ... }                      │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Benefits of modern approach:                                  │
│   ✓ More concise                                                │
│   ✓ More readable                                               │
│   ✓ Less typing                                                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## @RequestParam for Reading Form Data

### What is @RequestParam?

```
┌─────────────────────────────────────────────────────────────────┐
│                    @REQUESTPARAM                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   @RequestParam extracts query parameters or form data from     │
│   the HTTP request.                                             │
│                                                                 │
│   Syntax:                                                       │
│   @RequestParam("formFieldName") DataType variableName          │
│                                                                 │
│   Example:                                                      │
│   @RequestParam("bookname") String name                         │
│                                                                 │
│   What it does:                                                 │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ Form:  <input type="text" name="bookname" />            │   │
│   │                     │                                   │   │
│   │                     ▼                                   │   │
│   │ @RequestParam("bookname") extracts the value            │   │
│   │                     │                                   │   │
│   │                     ▼                                   │   │
│   │ Value stored in: String name                            │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### @RequestParam Attributes

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `value` / `name` | Form field name | `@RequestParam("bookname")` |
| `required` | Is field mandatory? | `@RequestParam(required=false)` |
| `defaultValue` | Default if not provided | `@RequestParam(defaultValue="0")` |

### @RequestParam Example

```java
// Various @RequestParam usages

// Basic usage - field is required
@RequestParam("bookname") String name

// With explicit value attribute
@RequestParam(value = "bookname") String name

// Optional parameter with default
@RequestParam(value = "price", required = false, defaultValue = "0") long price

// Multiple parameters
@PostMapping("book")
public String process(
    @RequestParam("bookname") String name,
    @RequestParam("price") long bookprice,
    @RequestParam(value = "author", required = false) String author
) { ... }
```

---

## Traditional vs Modern Approach

### Traditional Approach (Using HttpServletRequest)

```java
/**
 * OLD WAY: Using HttpServletRequest directly
 * 
 * This was the traditional way before Spring annotations
 */

@PostMapping("book")
public String afterSubmit(HttpServletRequest request) 
{
    // Old way to read request parameter
    
    Book book = new Book();
    
    // Manually extract parameters from request
    book.setBookName(request.getParameter("bookname"));
    book.setPrice(Long.parseLong(request.getParameter("price")));
    
    // Set attribute in request for view
    request.setAttribute("mb", book);
    
    return "success";
}
```

### Modern Approach (Using @RequestParam)

```java
/**
 * NEW WAY: Using @RequestParam
 * 
 * This is the modern, cleaner approach
 */

@PostMapping("book")
public String afterSubmit(
    @RequestParam("bookname") String name,
    @RequestParam("price") long bookprice,
    Model model) 
{
    // new way to read request parameter
    
    Book book = new Book();
    book.setBookName(name);
    book.setPrice(bookprice);
    
    model.addAttribute("mybook", book);
    
    return "success";
}
```

### Comparison Table

```
┌─────────────────────────────────────────────────────────────────┐
│           TRADITIONAL VS MODERN APPROACH                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   TRADITIONAL (HttpServletRequest):                             │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ • request.getParameter("bookname")                      │   │
│   │ • Returns String - manual parsing needed                │   │
│   │ • More verbose                                          │   │
│   │ • Tight coupling to Servlet API                         │   │
│   │ • request.setAttribute("mb", book)                      │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   MODERN (@RequestParam):                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ • @RequestParam("bookname") String name                 │   │
│   │ • Automatic type conversion                             │   │
│   │ • Clean and concise                                     │   │
│   │ • Loosely coupled                                       │   │
│   │ • model.addAttribute("mybook", book)                    │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Benefits of Modern Approach:                                  │
│   ✓ Automatic type conversion (String to long, etc.)            │
│   ✓ Built-in validation support                                 │
│   ✓ Cleaner code                                                │
│   ✓ Better testability                                          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Complete Code Examples

### Complete Controller with Both GET and POST

```java
/**
 * BookNewController.java - Complete Example
 * 
 * Demonstrates the typical pattern of:
 * - GET to show form
 * - POST to process form
 */

package com.example.demo;                              // Package declaration

import org.springframework.stereotype.Controller;      // Controller annotation
import org.springframework.ui.Model;                   // Model interface
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class BookNewController
{
    /**
     * GET /book - Display the book entry form
     * 
     * Called when:
     * - User types http://localhost:8080/book
     * - User clicks a link to /book
     */
    @GetMapping("book")
    public String before()
    {
        return "bookNew";    // Show bookNew.html form
    }
    
    /**
     * POST /book - Process form submission
     * 
     * Called when:
     * - User submits the form with method="post"
     * 
     * Parameters:
     * - bookname: extracted from form field
     * - price: extracted and converted to long
     * - model: provided by Spring for passing data to view
     */
    @PostMapping("book")
    public String afterSubmit(
        @RequestParam("bookname") String name,
        @RequestParam("price") long bookprice,
        Model model) 
    {
        // Create and populate Book object
        Book book = new Book();
        book.setBookName(name);
        book.setPrice(bookprice);
        
        // Add to model - accessible in view as ${mybook}
        model.addAttribute("mybook", book);
        
        return "success";    // Show success.html
    }
}
```

### Corresponding HTML Form (bookNew.html)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="ISO-8859-1">
    <title>Add Book</title>
</head>
<body>
    <!-- Form sends POST request to /book -->
    <form th:action="@{/book}" method="post">
        <table border="1">
            <tr>
                <td>Enter Name</td>
                <!-- name="bookname" matches @RequestParam("bookname") -->
                <td><input type="text" name="bookname" /></td>
            </tr>
            <tr>
                <td>Enter Price</td>
                <!-- name="price" matches @RequestParam("price") -->
                <td><input type="text" name="price" /></td>
            </tr>
            <tr>
                <td><input type="submit" value="Submit" /></td>
            </tr>
        </table>
    </form>
</body>
</html>
```

### Success Page (success.html)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="ISO-8859-1">
    <title>Success</title>
</head>
<body>
    <!-- Access mybook from model -->
    <p>Book Name: <span th:text="${mybook.bookName}"></span></p>
    <br/>
    <p>Price: <span th:text="${mybook.price}"></span></p>
</body>
</html>
```

---

## Request Handling Flow

### Complete Flow Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│              COMPLETE REQUEST HANDLING FLOW                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   PHASE 1: Display Form (GET Request)                           │
│   ════════════════════════════════════                          │
│                                                                 │
│   Browser: http://localhost:8080/book                           │
│        │                                                        │
│        ▼                                                        │
│   @GetMapping("book") → before() → return "bookNew"             │
│        │                                                        │
│        ▼                                                        │
│   View Resolver → templates/bookNew.html                        │
│        │                                                        │
│        ▼                                                        │
│   HTML Form displayed in browser                                │
│                                                                 │
│   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ │
│                                                                 │
│   PHASE 2: User Fills Form                                      │
│   ════════════════════════                                      │
│                                                                 │
│   User enters:                                                  │
│   ┌──────────────────────────────────────────┐                  │
│   │ Book Name: [Java Programming_________]   │                  │
│   │ Price:     [599_____________________ ]   │                  │
│   │           [Submit]                       │                  │
│   └──────────────────────────────────────────┘                  │
│                                                                 │
│   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ │
│                                                                 │
│   PHASE 3: Form Submission (POST Request)                       │
│   ═══════════════════════════════════════                       │
│                                                                 │
│   Form data: bookname=Java+Programming&price=599                │
│        │                                                        │
│        ▼                                                        │
│   @PostMapping("book")                                          │
│        │                                                        │
│        ▼                                                        │
│   @RequestParam extracts: name="Java Programming", price=599    │
│        │                                                        │
│        ▼                                                        │
│   Book object created and populated                             │
│        │                                                        │
│        ▼                                                        │
│   model.addAttribute("mybook", book)                            │
│        │                                                        │
│        ▼                                                        │
│   return "success" → templates/success.html                     │
│        │                                                        │
│        ▼                                                        │
│   ${mybook.bookName} and ${mybook.price} replaced               │
│        │                                                        │
│        ▼                                                        │
│   HTML with actual values sent to browser                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

### HTTP Method Mapping Annotations

| Annotation | HTTP Method | Shortcut For |
|------------|-------------|--------------|
| `@GetMapping("/path")` | GET | `@RequestMapping(value="/path", method=GET)` |
| `@PostMapping("/path")` | POST | `@RequestMapping(value="/path", method=POST)` |
| `@PutMapping("/path")` | PUT | `@RequestMapping(value="/path", method=PUT)` |
| `@DeleteMapping("/path")` | DELETE | `@RequestMapping(value="/path", method=DELETE)` |
| `@PatchMapping("/path")` | PATCH | `@RequestMapping(value="/path", method=PATCH)` |

### @RequestParam Options

```java
// Required parameter (default)
@RequestParam("name") String name

// Optional parameter
@RequestParam(value = "name", required = false) String name

// With default value
@RequestParam(value = "name", defaultValue = "Guest") String name

// Multiple parameters
public String method(
    @RequestParam("name") String name,
    @RequestParam("age") int age,
    @RequestParam(value = "city", required = false) String city
)
```

### Model Methods

| Method | Purpose |
|--------|---------|
| `model.addAttribute("key", value)` | Add data for view |
| `model.addAllAttributes(Map)` | Add multiple attributes |
| `model.containsAttribute("key")` | Check if attribute exists |

---

## Next Steps

After understanding Request Handling, proceed to:
- **Note 06**: ModelAndView & Model - Learn about passing data to views

---

*This note is part of the Advanced Java - Spring Boot & Spring MVC series*
