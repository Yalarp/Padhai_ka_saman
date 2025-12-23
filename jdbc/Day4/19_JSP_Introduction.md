# JSP - Introduction and Fundamentals

## Table of Contents
1. [Introduction](#1-introduction)
2. [What is JSP?](#2-what-is-jsp)
3. [JSP vs Servlet](#3-jsp-vs-servlet)
4. [JSP Lifecycle](#4-jsp-lifecycle)
5. [JSP Elements](#5-jsp-elements)
6. [Implicit Objects](#6-implicit-objects)
7. [JSP Directives](#7-jsp-directives)
8. [Code Examples](#8-code-examples)
9. [Key Points to Remember](#9-key-points-to-remember)
10. [Interview Questions](#10-interview-questions)

---

## 1. Introduction

Java Server Pages (JSP) is a server-side technology that allows you to create dynamic web content by mixing static HTML with Java code. JSP simplifies web development by allowing developers to embed Java directly in HTML pages.

---

## 2. What is JSP?

### Definition

JSP (Java Server Pages) is a **server-side technology** for creating dynamic web pages. It's an advanced version of Servlets that allows mixing HTML and Java code.

### Key Characteristics

| Feature | Description |
|---------|-------------|
| Server-side | Processed on the server, client sees only HTML |
| Middle-tier | Part of the presentation layer in MVC |
| Extension | Files have `.jsp` extension |
| Compilation | Converted to Servlet internally |
| Simplified | Easier than writing pure Servlets |

### Visual: JSP Processing

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         JSP PROCESSING FLOW                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   Developer writes:              Container processes:     Client receives:  │
│                                                                             │
│   ┌──────────────┐              ┌──────────────┐        ┌──────────────┐   │
│   │  page.jsp    │   ─────►    │  Servlet     │  ────► │  Pure HTML   │   │
│   │              │              │  (generated) │        │              │   │
│   │ <html>       │              │              │        │ <html>       │   │
│   │ <%           │              │ out.print()  │        │ Hello World! │   │
│   │  code        │              │ ...          │        │ </html>      │   │
│   │ %>           │              │              │        │              │   │
│   │ </html>      │              │              │        │              │   │
│   └──────────────┘              └──────────────┘        └──────────────┘   │
│                                                                             │
│   JSP + Java                    Compiled Java            Only HTML          │
│                                                          (No Java visible)  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. JSP vs Servlet

### Comparison Table

| Aspect | JSP | Servlet |
|--------|-----|---------|
| **Syntax** | HTML with embedded Java | Pure Java code |
| **Ease** | Easier for UI development | More complex |
| **Purpose** | View (presentation layer) | Controller (logic) |
| **File Extension** | `.jsp` | `.java` |
| **HTML Generation** | Static + Dynamic | Dynamic HTML only |
| **MVC Role** | View | Controller |
| **Development Speed** | Faster for UI | Slower for UI |

### Does Servlet Become Obsolete?

**NO!** In MVC architecture:
- **Model**: POJO (Plain Old Java Object)
- **View**: JSP (or Thymeleaf)
- **Controller**: Servlet

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                            MVC ARCHITECTURE                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│     ┌─────────┐        ┌──────────────┐        ┌─────────┐                │
│     │ Browser │───────►│   SERVLET    │───────►│  POJO   │                │
│     │         │        │ (Controller) │        │ (Model) │                │
│     │         │        │              │◄───────│         │                │
│     │         │        └──────┬───────┘        └─────────┘                │
│     │         │               │                                            │
│     │         │               │ forward                                    │
│     │         │               ▼                                            │
│     │         │◄──────  ┌──────────┐                                      │
│     │ (HTML)  │         │   JSP    │                                      │
│     └─────────┘         │  (View)  │                                      │
│                         └──────────┘                                       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 4. JSP Lifecycle

### First Request Processing

When a JSP is requested for the **first time**:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                   JSP LIFECYCLE - FIRST REQUEST                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  STEP 1: TRANSLATION                                                        │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ page.jsp ──────────────► page_jsp.java                      │           │
│  │ Container translates JSP to Java Servlet source code        │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                              │                                              │
│                              ▼                                              │
│  STEP 2: COMPILATION                                                        │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ page_jsp.java ────────► page_jsp.class                      │           │
│  │ Java compiler compiles the servlet                          │           │
│  │ (Compilation errors shown in browser on first request)      │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                              │                                              │
│                              ▼                                              │
│  STEP 3: LOADING                                                            │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ ClassLoader loads page_jsp.class into memory                │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                              │                                              │
│                              ▼                                              │
│  STEP 4: INSTANTIATION                                                      │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ Container creates instance using no-arg constructor         │           │
│  │ (Using Reflection API)                                      │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                              │                                              │
│                              ▼                                              │
│  STEP 5: INITIALIZATION                                                     │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ jspInit() method called                                     │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                              │                                              │
│                              ▼                                              │
│  STEP 6: THREAD HANDLING                                                    │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ Thread created or retrieved from thread pool                │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                              │                                              │
│                              ▼                                              │
│  STEP 7: REQUEST/RESPONSE CREATION                                          │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ Request and Response objects created                        │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                              │                                              │
│                              ▼                                              │
│  STEP 8: SERVICE                                                            │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ _jspService(request, response) method called                │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### JSP Lifecycle Methods

```java
// Called ONCE during initialization
public void jspInit() {
    // Override to perform initialization
}

// Called for EVERY request (auto-generated, cannot override!)
public void _jspService(HttpServletRequest request, 
                        HttpServletResponse response) {
    // Container generates this from your JSP code
}

// Called ONCE during destruction
public void jspDestroy() {
    // Override to perform cleanup
}
```

### Method Override Rules

| Method | Can Override? | Called |
|--------|---------------|--------|
| `jspInit()` | Yes | Once at startup |
| `_jspService()` | **No** | Every request |
| `jspDestroy()` | Yes | Once at shutdown |

---

## 5. JSP Elements

### Types of JSP Elements

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         JSP ELEMENTS                                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. DECLARATION    <%!  %>    Member variables and methods                  │
│  2. EXPRESSION     <%=  %>    Output a value                                │
│  3. SCRIPTLET      <%   %>    Java code block                               │
│  4. DIRECTIVE      <%@  %>    Instructions to container                     │
│  5. STANDARD ACTIONS <jsp:  >  Perform actions                              │
│  6. STATIC HTML                Normal HTML content                           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 1. Declaration <%! %>

Used to declare **instance variables** and **methods** in the generated servlet.

```jsp
<%!
    // Instance variable - shared across requests
    int counter = 0;
    
    // Method definition
    public int getSquare(int n) {
        return n * n;
    }
%>
```

**Generated Servlet Code:**
```java
public class page_jsp extends HttpServlet {
    int counter = 0;           // Becomes instance variable
    
    public int getSquare(int n) {
        return n * n;
    }
    
    public void _jspService(...) {
        // ...
    }
}
```

### 2. Expression <%= %>

Used to **output a value** to the response.

```jsp
<p>The current time is: <%= new java.util.Date() %></p>
<p>5 squared is: <%= getSquare(5) %></p>
<p>Your name is: <%= request.getParameter("name") %></p>
```

**Generated Servlet Code:**
```java
out.print(new java.util.Date());
out.print(getSquare(5));
out.print(request.getParameter("name"));
```

**Note:** No semicolon at the end!

### 3. Scriptlet <% %>

Used to write any **Java code block**.

```jsp
<%
    String name = request.getParameter("name");
    int age = Integer.parseInt(request.getParameter("age"));
    
    if (age >= 18) {
        out.println("You are an adult");
    } else {
        out.println("You are a minor");
    }
%>
```

### 4. Directive <%@ %>

Special instructions to the container.

```jsp
<%@ directive_name attribute="value" %>
```

Three types:
- **page** - Page-level settings
- **include** - Include other files
- **taglib** - Use custom tags

---

## 6. Implicit Objects

JSP provides **9 built-in objects** that are automatically available:

### Complete List

| Object | Type | Description |
|--------|------|-------------|
| `out` | JspWriter | Write to response |
| `request` | HttpServletRequest | Client request |
| `response` | HttpServletResponse | Server response |
| `session` | HttpSession | User session |
| `application` | ServletContext | Application context |
| `config` | ServletConfig | Servlet configuration |
| `exception` | JspException | Exception (error pages only) |
| `pageContext` | PageContext | Page-level context |
| `page` | Object | Current JSP instance (this) |

### Usage Examples

```jsp
<%
    // out - Write output
    out.println("Hello World");
    
    // request - Get parameters
    String name = request.getParameter("name");
    
    // response - Set headers
    response.setContentType("text/html");
    
    // session - Store user data
    session.setAttribute("user", "John");
    String user = (String) session.getAttribute("user");
    
    // application - Application-wide data
    application.setAttribute("count", 100);
    
    // config - Servlet config
    String initParam = config.getInitParameter("param1");
    
    // pageContext - Page scope access
    pageContext.setAttribute("local", "value");
%>
```

---

## 7. JSP Directives

### 1. Page Directive

```jsp
<%@ page attribute="value" %>
```

#### Common Attributes

| Attribute | Default | Description |
|-----------|---------|-------------|
| `import` | none | Import Java packages |
| `session` | true | Create session automatically |
| `isThreadSafe` | true | Thread safety mode |
| `errorPage` | none | Error handling page |
| `isErrorPage` | false | Is this an error page |
| `contentType` | text/html | Response content type |

#### Examples

```jsp
<%-- Import packages --%>
<%@ page import="java.util.*, java.sql.*" %>

<%-- Disable session --%>
<%@ page session="false" %>

<%-- Set error page --%>
<%@ page errorPage="error.jsp" %>

<%-- Mark as error page (enables exception object) --%>
<%@ page isErrorPage="true" %>

<%-- Make single-threaded (deprecated) --%>
<%@ page isThreadSafe="false" %>
```

### 2. Include Directive

Include content of another file at **translation time**.

```jsp
<%@ include file="header.html" %>

<h1>Main Content</h1>

<%@ include file="footer.html" %>
```

### 3. Taglib Directive

Use custom tag libraries.

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

<c:forEach var="item" items="${list}">
    ${item}
</c:forEach>
```

---

## 8. Code Examples

### Simple JSP Page

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <title>My First JSP</title>
</head>
<body>
    <h1>Welcome to JSP!</h1>
    
    <%-- This is a JSP comment (not sent to client) --%>
    
    <%-- Scriptlet: Java code --%>
    <%
        String name = request.getParameter("name");
        if (name == null) {
            name = "Guest";
        }
    %>
    
    <%-- Expression: Output value --%>
    <p>Hello, <%= name %>!</p>
    
    <%-- Declaration: Define method --%>
    <%!
        public String greet(String name) {
            return "Welcome, " + name + "!";
        }
    %>
    
    <p><%= greet(name) %></p>
    
    <p>Current time: <%= new java.util.Date() %></p>
</body>
</html>
```

### Error Page Example

**main.jsp:**
```jsp
<%@ page errorPage="error.jsp" language="java" 
         contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <title>Main Page</title>
</head>
<body>
    <%
        String str = null;
        str.length();  // This will throw NullPointerException!
    %>
</body>
</html>
```

**error.jsp:**
```jsp
<%@ page isErrorPage="true" language="java" 
         contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <title>Error Page</title>
</head>
<body>
    <h1>Oops! Something went wrong.</h1>
    <p>Error: <%= exception.getMessage() %></p>
</body>
</html>
```

---

## 9. Key Points to Remember

1. **JSP = HTML + Java** - Easier than pure servlets for UI

2. **Converted to Servlet** - Every JSP becomes a servlet internally

3. **9 Implicit Objects** - out, request, response, session, etc.

4. **Cannot override _jspService()** - Container generates it

5. **First request is slow** - Translation + Compilation

6. **Declaration = Instance level** - <%! %> creates instance variables

7. **Expression = No semicolon** - <%= value %> outputs value

8. **Scriptlet = Any Java code** - <% code; %> 

9. **page directive is common** - import, errorPage, session

10. **MVC: JSP is View** - Servlet is Controller

---

## 10. Interview Questions

### Basic Level

**Q1: What is JSP?**
> JSP (Java Server Pages) is a server-side technology that allows creating dynamic web pages by embedding Java code in HTML.

**Q2: What are JSP lifecycle methods?**
> 1. `jspInit()` - Called once at initialization
> 2. `_jspService()` - Called for every request (cannot override)
> 3. `jspDestroy()` - Called once at destruction

**Q3: How many implicit objects are there in JSP?**
> 9 implicit objects: out, request, response, session, application, config, exception, pageContext, page

### Intermediate Level

**Q4: What is the difference between include directive and include action?**
| Include Directive | Include Action |
|-------------------|----------------|
| `<%@ include %>` | `<jsp:include>` |
| Translation time | Request time |
| Static inclusion | Dynamic inclusion |
| Content merged | Runtime call |

**Q5: Why can't you override _jspService()?**
> Because the container generates this method from your JSP code. It contains all your scriptlets, expressions, and static content converted to Java code.

**Q6: What is the difference between <%! %> and <% %>?**
| Declaration <%! %> | Scriptlet <% %> |
|-------------------|-----------------|
| Instance level | Inside _jspService() |
| Declares fields/methods | Executes code |
| Runs once | Runs per request |

### Advanced Level

**Q7: How would you implement a counter that tracks total page visits?**
```jsp
<%!
    // Instance variable - shared across all requests
    int totalVisits = 0;
    
    // Synchronized to handle concurrent access
    synchronized void incrementCounter() {
        totalVisits++;
    }
%>

<%
    incrementCounter();
%>

<p>Total visits: <%= totalVisits %></p>
```

**Q8: What happens on the first JSP request?**
> 1. JSP translated to .java servlet file
> 2. Compiled to .class file
> 3. Class loaded into memory
> 4. Instance created via no-arg constructor
> 5. jspInit() called
> 6. Thread created/retrieved
> 7. Request/Response objects created
> 8. _jspService() called

**Q9: How do you configure common error page for all JSPs?**
```xml
<!-- In web.xml -->
<error-page>
    <exception-type>java.lang.Exception</exception-type>
    <location>/error.jsp</location>
</error-page>
```

---

## Next Topic: [JSP Implicit Objects Details →](./21_JSP_Implicit_Objects.md)
