# Forward and Include Actions - jsp:forward and jsp:include

## Table of Contents
1. [Introduction](#1-introduction)
2. [jsp:forward Action](#2-jspforward-action)
3. [jsp:include Action](#3-jspinclude-action)
4. [Comparison with Directives](#4-comparison-with-directives)
5. [Passing Parameters](#5-passing-parameters)
6. [Complete Code Examples](#6-complete-code-examples)
7. [Best Practices](#7-best-practices)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

The `jsp:forward` and `jsp:include` standard actions transfer control between resources dynamically at request time. They differ from directives in execution timing and flexibility.

---

## 2. jsp:forward Action

### Purpose

Forwards the entire request to another resource, transferring control completely.

### Syntax

```jsp
<%-- Simple forward --%>
<jsp:forward page="target.jsp"/>

<%-- Forward with parameters --%>
<jsp:forward page="target.jsp">
    <jsp:param name="key" value="value"/>
</jsp:forward>
```

### How It Works

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    jsp:forward BEHAVIOR                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Browser request to source.jsp                                              │
│       │                                                                     │
│       ▼                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐          │
│  │  source.jsp                                                   │          │
│  │  ┌────────────────────────────────────────────────────────┐  │          │
│  │  │ <html>                                                  │  │          │
│  │  │ <body>                                                  │  │          │
│  │  │   <h1>Some content</h1>  ← This is DISCARDED!          │  │          │
│  │  │   <jsp:forward page="target.jsp"/>                     │  │          │
│  │  │   <p>Never shown</p>     ← This never executes         │  │          │
│  │  │ </body>                                                 │  │          │
│  │  │ </html>                                                 │  │          │
│  │  └────────────────────────────────────────────────────────┘  │          │
│  └──────────────────────────────────────────────────────────────┘          │
│                          │                                                  │
│                          ▼ Forward (buffer cleared)                         │
│                                                                             │
│  ┌──────────────────────────────────────────────────────────────┐          │
│  │  target.jsp                                                   │          │
│  │  • Receives SAME request/response objects                    │          │
│  │  • Has access to all request attributes                      │          │
│  │  • Browser URL remains: source.jsp                           │          │
│  │  • Generates complete response                               │          │
│  └──────────────────────────────────────────────────────────────┘          │
│                          │                                                  │
│                          ▼                                                  │
│  Browser receives response (URL shows source.jsp)                          │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Key Points

- **Buffer is cleared** - Any content before forward is discarded
- **URL unchanged** - Browser still shows original URL
- **Same request** - Request attributes are preserved
- **One-way** - Control never returns to forwarding page
- **Must not commit** - Cannot forward after response is flushed

---

## 3. jsp:include Action

### Purpose

Includes the output of another resource at the current position, then continues.

### Syntax

```jsp
<%-- Simple include --%>
<jsp:include page="header.jsp"/>

<%-- Include with parameters --%>
<jsp:include page="sidebar.jsp">
    <jsp:param name="category" value="news"/>
</jsp:include>
```

### How It Works

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    jsp:include BEHAVIOR                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────────────────────────────────────────────────────────┐          │
│  │  main.jsp                                                     │          │
│  │  ┌────────────────────────────────────────────────────────┐  │          │
│  │  │ <html>                                                  │  │          │
│  │  │ <body>                                                  │  │          │
│  │  │   <jsp:include page="header.jsp"/>  ───────────┐       │  │          │
│  │  │                                                 │       │  │          │
│  │  │   ↓ (header output inserted here)             │       │  │          │
│  │  │                                                 │       │  │          │
│  │  │   <h1>Main Content</h1>  ← Continues here     │       │  │          │
│  │  │   <jsp:include page="footer.jsp"/>             │       │  │          │
│  │  │ </body>                                         │       │  │          │
│  │  │ </html>                                         │       │  │          │
│  │  └────────────────────────────────────────────────────────┘  │          │
│  └──────────────────────────────────────────────────────────────┘          │
│                                                        │                    │
│                    ┌───────────────────────────────────┘                   │
│                    │                                                        │
│                    ▼                                                        │
│  ┌──────────────────────────────────────────────────────────────┐          │
│  │  header.jsp                                                   │          │
│  │  • Executes independently                                    │          │
│  │  • Returns output to main.jsp                                │          │
│  │  • Can access passed parameters                              │          │
│  └──────────────────────────────────────────────────────────────┘          │
│                                                                             │
│  Final output combines all pages in order                                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Key Points

- **Output merged** - Included content appears at include position
- **Control returns** - Continues after include completes
- **Dynamic** - Executed at request time
- **Separate request** - Included page has its own processing
- **No buffer clearing** - Preserves existing output

---

## 4. Comparison with Directives

### Include: Directive vs Action

| Feature | <%@ include %> | <jsp:include> |
|---------|----------------|---------------|
| **Time** | Translation | Request |
| **Type** | Static | Dynamic |
| **Compilation** | Merged before | Separate |
| **Parameters** | Cannot pass | Can pass |
| **Change detection** | Needs recompile | Automatic |
| **Performance** | Faster | Slightly slower |
| **Use case** | Headers, footers | Dynamic content |

### Visual Difference

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    DIRECTIVE (Translation Time)                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  <%@ include file="header.html" %>                                          │
│                                                                             │
│  main.jsp + header.html  →  main_jsp.java  →  main_jsp.class               │
│      (merged at compile)                                                    │
│                                                                             │
│  • One servlet for both                                                     │
│  • Variables are shared                                                     │
│  • Fastest execution                                                        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                    ACTION (Request Time)                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  <jsp:include page="header.jsp"/>                                           │
│                                                                             │
│  main.jsp  →  main_jsp.class   (executes, calls header)                    │
│  header.jsp →  header_jsp.class (separate compilation)                     │
│                                                                             │
│  • Separate servlets                                                        │
│  • Variables NOT shared                                                     │
│  • More flexible                                                            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Passing Parameters

### Using jsp:param

```jsp
<%-- Forward with parameters --%>
<jsp:forward page="result.jsp">
    <jsp:param name="status" value="success"/>
    <jsp:param name="count" value="<%= items.size() %>"/>
</jsp:forward>

<%-- Include with parameters --%>
<jsp:include page="sidebar.jsp">
    <jsp:param name="section" value="products"/>
    <jsp:param name="highlight" value="true"/>
</jsp:include>
```

### Reading Parameters in Target

```jsp
<%-- In result.jsp or sidebar.jsp --%>
Status: ${param.status}
Count: ${param.count}

<%-- Or using scriptlet --%>
<% String status = request.getParameter("status"); %>
```

### Parameters Behavior

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    PARAMETER PASSING                                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Original URL: /source.jsp?existing=value                                   │
│                                                                             │
│  <jsp:forward page="target.jsp">                                            │
│      <jsp:param name="added" value="new"/>                                 │
│  </jsp:forward>                                                             │
│                                                                             │
│  In target.jsp:                                                             │
│  • ${param.existing}  →  "value"  (preserved!)                             │
│  • ${param.added}     →  "new"    (added)                                  │
│                                                                             │
│  Parameters are MERGED, not replaced!                                       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 6. Complete Code Examples

### Page Layout with Include

**layout.jsp:**
```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>My Website</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <%-- Include header dynamically --%>
    <jsp:include page="header.jsp">
        <jsp:param name="pageTitle" value="Home Page"/>
    </jsp:include>
    
    <main>
        <h1>Welcome to Our Site</h1>
        <p>Main content goes here...</p>
    </main>
    
    <%-- Include sidebar with section info --%>
    <jsp:include page="sidebar.jsp">
        <jsp:param name="activeSection" value="home"/>
    </jsp:include>
    
    <%-- Include footer --%>
    <jsp:include page="footer.jsp"/>
</body>
</html>
```

**header.jsp:**
```jsp
<header>
    <nav>
        <span class="logo">MySite</span>
        <span class="page-title">${param.pageTitle}</span>
        <ul>
            <li><a href="home">Home</a></li>
            <li><a href="products">Products</a></li>
            <li><a href="contact">Contact</a></li>
        </ul>
    </nav>
</header>
```

### Controller Pattern with Forward

**ControllerServlet.java:**
```java
@WebServlet("/process")
public class ControllerServlet extends HttpServlet {
    
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        String action = request.getParameter("action");
        
        // Process and set data
        if ("add".equals(action)) {
            request.setAttribute("result", "Item added successfully");
            request.setAttribute("status", "success");
        } else {
            request.setAttribute("result", "Unknown action");
            request.setAttribute("status", "error");
        }
        
        // Forward to view
        request.getRequestDispatcher("result.jsp").forward(request, response);
    }
}
```

**result.jsp:**
```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<body>
    <jsp:include page="header.jsp"/>
    
    <c:choose>
        <c:when test="${status == 'success'}">
            <div class="alert-success">${result}</div>
        </c:when>
        <c:otherwise>
            <div class="alert-error">${result}</div>
        </c:otherwise>
    </c:choose>
    
    <jsp:include page="footer.jsp"/>
</body>
</html>
```

### Conditional Forward

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

<c:choose>
    <c:when test="${empty sessionScope.user}">
        <jsp:forward page="login.jsp">
            <jsp:param name="redirect" value="dashboard.jsp"/>
        </jsp:forward>
    </c:when>
    <c:otherwise>
        <%-- User is logged in, show dashboard --%>
        <h1>Welcome, ${sessionScope.user.name}!</h1>
    </c:otherwise>
</c:choose>
```

---

## 7. Best Practices

### When to Use Forward

✅ MVC pattern (controller → view)  
✅ Error handling (redirect to error page)  
✅ Authentication checks  
✅ Form processing results  

### When to Use Include

✅ Page layouts (header, footer, sidebar)  
✅ Reusable components  
✅ Dynamic content sections  
✅ Menus with parameters  

### Common Mistakes

```jsp
<%-- WRONG: Content before forward is wasted --%>
<html>
<body>
<h1>Processing...</h1>
<jsp:forward page="result.jsp"/>  <%-- All above is discarded! --%>
</body>
</html>

<%-- RIGHT: Forward early or use include --%>
<%
    if (condition) {
%>
<jsp:forward page="result.jsp"/>
<%
    }
%>
<%-- Now show normal content --%>
```

---

## 8. Interview Questions

### Basic Level

**Q1: What is the difference between jsp:forward and jsp:include?**
| forward | include |
|---------|---------|
| Transfers control completely | Includes and returns |
| Buffer cleared | Content merged |
| One-way | Control returns |
| Like redirect (server-side) | Like embedding |

**Q2: Does the URL change with jsp:forward?**
> No! The browser URL remains unchanged. This is server-side forwarding.

**Q3: Can you pass parameters with these actions?**
> Yes, using `<jsp:param name="x" value="y"/>` inside the forward/include tags.

### Intermediate Level

**Q4: What happens to request attributes with jsp:forward?**
> They are preserved because the same request object is used.

**Q5: What is the difference between include directive and action?**
| Directive | Action |
|-----------|--------|
| Translation time | Request time |
| Static | Dynamic |
| Merged source | Separate compilation |
| Cannot pass params | Can pass params |

### Advanced Level

**Q6: What exception occurs if you forward after flushing output?**
> `IllegalStateException: Cannot forward after response has been committed`

**Q7: Can an included page set response headers?**
> No, only the main page should set response headers. The included page can only write body content.

---

## Related Notes
- [Redirect vs Forward →](../Day3/11_Redirect_Forward.md)
- [JSP Standard Actions →](./28_Standard_Actions.md)
