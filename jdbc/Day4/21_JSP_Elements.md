# JSP Elements - Complete Guide

## Table of Contents
1. [Introduction](#1-introduction)
2. [Types of JSP Elements](#2-types-of-jsp-elements)
3. [Declarations](#3-declarations)
4. [Expressions](#4-expressions)
5. [Scriptlets](#5-scriptlets)
6. [Comments](#6-comments)
7. [Best Practices](#7-best-practices)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

JSP elements are special tags that allow you to embed Java code in HTML pages. Understanding each element type is essential for JSP development.

---

## 2. Types of JSP Elements

### Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         JSP ELEMENTS OVERVIEW                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  SCRIPTING ELEMENTS                                                  │   │
│  │  ├── Declaration  <%!  %>  - Instance variables, methods            │   │
│  │  ├── Expression   <%=  %>  - Output a value                         │   │
│  │  └── Scriptlet    <%   %>  - Java code block                        │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  DIRECTIVE ELEMENTS                                                  │   │
│  │  ├── Page     <%@ page %>     - Page settings                       │   │
│  │  ├── Include  <%@ include %>  - Include files                       │   │
│  │  └── Taglib   <%@ taglib %>   - Use custom tags                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  ACTION ELEMENTS                                                     │   │
│  │  ├── <jsp:include>     - Include at request time                    │   │
│  │  ├── <jsp:forward>     - Forward request                            │   │
│  │  ├── <jsp:useBean>     - Use JavaBeans                              │   │
│  │  └── <jsp:setProperty> - Set bean properties                        │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  COMMENT ELEMENTS                                                    │   │
│  │  ├── <%-- JSP comment --%>   - Not sent to client                   │   │
│  │  └── <!-- HTML comment -->   - Sent to client                       │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Declarations

### Syntax

```jsp
<%! declaration %>
```

### Purpose

Declarations define **instance variables** and **methods** that become part of the generated servlet class, outside of `_jspService()`.

### Examples

```jsp
<%!
    // Instance variable - SHARED across requests!
    private int visitCount = 0;
    
    // Method definition
    public String getGreeting(String name) {
        return "Hello, " + name + "!";
    }
    
    // Inner class
    public class Helper {
        public String format(String text) {
            return text.toUpperCase();
        }
    }
    
    // Override lifecycle method
    public void jspInit() {
        System.out.println("JSP initialized");
    }
%>
```

### What Gets Generated

```java
public class mypage_jsp extends HttpJspBase {
    
    // Declaration content becomes instance-level
    private int visitCount = 0;
    
    public String getGreeting(String name) {
        return "Hello, " + name + "!";
    }
    
    // ...
    
    public void _jspService(...) {
        // Scriptlet/expression code goes here
    }
}
```

### Thread Safety Warning!

```jsp
<%!
    int counter = 0;  // DANGER: Shared by all users!
%>

<%
    counter++;  // Race condition possible!
    out.println("You are visitor: " + counter);
%>
```

---

## 4. Expressions

### Syntax

```jsp
<%= expression %>
```

**Note: NO semicolon at the end!**

### Purpose

Expressions evaluate a value and output it to the response. They're converted to `out.print(expression)`.

### Examples

```jsp
<%-- String expression --%>
<p>Your name: <%= request.getParameter("name") %></p>

<%-- Numeric expression --%>
<p>Sum: <%= 5 + 3 %></p>

<%-- Method call --%>
<p>Time: <%= new java.util.Date() %></p>

<%-- Ternary expression --%>
<p>Status: <%= (age >= 18) ? "Adult" : "Minor" %></p>

<%-- Calling declared method --%>
<p><%= getGreeting("World") %></p>
```

### What Gets Generated

```java
// <%= request.getParameter("name") %> becomes:
out.print(request.getParameter("name"));

// <%= 5 + 3 %> becomes:
out.print(5 + 3);
```

### Null Handling

```jsp
<%-- DANGER: May print "null" --%>
<%= request.getParameter("missing") %>

<%-- Better approach --%>
<%
    String param = request.getParameter("missing");
    if (param == null) param = "default";
%>
<%= param %>

<%-- Or use EL (best) --%>
${param.missing}  <%-- Outputs empty string if null --%>
```

---

## 5. Scriptlets

### Syntax

```jsp
<% java code %>
```

### Purpose

Scriptlets contain Java code that executes when the page is requested. The code goes inside `_jspService()`.

### Examples

```jsp
<%-- Variables (local to request) --%>
<%
    String name = request.getParameter("name");
    int age = Integer.parseInt(request.getParameter("age"));
%>

<%-- Conditional logic --%>
<%
    if (age >= 18) {
%>
        <p>Welcome, adult user!</p>
<%
    } else {
%>
        <p>Welcome, young user!</p>
<%
    }
%>

<%-- Loops --%>
<ul>
<%
    for (int i = 1; i <= 5; i++) {
%>
        <li>Item <%= i %></li>
<%
    }
%>
</ul>
```

### Mixing Scriptlets and HTML

```jsp
<table>
    <tr>
        <th>Name</th>
        <th>Price</th>
    </tr>
    <%
        List<Product> products = (List<Product>) request.getAttribute("products");
        for (Product p : products) {
    %>
    <tr>
        <td><%= p.getName() %></td>
        <td><%= p.getPrice() %></td>
    </tr>
    <%
        }
    %>
</table>
```

---

## 6. Comments

### JSP Comment (Hidden)

```jsp
<%-- This comment is NOT sent to the client --%>
<%-- Good for developer notes --%>
```

### HTML Comment (Visible)

```html
<!-- This comment IS sent to the client -->
<!-- Visible in page source -->
```

### Java Comment (inside scriptlet)

```jsp
<%
    // Single line comment
    
    /* Multi-line
       comment */
    
    String name = "test";
%>
```

### Comparison

| Type | Syntax | Visible to Client? |
|------|--------|-------------------|
| JSP Comment | `<%-- --%>` | No |
| HTML Comment | `<!-- -->` | Yes |
| Java Comment | `// /* */` | No |

---

## 7. Best Practices

### Avoid Scriptlets - Use JSTL/EL

```jsp
<%-- BAD: Scriptlet --%>
<%
    String name = request.getParameter("name");
    if (name != null && !name.isEmpty()) {
        out.println("Hello, " + name);
    } else {
        out.println("Hello, Guest");
    }
%>

<%-- GOOD: JSTL/EL --%>
<c:choose>
    <c:when test="${not empty param.name}">
        Hello, ${param.name}
    </c:when>
    <c:otherwise>
        Hello, Guest
    </c:otherwise>
</c:choose>
```

### Keep Logic in Servlets

```java
// Servlet (Controller)
protected void doGet(...) {
    List<User> users = userService.getAll();
    request.setAttribute("users", users);
    request.getRequestDispatcher("users.jsp").forward(request, response);
}
```

```jsp
<%-- JSP (View) - minimal code --%>
<c:forEach var="user" items="${users}">
    <p>${user.name}</p>
</c:forEach>
```

### Declaration Thread Safety

```jsp
<%!
    // WRONG: Instance variable for per-request data
    String userName;  // Shared between all users!
    
    // RIGHT: Use method parameters
    public String process(String name) {
        return name.toUpperCase();
    }
%>
```

---

## 8. Interview Questions

### Basic Level

**Q1: What are the three scripting elements?**
> 1. Declaration `<%! %>` - variables and methods
> 2. Expression `<%= %>` - output values
> 3. Scriptlet `<% %>` - Java code

**Q2: What's the difference between <%! %> and <% %>?**
| Declaration | Scriptlet |
|-------------|-----------|
| Instance level | Inside _jspService() |
| Variables shared | Variables per request |
| Can define methods | Only executable code |

**Q3: Why no semicolon in expressions?**
> Because `<%= expr %>` becomes `out.print(expr);` - the semicolon is added by the container.

### Intermediate Level

**Q4: What happens with this code?**
```jsp
<%! int count = 0; %>
<%= ++count %>
```
> The count variable is shared across all requests (instance level). Each request increments and displays it. This is NOT thread-safe!

**Q5: How do JSP comments differ from HTML comments?**
> JSP comments `<%-- --%>` are removed during translation - they never reach the client.
> HTML comments `<!-- -->` are output to the response and visible in page source.

**Q6: Where is declaration code placed in generated servlet?**
> Declaration code is placed at the class level (outside any method), making it instance-level code that's shared across requests.

### Advanced Level

**Q7: How would you create a thread-safe counter in JSP?**
```jsp
<%!
    private AtomicInteger counter = new AtomicInteger(0);
    
    public int getNextCount() {
        return counter.incrementAndGet();
    }
%>

<p>Visitor #<%= getNextCount() %></p>
```

**Q8: Why are scriptlets considered bad practice?**
> 1. Mix presentation with logic (violates MVC)
> 2. Hard to test (no unit testing)
> 3. Difficult to maintain
> 4. Not reusable
> 5. Better alternatives exist (JSTL, EL)

---

## Next Topic: [JSP Directives →](./22_JSP_Directives.md)
