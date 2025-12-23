# Servlet Filters - Introduction and Implementation

## Table of Contents
1. [Introduction](#1-introduction)
2. [What are Filters?](#2-what-are-filters)
3. [Filter Lifecycle](#3-filter-lifecycle)
4. [Types of Filters](#4-types-of-filters)
5. [Filter Interface Methods](#5-filter-interface-methods)
6. [Complete Code Example](#6-complete-code-example)
7. [Filter Chain](#7-filter-chain)
8. [web.xml Configuration](#8-webxml-configuration)
9. [Key Points to Remember](#9-key-points-to-remember)
10. [Interview Questions](#10-interview-questions)

---

## 1. Introduction

Filters are one of the most powerful features in Java EE. They allow you to intercept requests and responses, performing tasks like logging, authentication, compression, and encryption without modifying your servlets.

---

## 2. What are Filters?

### Definition

Filters are **pluggable components** that can intercept HTTP requests and responses. They can be easily plugged in or removed from the application by modifying the Deployment Descriptor (web.xml) without changing servlet code.

### Visual Representation

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         FILTER CHAIN FLOW                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   CLIENT                                                        SERVER      │
│   ┌───────┐                                                                 │
│   │Browser│                                                                 │
│   └───┬───┘                                                                 │
│       │                                                                     │
│       │ Request                                                             │
│       ▼                                                                     │
│   ┌─────────────────────────────────────────────────────────────────┐      │
│   │                      FILTER CHAIN                                │      │
│   │                                                                  │      │
│   │  ┌───────────┐     ┌───────────┐     ┌───────────┐             │      │
│   │  │ Filter 1  │────►│ Filter 2  │────►│  Servlet  │             │      │
│   │  │(Logging)  │     │(Auth)     │     │           │             │      │
│   │  │           │◄────│           │◄────│           │             │      │
│   │  └───────────┘     └───────────┘     └───────────┘             │      │
│   │                                                                  │      │
│   │  REQUEST FILTERS ──────────────────► SERVLET ◄── RESPONSE FILTERS│      │
│   │                                                                  │      │
│   └─────────────────────────────────────────────────────────────────┘      │
│       │                                                                     │
│       │ Response                                                            │
│       ▼                                                                     │
│   ┌───────┐                                                                 │
│   │Browser│                                                                 │
│   └───────┘                                                                 │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Key Advantages

| Advantage | Explanation |
|-----------|-------------|
| Pluggable | Add/remove via web.xml without code changes |
| Reusable | Same filter can apply to multiple servlets |
| Maintainable | Separate cross-cutting concerns |
| Chainable | Multiple filters can work together |

---

## 3. Filter Lifecycle

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         FILTER LIFECYCLE                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. INSTANTIATION (Container creates Filter object)                         │
│           │                                                                 │
│           ▼                                                                 │
│  2. init(FilterConfig config)  ─────────────────────────┐                  │
│     • Called ONCE when filter is loaded                  │                  │
│     • Initialize resources                               │ STARTUP          │
│     • Read filter configuration                         ─┘                  │
│           │                                                                 │
│           ▼                                                                 │
│  3. doFilter(request, response, chain)  ────────────────┐                  │
│     • Called for EVERY matching request                  │                  │
│     • Pre-processing (before servlet)                    │ RUNTIME          │
│     • chain.doFilter() invokes next in chain            │                  │
│     • Post-processing (after servlet)                   ─┘                  │
│           │                                                                 │
│           ▼                                                                 │
│  4. destroy()  ─────────────────────────────────────────┐                  │
│     • Called ONCE when filter is unloaded               │ SHUTDOWN          │
│     • Release resources                                 ─┘                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 4. Types of Filters

### Request Filters

Called **before** the request reaches the servlet.

**Common Uses:**
- Logging request details
- Authentication/Authorization
- Request modification
- Input validation

### Response Filters

Called **after** servlet execution but **before** response goes to client.

**Common Uses:**
- Compression (GZIP)
- Encryption
- Response modification
- Adding common headers

### Visual: Request vs Response Filter

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│                     REQUEST FILTER                                          │
│   ┌─────────────────────────────────────────────────────────────┐          │
│   │  Code BEFORE chain.doFilter()                                │          │
│   │  • Runs BEFORE servlet executes                              │          │
│   │  • Log request, validate input, check authentication         │          │
│   └─────────────────────────────────────────────────────────────┘          │
│                              │                                              │
│                              ▼                                              │
│                     chain.doFilter(req, res);                               │
│                              │                                              │
│                              ▼                                              │
│                     RESPONSE FILTER                                         │
│   ┌─────────────────────────────────────────────────────────────┐          │
│   │  Code AFTER chain.doFilter()                                 │          │
│   │  • Runs AFTER servlet executes                               │          │
│   │  • Compress response, add headers, log response              │          │
│   └─────────────────────────────────────────────────────────────┘          │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Filter Interface Methods

### The Filter Interface

```java
public interface Filter {
    
    // Called once when filter is initialized
    void init(FilterConfig filterConfig) throws ServletException;
    
    // Called for every matching request
    void doFilter(ServletRequest request, 
                  ServletResponse response, 
                  FilterChain chain) 
                  throws IOException, ServletException;
    
    // Called once when filter is destroyed
    void destroy();
}
```

### Method Details

| Method | When Called | Purpose |
|--------|-------------|---------|
| `init(FilterConfig)` | Once at startup | Initialize filter, read config |
| `doFilter(req, res, chain)` | Every request | Main logic, call chain.doFilter() |
| `destroy()` | Once at shutdown | Cleanup resources |

---

## 6. Complete Code Example

### login.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>Login Page</title>
</head>
<body>
    <h2>Login Form</h2>
    <form action="LoginServ" method="post">
        Name: <input type="text" name="nm"><br><br>
        Age: <input type="text" name="ag"><br><br>
        <input type="submit" value="Login">
    </form>
</body>
</html>
```

### FirstFilter.java - Request Filter

```java
package mypack;                                      // Line 1: Package declaration

import javax.servlet.*;                              // Line 2: Servlet imports
import javax.servlet.http.*;                         // Line 3: HTTP imports
import java.io.*;                                    // Line 4: IO imports

public class FirstFilter implements Filter          // Line 6: Implements Filter interface
{
    // Called ONCE when filter is first loaded
    public void init(FilterConfig conf)              // Line 9: Initialize filter
    {
        // Can read filter init parameters here
        // String param = conf.getInitParameter("paramName");
    }
    
    // Called for EVERY request matching this filter
    public void doFilter(ServletRequest req,         // Line 15: Main filter method
                         ServletResponse res, 
                         FilterChain chain)
    {
        try
        {
            // ═══════════════════════════════════════
            // REQUEST FILTER LOGIC (BEFORE SERVLET)
            // ═══════════════════════════════════════
            
            // Step 1: Get request parameters
            String name = req.getParameter("nm");    // Line 26: Get name parameter
            String age = req.getParameter("ag");     // Line 27: Get age parameter
            
            // Step 2: Log to file (example: audit logging)
            FileWriter fw = new FileWriter("d:\\loginfile.txt");  // Line 30: Open file
            fw.write(name);                          // Line 31: Write name
            fw.write(age);                           // Line 32: Write age
            fw.close();                              // Line 33: Close file
            
            // Step 3: Add message to response
            PrintWriter pw = res.getWriter();        // Line 36: Get writer
            pw.println("<br>Data saved in a file<br>"); // Line 37: Inform user
            
            // ═══════════════════════════════════════
            // PASS CONTROL TO NEXT FILTER OR SERVLET
            // ═══════════════════════════════════════
            chain.doFilter(req, res);                // Line 42: Continue chain!
            
            // ═══════════════════════════════════════
            // RESPONSE FILTER LOGIC (AFTER SERVLET)
            // ═══════════════════════════════════════
            // Any code here runs AFTER servlet executes
            
        }
        catch(Exception e)
        {
            System.out.println("in first filter: " + e);
        }
    }
    
    // Called ONCE when filter is destroyed
    public void destroy()                            // Line 55: Cleanup
    {
        // Release resources here
    }
}
```

### Line-by-Line Explanation

| Line | Code | Purpose |
|------|------|---------|
| 6 | `implements Filter` | Must implement Filter interface to be a filter |
| 26-27 | `req.getParameter()` | Read form data from request |
| 30-33 | `FileWriter` operations | Log data to file (audit trail) |
| 42 | `chain.doFilter(req, res)` | **CRITICAL**: Pass to next filter or servlet |

### LoginServ.java - The Servlet

```java
package mypack;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class LoginServ extends HttpServlet
{
    public void doPost(HttpServletRequest request,
                       HttpServletResponse response) 
                       throws ServletException, IOException
    {
        PrintWriter pw = response.getWriter();
        
        String name = request.getParameter("nm");
        String age = request.getParameter("ag");
        
        pw.println("<html><body>");
        pw.println("<h2>Welcome, " + name + "!</h2>");
        pw.println("<p>Your age is: " + age + "</p>");
        pw.println("</body></html>");
    }
}
```

---

## 7. Filter Chain

### What is FilterChain?

FilterChain is an interface that represents the chain of filters plus the final servlet/JSP. The **last component in the chain is always the servlet or JSP**.

### chain.doFilter() Explained

```java
chain.doFilter(req, res);
```

This method:
1. Calls the **next filter** in the chain (if any)
2. If no more filters, calls the **target servlet/JSP**
3. After servlet completes, control **returns back** through the chain

### Visual: Filter Chain Execution

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      FILTER CHAIN EXECUTION ORDER                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Filter1.doFilter()                                                         │
│  │                                                                          │
│  │  [Request Processing for Filter 1]                                       │
│  │         │                                                                │
│  │         ▼                                                                │
│  │  chain.doFilter() ─────────────────────────────┐                        │
│  │         │                                       │                        │
│  │         │  Filter2.doFilter()                  │                        │
│  │         │  │                                    │                        │
│  │         │  │  [Request Processing for Filter 2]│                        │
│  │         │  │         │                          │                        │
│  │         │  │         ▼                          │                        │
│  │         │  │  chain.doFilter() ─────────┐      │                        │
│  │         │  │         │                   │      │                        │
│  │         │  │         │  ┌───────────┐   │      │                        │
│  │         │  │         │  │  SERVLET  │   │      │                        │
│  │         │  │         │  └─────┬─────┘   │      │                        │
│  │         │  │         │        │         │      │                        │
│  │         │  │         │◄───────┘         │      │                        │
│  │         │  │         │                  │      │                        │
│  │         │  │  [Response Processing F2]◄─┘      │                        │
│  │         │  │         │                          │                        │
│  │         │◄─┘         │                          │                        │
│  │         │            │                          │                        │
│  │  [Response Processing for Filter 1]◄────────────┘                        │
│  │         │                                                                │
│  └─────────┘                                                                │
│                                                                             │
│  ORDER: F1-Pre → F2-Pre → Servlet → F2-Post → F1-Post                      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 8. web.xml Configuration

### Basic Filter Configuration

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app>
    
    <!-- FILTER DEFINITION -->
    <filter>
        <filter-name>FirstFilter</filter-name>
        <filter-class>mypack.FirstFilter</filter-class>
    </filter>
    
    <!-- FILTER MAPPING -->
    <filter-mapping>
        <filter-name>FirstFilter</filter-name>
        <!-- Apply to specific servlet -->
        <servlet-name>LoginServ</servlet-name>
    </filter-mapping>
    
    <!-- Apply filter to URL pattern -->
    <filter-mapping>
        <filter-name>FirstFilter</filter-name>
        <url-pattern>/*</url-pattern>  <!-- All URLs -->
    </filter-mapping>
    
    <!-- SERVLET DEFINITION -->
    <servlet>
        <servlet-name>LoginServ</servlet-name>
        <servlet-class>mypack.LoginServ</servlet-class>
    </servlet>
    
    <servlet-mapping>
        <servlet-name>LoginServ</servlet-name>
        <url-pattern>/LoginServ</url-pattern>
    </servlet-mapping>
    
</web-app>
```

### Multiple Filters

```xml
<!-- Filters are invoked in the ORDER they are defined -->
<filter>
    <filter-name>LoggingFilter</filter-name>
    <filter-class>mypack.LoggingFilter</filter-class>
</filter>

<filter>
    <filter-name>AuthFilter</filter-name>
    <filter-class>mypack.AuthFilter</filter-class>
</filter>

<filter-mapping>
    <filter-name>LoggingFilter</filter-name>    <!-- Called FIRST -->
    <url-pattern>/*</url-pattern>
</filter-mapping>

<filter-mapping>
    <filter-name>AuthFilter</filter-name>        <!-- Called SECOND -->
    <url-pattern>/secure/*</url-pattern>
</filter-mapping>
```

---

## 9. Key Points to Remember

1. **Filters are pluggable** - Add/remove via web.xml without code changes

2. **chain.doFilter() is essential** - Without it, request never reaches servlet

3. **Order matters** - Filters execute in the order defined in web.xml

4. **Last in chain is servlet** - FilterChain always ends with servlet/JSP

5. **init() called once** - At filter instantiation

6. **destroy() called once** - When container shuts down

7. **doFilter() called every request** - Main filter logic goes here

8. **Code before chain.doFilter()** = Request filter
   **Code after chain.doFilter()** = Response filter

9. **FilterConfig for init params** - Similar to ServletConfig

10. **URL patterns supported** - /*, *.jsp, /path/*, etc.

---

## 10. Interview Questions

### Basic Level

**Q1: What is a Filter in Java EE?**
> A Filter is a component that intercepts HTTP requests and responses. It can perform pre-processing before the servlet and post-processing after the servlet.

**Q2: What are the three methods in the Filter interface?**
> 1. `init(FilterConfig)` - Called once at startup
> 2. `doFilter(request, response, chain)` - Called for every request
> 3. `destroy()` - Called once at shutdown

**Q3: What happens if you don't call chain.doFilter()?**
> The request will never reach the servlet. The filter effectively blocks the request. This can be used intentionally for auth filters that block unauthorized access.

### Intermediate Level

**Q4: How do you configure the order of multiple filters?**
> Filters execute in the order their `<filter-mapping>` elements appear in web.xml. The first mapping executes first.

**Q5: What is the difference between request and response filters?**
> Request filter: Code before `chain.doFilter()` - runs before servlet
> Response filter: Code after `chain.doFilter()` - runs after servlet
> Both are in the same `doFilter()` method.

**Q6: How can you apply a filter to all servlets?**
```xml
<filter-mapping>
    <filter-name>MyFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

### Advanced Level

**Q7: How do you implement authentication using filters?**
```java
public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) 
        throws IOException, ServletException {
    HttpServletRequest request = (HttpServletRequest) req;
    HttpServletResponse response = (HttpServletResponse) res;
    
    HttpSession session = request.getSession(false);
    
    if (session == null || session.getAttribute("user") == null) {
        // Not logged in - redirect to login
        response.sendRedirect("login.html");
        return;  // Don't call chain.doFilter()!
    }
    
    // Logged in - continue to servlet
    chain.doFilter(req, res);
}
```

**Q8: What is the dispatcher type in filter mapping?**
> Dispatcher types control when filters are invoked:
> - `REQUEST` - Direct requests from client (default)
> - `FORWARD` - Requests forwarded by RequestDispatcher
> - `INCLUDE` - Requests included by RequestDispatcher
> - `ERROR` - Error page requests

```xml
<filter-mapping>
    <filter-name>MyFilter</filter-name>
    <url-pattern>/*</url-pattern>
    <dispatcher>REQUEST</dispatcher>
    <dispatcher>FORWARD</dispatcher>
</filter-mapping>
```

---

## Next Topic: [JSP Introduction →](./19_JSP_Introduction.md)
