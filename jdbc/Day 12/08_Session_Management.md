# 📘 Session Management in Spring MVC - Complete Guide

## Table of Contents
1. [Introduction to Sessions](#introduction-to-sessions)
2. [HttpSession in Spring MVC](#httpsession-in-spring-mvc)
3. [Session Methods](#session-methods)
4. [Setting Session Attributes](#setting-session-attributes)
5. [Accessing Session in Controller](#accessing-session-in-controller)
6. [Accessing Session in Thymeleaf](#accessing-session-in-thymeleaf)
7. [Session vs Request Scope](#session-vs-request-scope)
8. [Session Lifecycle](#session-lifecycle)
9. [Complete Code Examples](#complete-code-examples)
10. [Quick Reference](#quick-reference)

---

## Introduction to Sessions

### What is HTTP Session?

```
┌─────────────────────────────────────────────────────────────────┐
│                    HTTP SESSION CONCEPT                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Problem: HTTP is STATELESS                                    │
│   ───────────────────────────                                   │
│   Each request is independent - server doesn't remember         │
│   previous requests from the same user.                         │
│                                                                 │
│   Request 1 ──────▶ Server ──────▶ Response (Server forgets)    │
│   Request 2 ──────▶ Server ──────▶ Response (Who are you?)      │
│                                                                 │
│   Solution: Session                                             │
│   ─────────────────                                             │
│   Session creates a temporary storage on server that persists   │
│   across multiple requests from the same user.                  │
│                                                                 │
│   Request 1 ──────▶ Server ──────▶ Create Session (ID: ABC123)  │
│   Request 2 (ABC123) ▶ Server ──▶ Found session! Welcome back!  │
│   Request 3 (ABC123) ▶ Server ──▶ Session data still available  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Session Use Cases

```
┌─────────────────────────────────────────────────────────────────┐
│              SESSION USE CASES                                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ✓ User Authentication                                         │
│     Store logged-in user information                            │
│                                                                 │
│   ✓ Shopping Cart                                               │
│     Maintain cart items across pages                            │
│                                                                 │
│   ✓ User Preferences                                            │
│     Remember language, theme, settings                          │
│                                                                 │
│   ✓ Multi-Step Forms (Wizards)                                  │
│     Keep data from step 1 when on step 3                        │
│                                                                 │
│   ✓ Flash Messages                                              │
│     Show "Record saved!" after redirect                         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## HttpSession in Spring MVC

### What is HttpSession?

```
┌─────────────────────────────────────────────────────────────────┐
│                    HttpSession                                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   HttpSession is a Java interface that provides a way to        │
│   identify a user across multiple page requests.                │
│                                                                 │
│   Package: jakarta.servlet.http.HttpSession                     │
│           (or javax.servlet.http.HttpSession in older versions) │
│                                                                 │
│   Session Storage:                                              │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │                    SERVER                               │   │
│   │   ┌─────────────────────────────────────────────────┐   │   │
│   │   │ Session ID: ABC123                              │   │   │
│   │   │ ├── "user" → UserObject                         │   │   │
│   │   │ ├── "cart" → ShoppingCartObject                 │   │   │
│   │   │ └── "val" → 2000                                │   │   │
│   │   └─────────────────────────────────────────────────┘   │   │
│   │                                                         │   │
│   │   ┌─────────────────────────────────────────────────┐   │   │
│   │   │ Session ID: XYZ789                              │   │   │
│   │   │ ├── "user" → AnotherUser                        │   │   │
│   │   │ └── "cart" → EmptyCart                          │   │   │
│   │   └─────────────────────────────────────────────────┘   │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Each user has their own isolated session!                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Getting HttpSession in Controller

```java
// Method 1: Inject as method parameter (Recommended)
@PostMapping("book")
public String submit(HttpSession session) {
    session.setAttribute("key", value);
    return "success";
}

// Method 2: From HttpServletRequest
@PostMapping("book")
public String submit(HttpServletRequest request) {
    HttpSession session = request.getSession();
    // getSession() creates new session if none exists
    // getSession(false) returns null if no session exists
    return "success";
}

// Method 3: Using @SessionAttribute (for reading)
@GetMapping("profile")
public String profile(@SessionAttribute("user") User user) {
    // Throws exception if "user" not in session
    return "profile";
}
```

---

## Session Methods

### Common HttpSession Methods

```
┌─────────────────────────────────────────────────────────────────┐
│              HttpSession METHODS                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   STORING DATA                                                  │
│   ────────────                                                  │
│   session.setAttribute("name", value)                           │
│       └── Store any object with a String key                    │
│                                                                 │
│   RETRIEVING DATA                                               │
│   ───────────────                                               │
│   Object value = session.getAttribute("name")                   │
│       └── Returns Object, needs casting                         │
│                                                                 │
│   REMOVING DATA                                                 │
│   ─────────────                                                 │
│   session.removeAttribute("name")                               │
│       └── Removes specific attribute                            │
│                                                                 │
│   DESTROYING SESSION                                            │
│   ───────────────────                                           │
│   session.invalidate()                                          │
│       └── Destroys entire session (used for logout)             │
│                                                                 │
│   SESSION INFO                                                  │
│   ────────────                                                  │
│   session.getId()                                               │
│       └── Returns session ID (e.g., "ABCD1234567890")           │
│                                                                 │
│   session.getCreationTime()                                     │
│       └── Returns when session was created (milliseconds)       │
│                                                                 │
│   session.getLastAccessedTime()                                 │
│       └── Returns last access time (for timeout calculation)    │
│                                                                 │
│   session.setMaxInactiveInterval(seconds)                       │
│       └── Set timeout (e.g., 1800 = 30 minutes)                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Methods Table

| Method | Return Type | Description |
|--------|-------------|-------------|
| `setAttribute(name, value)` | void | Store attribute |
| `getAttribute(name)` | Object | Retrieve attribute |
| `removeAttribute(name)` | void | Remove attribute |
| `invalidate()` | void | Destroy session |
| `getId()` | String | Get session ID |
| `isNew()` | boolean | Check if new session |
| `getCreationTime()` | long | Creation timestamp |
| `setMaxInactiveInterval(sec)` | void | Set timeout |

---

## Setting Session Attributes

### Example with Line-by-Line Explanation

```java
/**
 * Controller demonstrating session usage
 */
package com.example.demo;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.servlet.ModelAndView;
import jakarta.servlet.http.HttpSession;           // Import HttpSession

@Controller
public class BookNewController
{
    @GetMapping("book")
    public ModelAndView before()
    {
        Book defaultBook = new Book();
        return new ModelAndView("bookNew", "mybook", defaultBook);
    }
    
    /**
     * POST /book - Process form and store in session
     * 
     * HttpSession is automatically injected by Spring
     */
    @PostMapping("book")
    public String afterSubmit(
        @ModelAttribute("mb") Book book,       // Binds form → Book
        HttpSession session)                   // Spring injects session
    {
        // ┌────────────────────────────────────────────────────────┐
        // │ session.setAttribute("val", 2000)                      │
        // │                                                        │
        // │ What this does:                                        │
        // │ 1. Creates key "val" in session storage                │
        // │ 2. Stores value 2000 (auto-boxed to Integer)           │
        // │ 3. Available across ALL requests from this user        │
        // │ 4. Persists until session expires or invalidate()      │
        // └────────────────────────────────────────────────────────┘
        session.setAttribute("val", 2000);
        
        // Can store complex objects too
        session.setAttribute("currentBook", book);
        session.setAttribute("userName", "John Doe");
        
        return "success";
    }
    
    /**
     * Example: Reading from session
     */
    @GetMapping("profile")
    public String showProfile(HttpSession session, Model model)
    {
        // Retrieve from session (returns Object, needs casting)
        Integer val = (Integer) session.getAttribute("val");
        Book savedBook = (Book) session.getAttribute("currentBook");
        
        if (val != null) {
            model.addAttribute("sessionValue", val);
        }
        
        return "profile";
    }
    
    /**
     * Example: Logout - Destroy session
     */
    @GetMapping("logout")
    public String logout(HttpSession session)
    {
        session.invalidate();  // Destroys entire session
        return "redirect:/login";
    }
}
```

---

## Accessing Session in Controller

### Different Ways to Access Session

```
┌─────────────────────────────────────────────────────────────────┐
│         WAYS TO ACCESS SESSION IN CONTROLLER                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. HttpSession as Parameter (Most Common)                     │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @PostMapping("save")                                    │   │
│   │ public String save(HttpSession session) {               │   │
│   │     session.setAttribute("data", value);                │   │
│   │     Object data = session.getAttribute("data");         │   │
│   │ }                                                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   2. @SessionAttribute (For Reading Specific Attribute)         │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @GetMapping("profile")                                  │   │
│   │ public String profile(                                  │   │
│   │     @SessionAttribute("user") User user) {              │   │
│   │     // "user" must exist in session or exception!       │   │
│   │ }                                                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   3. @SessionAttributes on Class (Auto-store model attrs)       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @Controller                                             │   │
│   │ @SessionAttributes("shoppingCart")                      │   │
│   │ public class CartController {                           │   │
│   │     // "shoppingCart" model attr auto-stored in session │   │
│   │ }                                                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Accessing Session in Thymeleaf

### Syntax: ${session.attributeName}

```
┌─────────────────────────────────────────────────────────────────┐
│         ACCESSING SESSION IN THYMELEAF                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Thymeleaf provides direct access to session via:              │
│   ${session.attributeName}                                      │
│                                                                 │
│   Examples:                                                     │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ <!-- Simple value -->                                   │   │
│   │ <p th:text="${session.val}">0</p>                       │   │
│   │                                                         │   │
│   │ <!-- Object property -->                                │   │
│   │ <p th:text="${session.user.name}">Guest</p>             │   │
│   │                                                         │   │
│   │ <!-- Conditional check -->                              │   │
│   │ <div th:if="${session.user != null}">                   │   │
│   │     Welcome, <span th:text="${session.user.name}"/>     │   │
│   │ </div>                                                  │   │
│   │                                                         │   │
│   │ <div th:if="${session.user == null}">                   │   │
│   │     Please <a href="/login">login</a>                   │   │
│   │ </div>                                                  │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Complete Thymeleaf Session Example

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="ISO-8859-1">
    <title>Success Page</title>
</head>
<body>

<!-- Display book from model (request scope via @ModelAttribute) -->
<h2>Book Details (from @ModelAttribute)</h2>
<p>Book Name: <span th:text="${mb.bookName}">Name here</span></p>
<p>Price: <span th:text="${mb.price}">Price here</span></p>

<hr/>

<!-- Display value from session -->
<h2>Session Data</h2>
<p>Session value is: <span th:text="${session.val}">0</span></p>

<!-- Conditional: Check if session attribute exists -->
<div th:if="${session.val != null}">
    <p>Session contains 'val' with value: 
       <strong th:text="${session.val}"></strong>
    </p>
</div>

<!-- Access complex object in session -->
<div th:if="${session.currentBook != null}">
    <h3>Book from Session</h3>
    <p th:text="${session.currentBook.bookName}">Book Name</p>
    <p th:text="${session.currentBook.price}">Price</p>
</div>

</body>
</html>
```

---

## Session vs Request Scope

### Comparison Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│           SESSION SCOPE vs REQUEST SCOPE                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   REQUEST SCOPE                   SESSION SCOPE                 │
│   ═════════════                   ═════════════                 │
│                                                                 │
│   Lifetime: Single request        Lifetime: Multiple requests   │
│   ─────────────────────────       ─────────────────────────     │
│                                                                 │
│   Request 1 ──▶ Data available    Request 1 ──▶ Store data      │
│              ▼                                 │                │
│           Response                             ▼                │
│              ▼                    Request 2 ──▶ Data available  │
│         Data GONE!                             │                │
│                                                ▼                │
│                                   Request 3 ──▶ Data available  │
│                                                                 │
│   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ │
│                                                                 │
│   How to Store:                   How to Store:                 │
│   ─────────────                   ─────────────                 │
│   model.addAttribute("key", val)  session.setAttribute("key",v) │
│                                                                 │
│   How to Access (Thymeleaf):      How to Access (Thymeleaf):    │
│   ─────────────────────────       ─────────────────────────     │
│   ${key}                          ${session.key}                │
│                                                                 │
│   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ │
│                                                                 │
│   Use For:                        Use For:                      │
│   ────────                        ────────                      │
│   • Form data display             • User authentication         │
│   • Search results                • Shopping cart               │
│   • Error messages                • User preferences            │
│   • Page-specific data            • Multi-page wizard data      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Code Comparison

```java
// REQUEST SCOPE - Data for single request
@PostMapping("book")
public String submit(@ModelAttribute("mb") Book book, Model model) {
    model.addAttribute("message", "Book saved!");
    // "message" available only in this response
    return "success";
}

// SESSION SCOPE - Data persists across requests
@PostMapping("book")
public String submit(@ModelAttribute("mb") Book book, HttpSession session) {
    session.setAttribute("lastBook", book);
    // "lastBook" available in future requests
    return "success";
}
```

---

## Session Lifecycle

### Session Creation and Expiration

```
┌─────────────────────────────────────────────────────────────────┐
│              SESSION LIFECYCLE                                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. CREATION                                                   │
│   ───────────                                                   │
│   Session created when:                                         │
│   • First access (getSession() or getSession(true))             │
│   • Spring auto-creates if needed                               │
│                                                                 │
│   2. ACTIVE PERIOD                                              │
│   ────────────────                                              │
│   Session remains active while:                                 │
│   • User makes requests within timeout period                   │
│   • Default timeout: 30 minutes (configurable)                  │
│                                                                 │
│   Timeline:                                                     │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ Request 1 ────▶ Timer resets                            │   │
│   │    10 min                                               │   │
│   │ Request 2 ────▶ Timer resets                            │   │
│   │    5 min                                                │   │
│   │ Request 3 ────▶ Timer resets                            │   │
│   │    ...                                                  │   │
│   │    35 min (no request) ──▶ Session EXPIRED              │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   3. TERMINATION                                                │
│   ──────────────                                                │
│   Session ends when:                                            │
│   • session.invalidate() called                                 │
│   • Timeout exceeded (no activity)                              │
│   • Server restart (unless persistent sessions enabled)         │
│   • Browser closed (session cookie deleted)                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Configuring Session Timeout

```properties
# application.properties

# Set session timeout to 30 minutes
server.servlet.session.timeout=30m

# Set session timeout to 1800 seconds (same as 30m)
server.servlet.session.timeout=1800s
```

```java
// Programmatically set timeout for specific session
@PostMapping("login")
public String login(HttpSession session) {
    session.setMaxInactiveInterval(3600); // 1 hour in seconds
    return "dashboard";
}
```

---

## Complete Code Examples

### BookNewController.java

```java
package com.example.demo;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.servlet.ModelAndView;
import jakarta.servlet.http.HttpSession;

@Controller
public class BookNewController
{
    // Home page
    @GetMapping("/")
    public String home()
    {
        return "Home";
    }
    
    // Display form
    @GetMapping("book")
    public ModelAndView before()
    {
        Book defaultBook = new Book();
        return new ModelAndView("bookNew", "mybook", defaultBook);
    }
    
    // Process form - demonstrates session usage
    @PostMapping("book")
    public String afterSubmit(
        @ModelAttribute("mb") Book book,
        HttpSession session)
    {
        // Store in session - available across requests
        session.setAttribute("val", 2000);
        session.setAttribute("savedBook", book);
        
        // "mb" is already in request scope via @ModelAttribute
        return "success";
    }
    
    // Show profile - access session data
    @GetMapping("profile")
    public String showProfile(HttpSession session, Model model)
    {
        // Read from session
        Integer val = (Integer) session.getAttribute("val");
        Book book = (Book) session.getAttribute("savedBook");
        
        if (val != null) {
            model.addAttribute("sessionVal", val);
        }
        if (book != null) {
            model.addAttribute("sessionBook", book);
        }
        
        return "profile";
    }
    
    // Logout - clear session
    @GetMapping("logout")
    public String logout(HttpSession session)
    {
        session.invalidate();
        return "redirect:/";
    }
}
```

### success.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="ISO-8859-1">
    <title>Success</title>
</head>
<body>

<h1>Book Saved Successfully!</h1>

<!-- Request Scope: From @ModelAttribute("mb") -->
<h2>From Request Scope (@ModelAttribute)</h2>
<p>Book Name: <span th:text="${mb.bookName}">Name</span></p>
<p>Price: <span th:text="${mb.price}">0</span></p>

<hr/>

<!-- Session Scope: From session.setAttribute() -->
<h2>From Session Scope</h2>
<p>Session value: <span th:text="${session.val}">0</span></p>

<div th:if="${session.savedBook != null}">
    <p>Session Book: <span th:text="${session.savedBook.bookName}"></span></p>
</div>

<hr/>

<a href="profile">View Profile (tests session persistence)</a>
<br/>
<a href="logout">Logout (destroys session)</a>

</body>
</html>
```

---

## Quick Reference

### Session Operations

| Operation | Code |
|-----------|------|
| Store data | `session.setAttribute("key", value)` |
| Retrieve data | `session.getAttribute("key")` |
| Remove data | `session.removeAttribute("key")` |
| Destroy session | `session.invalidate()` |
| Get session ID | `session.getId()` |
| Set timeout | `session.setMaxInactiveInterval(seconds)` |

### Accessing in Thymeleaf

| Access | Syntax |
|--------|--------|
| Session attribute | `${session.key}` |
| Check existence | `th:if="${session.key != null}"` |
| Object property | `${session.user.name}` |

### Configuration

```properties
# application.properties
server.servlet.session.timeout=30m
```

---

## Next Steps

After understanding Session Management, proceed to:
- **Note 09**: Thymeleaf Iterations & Expressions - Learn th:each, th:text, and more

---

*This note is part of the Advanced Java - Spring Boot & Spring MVC series*
