# Hidden Fields - State Management Technique

## Table of Contents
1. [Introduction](#1-introduction)
2. [What are Hidden Fields?](#2-what-are-hidden-fields)
3. [How Hidden Fields Work](#3-how-hidden-fields-work)
4. [Complete Code Example](#4-complete-code-example)
5. [Execution Flow](#5-execution-flow)
6. [Advantages and Disadvantages](#6-advantages-and-disadvantages)
7. [Key Points to Remember](#7-key-points-to-remember)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

Hidden fields are the simplest technique to maintain state between HTTP requests. They work by embedding data in HTML forms that is not visible to the user but gets submitted along with the form.

---

## 2. What are Hidden Fields?

### HTML Syntax

```html
<input type="hidden" name="fieldName" value="fieldValue">
```

### Visual Representation

```
┌─────────────────────────────────────────────────────────────┐
│                   WHAT USER SEES                             │
├─────────────────────────────────────────────────────────────┤
│  Name: [John        ]                                       │
│  Age:  [25          ]                                       │
│                                                             │
│  [Submit Button]                                            │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│              WHAT ACTUALLY EXISTS IN HTML                    │
├─────────────────────────────────────────────────────────────┤
│  <input type="text" name="name" value="John">               │
│  <input type="text" name="age" value="25">                  │
│  <input type="hidden" name="userId" value="12345">  ← HIDDEN│
│  <input type="hidden" name="token" value="abc123">  ← HIDDEN│
│  <input type="submit" value="Submit">                       │
└─────────────────────────────────────────────────────────────┘
```

### Key Concept

- The hidden field is **present in the HTML** but **not displayed** on the screen
- When the form is submitted, hidden values are sent along with visible form data
- User cannot see the hidden values (unless they inspect the page source)

---

## 3. How Hidden Fields Work

### Step-by-Step Flow

```
Step 1: User fills form on Page 1
        ┌─────────────────────────────┐
        │ Name: [John]                │
        │ Age:  [25]                  │
        │ [Submit]                    │
        └─────────────────────────────┘
                    │
                    ▼
Step 2: Servlet 1 receives name and age
        Creates new form with hidden fields
        ┌─────────────────────────────┐
        │ <hidden name="a1" value="John">  │
        │ <hidden name="a2" value="25">    │
        │ [Done Button]                     │
        └─────────────────────────────┘
                    │
                    ▼
Step 3: User clicks Done
        Hidden values "John" and "25" sent to Servlet 2
                    │
                    ▼
Step 4: Servlet 2 retrieves and displays the values
        Output: "John 25"
```

---

## 4. Complete Code Example

### File 1: log.html (Initial Form)

```html
<!DOCTYPE html>
<html>
<head>
    <title>Hidden Fields Demo</title>              <!-- Line 1-4: Standard HTML structure -->
</head>
<body>
    <h2>Enter Your Details</h2>                    <!-- Line 7: Heading for the form -->
    
    <form action="HiddenServ1" method="post">      <!-- Line 9: Form submits to HiddenServ1 using POST -->
        
        Name: <input type="text" name="name"><br>  <!-- Line 11: Text input for name -->
        
        Age: <input type="text" name="age"><br>    <!-- Line 13: Text input for age -->
        
        <input type="submit" value="Submit">       <!-- Line 15: Submit button -->
        
    </form>
</body>
</html>
```

#### Line-by-Line Explanation:

| Line | Code | Explanation |
|------|------|-------------|
| 9 | `form action="HiddenServ1"` | Form data will be sent to HiddenServ1 servlet |
| 9 | `method="post"` | Data sent via POST method (not visible in URL) |
| 11 | `name="name"` | Parameter name to retrieve in servlet |
| 13 | `name="age"` | Parameter name to retrieve in servlet |

---

### File 2: HiddenServ1.java (First Servlet)

```java
import java.io.*;                                    // Line 1: Import for PrintWriter & IOException
import javax.servlet.*;                              // Line 2: Import for ServletException
import javax.servlet.http.*;                         // Line 3: Import for HttpServlet classes

public class HiddenServ1 extends HttpServlet         // Line 5: Class extends HttpServlet
{
    public void doPost(HttpServletRequest request,   // Line 7: doPost handles POST requests
                       HttpServletResponse response) 
                       throws ServletException, IOException
    {
        // Step 1: Get PrintWriter to send response to browser
        PrintWriter pw = response.getWriter();       // Line 12: Creates output stream to browser
        
        // Step 2: Retrieve form parameters from request
        String str1 = request.getParameter("name");  // Line 15: Gets "name" value from form
        String str2 = request.getParameter("age");   // Line 16: Gets "age" value from form
        
        // Step 3: Generate HTML form with HIDDEN fields
        pw.println("<html><body>");                  // Line 19: Start HTML
        pw.println("<form action=HiddenServ2 method=post>");  // Line 20: Form to HiddenServ2
        
        // Step 4: Create hidden fields with the values from Step 2
        pw.println("<input type=hidden name=a1 value=" + str1 + ">");  // Line 23: Hidden field with name
        pw.println("<input type=hidden name=a2 value=" + str2 + ">");  // Line 24: Hidden field with age
        
        // Step 5: Add submit button
        pw.println("<input type=submit value=done>");  // Line 27: Submit button shows "done"
        
        pw.println("</form></body></html>");         // Line 29: Close HTML tags
    }
}
```

#### Line-by-Line Explanation:

| Line | Code | What It Does |
|------|------|--------------|
| 12 | `response.getWriter()` | Gets output stream to write HTML to browser |
| 15 | `request.getParameter("name")` | Retrieves the "name" value entered in log.html |
| 16 | `request.getParameter("age")` | Retrieves the "age" value entered in log.html |
| 20 | `action=HiddenServ2` | Next form submits to HiddenServ2 servlet |
| 23 | `type=hidden name=a1` | Creates hidden field named "a1" containing the name |
| 24 | `type=hidden name=a2` | Creates hidden field named "a2" containing the age |
| 27 | `value=done` | Button text that user sees |

---

### File 3: HiddenServ2.java (Second Servlet)

```java
import java.io.*;                                    // Line 1: Import for PrintWriter & IOException
import javax.servlet.*;                              // Line 2: Import for ServletException
import javax.servlet.http.*;                         // Line 3: Import for HttpServlet classes

public class HiddenServ2 extends HttpServlet         // Line 5: Class extends HttpServlet
{
    public void doPost(HttpServletRequest request,   // Line 7: doPost handles POST requests
                       HttpServletResponse response) 
                       throws ServletException, IOException
    {
        // Step 1: Get PrintWriter to send response
        PrintWriter pw = response.getWriter();       // Line 12: Creates output stream
        
        // Step 2: Retrieve values from hidden fields
        // Note: We use "a1" and "a2" because that's what HiddenServ1 named them
        pw.println(request.getParameter("a1"));      // Line 16: Prints the name (was hidden)
        pw.println(request.getParameter("a2"));      // Line 17: Prints the age (was hidden)
    }
}
```

#### Line-by-Line Explanation:

| Line | Code | What It Does |
|------|------|--------------|
| 16 | `getParameter("a1")` | Gets value of hidden field "a1" (contains name) |
| 17 | `getParameter("a2")` | Gets value of hidden field "a2" (contains age) |

---

## 5. Execution Flow

### Complete Flow Diagram

```
┌────────────────────────────────────────────────────────────────────┐
│                           STEP 1                                   │
│  User opens log.html in browser                                    │
│  ┌──────────────────────────────┐                                 │
│  │ Name: [John            ]     │                                 │
│  │ Age:  [25              ]     │                                 │
│  │ [Submit]                     │                                 │
│  └──────────────────────────────┘                                 │
└────────────────────────────────────────────────────────────────────┘
                                │
                                │ POST request with name=John, age=25
                                ▼
┌────────────────────────────────────────────────────────────────────┐
│                           STEP 2                                   │
│  HiddenServ1 receives request                                      │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │ String str1 = request.getParameter("name"); // "John"        │ │
│  │ String str2 = request.getParameter("age");  // "25"          │ │
│  └──────────────────────────────────────────────────────────────┘ │
│                                                                    │
│  HiddenServ1 generates new HTML form:                              │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │ <input type=hidden name=a1 value=John>  ← User can't see     │ │
│  │ <input type=hidden name=a2 value=25>    ← User can't see     │ │
│  │ [done]                                   ← User sees button  │ │
│  └──────────────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────────────┘
                                │
                                │ User clicks "done" button
                                │ POST request with a1=John, a2=25
                                ▼
┌────────────────────────────────────────────────────────────────────┐
│                           STEP 3                                   │
│  HiddenServ2 receives request                                      │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │ request.getParameter("a1") → returns "John"                  │ │
│  │ request.getParameter("a2") → returns "25"                    │ │
│  └──────────────────────────────────────────────────────────────┘ │
│                                                                    │
│  Browser displays: John 25                                         │
└────────────────────────────────────────────────────────────────────┘
```

### Data Flow Summary

```
log.html         →    HiddenServ1         →    HiddenServ2
   │                       │                        │
   │ sends:               │ receives:              │ receives:
   │ name=John            │ name=John              │ a1=John
   │ age=25               │ age=25                 │ a2=25
   │                       │                        │
   │                       │ sends:                │ displays:
   │                       │ hidden a1=John        │ John 25
   │                       │ hidden a2=25          │
```

---

## 6. Advantages and Disadvantages

### Advantages ✓

| Advantage | Explanation |
|-----------|-------------|
| Simple | Very easy to implement, just add HTML input tags |
| No Server Memory | Data stored in HTML, not on server |
| No Cookie Required | Works even if cookies are disabled |
| Stateless Server | Server doesn't need to track anything |

### Disadvantages ✗

| Disadvantage | Explanation |
|--------------|-------------|
| Only POST Forms | Only works when submitting forms, not with links |
| Security Risk | User can view/modify using browser developer tools |
| No Java Objects | Can only store text values, not objects |
| Visible in Source | Anyone can "View Page Source" and see the values |
| Limited Scope | Data only travels one hop at a time |

### Security Concern Example

```
What user sees:          What's in the source code:
┌────────────────┐       ┌───────────────────────────────────────┐
│ [Done Button]  │       │ <input type="hidden"                  │
│                │       │        name="price"                   │
│                │       │        value="100">   ← SECURITY RISK │
└────────────────┘       └───────────────────────────────────────┘

Malicious user can:
1. Right-click → Inspect Element
2. Change value="100" to value="1"
3. Submit form
4. Pay $1 instead of $100!
```

---

## 7. Key Points to Remember

1. **Hidden ≠ Secure**: Hidden just means "not displayed", data is still in HTML source

2. **POST method required**: Hidden fields work with form submissions only

3. **Name-Value pairs**: Always specify name attribute to retrieve value in servlet

4. **Data conversion**: All hidden field values are strings, convert if needed

5. **One-way transfer**: Data moves from one page to next, not bidirectional

6. **No special imports**: Hidden fields are pure HTML, no Java libraries needed

7. **Limited use case**: Best for passing small, non-sensitive data between pages

8. **Cannot store objects**: Unlike session, cannot store Java objects

---

## 8. Interview Questions

### Basic Level

**Q1: What is a hidden field in HTML?**
> A hidden field is an HTML input element with `type="hidden"`. It stores data that is submitted with the form but is not visible to the user on the webpage.

**Q2: How do you create a hidden field?**
> Using the syntax: `<input type="hidden" name="fieldName" value="fieldValue">`

**Q3: How do you read a hidden field value in a servlet?**
> Using `request.getParameter("fieldName")` - the same method used for visible form fields.

### Intermediate Level

**Q4: Why are hidden fields considered insecure?**
> Because:
> 1. Users can view the values using "View Source" or Developer Tools
> 2. Users can modify the values before form submission
> 3. Values travel in the HTTP request body (can be intercepted)

**Q5: What is the difference between hidden fields and session?**
| Hidden Fields | Session |
|---------------|---------|
| Client-side storage | Server-side storage |
| Only stores strings | Can store any Java object |
| Visible in HTML source | Completely hidden |
| Works only with forms | Works with any request |
| No server memory needed | Uses server memory |

**Q6: When would you use hidden fields instead of session?**
> Use hidden fields when:
> - You need to pass data only between 2 pages
> - The data is not sensitive
> - You want to minimize server memory usage
> - The workflow is purely form-based

### Advanced Level

**Q7: Can hidden fields work with GET method?**
> Yes, hidden fields work with GET as well, but the values will appear in the URL query string, making them even more visible. Example: `servlet?a1=John&a2=25`

**Q8: How would you make hidden fields more secure?**
> 1. Encrypt the values before putting them in hidden fields
> 2. Use HMAC to detect tampering
> 3. Store sensitive data on server (session) and use only a token in hidden field
> 4. Validate all hidden field values on the server side

**Q9: What happens if a user bookmarks a page with hidden fields?**
> The bookmark saves the URL only, not the form data. When the user opens the bookmark, the hidden field values are lost because they were part of the POST request body, not the URL.

---

## Next Topic: [Cookies Fundamentals →](./03_Cookies_Fundamentals.md)
