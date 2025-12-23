# Redirect vs Forward Mechanisms

## Table of Contents
1. [Introduction](#1-introduction)
2. [sendRedirect() - Redirect Mechanism](#2-sendredirect---redirect-mechanism)
3. [RequestDispatcher.forward() - Forward Mechanism](#3-requestdispatcherforward---forward-mechanism)
4. [Complete Code Examples](#4-complete-code-examples)
5. [Comparative Study](#5-comparative-study)
6. [RequestDispatcher Include](#6-requestdispatcher-include)
7. [Key Points to Remember](#7-key-points-to-remember)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

When one web component (servlet or JSP) cannot fully process a request, it needs to delegate to another component. Java provides two main mechanisms for this:

1. **Redirect** - Client-side redirection
2. **Forward** - Server-side forwarding

Understanding when to use which is crucial for building proper web applications.

---

## 2. sendRedirect() - Redirect Mechanism

### What is Redirect?

Redirect is a **client-side** operation where the server tells the browser to make a **new request** to a different URL.

### Visual Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         REDIRECT FLOW                                        │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   CLIENT                          SERVER                                    │
│   ┌───────┐                      ┌───────────────────┐                     │
│   │Browser│ ─── Request 1 ────► │ RedirectingServlet│                     │
│   │       │                      │                   │                     │
│   │       │ ◄─ Response (302) ── │ sendRedirect()   │                     │
│   │       │   Location: /Other   │                   │                     │
│   └───┬───┘                      └───────────────────┘                     │
│       │                                                                     │
│       │ (Browser sees 302 status)                                          │
│       │ (Browser makes NEW request)                                        │
│       │                                                                     │
│   ┌───▼───┐                      ┌───────────────────┐                     │
│   │Browser│ ─── Request 2 ────► │ RedirectedServlet │                     │
│   │       │                      │                   │                     │
│   │       │ ◄─ Final Response ── │ (processes)      │                     │
│   └───────┘                      └───────────────────┘                     │
│                                                                             │
│   KEY POINTS:                                                               │
│   • TWO separate HTTP requests                                              │
│   • URL in browser CHANGES                                                  │
│   • Client KNOWS about the second servlet                                   │
│   • Request/Response objects are DIFFERENT                                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Code Syntax

```java
response.sendRedirect("targetURL");
```

---

## 3. RequestDispatcher.forward() - Forward Mechanism

### What is Forward?

Forward is a **server-side** operation where the servlet internally passes the same request to another component **without the client knowing**.

### Visual Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         FORWARD FLOW                                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   CLIENT                          SERVER                                    │
│   ┌───────┐                      ┌────────────────────────────────────┐    │
│   │Browser│ ─── Request  ──────►│          WEB CONTAINER             │    │
│   │       │                      │                                    │    │
│   │       │                      │  ┌───────────────────┐            │    │
│   │       │                      │  │ ForwardingServlet │            │    │
│   │       │                      │  │                   │            │    │
│   │       │                      │  │ forward() ────────┼───┐        │    │
│   │       │                      │  └───────────────────┘   │        │    │
│   │       │                      │                          │        │    │
│   │       │                      │  ┌───────────────────┐   │        │    │
│   │       │                      │  │ ForwardedServlet  │◄──┘        │    │
│   │       │                      │  │                   │            │    │
│   │       │ ◄─ Final Response ───│──│ (processes)      │            │    │
│   └───────┘                      │  └───────────────────┘            │    │
│                                  └────────────────────────────────────┘    │
│                                                                             │
│   KEY POINTS:                                                               │
│   • ONE HTTP request                                                        │
│   • URL in browser DOES NOT change                                          │
│   • Client thinks first servlet sent response                               │
│   • SAME request/response objects passed                                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Code Syntax

```java
RequestDispatcher rd = request.getRequestDispatcher("targetServlet");
rd.forward(request, response);
```

---

## 4. Complete Code Examples

### Redirect Example

#### RedirectingServ.java

```java
import javax.servlet.*;                              // Line 1: Servlet imports
import javax.servlet.http.*;                         // Line 2: HTTP imports
import java.io.*;                                    // Line 3: IO imports

public class RedirectingServ extends HttpServlet     // Line 5: Servlet class
{
    public void doGet(HttpServletRequest request,    // Line 7: Handle GET
                      HttpServletResponse response)
    {
        try
        {
            // KEY LINE: Tell browser to go to RedirectedServ
            // This sends HTTP 302 status with Location header
            response.sendRedirect("RedirectedServ"); // Line 13: Redirect!
        }
        catch(Exception ee)
        {
            System.out.println("in redirecting: " + ee);
        }
    }
}
```

#### RedirectedServ.java

```java
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class RedirectedServ extends HttpServlet
{
    public void doGet(HttpServletRequest request,
                      HttpServletResponse response) 
                      throws ServletException, IOException
    {
        PrintWriter pw = response.getWriter();
        pw.println("I am the Redirected Servlet!");
        pw.println("This is a NEW request from the browser.");
    }
}
```

### Forward Example

#### ForwardingServ.java

```java
import javax.servlet.*;                              // Line 1: Servlet imports
import javax.servlet.http.*;                         // Line 2: HTTP imports
import java.io.*;                                    // Line 3: IO imports

public class ForwardingServ extends HttpServlet      // Line 5: Servlet class
{
    public void doGet(HttpServletRequest request,    // Line 7: Handle GET
                      HttpServletResponse response)
    {
        try
        {
            // STEP 1: Create RequestDispatcher for target servlet
            // This creates a "ticket" to forward to ForwardedServ
            RequestDispatcher rd = request.getRequestDispatcher("ForwardedServ");
            // Line 14: Create dispatcher
            
            // STEP 2: Forward the SAME request and response
            // Control passes to ForwardedServ
            rd.forward(request, response);           // Line 18: Forward!
        }
        catch(Exception ee)
        {
            System.out.println("in forwarding: " + ee);
        }
    }
}
```

#### ForwardedServ.java

```java
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class ForwardedServ extends HttpServlet
{
    public void doGet(HttpServletRequest request,
                      HttpServletResponse response) 
                      throws ServletException, IOException
    {
        PrintWriter pw = response.getWriter();
        pw.println("I am the Forwarded Servlet!");
        pw.println("This is the SAME request that went to ForwardingServ.");
    }
}
```

---

## 5. Comparative Study

### Side-by-Side Comparison

| Feature | Redirect | Forward |
|---------|----------|---------|
| **Number of Requests** | 2 requests | 1 request |
| **Client Awareness** | Client knows | Client doesn't know |
| **URL in Browser** | Changes | Doesn't change |
| **Speed** | Slower (extra round trip) | Faster |
| **Request/Response** | New objects | Same objects |
| **Scope** | Can go anywhere (even external sites) | Within web container only |
| **Request Attributes** | Lost | Preserved |
| **Method** | response.sendRedirect() | rd.forward() |

### When to Use What

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        REDIRECT vs FORWARD Decision                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   USE REDIRECT when:                                                        │
│   ┌───────────────────────────────────────────────────────────────┐        │
│   │ • Going to an EXTERNAL website (different domain)             │        │
│   │ • After POST to prevent form resubmission                     │        │
│   │ • When URL SHOULD change (e.g., after login, redirect to home)│        │
│   │ • When client needs to BOOKMARK the final page                │        │
│   │ • After database operation (Post-Redirect-Get pattern)        │        │
│   └───────────────────────────────────────────────────────────────┘        │
│                                                                             │
│   USE FORWARD when:                                                         │
│   ┌───────────────────────────────────────────────────────────────┐        │
│   │ • Staying within the SAME application                         │        │
│   │ • Need to PRESERVE request attributes                         │        │
│   │ • In MVC pattern (controller forwards to view)                │        │
│   │ • When client shouldn't know about internal routing           │        │
│   │ • For better PERFORMANCE                                      │        │
│   └───────────────────────────────────────────────────────────────┘        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Request Attribute Preservation

```java
// In ForwardingServ (Forward preserves attributes)
request.setAttribute("message", "Hello from ForwardingServ");
RequestDispatcher rd = request.getRequestDispatcher("ForwardedServ");
rd.forward(request, response);

// In ForwardedServ
String message = (String) request.getAttribute("message");
// message = "Hello from ForwardingServ"  ← PRESERVED!

// With Redirect (Attributes are LOST)
request.setAttribute("message", "Hello");  // This is lost!
response.sendRedirect("RedirectedServ");

// In RedirectedServ
String message = (String) request.getAttribute("message");
// message = null  ← LOST! (New request)
```

---

## 6. RequestDispatcher Include

### What is Include?

Include is similar to forward, but instead of giving up control, the **including servlet continues** after the included servlet finishes.

### Visual Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         INCLUDE FLOW                                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   IncluderServ                          IncludedServ                        │
│   ┌─────────────────┐                  ┌─────────────────┐                 │
│   │ 1. Start output │                  │                 │                 │
│   │    "Header"     │                  │                 │                 │
│   │                 │                  │                 │                 │
│   │ 2. include() ───┼──────────────────►│ 3. Add content │                 │
│   │                 │                  │    "Middle"     │                 │
│   │                 │◄─────────────────┼── 4. Return     │                 │
│   │                 │                  │                 │                 │
│   │ 5. Continue     │                  └─────────────────┘                 │
│   │    "Footer"     │                                                       │
│   └─────────────────┘                                                       │
│                                                                             │
│   OUTPUT: Header + Middle + Footer                                          │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Include Code Example

```java
// IncluderServ.java
public void doGet(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    PrintWriter pw = response.getWriter();
    
    // Output before include
    pw.println("<html><body>");
    pw.println("<h1>Header Section</h1>");
    
    // Include another servlet's output
    RequestDispatcher rd = request.getRequestDispatcher("IncludedServ");
    rd.include(request, response);  // Include, don't forward!
    
    // Output after include (continues here!)
    pw.println("<h1>Footer Section</h1>");
    pw.println("</body></html>");
}

// IncludedServ.java
public void doGet(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    PrintWriter pw = response.getWriter();
    pw.println("<h2>This is the included content</h2>");
}
```

### Forward vs Include

| Feature | Forward | Include |
|---------|---------|---------|
| Control returns? | No | Yes |
| Can write before? | No (IllegalStateException) | Yes |
| Can write after? | No | Yes |
| Use case | Delegation | Composition |

---

## 7. Key Points to Remember

1. **Redirect = 2 requests, Forward = 1 request**

2. **Redirect changes URL, Forward doesn't**

3. **Forward is faster** (no extra network round trip)

4. **Redirect can go anywhere**, Forward stays in container

5. **Request attributes preserved in Forward**, lost in Redirect

6. **Use Forward for MVC** (Controller to View)

7. **Use Redirect after POST** (Post-Redirect-Get pattern)

8. **RequestDispatcher created from**:
   - `request.getRequestDispatcher()` - within context
   - `context.getRequestDispatcher()` - across contexts

9. **Include returns control** to includer, Forward doesn't

10. **Cannot write to response before Forward** (but can before Include)

---

## 8. Interview Questions

### Basic Level

**Q1: What is the difference between sendRedirect() and forward()?**
| sendRedirect() | forward() |
|----------------|-----------|
| Client-side redirect | Server-side forward |
| 2 HTTP requests | 1 HTTP request |
| URL changes in browser | URL stays same |
| Slower | Faster |

**Q2: Can you forward a request to an external website?**
> No. Forward only works within the same web container. For external URLs, you must use sendRedirect().

**Q3: What happens to request attributes during redirect?**
> Request attributes are lost because a new request is created. To pass data, you can use URL parameters, session, or cookies.

### Intermediate Level

**Q4: Explain the Post-Redirect-Get pattern.**
> After a POST request (like form submission), always redirect to a GET page:
> 1. User submits form (POST)
> 2. Server processes and saves data
> 3. Server sends redirect to confirmation page
> 4. Browser makes GET request to confirmation page
> 
> This prevents duplicate form submissions when user refreshes.

**Q5: What exception is thrown if you write to response before forward?**
> `IllegalStateException` is thrown if the response is already committed (headers already sent) before calling forward().

**Q6: What is the difference between request and context RequestDispatcher?**
```java
// From request - relative path
RequestDispatcher rd = request.getRequestDispatcher("TargetServlet");

// From context - absolute path (starts with /)
RequestDispatcher rd = getServletContext().getRequestDispatcher("/TargetServlet");

// Context dispatcher for OTHER contexts
ServletContext other = getServletContext().getContext("/otherapp");
RequestDispatcher rd = other.getRequestDispatcher("/servlet");
```

### Advanced Level

**Q7: How do you forward to a servlet in a different web application?**
```java
// First, enable cross-context in server configuration
// In Tomcat, set crossContext="true" in context.xml

// Then in code:
ServletContext otherContext = getServletContext().getContext("/otherapp");
if (otherContext != null) {
    RequestDispatcher rd = otherContext.getRequestDispatcher("/TargetServlet");
    rd.forward(request, response);
}
```

**Q8: When would include() be better than forward()?**
> Use include() when:
> - Building reusable components (header, footer)
> - Aggregating content from multiple sources
> - Need to add content before and after included content
> - Creating templates with placeholders

**Q9: Can you modify the response after forward() returns?**
> No, once forward() is called, control is completely transferred. Any code after forward() is not recommended and attempting to write to response will cause issues. Best practice is to return immediately after forward():
```java
rd.forward(request, response);
return;  // Good practice
```

---

## Next Topic: [Servlet Init Methods →](../Day4/13_Servlet_Init_Methods.md)
