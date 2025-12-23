# JSP Implicit Objects - Complete Guide

## Table of Contents
1. [Introduction](#1-introduction)
2. [All Nine Implicit Objects](#2-all-nine-implicit-objects)
3. [Request and Response](#3-request-and-response)
4. [Session and Application](#4-session-and-application)
5. [PageContext](#5-pagecontext)
6. [Out, Config, Page, Exception](#6-out-config-page-exception)
7. [Scope Objects Summary](#7-scope-objects-summary)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

Implicit objects are pre-defined variables available in every JSP without explicit declaration. The container creates these objects and makes them available in `_jspService()`.

---

## 2. All Nine Implicit Objects

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    JSP IMPLICIT OBJECTS                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Object        Type                          Scope        Purpose           │
│  ────────────────────────────────────────────────────────────────────       │
│  request       HttpServletRequest            Request      Client request    │
│  response      HttpServletResponse           Page         Server response   │
│  session       HttpSession                   Session      User session      │
│  application   ServletContext                Application  Entire app        │
│  out           JspWriter                     Page         Output stream     │
│  config        ServletConfig                 Page         Servlet config    │
│  pageContext   PageContext                   Page         Context wrapper   │
│  page          Object (this)                 Page         Current servlet   │
│  exception     Throwable                     Page         Error info        │
│                                                                             │
│  Note: exception only available when isErrorPage="true"                     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Quick Reference Table

| Object | Type | Get Attribute | Set Attribute |
|--------|------|---------------|---------------|
| `request` | HttpServletRequest | `getAttribute()` | `setAttribute()` |
| `session` | HttpSession | `getAttribute()` | `setAttribute()` |
| `application` | ServletContext | `getAttribute()` | `setAttribute()` |
| `pageContext` | PageContext | `getAttribute()` | `setAttribute()` |

---

## 3. Request and Response

### request Object

```jsp
<%-- Get parameters --%>
<%= request.getParameter("name") %>
<%= request.getParameter("age") %>

<%-- Get all parameter names --%>
<%
    java.util.Enumeration<String> params = request.getParameterNames();
    while (params.hasMoreElements()) {
        String name = params.nextElement();
        out.println(name + ": " + request.getParameter(name));
    }
%>

<%-- Get request info --%>
Method: <%= request.getMethod() %>
URI: <%= request.getRequestURI() %>
Context Path: <%= request.getContextPath() %>
Remote Address: <%= request.getRemoteAddr() %>

<%-- Get/Set attributes --%>
<%
    request.setAttribute("user", userObject);
    User user = (User) request.getAttribute("user");
%>

<%-- Get headers --%>
User-Agent: <%= request.getHeader("User-Agent") %>
Accept: <%= request.getHeader("Accept") %>
```

### response Object

```jsp
<%-- Set content type --%>
<%
    response.setContentType("text/html;charset=UTF-8");
%>

<%-- Redirect --%>
<%
    response.sendRedirect("other.jsp");
%>

<%-- Add headers --%>
<%
    response.setHeader("Cache-Control", "no-cache");
    response.addHeader("Custom-Header", "value");
%>

<%-- Add cookies --%>
<%
    Cookie cookie = new Cookie("username", "john");
    cookie.setMaxAge(3600);  // 1 hour
    response.addCookie(cookie);
%>
```

---

## 4. Session and Application

### session Object

```jsp
<%-- Requires session="true" (default) --%>
<%@ page session="true" %>

<%-- Store in session --%>
<%
    session.setAttribute("username", "john");
    session.setAttribute("cart", shoppingCart);
%>

<%-- Retrieve from session --%>
Username: <%= session.getAttribute("username") %>
<%
    ShoppingCart cart = (ShoppingCart) session.getAttribute("cart");
%>

<%-- Session info --%>
Session ID: <%= session.getId() %>
Creation Time: <%= new java.util.Date(session.getCreationTime()) %>
Last Accessed: <%= new java.util.Date(session.getLastAccessedTime()) %>
Is New: <%= session.isNew() %>

<%-- Invalidate session --%>
<%
    session.invalidate();
%>
```

### application Object

```jsp
<%-- Store application-wide data --%>
<%
    application.setAttribute("visitorCount", 0);
    
    // Thread-safe increment
    synchronized(application) {
        Integer count = (Integer) application.getAttribute("visitorCount");
        application.setAttribute("visitorCount", count + 1);
    }
%>

<%-- Get init parameters (from web.xml context-param) --%>
DB URL: <%= application.getInitParameter("dbUrl") %>
App Name: <%= application.getInitParameter("appName") %>

<%-- Get real path --%>
Real Path: <%= application.getRealPath("/") %>
Server Info: <%= application.getServerInfo() %>
```

---

## 5. PageContext

PageContext is the most powerful implicit object - it provides access to all other implicit objects and all scopes.

### Access Other Implicit Objects

```jsp
<%
    HttpServletRequest req = (HttpServletRequest) pageContext.getRequest();
    HttpServletResponse res = (HttpServletResponse) pageContext.getResponse();
    HttpSession sess = pageContext.getSession();
    ServletContext app = pageContext.getServletContext();
    JspWriter writer = pageContext.getOut();
%>
```

### Scope Constants

```java
PageContext.PAGE_SCOPE        // 1
PageContext.REQUEST_SCOPE     // 2
PageContext.SESSION_SCOPE     // 3
PageContext.APPLICATION_SCOPE // 4
```

### Get/Set Attributes in Any Scope

```jsp
<%
    // Set in specific scope
    pageContext.setAttribute("var", value, PageContext.PAGE_SCOPE);
    pageContext.setAttribute("var", value, PageContext.REQUEST_SCOPE);
    pageContext.setAttribute("var", value, PageContext.SESSION_SCOPE);
    pageContext.setAttribute("var", value, PageContext.APPLICATION_SCOPE);
    
    // Get from specific scope
    Object val = pageContext.getAttribute("var", PageContext.SESSION_SCOPE);
    
    // Find in all scopes (page → request → session → application)
    Object found = pageContext.findAttribute("var");
%>
```

### Forward and Include

```jsp
<%
    // Forward to another resource
    pageContext.forward("other.jsp");
    
    // Include another resource
    pageContext.include("header.jsp");
%>
```

---

## 6. Out, Config, Page, Exception

### out Object (JspWriter)

```jsp
<%-- Output text --%>
<% out.print("Hello"); %>
<% out.println("World"); %>
<% out.write("Data"); %>

<%-- Buffer info --%>
Buffer Size: <%= out.getBufferSize() %>
Remaining: <%= out.getRemaining() %>

<%-- Clear buffer --%>
<%
    out.clearBuffer();  // Clear but continue
    // out.clear();     // Clear and throw exception if already flushed
%>

<%-- Flush output --%>
<% out.flush(); %>
```

### config Object (ServletConfig)

```jsp
<%-- Get servlet name --%>
Servlet Name: <%= config.getServletName() %>

<%-- Get init parameters --%>
<%
    String param = config.getInitParameter("paramName");
%>

<%-- Get servlet context --%>
<%
    ServletContext ctx = config.getServletContext();
%>
```

### page Object

```jsp
<%-- Reference to current servlet instance (this) --%>
Page Class: <%= page.getClass().getName() %>

<%-- Usually not used directly --%>
<%
    // page == this
    // page is of type Object (for compatibility)
%>
```

### exception Object

```jsp
<%@ page isErrorPage="true" %>

<%-- Only available when isErrorPage="true" --%>
Exception Type: <%= exception.getClass().getName() %>
Message: <%= exception.getMessage() %>

<%-- Print stack trace --%>
<pre>
<% exception.printStackTrace(new java.io.PrintWriter(out)); %>
</pre>
```

---

## 7. Scope Objects Summary

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    SCOPE AND IMPLICIT OBJECTS                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  SCOPE          IMPLICIT OBJECT    LIFETIME                                 │
│  ─────────────────────────────────────────────────────────────              │
│                                                                             │
│  Page           pageContext        Single page (one JSP)                    │
│                                    Attributes lost on page end              │
│                                                                             │
│  Request        request            Single HTTP request                      │
│                                    Survives include/forward                 │
│                                    Lost on redirect                         │
│                                                                             │
│  Session        session            User session                             │
│                                    Login to logout/timeout                  │
│                                                                             │
│  Application    application        Entire application                       │
│                                    Server start to shutdown                 │
│                                    Shared by all users                      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### EL Access to Implicit Objects

| JSP Implicit | EL Equivalent |
|--------------|---------------|
| `pageContext` | `${pageContext}` |
| `request.getParameter("x")` | `${param.x}` |
| `request.getParameterValues("x")` | `${paramValues.x}` |
| `request.getHeader("x")` | `${header.x}` |
| `request.getCookies()` | `${cookie.name}` |
| Scope attributes | `${pageScope.x}`, `${requestScope.x}`, etc. |

---

## 8. Interview Questions

### Basic Level

**Q1: How many implicit objects are there in JSP?**
> Nine: request, response, session, application, out, config, pageContext, page, and exception.

**Q2: When is the exception object available?**
> Only when `isErrorPage="true"` is set in the page directive.

**Q3: What is the type of the 'out' object?**
> `JspWriter` - a subclass of `PrintWriter` with buffering support.

### Intermediate Level

**Q4: What is the difference between pageContext and other implicit objects?**
> PageContext is a wrapper that provides access to all other implicit objects and can access all scopes. It's the most versatile implicit object.

**Q5: How do you access session in EL?**
```jsp
<%-- Store --%>
<% session.setAttribute("user", "John"); %>

<%-- Access with EL --%>
${sessionScope.user}
```

**Q6: What's the difference between out.print() and out.println()?**
> `println()` adds a newline character after the output; `print()` does not. In HTML, both appear the same since browsers ignore newlines.

### Advanced Level

**Q7: How does pageContext.findAttribute() work?**
> It searches for an attribute in all scopes in order:
> 1. Page scope
> 2. Request scope
> 3. Session scope
> 4. Application scope
> 
> Returns the first match or null if not found.

**Q8: How do you disable session in JSP?**
```jsp
<%@ page session="false" %>
<%
    // session implicit object will NOT be available
    // Trying to use it causes compilation error
%>
```

**Q9: What implicit objects correspond to each scope?**
| Scope | Implicit Object | Type |
|-------|----------------|------|
| Page | pageContext | PageContext |
| Request | request | HttpServletRequest |
| Session | session | HttpSession |
| Application | application | ServletContext |

---

## Next Topic: [Attributes in Servlet →](./24_Attributes_Servlet.md)
