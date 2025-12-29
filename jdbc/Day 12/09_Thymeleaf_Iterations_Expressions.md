# 📘 Thymeleaf Iterations & Expressions - Complete Guide

## Table of Contents
1. [Introduction to Thymeleaf Expressions](#introduction-to-thymeleaf-expressions)
2. [th:each for Iteration](#theach-for-iteration)
3. [th:text for Text Output](#thtext-for-text-output)
4. [th:action for Form Submission](#thaction-for-form-submission)
5. [Link Expressions @{...}](#link-expressions)
6. [Conditional Rendering](#conditional-rendering)
7. [Complete Code Examples](#complete-code-examples)
8. [Quick Reference](#quick-reference)

---

## Introduction to Thymeleaf Expressions

### Expression Types Overview

```
┌─────────────────────────────────────────────────────────────────┐
│              THYMELEAF EXPRESSION TYPES                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. Variable Expression: ${...}                                │
│      ├── Access model attributes                                │
│      ├── Example: ${book.name}                                  │
│      └── Uses: Anywhere in template                             │
│                                                                 │
│   2. Selection Expression: *{...}                               │
│      ├── Access properties of selected object                   │
│      ├── Example: *{name} (within th:object)                    │
│      └── Uses: Inside forms with th:object                      │
│                                                                 │
│   3. Link Expression: @{...}                                    │
│      ├── Create URLs (context-aware)                            │
│      ├── Example: @{/book}                                      │
│      └── Uses: th:href, th:action, th:src                       │
│                                                                 │
│   4. Message Expression: #{...}                                 │
│      ├── Internationalization messages                          │
│      ├── Example: #{welcome.message}                            │
│      └── Uses: Multi-language support                           │
│                                                                 │
│   5. Fragment Expression: ~{...}                                │
│      ├── Include template fragments                             │
│      ├── Example: ~{common :: header}                           │
│      └── Uses: Template composition                             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## th:each for Iteration

### What is th:each?

```
┌─────────────────────────────────────────────────────────────────┐
│                    th:each ITERATION                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   th:each iterates over collections like List, Set, Map, Array  │
│                                                                 │
│   Syntax:                                                       │
│   th:each="item : ${collection}"                                │
│             │         │                                         │
│             │         └── Collection from model                 │
│             └── Loop variable (current item)                    │
│                                                                 │
│   Example:                                                      │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ <tr th:each="book : ${booklist}">                       │   │
│   │     <td th:text="${book.bookName}"></td>                │   │
│   │     <td th:text="${book.price}"></td>                   │   │
│   │ </tr>                                                   │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   For each Book in booklist:                                    │
│   - book = current Book object                                  │
│   - ${book.bookName} = book's name                              │
│   - ${book.price} = book's price                                │
│   - New <tr> created for each book                              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### th:each with Status Variable

```
┌─────────────────────────────────────────────────────────────────┐
│              th:each WITH STATUS VARIABLE                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Syntax:                                                       │
│   th:each="item, status : ${collection}"                        │
│                   │                                             │
│                   └── Status variable                           │
│                                                                 │
│   Status properties:                                            │
│   ┌─────────────────┬───────────────────────────────────────┐   │
│   │ Property        │ Description                           │   │
│   ├─────────────────┼───────────────────────────────────────┤   │
│   │ status.index    │ Current iteration index (0-based)     │   │
│   │ status.count    │ Current iteration count (1-based)     │   │
│   │ status.size     │ Total number of elements              │   │
│   │ status.current  │ Current element                       │   │
│   │ status.first    │ Is this the first iteration?          │   │
│   │ status.last     │ Is this the last iteration?           │   │
│   │ status.even     │ Is current index even?                │   │
│   │ status.odd      │ Is current index odd?                 │   │
│   └─────────────────┴───────────────────────────────────────┘   │
│                                                                 │
│   Example with alternating row colors:                          │
│   <tr th:each="book, stat : ${booklist}"                        │
│       th:class="${stat.odd} ? 'odd-row' : 'even-row'">          │
│       <td th:text="${stat.count}"></td>                         │
│       <td th:text="${book.bookName}"></td>                      │
│   </tr>                                                         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### th:each Complete Example

```html
<!-- Controller passes: ModelAndView("Final", "mylist", booklist) -->

<table border="10">
    <thead>
        <tr>
            <th>#</th>
            <th>Book Name</th>
            <th>Price</th>
        </tr>
    </thead>
    <tbody>
        <!-- Iterate with status variable -->
        <tr th:each="ref, stat : ${mylist}"
            th:class="${stat.odd} ? 'highlight' : ''">
            
            <!-- Show row number (1-based) -->
            <td th:text="${stat.count}">1</td>
            
            <!-- Access bookName property of current Book -->
            <td th:text="${ref.bookName}">Book Name</td>
            
            <!-- Access price property of current Book -->
            <td th:text="${ref.price}">0</td>
        </tr>
    </tbody>
</table>
```

---

## th:text for Text Output

### What is th:text?

```
┌─────────────────────────────────────────────────────────────────┐
│                    th:text                                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   th:text replaces element content with expression value        │
│                                                                 │
│   Syntax:                                                       │
│   <element th:text="${expression}">Default text</element>       │
│                                                                 │
│   How it works:                                                 │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ Template:                                               │   │
│   │ <p th:text="${name}">Default Name</p>                   │   │
│   │                                                         │   │
│   │ If name = "John":                                       │   │
│   │ <p>John</p>                                             │   │
│   │                                                         │   │
│   │ If template opened in browser (no server):              │   │
│   │ <p>Default Name</p>  ← Natural templating!              │   │
│   │                                                         │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Important: th:text ESCAPES HTML (safe from XSS)               │
│   For unescaped HTML, use th:utext (use with caution!)          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### th:text Examples

```html
<!-- Simple variable -->
<p th:text="${message}">Default message</p>

<!-- Object property -->
<p th:text="${mybook.bookName}">Book Title</p>

<!-- Method call -->
<p th:text="${mybook.getPrice()}">0</p>

<!-- String concatenation -->
<p th:text="'Welcome, ' + ${userName} + '!'">Welcome, Guest!</p>

<!-- String concatenation (preferred syntax) -->
<p th:text="|Welcome, ${userName}!|">Welcome, Guest!</p>

<!-- Conditional (Elvis operator) -->
<p th:text="${userName} ?: 'Guest'">Guest</p>

<!-- Formatting -->
<p th:text="${#numbers.formatDecimal(price, 1, 2)}">0.00</p>
```

### th:text vs th:utext

```
┌─────────────────────────────────────────────────────────────────┐
│              th:text vs th:utext                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   th:text - ESCAPED (Safe)                                      │
│   ─────────────────────────                                     │
│   Input: "<b>Hello</b>"                                         │
│   Output: &lt;b&gt;Hello&lt;/b&gt;                              │
│   Display: <b>Hello</b> (as text)                               │
│                                                                 │
│   th:utext - UNESCAPED (Use with caution!)                      │
│   ────────────────────────────────────────                      │
│   Input: "<b>Hello</b>"                                         │
│   Output: <b>Hello</b>                                          │
│   Display: Hello (bold)                                         │
│                                                                 │
│   ⚠️ WARNING: th:utext can lead to XSS attacks if used with     │
│              user-provided content!                             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## th:action for Form Submission

### What is th:action?

```
┌─────────────────────────────────────────────────────────────────┐
│                    th:action                                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   th:action sets the form's action URL using link expression    │
│                                                                 │
│   Syntax:                                                       │
│   <form th:action="@{/path}" method="post">                     │
│                                                                 │
│   Benefits over plain action:                                   │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ Plain: <form action="/book">                            │   │
│   │   ↳ Hardcoded path                                      │   │
│   │   ↳ Breaks if context path changes                      │   │
│   │                                                         │   │
│   │ Thymeleaf: <form th:action="@{/book}">                  │   │
│   │   ↳ Context-aware URL                                   │   │
│   │   ↳ Automatically prepends context path if needed       │   │
│   │   ↳ Example: /myapp/book (if deployed at /myapp)        │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### th:action Example

```html
<form th:action="@{/book}" method="post">
    <!-- Form content -->
    <input type="text" name="bookName" />
    <input type="submit" value="Submit" />
</form>

<!-- With query parameter -->
<form th:action="@{/book(category=${cat})}" method="get">
    <!-- Generates: /book?category=fiction -->
</form>
```

---

## Link Expressions

### Link Expression Types

```
┌─────────────────────────────────────────────────────────────────┐
│              LINK EXPRESSIONS @{...}                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. ABSOLUTE URL                                               │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ <a th:href="@{https://example.com}">External</a>        │   │
│   │ Output: <a href="https://example.com">External</a>      │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   2. CONTEXT-RELATIVE URL                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ <a th:href="@{/book}">Books</a>                         │   │
│   │ Output: <a href="/book">Books</a>                       │   │
│   │ Or if context=/myapp: <a href="/myapp/book">Books</a>   │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   3. WITH QUERY PARAMETERS                                      │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ <a th:href="@{/book(id=${book.id})}">View</a>           │   │
│   │ Output: <a href="/book?id=123">View</a>                 │   │
│   │                                                         │   │
│   │ Multiple params:                                        │   │
│   │ <a th:href="@{/book(id=${id}, action='edit')}">Edit</a> │   │
│   │ Output: <a href="/book?id=123&action=edit">Edit</a>     │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   4. WITH PATH VARIABLES                                        │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ <a th:href="@{/book/{id}(id=${book.id})}">View</a>      │   │
│   │ Output: <a href="/book/123">View</a>                    │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Common Link Expression Uses

```html
<!-- Hyperlink -->
<a th:href="@{/products}">View Products</a>

<!-- Form action -->
<form th:action="@{/save}" method="post">

<!-- Image source -->
<img th:src="@{/images/logo.png}" alt="Logo">

<!-- CSS stylesheet -->
<link th:href="@{/css/style.css}" rel="stylesheet">

<!-- JavaScript -->
<script th:src="@{/js/app.js}"></script>
```

---

## Conditional Rendering

### th:if and th:unless

```
┌─────────────────────────────────────────────────────────────────┐
│              CONDITIONAL RENDERING                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   th:if - Render if condition is TRUE                          │
│   ──────────────────────────────────────                        │
│   <div th:if="${user != null}">                                 │
│       Welcome, <span th:text="${user.name}"></span>             │
│   </div>                                                        │
│                                                                 │
│   th:unless - Render if condition is FALSE                      │
│   ────────────────────────────────────────                      │
│   <div th:unless="${user != null}">                             │
│       Please login                                              │
│   </div>                                                        │
│                                                                 │
│   What qualifies as "true":                                     │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ • Boolean true                                          │   │
│   │ • Non-null object                                       │   │
│   │ • Non-zero number                                       │   │
│   │ • Non-empty string (not "false")                        │   │
│   │ • Non-empty collection                                  │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   What qualifies as "false":                                    │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ • Boolean false                                         │   │
│   │ • null                                                  │   │
│   │ • Zero (0)                                              │   │
│   │ • String "false"                                        │   │
│   │ • Empty collection                                      │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### th:switch and th:case

```html
<div th:switch="${user.role}">
    <p th:case="'admin'">Admin Dashboard</p>
    <p th:case="'user'">User Dashboard</p>
    <p th:case="*">Guest View</p>  <!-- Default case -->
</div>
```

---

## Complete Code Examples

### Controller

```java
package com.example.demo;

import java.util.ArrayList;
import java.util.List;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class BookNewController
{
    /**
     * GET /book - Display list of books
     */
    @GetMapping("book")
    public ModelAndView before()
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
        
        // Return view with list
        // "Final" = view name (Final.html)
        // "mylist" = attribute name
        // booklist = attribute value (List<Book>)
        return new ModelAndView("Final", "mylist", booklist);
    }
}
```

### View (Final.html)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="ISO-8859-1">
    <title>Book List</title>
    <style>
        .odd-row { background-color: #f2f2f2; }
        .even-row { background-color: #ffffff; }
    </style>
</head>
<body>

<h1>Book Inventory</h1>

<!-- Check if list is empty -->
<div th:if="${#lists.isEmpty(mylist)}">
    <p>No books available.</p>
</div>

<!-- Display table if list has items -->
<table border="10" th:unless="${#lists.isEmpty(mylist)}">
    <thead>
        <tr>
            <th>#</th>
            <th>Book Name</th>
            <th>Price</th>
            <th>Action</th>
        </tr>
    </thead>
    <tbody>
        <!-- th:each iterates over mylist -->
        <!-- ref = current Book object -->
        <!-- stat = iteration status -->
        <tr th:each="ref, stat : ${mylist}"
            th:class="${stat.odd} ? 'odd-row' : 'even-row'">
            
            <!-- Row number (1-based count) -->
            <td th:text="${stat.count}">1</td>
            
            <!-- Book name using th:text -->
            <td th:text="${ref.bookName}">Book Name</td>
            
            <!-- Price using th:text -->
            <td th:text="${ref.price}">0</td>
            
            <!-- Link with path variable -->
            <td>
                <a th:href="@{/book/{id}(id=${stat.index})}">View</a>
            </td>
        </tr>
    </tbody>
</table>

<!-- Total count -->
<p>Total books: <span th:text="${#lists.size(mylist)}">0</span></p>

</body>
</html>
```

### Expected Output

```
┌─────────────────────────────────────────────────────────────────┐
│                    BROWSER OUTPUT                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Book Inventory                                                │
│                                                                 │
│   ┌────┬─────────────────────────────┬───────┬─────────┐        │
│   │ #  │ Book Name                   │ Price │ Action  │        │
│   ├────┼─────────────────────────────┼───────┼─────────┤        │
│   │ 1  │ Java Black Book             │ 900   │ View    │        │
│   ├────┼─────────────────────────────┼───────┼─────────┤        │
│   │ 2  │ Understanding Pointers in C │ 400   │ View    │        │
│   ├────┼─────────────────────────────┼───────┼─────────┤        │
│   │ 3  │ The complete JavaEE Guide   │ 800   │ View    │        │
│   └────┴─────────────────────────────┴───────┴─────────┘        │
│                                                                 │
│   Total books: 3                                                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

### Thymeleaf Attributes

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `th:text` | Set text content | `th:text="${name}"` |
| `th:each` | Iterate collection | `th:each="item : ${list}"` |
| `th:if` | Conditional render | `th:if="${condition}"` |
| `th:unless` | Inverse conditional | `th:unless="${condition}"` |
| `th:action` | Form action URL | `th:action="@{/save}"` |
| `th:href` | Link URL | `th:href="@{/page}"` |
| `th:src` | Image/script source | `th:src="@{/img/logo.png}"` |
| `th:class` | CSS class | `th:class="${active} ? 'on' : 'off'"` |
| `th:style` | Inline style | `th:style="'color:' + ${color}"` |
| `th:object` | Form binding | `th:object="${book}"` |
| `th:field` | Input binding | `th:field="*{name}"` |

### Expression Syntax

| Type | Syntax | Example |
|------|--------|---------|
| Variable | `${...}` | `${book.name}` |
| Selection | `*{...}` | `*{name}` |
| Link | `@{...}` | `@{/books}` |
| Message | `#{...}` | `#{welcome}` |
| Fragment | `~{...}` | `~{header :: nav}` |

### Status Variable Properties

| Property | Description |
|----------|-------------|
| `index` | 0-based index |
| `count` | 1-based count |
| `size` | Total elements |
| `first` | Is first? |
| `last` | Is last? |
| `odd` | Is odd index? |
| `even` | Is even index? |

---

*This note is part of the Advanced Java - Spring Boot & Spring MVC series*
