# Error Handling in JSP

## Table of Contents
1. [Introduction](#1-introduction)
2. [Types of Errors](#2-types-of-errors)
3. [Page-Level Error Handling](#3-page-level-error-handling)
4. [Application-Level Error Handling](#4-application-level-error-handling)
5. [Common Error Pages](#5-common-error-pages)
6. [Complete Code Examples](#6-complete-code-examples)
7. [Key Points to Remember](#7-key-points-to-remember)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

Proper error handling is crucial for user experience and debugging. JSP provides multiple mechanisms to handle errors gracefully, from page-specific handling to application-wide error pages.

---

## 2. Types of Errors

### Error Categories

| Type | Description | Example |
|------|-------------|---------|
| **Translation Errors** | Syntax errors in JSP | `<% int x = %>`  |
| **Compilation Errors** | Java compilation fails | Type mismatch |
| **Runtime Exceptions** | Errors during execution | NullPointerException |
| **HTTP Errors** | Client/server errors | 404, 500 |

### Error Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ERROR HANDLING FLOW                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Exception occurs in page.jsp                                               │
│           │                                                                 │
│           ▼                                                                 │
│  ┌─────────────────────────────────────┐                                   │
│  │ Does page.jsp have errorPage attr? │                                   │
│  └─────────────────┬───────────────────┘                                   │
│           │                                                                 │
│     ┌─────┴─────┐                                                          │
│     ▼           ▼                                                          │
│   [YES]       [NO]                                                         │
│     │           │                                                          │
│     │           ▼                                                          │
│     │    ┌─────────────────────────────────────┐                          │
│     │    │ Is there web.xml error-page config?│                          │
│     │    └─────────────────┬───────────────────┘                          │
│     │           │                                                          │
│     │     ┌─────┴─────┐                                                   │
│     │     ▼           ▼                                                   │
│     │   [YES]       [NO]                                                  │
│     │     │           │                                                   │
│     ▼     ▼           ▼                                                   │
│  Forward to    Forward to    Container's                                   │
│  specified     web.xml       default error                                │
│  errorPage     error page    page (ugly!)                                 │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Page-Level Error Handling

### Using errorPage Directive

In the page where error might occur:

```jsp
<%@ page errorPage="error.jsp" %>

<%
    // This might throw an exception
    String str = null;
    str.length();  // NullPointerException!
%>
```

### Creating the Error Page

In error.jsp (the error handler):

```jsp
<%@ page isErrorPage="true" %>

<!DOCTYPE html>
<html>
<head>
    <title>Error Occurred</title>
</head>
<body>
    <h1>Oops! Something went wrong.</h1>
    
    <%-- 'exception' implicit object is only available when isErrorPage="true" --%>
    <p>Error: <%= exception.getMessage() %></p>
    
    <%-- More details (for development only) --%>
    <pre>
    <% exception.printStackTrace(new java.io.PrintWriter(out)); %>
    </pre>
</body>
</html>
```

### Key Attributes

| Attribute | Where Used | Purpose |
|-----------|------------|---------|
| `errorPage="error.jsp"` | Any JSP | Specifies which page handles errors |
| `isErrorPage="true"` | Error page | Enables `exception` implicit object |

---

## 4. Application-Level Error Handling

### web.xml Configuration

#### By Exception Type

```xml
<web-app>
    <!-- Handle specific exception types -->
    <error-page>
        <exception-type>java.lang.NullPointerException</exception-type>
        <location>/errors/null-error.jsp</location>
    </error-page>
    
    <error-page>
        <exception-type>java.sql.SQLException</exception-type>
        <location>/errors/database-error.jsp</location>
    </error-page>
    
    <!-- Catch-all for any exception -->
    <error-page>
        <exception-type>java.lang.Exception</exception-type>
        <location>/errors/general-error.jsp</location>
    </error-page>
</web-app>
```

#### By HTTP Error Code

```xml
<web-app>
    <!-- 404 Not Found -->
    <error-page>
        <error-code>404</error-code>
        <location>/errors/404.jsp</location>
    </error-page>
    
    <!-- 403 Forbidden -->
    <error-page>
        <error-code>403</error-code>
        <location>/errors/403.jsp</location>
    </error-page>
    
    <!-- 500 Internal Server Error -->
    <error-page>
        <error-code>500</error-code>
        <location>/errors/500.jsp</location>
    </error-page>
</web-app>
```

---

## 5. Common Error Pages

### Professional 404 Page

```jsp
<%@ page isErrorPage="true" contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>Page Not Found</title>
    <style>
        body { 
            font-family: Arial, sans-serif; 
            text-align: center; 
            padding: 50px;
            background: #f5f5f5;
        }
        .error-code { 
            font-size: 100px; 
            color: #e74c3c; 
            margin: 0;
        }
        .message { 
            font-size: 24px; 
            color: #333; 
        }
        .back-link { 
            margin-top: 20px; 
        }
        a { 
            color: #3498db; 
            text-decoration: none; 
        }
    </style>
</head>
<body>
    <h1 class="error-code">404</h1>
    <p class="message">Oops! The page you're looking for doesn't exist.</p>
    <p>The requested URL was not found on this server.</p>
    <div class="back-link">
        <a href="${pageContext.request.contextPath}/">Go to Homepage</a>
    </div>
</body>
</html>
```

### Professional 500 Page

```jsp
<%@ page isErrorPage="true" contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>Server Error</title>
    <style>
        body { 
            font-family: Arial, sans-serif; 
            text-align: center; 
            padding: 50px;
            background: #f5f5f5;
        }
        .error-code { 
            font-size: 100px; 
            color: #e74c3c; 
            margin: 0;
        }
        .message { 
            font-size: 24px; 
            color: #333; 
        }
    </style>
</head>
<body>
    <h1 class="error-code">500</h1>
    <p class="message">Internal Server Error</p>
    <p>Something went wrong on our end. We're working to fix it.</p>
    
    <%-- Only show details in development --%>
    <%
        String env = application.getInitParameter("environment");
        if ("development".equals(env) && exception != null) {
    %>
        <div style="text-align: left; background: #fff; padding: 20px; margin: 20px;">
            <h3>Debug Info:</h3>
            <p><strong>Exception:</strong> <%= exception.getClass().getName() %></p>
            <p><strong>Message:</strong> <%= exception.getMessage() %></p>
            <pre><% exception.printStackTrace(new java.io.PrintWriter(out)); %></pre>
        </div>
    <% } %>
    
    <p><a href="${pageContext.request.contextPath}/">Return to Homepage</a></p>
</body>
</html>
```

---

## 6. Complete Code Examples

### main.jsp - Page with Error Handling

```jsp
<%@ page errorPage="myerror.jsp" contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>Main Page</title>
</head>
<body>
    <h2>Division Calculator</h2>
    
    <%
        String num1Str = request.getParameter("num1");
        String num2Str = request.getParameter("num2");
        
        if (num1Str != null && num2Str != null) {
            int num1 = Integer.parseInt(num1Str);
            int num2 = Integer.parseInt(num2Str);
            int result = num1 / num2;  // May throw ArithmeticException!
    %>
            <p>Result: <%= num1 %> / <%= num2 %> = <%= result %></p>
    <%
        } else {
    %>
        <form>
            Number 1: <input type="text" name="num1"><br><br>
            Number 2: <input type="text" name="num2"><br><br>
            <input type="submit" value="Calculate">
        </form>
    <% } %>
</body>
</html>
```

### myerror.jsp - Error Handler

```jsp
<%@ page isErrorPage="true" contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>Error</title>
    <style>
        .error-box {
            background: #ffebee;
            border: 1px solid #f44336;
            padding: 20px;
            border-radius: 5px;
            max-width: 600px;
            margin: 50px auto;
        }
        .error-title {
            color: #c62828;
        }
    </style>
</head>
<body>
    <div class="error-box">
        <h2 class="error-title">An Error Occurred</h2>
        
        <p><strong>Error Type:</strong> <%= exception.getClass().getName() %></p>
        <p><strong>Message:</strong> <%= exception.getMessage() %></p>
        
        <%-- Check for specific exception types --%>
        <%
            if (exception instanceof ArithmeticException) {
        %>
            <p style="color: #1976d2;">
                <strong>Tip:</strong> You cannot divide by zero!
            </p>
        <%
            } else if (exception instanceof NumberFormatException) {
        %>
            <p style="color: #1976d2;">
                <strong>Tip:</strong> Please enter valid numbers only.
            </p>
        <% } %>
        
        <p><a href="main.jsp">Try Again</a></p>
    </div>
</body>
</html>
```

### Complete web.xml Error Configuration

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         version="4.0">
    
    <!-- Environment setting for error page detail level -->
    <context-param>
        <param-name>environment</param-name>
        <param-value>development</param-value>
    </context-param>
    
    <!-- HTTP Error Pages -->
    <error-page>
        <error-code>400</error-code>
        <location>/errors/400.jsp</location>
    </error-page>
    
    <error-page>
        <error-code>401</error-code>
        <location>/errors/401.jsp</location>
    </error-page>
    
    <error-page>
        <error-code>403</error-code>
        <location>/errors/403.jsp</location>
    </error-page>
    
    <error-page>
        <error-code>404</error-code>
        <location>/errors/404.jsp</location>
    </error-page>
    
    <error-page>
        <error-code>500</error-code>
        <location>/errors/500.jsp</location>
    </error-page>
    
    <!-- Exception-Based Error Pages -->
    <error-page>
        <exception-type>java.lang.NullPointerException</exception-type>
        <location>/errors/null-error.jsp</location>
    </error-page>
    
    <error-page>
        <exception-type>java.sql.SQLException</exception-type>
        <location>/errors/db-error.jsp</location>
    </error-page>
    
    <!-- Catch-all for unhandled exceptions -->
    <error-page>
        <exception-type>java.lang.Throwable</exception-type>
        <location>/errors/general-error.jsp</location>
    </error-page>
    
</web-app>
```

---

## 7. Key Points to Remember

1. **errorPage specifies handler** - `<%@ page errorPage="error.jsp" %>`

2. **isErrorPage enables exception** - Must be `true` to use implicit `exception` object

3. **web.xml for app-wide errors** - Applies to all pages

4. **Page directive takes priority** - Over web.xml for that page

5. **HTTP codes for client errors** - 404, 403, etc.

6. **Exception types for runtime errors** - NullPointer, SQLException, etc.

7. **Throwable catches all** - Use as fallback

8. **Hide details in production** - Don't expose stack traces to users

9. **Log errors server-side** - Even if showing friendly message to user

10. **Test error pages** - Make sure they work before going live

---

## 8. Interview Questions

### Basic Level

**Q1: How do you specify an error page for a JSP?**
> Use the errorPage attribute in the page directive:
```jsp
<%@ page errorPage="error.jsp" %>
```

**Q2: What is the purpose of isErrorPage?**
> When set to `true`, it enables the `exception` implicit object, allowing access to the exception that caused the error.

**Q3: How do you access the exception in an error page?**
```jsp
<%@ page isErrorPage="true" %>
<p>Error: <%= exception.getMessage() %></p>
```

### Intermediate Level

**Q4: What is the difference between error handling in page directive vs web.xml?**
| Page Directive | web.xml |
|----------------|---------|
| Page-specific | Application-wide |
| Specified in JSP | Central configuration |
| Takes priority | Fallback |
| For specific pages | For all pages |

**Q5: How do you handle 404 errors?**
```xml
<error-page>
    <error-code>404</error-code>
    <location>/errors/notfound.jsp</location>
</error-page>
```

**Q6: Can you handle different exceptions differently?**
> Yes, using web.xml with multiple exception-type entries:
```xml
<error-page>
    <exception-type>java.sql.SQLException</exception-type>
    <location>/errors/db-error.jsp</location>
</error-page>

<error-page>
    <exception-type>java.lang.NullPointerException</exception-type>
    <location>/errors/null-error.jsp</location>
</error-page>
```

### Advanced Level

**Q7: What request attributes are available in error page?**
> Container sets these attributes:
> - `javax.servlet.error.status_code` - HTTP status code
> - `javax.servlet.error.exception_type` - Exception class
> - `javax.servlet.error.message` - Error message
> - `javax.servlet.error.exception` - The exception object
> - `javax.servlet.error.request_uri` - Requested URI

```jsp
<%
    Integer statusCode = (Integer) request.getAttribute("javax.servlet.error.status_code");
    String uri = (String) request.getAttribute("javax.servlet.error.request_uri");
    Throwable exception = (Throwable) request.getAttribute("javax.servlet.error.exception");
%>
```

**Q8: How do you handle errors in production vs development?**
```jsp
<%
    String env = application.getInitParameter("environment");
    if ("development".equals(env)) {
        // Show detailed stack trace
        exception.printStackTrace(new java.io.PrintWriter(out));
    } else {
        // Production: log error, show friendly message
        log("Error occurred: " + exception.getMessage());
%>
        <p>Sorry, something went wrong. Please try again later.</p>
<%
    }
%>
```

---

## Next Topic: [Accessing Business Logic →](./23_Accessing_Business_Logic.md)
