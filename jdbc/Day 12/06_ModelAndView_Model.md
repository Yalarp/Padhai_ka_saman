# 📘 ModelAndView & Model - Complete Guide

## Table of Contents
1. [Introduction](#introduction)
2. [Model Interface](#model-interface)
3. [ModelAndView Class](#modelandview-class)
4. [Comparison: Model vs ModelAndView](#comparison-model-vs-modelandview)
5. [Working with Collections](#working-with-collections)
6. [Complete Code Examples](#complete-code-examples)
7. [Execution Flow](#execution-flow)
8. [Quick Reference](#quick-reference)

---

## Introduction

### Passing Data to Views

In Spring MVC, controllers need to pass data to views for rendering. There are two primary ways:

```
┌─────────────────────────────────────────────────────────────────┐
│           PASSING DATA TO VIEWS                                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Two approaches:                                               │
│                                                                 │
│   1. MODEL INTERFACE                                            │
│      ├── Passed as method parameter                             │
│      ├── Use with String return type                            │
│      └── model.addAttribute("key", value)                       │
│                                                                 │
│   2. MODELANDVIEW CLASS                                         │
│      ├── Return type of method                                  │
│      ├── Combines model + view name                             │
│      └── new ModelAndView("viewName", "key", value)             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Model Interface

### What is Model?

```
┌─────────────────────────────────────────────────────────────────┐
│                    MODEL INTERFACE                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Model is an interface that Spring provides for storing        │
│   attributes to be displayed in the view.                       │
│                                                                 │
│   Key Methods:                                                  │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ addAttribute(String name, Object value)                 │   │
│   │   → Adds single attribute                               │   │
│   │                                                         │   │
│   │ addAllAttributes(Map<String, ?> attributes)             │   │
│   │   → Adds multiple attributes                            │   │
│   │                                                         │   │
│   │ containsAttribute(String name)                          │   │
│   │   → Checks if attribute exists                          │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Usage Pattern:                                                │
│   public String method(Model model) {                           │
│       model.addAttribute("key", value);                         │
│       return "viewName";  // String return type                 │
│   }                                                             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Model Example with Code

```java
/**
 * Controller using Model interface
 */

package com.example.demo;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;                   // Import Model interface
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class BookNewController
{
    @PostMapping("book")
    public String afterSubmit(
        @RequestParam("bookname") String name,         // Form field value
        @RequestParam("price") long bookprice,         // Form field value
        Model model)                                   // Spring injects Model
    {
        Book book = new Book();                        // Create Book object
        book.setBookName(name);                        // Set book name
        book.setPrice(bookprice);                      // Set price
        
        // Add book to model - accessible in view as ${mybook}
        model.addAttribute("mybook", book);
        
        return "success";                              // Return view name (String)
    }
}
```

### Line-by-Line Explanation

| Line | Code | Explanation |
|------|------|-------------|
| `Model model` | Method parameter | Spring automatically injects Model instance |
| `model.addAttribute("mybook", book)` | Add to model | "mybook" = key, book = value |
| `return "success"` | View name | Controller returns String, not ModelAndView |

### Accessing Model Data in View

```html
<!-- success.html -->
<p th:text="${mybook.bookName}">
<!-- ${mybook} accesses the "mybook" attribute added to Model -->
<p th:text="${mybook.price}">
```

---

## ModelAndView Class

### What is ModelAndView?

```
┌─────────────────────────────────────────────────────────────────┐
│                    MODELANDVIEW CLASS                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ModelAndView combines both the model data and view name       │
│   in a single return object.                                    │
│                                                                 │
│   Constructors:                                                 │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ new ModelAndView("viewName")                            │   │
│   │   → Just view name, no model data                       │   │
│   │                                                         │   │
│   │ new ModelAndView("viewName", "attrName", attrValue)     │   │
│   │   → View name + single attribute                        │   │
│   │                                                         │   │
│   │ new ModelAndView("viewName", modelMap)                  │   │
│   │   → View name + multiple attributes via Map             │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Usage Pattern:                                                │
│   public ModelAndView method() {                                │
│       return new ModelAndView("viewName", "key", value);        │
│   }                                                             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### ModelAndView Constructor Breakdown

```java
// Constructor with three parameters
new ModelAndView("Final", "mylist", booklist)

// Breaking it down:
// ┌────────────────┬────────────────────────────────────────┐
// │ Parameter 1    │ "Final" = view name (Final.html)       │
// │ Parameter 2    │ "mylist" = attribute name in view      │
// │ Parameter 3    │ booklist = attribute value (List<Book>)│
// └────────────────┴────────────────────────────────────────┘
```

### ModelAndView Example with Code

```java
/**
 * Controller using ModelAndView class
 */

package com.example.demo;

import java.util.ArrayList;
import java.util.List;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.servlet.ModelAndView;    // Import ModelAndView

@Controller
public class BookNewController
{
    @GetMapping("book")
    public ModelAndView before()                        // Return type is ModelAndView
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
        
        // Return ModelAndView with view name and model data
        return new ModelAndView("Final", "mylist", booklist);
    }
}
```

### Line-by-Line Explanation

| Line | Code | Explanation |
|------|------|-------------|
| `public ModelAndView before()` | Return type | Method returns ModelAndView, not String |
| `Book mybook1 = new Book()` | Create objects | Prepare data to send to view |
| `List<Book> booklist` | Collection | Store multiple Book objects |
| `new ModelAndView("Final", "mylist", booklist)` | Create MAV | Combines view + model in one object |

---

## Comparison: Model vs ModelAndView

### Side-by-Side Comparison

```
┌─────────────────────────────────────────────────────────────────┐
│           MODEL vs MODELANDVIEW                                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   MODEL (Interface)             MODELANDVIEW (Class)            │
│   ═════════════════             ════════════════════            │
│                                                                 │
│   Return: String                Return: ModelAndView            │
│                                                                 │
│   @GetMapping("book")           @GetMapping("book")             │
│   public String show(           public ModelAndView show() {    │
│       Model model) {                                            │
│                                                                 │
│       Book book = new Book();       Book book = new Book();     │
│       model.addAttribute(           return new ModelAndView(    │
│           "book", book);                "viewName",             │
│                                         "book", book);          │
│                                     }                           │
│       return "viewName";                                        │
│   }                                                             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### When to Use Which?

| Use Case | Recommended |
|----------|-------------|
| Simple data passing | Model (cleaner syntax) |
| Multiple attributes | Model (multiple addAttribute calls) |
| View + single attribute | ModelAndView (more explicit) |
| Dynamic view selection | ModelAndView (can set view name programmatically) |
| Testing | Model (easier to mock) |

---

## Working with Collections

### Passing Lists to View

```java
/**
 * Passing a List to the view
 */

@GetMapping("book")
public ModelAndView showBooks()
{
    // Create list of books
    List<Book> booklist = new ArrayList<Book>();
    
    Book book1 = new Book();
    book1.setBookName("Java Black Book"); 
    book1.setPrice(900);
    booklist.add(book1);
    
    Book book2 = new Book();
    book2.setBookName("Understanding Pointers in C"); 
    book2.setPrice(400);
    booklist.add(book2);
    
    // Pass list to view
    return new ModelAndView("Final", "mylist", booklist);
}
```

### Iterating in Thymeleaf View (Final.html)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="ISO-8859-1">
    <title>Book List</title>
</head>
<body>

<table border="10">
    <!-- th:each iterates over the list -->
    <!-- ref is the loop variable for each Book object -->
    <tr th:each="ref : ${mylist}">
        <!-- Access bookName property of each Book -->
        <td th:text="${ref.bookName}" />
        <!-- Access price property of each Book -->
        <td th:text="${ref.price}" />
    </tr>
</table>

</body>
</html>
```

### Line-by-Line HTML Explanation

| Line | Code | Explanation |
|------|------|-------------|
| `th:each="ref : ${mylist}"` | Iteration | Loop through "mylist" collection, "ref" is current item |
| `${mylist}` | Variable expression | Accesses "mylist" attribute from ModelAndView |
| `${ref.bookName}` | Property access | Gets bookName from current Book object |
| `${ref.price}` | Property access | Gets price from current Book object |

### Expected Output

```
┌─────────────────────────────────────────────────────────────────┐
│                    BROWSER OUTPUT                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌─────────────────────────────┬───────────┐                   │
│   │ Java Black Book             │ 900       │                   │
│   ├─────────────────────────────┼───────────┤                   │
│   │ Understanding Pointers in C │ 400       │                   │
│   ├─────────────────────────────┼───────────┤                   │
│   │ The complete JavaEE Guide   │ 800       │                   │
│   └─────────────────────────────┴───────────┘                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Complete Code Examples

### Book.java (Model/POJO Class)

```java
/**
 * Book.java - Model class representing a Book entity
 * 
 * This is a POJO (Plain Old Java Object) that holds book data.
 */

package com.example.demo;

public class Book 
{
    // Private fields (encapsulation)
    private String bookName;
    private long price;
    
    // toString for debugging
    @Override
    public String toString() {
        return "[" + bookName + "   " + price + "]";
    }
    
    // Getter for bookName
    public String getBookName() {
        return bookName;
    }
    
    // Setter for bookName
    public void setBookName(String bookName) {
        this.bookName = bookName;
    }
    
    // Getter for price
    public long getPrice() {
        return price;
    }
    
    // Setter for price
    public void setPrice(long price) {
        this.price = price;
    }
}
```

### Controller with Both Approaches

```java
/**
 * Controller demonstrating both Model and ModelAndView
 */

package com.example.demo;

import java.util.ArrayList;
import java.util.List;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class BookController
{
    /**
     * Using ModelAndView - display list of books
     */
    @GetMapping("books")
    public ModelAndView showAllBooks()
    {
        List<Book> books = createBookList();
        return new ModelAndView("bookList", "mylist", books);
    }
    
    /**
     * Using Model - display single book form
     */
    @GetMapping("book")
    public ModelAndView showForm()
    {
        Book defaultBook = new Book();  // Empty book for form binding
        return new ModelAndView("bookNew", "mybook", defaultBook);
    }
    
    /**
     * Using Model - process form submission
     */
    @PostMapping("book")
    public String processForm(
        @RequestParam("bookname") String name,
        @RequestParam("price") long price,
        Model model)
    {
        Book book = new Book();
        book.setBookName(name);
        book.setPrice(price);
        
        model.addAttribute("mybook", book);
        return "success";
    }
    
    // Helper method
    private List<Book> createBookList() {
        List<Book> list = new ArrayList<>();
        // ... add books
        return list;
    }
}
```

---

## Execution Flow

### ModelAndView Execution Flow

```
┌─────────────────────────────────────────────────────────────────┐
│              MODELANDVIEW EXECUTION FLOW                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. Request: GET /book                                         │
│      │                                                          │
│      ▼                                                          │
│   2. @GetMapping("book") matched                                │
│      │                                                          │
│      ▼                                                          │
│   3. before() method executes                                   │
│      ├── Creates Book objects                                   │
│      ├── Creates List<Book>                                     │
│      └── Adds books to list                                     │
│      │                                                          │
│      ▼                                                          │
│   4. Returns: new ModelAndView("Final", "mylist", booklist)     │
│      │                                                          │
│      ▼                                                          │
│   5. DispatcherServlet receives ModelAndView                    │
│      ├── Extracts view name: "Final"                            │
│      └── Extracts model: "mylist" → booklist                    │
│      │                                                          │
│      ▼                                                          │
│   6. View Resolver: "Final" → templates/Final.html              │
│      │                                                          │
│      ▼                                                          │
│   7. Thymeleaf processes Final.html                             │
│      ├── th:each="ref : ${mylist}" iterates booklist            │
│      └── ${ref.bookName}, ${ref.price} replaced                 │
│      │                                                          │
│      ▼                                                          │
│   8. Pure HTML sent to browser                                  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

### Model Methods

| Method | Description |
|--------|-------------|
| `addAttribute(name, value)` | Add single attribute |
| `addAllAttributes(Map)` | Add multiple attributes |
| `mergeAttributes(Map)` | Merge with existing |
| `containsAttribute(name)` | Check existence |

### ModelAndView Constructors

| Constructor | Use Case |
|-------------|----------|
| `ModelAndView()` | Empty, set later |
| `ModelAndView("view")` | View only |
| `ModelAndView("view", "name", obj)` | View + single attribute |
| `ModelAndView("view", Map)` | View + multiple attributes |

### ModelAndView Methods

| Method | Description |
|--------|-------------|
| `setViewName(name)` | Set view name |
| `addObject(name, value)` | Add model attribute |
| `addAllObjects(Map)` | Add multiple |
| `getViewName()` | Get view name |
| `getModel()` | Get model Map |

---

## Next Steps

After understanding Model and ModelAndView, proceed to:
- **Note 07**: @ModelAttribute & Form Binding - Learn two-way data binding

---

*This note is part of the Advanced Java - Spring Boot & Spring MVC series*
