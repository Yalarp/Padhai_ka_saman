# Attributes in Servlet - Complete Guide

## Table of Contents
1. [Introduction](#1-introduction)
2. [What are Attributes?](#2-what-are-attributes)
3. [Attribute Scopes](#3-attribute-scopes)
4. [Attribute Methods](#4-attribute-methods)
5. [Code Examples](#5-code-examples)
6. [Attributes vs Parameters](#6-attributes-vs-parameters)
7. [Interview Questions](#7-interview-questions)

---

## 1. Introduction

Attributes are objects that can be stored in different scopes to share data between servlets, JSPs, and other components. Unlike parameters (which are strings from client), attributes can be any Java object.

---

## 2. What are Attributes?

### Attributes Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ATTRIBUTES IN SERVLETS                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  PARAMETERS vs ATTRIBUTES                                                   │
│                                                                             │
│  PARAMETERS:                        ATTRIBUTES:                             │
│  ┌─────────────────────────┐       ┌─────────────────────────┐             │
│  │ From client (form/URL)  │       │ Set by server code      │             │
│  │ Always String           │       │ Any Java Object         │             │
│  │ Read-only               │       │ Read/Write              │             │
│  │ getParameter()          │       │ get/setAttribute()      │             │
│  └─────────────────────────┘       └─────────────────────────┘             │
│                                                                             │
│  Example:                                                                   │
│  ?name=John                         request.setAttribute("user", userObj)  │
│  request.getParameter("name")       request.getAttribute("user")           │
│  → "John" (String)                  → User object                          │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Attribute Scopes

### Three Scope Levels in Servlet

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    ATTRIBUTE SCOPES IN SERVLET                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  APPLICATION SCOPE (ServletContext)                                  │   │
│  │  • Shared by ALL users and ALL components                           │   │
│  │  • Lives: Application startup → shutdown                            │   │
│  │  • Use: Global config, shared caches                                │   │
│  │  ┌───────────────────────────────────────────────────────────────┐ │   │
│  │  │  SESSION SCOPE (HttpSession)                                   │ │   │
│  │  │  • Per-user, across requests                                   │ │   │
│  │  │  • Lives: Session start → timeout/invalidate                   │ │   │
│  │  │  • Use: User login, shopping cart                              │ │   │
│  │  │  ┌─────────────────────────────────────────────────────────┐ │ │   │
│  │  │  │  REQUEST SCOPE (HttpServletRequest)                      │ │ │   │
│  │  │  │  • Single request, including forwards                    │ │ │   │
│  │  │  │  • Lives: Request start → response sent                  │ │ │   │
│  │  │  │  • Use: Servlet → JSP data transfer                      │ │ │   │
│  │  │  └─────────────────────────────────────────────────────────┘ │ │   │
│  │  └───────────────────────────────────────────────────────────────┘ │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Scope Comparison

| Scope | Object | Lifetime | Shared By |
|-------|--------|----------|-----------|
| Request | HttpServletRequest | Single request | Forwarded components |
| Session | HttpSession | User session | All requests from user |
| Application | ServletContext | App lifetime | All users, all requests |

---

## 4. Attribute Methods

### Request Scope Methods

```java
// Set attribute
request.setAttribute("name", value);

// Get attribute
Object value = request.getAttribute("name");

// Remove attribute
request.removeAttribute("name");

// Get all attribute names
Enumeration<String> names = request.getAttributeNames();
```

### Session Scope Methods

```java
HttpSession session = request.getSession();

// Set attribute
session.setAttribute("user", userObject);

// Get attribute
User user = (User) session.getAttribute("user");

// Remove attribute
session.removeAttribute("user");

// Get all attribute names
Enumeration<String> names = session.getAttributeNames();
```

### Application Scope Methods

```java
ServletContext context = getServletContext();

// Set attribute
context.setAttribute("config", configObject);

// Get attribute
Config config = (Config) context.getAttribute("config");

// Remove attribute
context.removeAttribute("config");

// Get all attribute names
Enumeration<String> names = context.getAttributeNames();
```

---

## 5. Code Examples

### Request Scope - Servlet to JSP

**ControllerServlet.java:**
```java
@WebServlet("/products")
public class ControllerServlet extends HttpServlet {
    
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // Create data
        List<Product> products = productService.getAllProducts();
        String message = "Found " + products.size() + " products";
        
        // Set as request attributes
        request.setAttribute("products", products);
        request.setAttribute("message", message);
        
        // Forward to JSP (request attributes preserved)
        RequestDispatcher rd = request.getRequestDispatcher("products.jsp");
        rd.forward(request, response);
    }
}
```

**products.jsp:**
```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

<h2>${message}</h2>

<table>
    <c:forEach var="product" items="${products}">
        <tr>
            <td>${product.name}</td>
            <td>${product.price}</td>
        </tr>
    </c:forEach>
</table>
```

### Session Scope - Login Example

**LoginServlet.java:**
```java
@WebServlet("/login")
public class LoginServlet extends HttpServlet {
    
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        
        User user = userService.authenticate(username, password);
        
        if (user != null) {
            // Store user in session
            HttpSession session = request.getSession();
            session.setAttribute("loggedInUser", user);
            session.setAttribute("loginTime", new java.util.Date());
            
            response.sendRedirect("dashboard.jsp");
        } else {
            request.setAttribute("error", "Invalid credentials");
            request.getRequestDispatcher("login.jsp").forward(request, response);
        }
    }
}
```

**dashboard.jsp:**
```jsp
<%
    User user = (User) session.getAttribute("loggedInUser");
    if (user == null) {
        response.sendRedirect("login.jsp");
        return;
    }
%>

<h2>Welcome, ${sessionScope.loggedInUser.name}!</h2>
<p>Logged in at: ${sessionScope.loginTime}</p>
```

### Application Scope - Visitor Counter

**AppInitListener.java:**
```java
@WebListener
public class AppInitListener implements ServletContextListener {
    
    public void contextInitialized(ServletContextEvent sce) {
        ServletContext context = sce.getServletContext();
        context.setAttribute("visitorCount", new AtomicInteger(0));
    }
}
```

**CounterServlet.java:**
```java
@WebServlet("/visit")
public class CounterServlet extends HttpServlet {
    
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        ServletContext context = getServletContext();
        AtomicInteger counter = (AtomicInteger) context.getAttribute("visitorCount");
        int count = counter.incrementAndGet();
        
        response.getWriter().println("You are visitor #" + count);
    }
}
```

---

## 6. Attributes vs Parameters

### Complete Comparison

| Feature | Parameters | Attributes |
|---------|------------|------------|
| **Source** | Client (form/URL) | Server code |
| **Type** | String only | Any Object |
| **Read/Write** | Read-only | Read/Write |
| **Set by** | Client request | setAttribute() |
| **Get by** | getParameter() | getAttribute() |
| **Scopes** | Request only | Request, Session, Application |
| **Casting needed** | No (always String) | Yes (returns Object) |

### Example Comparison

```java
// PARAMETERS - from URL: /servlet?name=John&age=25

// Get parameter (always String)
String name = request.getParameter("name");    // "John"
String ageStr = request.getParameter("age");   // "25"
int age = Integer.parseInt(ageStr);            // Need to convert!

// ----------------------------------------------------------

// ATTRIBUTES - set by code

// Set any object
User user = new User("John", 25);
request.setAttribute("user", user);

// Get (returns Object, need to cast)
User retrievedUser = (User) request.getAttribute("user");
```

---

## 7. Interview Questions

### Basic Level

**Q1: What is the difference between parameters and attributes?**
| Parameter | Attribute |
|-----------|-----------|
| From client | Set by server |
| String only | Any Object |
| Read-only | Read/Write |
| Request scope only | Any scope |

**Q2: How do you set and get a request attribute?**
```java
request.setAttribute("name", value);
Object value = request.getAttribute("name");
```

**Q3: What are the three scopes for attributes in servlets?**
> Request, Session, and Application (ServletContext)

### Intermediate Level

**Q4: Why do you need to cast when getting an attribute?**
> Because `getAttribute()` returns `Object`. The container doesn't know what type you stored.
```java
User user = (User) request.getAttribute("user");
```

**Q5: What happens to request attributes on redirect?**
> They are **lost**! Redirect causes a new request. Use session attributes or URL parameters instead.

**Q6: How do you share data between servlets using forward?**
```java
// In first servlet
request.setAttribute("data", myData);
request.getRequestDispatcher("secondServlet").forward(request, response);

// In second servlet
Object data = request.getAttribute("data");  // Works!
```

### Advanced Level

**Q7: How do you make application attributes thread-safe?**
```java
// Using synchronized block
synchronized(getServletContext()) {
    Integer count = (Integer) getServletContext().getAttribute("count");
    getServletContext().setAttribute("count", count + 1);
}

// Or use thread-safe objects
AtomicInteger counter = new AtomicInteger(0);
getServletContext().setAttribute("counter", counter);
// Later:
counter.incrementAndGet();  // Thread-safe!
```

**Q8: When should you use each scope?**
| Scope | Use When |
|-------|----------|
| Request | Servlet → JSP data transfer, error messages |
| Session | User login, shopping cart, preferences |
| Application | Global config, caches, counters |

---

## Next Topic: [Expression Language →](../Day5/26_Expression_Language.md)
