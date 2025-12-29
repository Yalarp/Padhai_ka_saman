# 📘 @ModelAttribute & Form Binding - Complete Guide

## Table of Contents
1. [Introduction to Form Binding](#introduction-to-form-binding)
2. [@ModelAttribute Annotation](#modelattribute-annotation)
3. [th:object for Form Binding](#thobject-for-form-binding)
4. [th:field for Input Binding](#thfield-for-input-binding)
5. [Two-Way Data Binding](#two-way-data-binding)
6. [Command Object Pattern](#command-object-pattern)
7. [Complete Code Examples](#complete-code-examples)
8. [Execution Flow](#execution-flow)
9. [Quick Reference](#quick-reference)

---

## Introduction to Form Binding

### What is Form Binding?

```
┌─────────────────────────────────────────────────────────────────┐
│                    FORM BINDING CONCEPT                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Form binding is the AUTOMATIC MAPPING between:                │
│                                                                 │
│   ┌───────────────────┐         ┌───────────────────┐           │
│   │   HTML FORM       │  ←───►  │   JAVA OBJECT     │           │
│   │   FIELDS          │         │   PROPERTIES      │           │
│   └───────────────────┘         └───────────────────┘           │
│                                                                 │
│   <input name="bookName">  ←───►  book.setBookName()            │
│   <input name="price">     ←───►  book.setPrice()               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Form Binding Components

```
┌─────────────────────────────────────────────────────────────────┐
│              FORM BINDING COMPONENTS                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   VIEW (Thymeleaf)              CONTROLLER                      │
│   ═════════════════             ═══════════                     │
│                                                                 │
│   th:object="${mybook}"    ←→   @ModelAttribute Book book       │
│        │                              │                         │
│        │                              │                         │
│        ▼                              ▼                         │
│   th:field="*{bookName}"   ←→   book.getBookName()              │
│   th:field="*{price}"      ←→   book.getPrice()                 │
│                                                                 │
│   Benefits:                                                     │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ ✓ No manual parameter extraction                        │   │
│   │ ✓ Automatic type conversion (String → long, etc.)       │   │
│   │ ✓ Pre-populate form with existing data                  │   │
│   │ ✓ Built-in validation support                           │   │
│   │ ✓ Error handling with BindingResult                     │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Without vs With Form Binding

```
┌─────────────────────────────────────────────────────────────────┐
│           WITHOUT vs WITH FORM BINDING                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   WITHOUT Form Binding (Manual):                                │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @PostMapping("book")                                    │   │
│   │ public String submit(                                   │   │
│   │     @RequestParam("bookname") String name,              │   │
│   │     @RequestParam("price") long price,                  │   │
│   │     Model model) {                                      │   │
│   │                                                         │   │
│   │     Book book = new Book();  // Create manually         │   │
│   │     book.setBookName(name);  // Set each field          │   │
│   │     book.setPrice(price);    // Set each field          │   │
│   │     model.addAttribute("book", book);                   │   │
│   │     return "success";                                   │   │
│   │ }                                                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   WITH Form Binding (@ModelAttribute):                          │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @PostMapping("book")                                    │   │
│   │ public String submit(                                   │   │
│   │     @ModelAttribute("mb") Book book) {                  │   │
│   │                                                         │   │
│   │     // Book is automatically created and populated!     │   │
│   │     // Also automatically added to Model as "mb"        │   │
│   │     return "success";                                   │   │
│   │ }                                                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## @ModelAttribute Annotation

### What is @ModelAttribute?

```
┌─────────────────────────────────────────────────────────────────┐
│                    @MODELATTRIBUTE                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   @ModelAttribute automatically binds form data to a Java       │
│   object (also called "command object" or "form backing bean")  │
│                                                                 │
│   Two main uses:                                                │
│                                                                 │
│   1. ON METHOD PARAMETER (most common)                          │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @PostMapping("book")                                    │   │
│   │ public String submit(                                   │   │
│   │     @ModelAttribute("mb") Book book) { ... }            │   │
│   │                        │                                │   │
│   │                        └── Attribute name in view       │   │
│   │                                                         │   │
│   │ What happens:                                           │   │
│   │ 1. Spring creates new Book()                            │   │
│   │ 2. Spring reads form data                               │   │
│   │ 3. Spring calls setters (setBookName, setPrice)         │   │
│   │ 4. Spring adds Book to Model with key "mb"              │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   2. ON METHOD (adds to model before every handler)             │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @ModelAttribute("categories")                           │   │
│   │ public List<String> populateCategories() {              │   │
│   │     return Arrays.asList("Fiction", "Non-Fiction");     │   │
│   │ }                                                       │   │
│   │                                                         │   │
│   │ Runs before EVERY @RequestMapping method in controller  │   │
│   │ Adds result to Model automatically                      │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### @ModelAttribute Example with Line-by-Line Explanation

```java
/**
 * BookNewController.java - Complete @ModelAttribute Example
 */

package com.example.demo;                              // Line 1: Package

import org.springframework.stereotype.Controller;      // Line 2: Import
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.servlet.ModelAndView;
import jakarta.servlet.http.HttpSession;               // Line 7: Session import

@Controller                                            // Line 8: Controller annotation
public class BookNewController
{
    /**
     * Home page displays a hyperlink to the book form
     */
    @GetMapping("/")                                   // Line 12: Root URL mapping
    public String home()                               // Line 13: Home method
    {
        return "Home";                                 // Line 15: Return Home.html
    }
    
    /**
     * GET /book - Display the form
     * 
     * Creates an EMPTY Book object for th:object binding.
     * This enables *{...} expressions in the form.
     */
    @GetMapping("book")                                // Line 22: GET /book
    public ModelAndView before()                       // Line 23: Display form
    {
        Book defaultBook = new Book();                 // Line 25: Create empty Book
        
        // Return view "bookNew" with "mybook" as attribute name
        return new ModelAndView("bookNew", "mybook", defaultBook);
        // "bookNew" → View name (bookNew.html)
        // "mybook"  → Attribute name (used in th:object)
        // defaultBook → The Book object
    }
    
    /**
     * POST /book - Process form submission
     * 
     * @ModelAttribute("mb") does THREE things:
     * 1. Creates new Book instance
     * 2. Binds form fields to Book properties (calls setters)
     * 3. Adds Book to Model with attribute name "mb"
     */
    @PostMapping("book")                               // Line 38: POST /book
    public String afterSubmit(
        @ModelAttribute("mb") Book book,               // Line 40: Bind form → Book
        HttpSession session)                           // Line 41: Access session
    {
        // At this point, book already has:
        // - book.getBookName() = value from form field "bookName"
        // - book.getPrice() = value from form field "price"
        
        session.setAttribute("val", 2000);             // Line 46: Store in session
        
        return "success";                              // Line 48: Return success.html
        // success.html can access ${mb.bookName} and ${mb.price}
    }
}
```

### Line-by-Line Explanation Table

| Line | Code | Explanation |
|------|------|-------------|
| 25 | `Book defaultBook = new Book()` | Create empty Book for form binding |
| 27 | `ModelAndView("bookNew", "mybook", defaultBook)` | View + attribute for th:object |
| 40 | `@ModelAttribute("mb") Book book` | Auto-bind form data to Book |
| 41 | `HttpSession session` | Spring injects session automatically |
| 46 | `session.setAttribute("val", 2000)` | Store value accessible across requests |
| 48 | `return "success"` | Book is already in model as "mb" |

---

## th:object for Form Binding

### What is th:object?

```
┌─────────────────────────────────────────────────────────────────┐
│                    th:object                                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   th:object binds an ENTIRE FORM to a Java object               │
│                                                                 │
│   Syntax:                                                       │
│   <form th:object="${attributeName}">                           │
│                                                                 │
│   What it does:                                                 │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ 1. Connects form to model attribute                     │   │
│   │ 2. Enables *{...} selection expressions                 │   │
│   │ 3. Pre-populates fields with object values              │   │
│   │ 4. Works with @ModelAttribute on POST                   │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Connection between Controller and View:                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ Controller:                                             │   │
│   │   new ModelAndView("bookNew", "mybook", book)           │   │
│   │                              ↓                          │   │
│   │ View:                        ↓                          │   │
│   │   <form th:object="${mybook}">                          │   │
│   │                     └───────┘                           │   │
│   │              Same attribute name!                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### th:object Example

```html
<!-- bookNew.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="ISO-8859-1">
    <title>Add Book</title>
</head>
<body>

<!-- 
    th:action="@{/book}" → Form submits to /book
    th:object="${mybook}" → Bind form to "mybook" object
    method="post" → Triggers @PostMapping("book")
-->
<form th:action="@{/book}" th:object="${mybook}" method="post">
    
    <!-- Now we can use *{...} expressions -->
    <table border="1">
        <tr>
            <td>Enter Name</td>
            <td><input type="text" th:field="*{bookName}" /></td>
        </tr>
        <tr>
            <td>Enter Price</td>
            <td><input type="text" th:field="*{price}" /></td>
        </tr>
        <tr>
            <td><input type="submit" value="Submit" /></td>
        </tr>
    </table>
</form>

</body>
</html>
```

---

## th:field for Input Binding

### What is th:field?

```
┌─────────────────────────────────────────────────────────────────┐
│                    th:field                                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   th:field binds a form INPUT to an object PROPERTY             │
│                                                                 │
│   Syntax:                                                       │
│   <input type="text" th:field="*{propertyName}" />              │
│                                                                 │
│   What th:field="*{bookName}" generates:                        │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ <input type="text"                                      │   │
│   │        id="bookName"        ← Sets id attribute         │   │
│   │        name="bookName"      ← Sets name attribute       │   │
│   │        value="Java Guide"/> ← Sets value (if exists)    │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   THREE things th:field does automatically:                     │
│   ┌────────────────────────────────────────────────────────┐    │
│   │ 1. Sets name attribute (for form submission)           │    │
│   │ 2. Sets id attribute (for label linking, JS access)    │    │
│   │ 3. Sets value attribute (pre-population from object)   │    │
│   └────────────────────────────────────────────────────────┘    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### *{...} Selection Expression

```
┌─────────────────────────────────────────────────────────────────┐
│              SELECTION EXPRESSION *{...}                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   *{...} is a SELECTION expression                              │
│   It accesses properties of the object bound by th:object       │
│                                                                 │
│   Comparison:                                                   │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ ${...} Variable Expression:                             │   │
│   │   ${mybook.bookName} - Full path from model root        │   │
│   │                                                         │   │
│   │ *{...} Selection Expression:                            │   │
│   │   *{bookName} - Relative to th:object                   │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Both are equivalent inside the form:                          │
│   <form th:object="${mybook}">                                  │
│       <input th:field="*{bookName}" />                          │
│       <!-- Same as: th:value="${mybook.bookName}" -->           │
│   </form>                                                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### th:field vs name Attribute Comparison

```
┌─────────────────────────────────────────────────────────────────┐
│           th:field vs name ATTRIBUTE                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   WITHOUT th:object/th:field (using name attribute):            │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ <form action="/book" method="post">                     │   │
│   │   <input type="text" name="bookname" />                 │   │
│   │   <input type="text" name="price" />                    │   │
│   │ </form>                                                 │   │
│   │                                                         │   │
│   │ Controller uses @RequestParam:                          │   │
│   │ @PostMapping("book")                                    │   │
│   │ public String submit(                                   │   │
│   │     @RequestParam("bookname") String name,              │   │
│   │     @RequestParam("price") long price)                  │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   WITH th:object/th:field (automatic binding):                  │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ <form th:action="@{/book}" th:object="${mybook}"        │   │
│   │       method="post">                                    │   │
│   │   <input type="text" th:field="*{bookName}" />          │   │
│   │   <input type="text" th:field="*{price}" />             │   │
│   │ </form>                                                 │   │
│   │                                                         │   │
│   │ Controller uses @ModelAttribute:                        │   │
│   │ @PostMapping("book")                                    │   │
│   │ public String submit(                                   │   │
│   │     @ModelAttribute("mybook") Book book)                │   │
│   │ // book.getBookName() and book.getPrice() are set!      │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Benefits of th:field:                                         │
│   ✓ Automatic name/id generation from property name             │
│   ✓ Pre-population from existing object values                  │
│   ✓ Works with validation and error display                     │
│   ✓ Cleaner code, less prone to typos                           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Two-Way Data Binding

### How Two-Way Binding Works

```
┌─────────────────────────────────────────────────────────────────┐
│              TWO-WAY DATA BINDING                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   DIRECTION 1: Object → Form (GET request)                      │
│   ════════════════════════════════════════                      │
│                                                                 │
│   Controller creates object with data:                          │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ Book book = new Book();                                 │   │
│   │ book.setBookName("Java Guide");                         │   │
│   │ book.setPrice(599);                                     │   │
│   │ return new ModelAndView("form", "mybook", book);        │   │
│   └─────────────────────────────────────────────────────────┘   │
│                     │                                           │
│                     ▼                                           │
│   Thymeleaf renders with values:                                │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ <input th:field="*{bookName}" />                        │   │
│   │        ↓                                                │   │
│   │ <input name="bookName" id="bookName" value="Java Guide"/>│  │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ │
│                                                                 │
│   DIRECTION 2: Form → Object (POST request)                     │
│   ════════════════════════════════════════                      │
│                                                                 │
│   User edits form and submits:                                  │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ User types: "Spring Boot Mastery" in bookName field     │   │
│   │ User types: "899" in price field                        │   │
│   │ User clicks Submit                                      │   │
│   └─────────────────────────────────────────────────────────┘   │
│                     │                                           │
│                     ▼                                           │
│   Form data sent: bookName=Spring+Boot+Mastery&price=899        │
│                     │                                           │
│                     ▼                                           │
│   @ModelAttribute binds to Book:                                │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @ModelAttribute("mybook") Book book                     │   │
│   │ // book.getBookName() → "Spring Boot Mastery"           │   │
│   │ // book.getPrice() → 899                                │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Command Object Pattern

### What is a Command Object?

```
┌─────────────────────────────────────────────────────────────────┐
│              COMMAND OBJECT PATTERN                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   A Command Object (Form Backing Bean) is a POJO that holds     │
│   form data. It acts as a bridge between the form and the       │
│   controller.                                                   │
│                                                                 │
│   Requirements:                                                 │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ 1. Default no-arg constructor                           │   │
│   │    public Book() { }                                    │   │
│   │                                                         │   │
│   │ 2. Private fields                                       │   │
│   │    private String bookName;                             │   │
│   │    private long price;                                  │   │
│   │                                                         │   │
│   │ 3. Public getters/setters (JavaBean naming convention)  │   │
│   │    public String getBookName() { return bookName; }     │   │
│   │    public void setBookName(String name) { ... }         │   │
│   │                                                         │   │
│   │ 4. Field names match form field names                   │   │
│   │    th:field="*{bookName}" → setBookName() is called     │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Book.java - Command Object Example

```java
/**
 * Book.java - Command Object / Form Backing Bean
 */
package com.example.demo;

public class Book 
{
    // Private fields
    private String bookName;   // Matches th:field="*{bookName}"
    private long price;        // Matches th:field="*{price}"
    
    // Default constructor (required for Spring binding)
    public Book() { }
    
    // Getter for bookName - Called when rendering form
    public String getBookName() {
        return bookName;
    }
    
    // Setter for bookName - Called when binding form data
    public void setBookName(String bookName) {
        this.bookName = bookName;
    }
    
    // Getter for price
    public long getPrice() {
        return price;
    }
    
    // Setter for price - Automatic type conversion from String
    public void setPrice(long price) {
        this.price = price;
    }
    
    // toString for debugging
    @Override
    public String toString() {
        return "[" + bookName + "   " + price + "]";
    }
}
```

---

## Complete Code Examples

### Home.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="ISO-8859-1">
    <title>Home</title>
</head>
<body>
    <!-- Simple hyperlink - triggers GET /book -->
    <a href="book">Click here to add Book</a>
</body>
</html>
```

### bookNew.html - Form with Binding

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="ISO-8859-1">
    <title>Add Book</title>
</head>
<body>

<!--
    th:action="@{/book}" - Form submits to /book (POST)
    th:object="${mybook}" - Binds form to "mybook" from controller
    method="post" - Triggers @PostMapping("book")
-->
<form th:action="@{/book}" th:object="${mybook}" method="post">
    <table border="1">
        <tr>
            <td>Enter Name</td>
            <!-- th:field="*{bookName}" generates:
                 name="bookName" id="bookName" value="" -->
            <td><input type="text" th:field="*{bookName}" /></td>
        </tr>
        <tr>
            <td>Enter Price</td>
            <!-- th:field="*{price}" generates:
                 name="price" id="price" value="0" -->
            <td><input type="text" th:field="*{price}" /></td>
        </tr>
        <tr>
            <td><input type="submit" value="Submit" /></td>
        </tr>
    </table>
</form>

</body>
</html>
```

### success.html - Display Submitted Data

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="ISO-8859-1">
    <title>Success</title>
</head>
<body>

<!-- Access book via "mb" (from @ModelAttribute("mb")) -->
<p>Book Name: <span th:text="${mb.bookName}">Name here</span></p>
<br/>
<p>Price: <span th:text="${mb.price}">Price here</span></p>
<br/>

<!-- Access session attribute -->
<p>Session value is: <span th:text="${session.val}">0</span></p>

</body>
</html>
```

---

## Execution Flow

### Complete Step-by-Step Flow

```
┌─────────────────────────────────────────────────────────────────┐
│         COMPLETE @MODELATTRIBUTE EXECUTION FLOW                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   STEP 1: User visits http://localhost:8080/                    │
│   ═══════════════════════════════════════════                   │
│           │                                                     │
│           ▼                                                     │
│   @GetMapping("/") → home() → returns "Home"                    │
│           │                                                     │
│           ▼                                                     │
│   Home.html renders with link "Click here to add Book"          │
│                                                                 │
│   STEP 2: User clicks the link                                  │
│   ═════════════════════════════                                 │
│           │                                                     │
│           ▼                                                     │
│   Browser sends: GET /book                                      │
│           │                                                     │
│           ▼                                                     │
│   @GetMapping("book") → before()                                │
│           │                                                     │
│           ▼                                                     │
│   Creates: Book defaultBook = new Book()                        │
│           │ (empty book, bookName=null, price=0)                │
│           ▼                                                     │
│   Returns: ModelAndView("bookNew", "mybook", defaultBook)       │
│                                                                 │
│   STEP 3: Thymeleaf renders bookNew.html                        │
│   ═══════════════════════════════════════                       │
│           │                                                     │
│           ▼                                                     │
│   th:object="${mybook}" finds the Book object                   │
│           │                                                     │
│           ▼                                                     │
│   th:field="*{bookName}" generates:                             │
│     <input name="bookName" id="bookName" value="" />            │
│           │                                                     │
│           ▼                                                     │
│   th:field="*{price}" generates:                                │
│     <input name="price" id="price" value="0" />                 │
│                                                                 │
│   STEP 4: User fills form and clicks Submit                     │
│   ═════════════════════════════════════════                     │
│           │                                                     │
│           ▼                                                     │
│   User types: bookName="Spring Guide", price=599                │
│           │                                                     │
│           ▼                                                     │
│   Form submits: POST /book                                      │
│   Form data: bookName=Spring+Guide&price=599                    │
│                                                                 │
│   STEP 5: @ModelAttribute binds form to Book                    │
│   ═════════════════════════════════════════                     │
│           │                                                     │
│           ▼                                                     │
│   @PostMapping("book") matches                                  │
│           │                                                     │
│           ▼                                                     │
│   @ModelAttribute("mb") Book book                               │
│       1. Spring calls: new Book()                               │
│       2. Spring calls: book.setBookName("Spring Guide")         │
│       3. Spring calls: book.setPrice(599)                       │
│       4. Spring adds to model: "mb" → book                      │
│                                                                 │
│   STEP 6: Controller processes and returns                      │
│   ════════════════════════════════════════                      │
│           │                                                     │
│           ▼                                                     │
│   session.setAttribute("val", 2000)                             │
│           │                                                     │
│           ▼                                                     │
│   return "success" → success.html                               │
│                                                                 │
│   STEP 7: success.html renders                                  │
│   ═══════════════════════════                                   │
│           │                                                     │
│           ▼                                                     │
│   ${mb.bookName} → "Spring Guide"                               │
│   ${mb.price} → 599                                             │
│   ${session.val} → 2000                                         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

### @ModelAttribute Syntax Variations

| Syntax | Behavior |
|--------|----------|
| `@ModelAttribute Book book` | Attribute name = "book" (lowercase class name) |
| `@ModelAttribute("mb") Book book` | Attribute name = "mb" (explicit) |
| `@ModelAttribute("mb")` on method | Adds return value to model as "mb" |

### Thymeleaf Binding Attributes

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `th:object` | Bind form to object | `th:object="${book}"` |
| `th:field` | Bind input to property | `th:field="*{name}"` |
| `th:action` | Form submission URL | `th:action="@{/save}"` |

### Expression Types Comparison

| Expression | Syntax | Usage |
|------------|--------|-------|
| Variable | `${name}` | Access model attributes |
| Selection | `*{prop}` | Access object properties (within th:object) |
| Link | `@{/path}` | Create URLs |

---

## Next Steps

After understanding @ModelAttribute, proceed to:
- **Note 08**: Session Management - Learn session handling in Spring MVC

---

*This note is part of the Advanced Java - Spring Boot & Spring MVC series*
