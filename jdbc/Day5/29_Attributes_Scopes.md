# Attributes in JSP - All Four Scopes

## Table of Contents
1. [Introduction](#1-introduction)
2. [Four Scopes Overview](#2-four-scopes-overview)
3. [Page Scope](#3-page-scope)
4. [Request Scope](#4-request-scope)
5. [Session Scope](#5-session-scope)
6. [Application Scope](#6-application-scope)
7. [Comparison Table](#7-comparison-table)
8. [Complete Code Examples](#8-complete-code-examples)
9. [Key Points to Remember](#9-key-points-to-remember)
10. [Interview Questions](#10-interview-questions)

---

## 1. Introduction

Attributes are objects stored at different scopes in a web application. Understanding scopes is crucial for proper data management - storing data at the correct scope ensures both functionality and performance.

---

## 2. Four Scopes Overview

### Visual Representation

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           SCOPE HIERARCHY                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                    APPLICATION SCOPE                                 │   │
│  │  Lifetime: Entire application (server running)                      │   │
│  │  Shared by: All users, all sessions, all requests                   │   │
│  │  Object: ServletContext                                             │   │
│  │                                                                      │   │
│  │  ┌────────────────────────────────────────────────────────────┐    │   │
│  │  │                   SESSION SCOPE                             │    │   │
│  │  │  Lifetime: User's session (login to logout/timeout)        │    │   │
│  │  │  Shared by: All requests from same user                    │    │   │
│  │  │  Object: HttpSession                                        │    │   │
│  │  │                                                             │    │   │
│  │  │  ┌───────────────────────────────────────────────────┐    │    │   │
│  │  │  │               REQUEST SCOPE                        │    │    │   │
│  │  │  │  Lifetime: Single HTTP request                     │    │    │   │
│  │  │  │  Shared by: Forwarded pages (dispatcher)           │    │    │   │
│  │  │  │  Object: HttpServletRequest                        │    │    │   │
│  │  │  │                                                    │    │    │   │
│  │  │  │  ┌──────────────────────────────────────────┐    │    │    │   │
│  │  │  │  │           PAGE SCOPE                      │    │    │    │   │
│  │  │  │  │  Lifetime: Single JSP page                │    │    │    │   │
│  │  │  │  │  Shared by: Nothing (most restricted)    │    │    │    │   │
│  │  │  │  │  Object: PageContext                      │    │    │    │   │
│  │  │  │  └──────────────────────────────────────────┘    │    │    │   │
│  │  │  └───────────────────────────────────────────────────┘    │    │   │
│  │  └────────────────────────────────────────────────────────────┘    │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Page Scope

### Characteristics

| Property | Value |
|----------|-------|
| **Object** | PageContext |
| **Lifetime** | Current JSP page only |
| **Visibility** | Only within that page |
| **Use Case** | Temporary calculations, local variables |

### How to Use

```jsp
<%
    // Set attribute in page scope
    pageContext.setAttribute("localVar", "Page Value");
    
    // Get attribute from page scope
    String value = (String) pageContext.getAttribute("localVar");
%>

<%-- With EL --%>
${pageScope.localVar}
```

### Key Points

- Most restrictive scope
- Lost after page execution completes
- Not available to included/forwarded pages
- Good for temporary data within single page

---

## 4. Request Scope

### Characteristics

| Property | Value |
|----------|-------|
| **Object** | HttpServletRequest |
| **Lifetime** | Single HTTP request |
| **Visibility** | Original page + forwarded pages |
| **Use Case** | Data transfer between servlet and JSP |

### How to Use

```java
// In Servlet
request.setAttribute("userData", user);
RequestDispatcher rd = request.getRequestDispatcher("display.jsp");
rd.forward(request, response);
```

```jsp
<%-- In JSP --%>
<%
    // Set attribute
    request.setAttribute("message", "Hello from JSP");
    
    // Get attribute
    String msg = (String) request.getAttribute("message");
%>

<%-- With EL --%>
${requestScope.message}
${userData.name}  <%-- Auto-searches scopes --%>
```

### Request Scope with Forward

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    REQUEST SCOPE PRESERVATION                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Browser → Servlet A → forward → JSP B                                      │
│                                                                             │
│  ┌────────────┐       ┌────────────┐       ┌────────────┐                 │
│  │  Request   │ ────► │ Servlet A  │ ────► │   JSP B    │                 │
│  │            │       │            │       │            │                 │
│  │ userData=  │       │ Adds more  │       │ Can access │                 │
│  │ [object]   │       │ attributes │       │ ALL attrs  │                 │
│  └────────────┘       └────────────┘       └────────────┘                 │
│                                                                             │
│  Same request object throughout → attributes preserved                      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Request vs Redirect

```
FORWARD (Request scope preserved):
Servlet → forward → JSP (SAME request object, attrs work)

REDIRECT (Request scope LOST):
Servlet → redirect → Browser → new request → JSP (NEW request object!)
```

---

## 5. Session Scope

### Characteristics

| Property | Value |
|----------|-------|
| **Object** | HttpSession |
| **Lifetime** | User session duration |
| **Visibility** | All requests from same user |
| **Use Case** | User login data, shopping cart |

### How to Use

```java
// In Servlet
HttpSession session = request.getSession();
session.setAttribute("loggedInUser", user);
session.setAttribute("cartItems", cart);
```

```jsp
<%-- In JSP --%>
<%
    // Set attribute
    session.setAttribute("preference", "dark-mode");
    
    // Get attribute
    String pref = (String) session.getAttribute("preference");
%>

<%-- With EL --%>
${sessionScope.loggedInUser.name}
${sessionScope.cartItems}
```

### Session Scope Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    SESSION SCOPE PERSISTENCE                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  USER A (one session):                                                      │
│                                                                             │
│  Request 1: Login                                                           │
│  ┌────────────┐                                                            │
│  │ session.setAttribute("user", userA);                                    │
│  └────────────┘                                                            │
│         │                                                                   │
│         ▼                                                                   │
│  Request 2: View Products (different page, same session)                   │
│  ┌────────────┐                                                            │
│  │ ${sessionScope.user.name} → "User A" ✓                                 │
│  └────────────┘                                                            │
│         │                                                                   │
│         ▼                                                                   │
│  Request 3: Checkout (different page, same session)                        │
│  ┌────────────┐                                                            │
│  │ ${sessionScope.user.name} → "User A" ✓                                 │
│  └────────────┘                                                            │
│                                                                             │
│  All requests see the same session attributes                              │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 6. Application Scope

### Characteristics

| Property | Value |
|----------|-------|
| **Object** | ServletContext |
| **Lifetime** | Application lifetime (server restart clears) |
| **Visibility** | All users, all sessions |
| **Use Case** | Global configuration, visitor counter |

### How to Use

```java
// In Servlet
ServletContext context = getServletContext();
context.setAttribute("siteConfig", config);
context.setAttribute("visitorCount", count);
```

```jsp
<%-- In JSP --%>
<%
    // Set attribute
    application.setAttribute("appName", "MyWebApp");
    
    // Get attribute
    String name = (String) application.getAttribute("appName");
%>

<%-- With EL --%>
${applicationScope.siteConfig}
${applicationScope.visitorCount}
```

### Application Scope - Global Data

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    APPLICATION SCOPE - SHARED BY ALL                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│                     Application (ServletContext)                            │
│                     ┌──────────────────────────┐                           │
│                     │ visitorCount = 1500      │                           │
│                     │ dbConfig = {...}         │                           │
│                     │ appVersion = "2.0"       │                           │
│                     └──────────────────────────┘                           │
│                              ▲                                              │
│           ┌──────────────────┼──────────────────┐                          │
│           │                  │                  │                          │
│     ┌─────┴─────┐     ┌─────┴─────┐     ┌─────┴─────┐                     │
│     │  User A   │     │  User B   │     │  User C   │                     │
│     │  Session  │     │  Session  │     │  Session  │                     │
│     └───────────┘     └───────────┘     └───────────┘                     │
│                                                                             │
│  All users see: ${applicationScope.visitorCount} → 1500                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 7. Comparison Table

| Scope | Object | Lifetime | Visibility | Use Case |
|-------|--------|----------|------------|----------|
| **Page** | PageContext | Single page | Current page only | Temporary |
| **Request** | HttpServletRequest | Single request | + forwarded pages | MVC data transfer |
| **Session** | HttpSession | User session | All user's requests | User data, cart |
| **Application** | ServletContext | App lifetime | All users | Global config |

### EL Implicit Objects

| Scope | EL Object | Example |
|-------|-----------|---------|
| Page | `pageScope` | `${pageScope.var}` |
| Request | `requestScope` | `${requestScope.var}` |
| Session | `sessionScope` | `${sessionScope.var}` |
| Application | `applicationScope` | `${applicationScope.var}` |

---

## 8. Complete Code Examples

### AllScopesDemo.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>All Scopes Demo</title>
</head>
<body>
    <h2>Setting Attributes in All Scopes</h2>
    
    <%
        // Page Scope - only this page
        pageContext.setAttribute("pageVar", "I'm in PAGE scope");
        
        // Request Scope - this request (including forwards)
        request.setAttribute("requestVar", "I'm in REQUEST scope");
        
        // Session Scope - this user's session
        session.setAttribute("sessionVar", "I'm in SESSION scope");
        
        // Application Scope - entire application
        application.setAttribute("appVar", "I'm in APPLICATION scope");
    %>
    
    <h3>Reading with JSP Expressions</h3>
    <p>Page: <%= pageContext.getAttribute("pageVar") %></p>
    <p>Request: <%= request.getAttribute("requestVar") %></p>
    <p>Session: <%= session.getAttribute("sessionVar") %></p>
    <p>Application: <%= application.getAttribute("appVar") %></p>
    
    <h3>Reading with EL (Explicit Scope)</h3>
    <p>Page: ${pageScope.pageVar}</p>
    <p>Request: ${requestScope.requestVar}</p>
    <p>Session: ${sessionScope.sessionVar}</p>
    <p>Application: ${applicationScope.appVar}</p>
    
    <h3>Reading with EL (Auto-Search)</h3>
    <p>pageVar: ${pageVar}</p>
    <p>requestVar: ${requestVar}</p>
    <p>sessionVar: ${sessionVar}</p>
    <p>appVar: ${appVar}</p>
    
    <a href="checkScopes.jsp">Check Scopes on Another Page</a>
</body>
</html>
```

### checkScopes.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>Check Scopes</title>
</head>
<body>
    <h2>Checking Scopes from Another Page</h2>
    
    <p>Page scope pageVar: ${pageScope.pageVar}</p>
    <%-- Will be empty - page scope doesn't persist! --%>
    
    <p>Request scope requestVar: ${requestScope.requestVar}</p>
    <%-- Will be empty - new request (redirect/link), not forward --%>
    
    <p>Session scope sessionVar: ${sessionScope.sessionVar}</p>
    <%-- Will work - same user session! --%>
    
    <p>Application scope appVar: ${applicationScope.appVar}</p>
    <%-- Will work - application-wide! --%>
    
    <h3>Summary:</h3>
    <ul>
        <li>Page Scope: <strong>NOT available</strong> (page ended)</li>
        <li>Request Scope: <strong>NOT available</strong> (new request)</li>
        <li>Session Scope: <strong>AVAILABLE</strong> (same session)</li>
        <li>Application Scope: <strong>AVAILABLE</strong> (always)</li>
    </ul>
</body>
</html>
```

### Request Scope with Forward Example

#### ControllerServlet.java

```java
@WebServlet("/process")
public class ControllerServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // Set data in request scope
        request.setAttribute("message", "Hello from Controller!");
        request.setAttribute("timestamp", System.currentTimeMillis());
        
        // Forward to JSP - request scope preserved!
        RequestDispatcher rd = request.getRequestDispatcher("view.jsp");
        rd.forward(request, response);
    }
}
```

#### view.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html>
<body>
    <h2>View Page</h2>
    
    <%-- These work because we forwarded (same request) --%>
    <p>Message: ${requestScope.message}</p>
    <p>Timestamp: ${requestScope.timestamp}</p>
</body>
</html>
```

---

## 9. Key Points to Remember

1. **Page scope is most restrictive** - Only current page

2. **Request scope survives forwards** - But NOT redirects

3. **Session scope is per-user** - Don't store too much (memory)

4. **Application scope is global** - Thread-safety concerns!

5. **EL auto-searches** - page → request → session → application

6. **Redirect kills request scope** - New request = new object

7. **Session timeout** - Default 30 minutes of inactivity

8. **Application scope thread-safe?** - NO! Use synchronized access

9. **Memory considerations** - Session/Application can leak memory

10. **Choose smallest scope** - Only use larger scope when needed

---

## 10. Interview Questions

### Basic Level

**Q1: What are the four scopes in JSP?**
> Page, Request, Session, and Application. They differ in lifetime and visibility.

**Q2: Which scope is lost after redirect?**
> Request scope is lost because redirect creates a new HTTP request with a new request object.

**Q3: How do you set an attribute in session scope?**
```java
session.setAttribute("key", value);
// Or in JSP
<% session.setAttribute("key", value); %>
```

### Intermediate Level

**Q4: When would you use request scope vs session scope?**
| Request Scope | Session Scope |
|--------------|---------------|
| One-time data transfer | Persistent user data |
| Servlet to JSP (forward) | Login information |
| Error messages | Shopping cart |
| Search results | User preferences |

**Q5: Is application scope thread-safe?**
> No! Application scope (ServletContext) is shared by all users and threads. You must synchronize access when modifying attributes:
```java
synchronized(application) {
    Integer count = (Integer) application.getAttribute("count");
    application.setAttribute("count", count + 1);
}
```

**Q6: What is the EL search order for ${myVar}?**
> Page → Request → Session → Application
> First match is returned.

### Advanced Level

**Q7: How do you pass data after redirect?**
> Since request scope is lost:
> 1. Use session scope (temporary)
> 2. Use URL parameters
> 3. Use Flash scope (Spring MVC)
```java
// Method 1: Session (remember to remove)
session.setAttribute("message", "Saved!");
response.sendRedirect("success.jsp");
// In success.jsp: remove after reading

// Method 2: URL parameter
response.sendRedirect("success.jsp?msg=Saved");
```

**Q8: What happens to session scope when session times out?**
> All session attributes are destroyed. The session object becomes invalid. Next request creates a new session with no attributes.

**Q9: How would you implement a visitor counter using application scope?**
```java
// In ServletContextListener
public void contextInitialized(ServletContextEvent sce) {
    ServletContext ctx = sce.getServletContext();
    ctx.setAttribute("visitorCount", new AtomicInteger(0));
}

// In Servlet
AtomicInteger count = (AtomicInteger) application.getAttribute("visitorCount");
int newCount = count.incrementAndGet();
```

---

## Next Topic: [WAR File Deployment →](./35_WAR_File_Deployment.md)
