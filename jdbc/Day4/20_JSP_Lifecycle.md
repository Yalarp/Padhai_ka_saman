# JSP Lifecycle - Complete Guide

## Table of Contents
1. [Introduction](#1-introduction)
2. [JSP Lifecycle Phases](#2-jsp-lifecycle-phases)
3. [Translation Phase](#3-translation-phase)
4. [Compilation Phase](#4-compilation-phase)
5. [Lifecycle Methods](#5-lifecycle-methods)
6. [Generated Servlet Code](#6-generated-servlet-code)
7. [Key Points to Remember](#7-key-points-to-remember)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

Understanding the JSP lifecycle is crucial for debugging and optimization. Unlike servlets that you write directly, JSPs are translated to servlets by the container.

---

## 2. JSP Lifecycle Phases

### Complete Lifecycle Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         JSP LIFECYCLE PHASES                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────┐                                                       │
│  │   page.jsp      │ ─── Your JSP file                                     │
│  └────────┬────────┘                                                       │
│           │                                                                 │
│           ▼ [1] TRANSLATION                                                 │
│  ┌─────────────────┐                                                       │
│  │  page_jsp.java  │ ─── Generated servlet source                          │
│  └────────┬────────┘                                                       │
│           │                                                                 │
│           ▼ [2] COMPILATION                                                 │
│  ┌─────────────────┐                                                       │
│  │  page_jsp.class │ ─── Compiled bytecode                                 │
│  └────────┬────────┘                                                       │
│           │                                                                 │
│           ▼ [3] LOADING                                                     │
│  ┌─────────────────┐                                                       │
│  │ ClassLoader     │ ─── Class loaded into memory                          │
│  └────────┬────────┘                                                       │
│           │                                                                 │
│           ▼ [4] INSTANTIATION                                               │
│  ┌─────────────────┐                                                       │
│  │ new page_jsp()  │ ─── Object created                                    │
│  └────────┬────────┘                                                       │
│           │                                                                 │
│           ▼ [5] INITIALIZATION                                              │
│  ┌─────────────────┐                                                       │
│  │ jspInit()       │ ─── Called once                                       │
│  └────────┬────────┘                                                       │
│           │                                                                 │
│           ▼ [6] REQUEST HANDLING (repeated)                                 │
│  ┌─────────────────┐                                                       │
│  │ _jspService()   │ ─── Called per request                                │
│  └────────┬────────┘                                                       │
│           │                                                                 │
│           ▼ [7] DESTRUCTION                                                 │
│  ┌─────────────────┐                                                       │
│  │ jspDestroy()    │ ─── Called once at shutdown                           │
│  └─────────────────┘                                                       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### First Request vs Subsequent Requests

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    FIRST REQUEST vs SUBSEQUENT                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  FIRST REQUEST:                                                             │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ Translation → Compilation → Loading → Instantiation →       │           │
│  │ jspInit() → _jspService()                                    │           │
│  │                                                              │           │
│  │ [Slow - includes translation/compilation]                   │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
│  SUBSEQUENT REQUESTS:                                                       │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ _jspService() only!                                          │           │
│  │                                                              │           │
│  │ [Fast - servlet already loaded]                             │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
│  ─────────────────────────────────────────────────────────────             │
│                                                                             │
│  IF JSP MODIFIED:                                                           │
│  Container detects change → Re-translate → Re-compile → Reload             │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Translation Phase

### What Happens During Translation?

The container converts JSP elements to Java code:

| JSP Element | Becomes in Java |
|-------------|-----------------|
| Static HTML | `out.write("...")` |
| `<%= expr %>` | `out.print(expr)` |
| `<% code %>` | Code in _jspService() |
| `<%! decl %>` | Instance/method declarations |
| `<%@ page %>` | Import statements, settings |

### Example Translation

**Original JSP (hello.jsp):**
```jsp
<%@ page contentType="text/html" %>
<%! int count = 0; %>
<html>
<body>
    <h1>Hello, <%= request.getParameter("name") %>!</h1>
    <p>Visit count: <%= ++count %></p>
</body>
</html>
```

**Generated Java (simplified):**
```java
public class hello_jsp extends HttpJspBase {
    
    // From <%! declaration %>
    int count = 0;
    
    public void _jspService(HttpServletRequest request, 
                            HttpServletResponse response) 
            throws ServletException, IOException {
        
        response.setContentType("text/html");
        JspWriter out = response.getWriter();
        
        // Static HTML
        out.write("<html>\n<body>\n");
        out.write("    <h1>Hello, ");
        
        // From <%= expression %>
        out.print(request.getParameter("name"));
        
        out.write("!</h1>\n    <p>Visit count: ");
        out.print(++count);
        out.write("</p>\n</body>\n</html>");
    }
}
```

---

## 4. Compilation Phase

### Compilation Process

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    COMPILATION PHASE                                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. Translation produces: page_jsp.java                                     │
│                              │                                              │
│                              ▼                                              │
│  2. Java compiler (javac) compiles to: page_jsp.class                      │
│                              │                                              │
│                              ▼                                              │
│  3. If compilation ERROR:                                                   │
│     ┌─────────────────────────────────────────────────────────┐            │
│     │  Error page shown to user with:                          │            │
│     │  • Line number in JSP                                    │            │
│     │  • Error description                                     │            │
│     │  • Stack trace (in development)                          │            │
│     └─────────────────────────────────────────────────────────┘            │
│                                                                             │
│  Location of generated files (Tomcat example):                             │
│  TOMCAT_HOME/work/Catalina/localhost/AppName/                              │
│      └── org/apache/jsp/                                                   │
│          ├── page_jsp.java                                                 │
│          └── page_jsp.class                                                │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Lifecycle Methods

### The Three Lifecycle Methods

```java
public interface JspPage extends Servlet {
    
    // Called once after instantiation
    void jspInit();
    
    // Called once before destruction
    void jspDestroy();
}

public interface HttpJspPage extends JspPage {
    
    // Called for every request (auto-generated - cannot override!)
    void _jspService(HttpServletRequest request, 
                     HttpServletResponse response)
            throws ServletException, IOException;
}
```

### Method Comparison

| Method | When Called | Can Override? | Purpose |
|--------|-------------|---------------|---------|
| `jspInit()` | Once at startup | Yes | Initialize resources |
| `_jspService()` | Every request | **No** | Handle request |
| `jspDestroy()` | Once at shutdown | Yes | Cleanup resources |

### Overriding jspInit() and jspDestroy()

```jsp
<%@ page contentType="text/html" %>

<%!
    // Override jspInit - called once
    public void jspInit() {
        System.out.println("JSP Initialized!");
        // Initialize database connections, load config, etc.
    }
    
    // Override jspDestroy - called once
    public void jspDestroy() {
        System.out.println("JSP Destroyed!");
        // Close connections, release resources, etc.
    }
%>

<html>
<body>
    <p>Current time: <%= new java.util.Date() %></p>
</body>
</html>
```

---

## 6. Generated Servlet Code

### Complete Generated Structure

```java
package org.apache.jsp;

import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.jsp.*;

public final class mypage_jsp extends org.apache.jasper.runtime.HttpJspBase
        implements org.apache.jasper.runtime.JspSourceDependent {
    
    // =============================
    // From <%! declarations %>
    // =============================
    int counter = 0;
    
    public int getCounter() {
        return counter;
    }
    
    // =============================
    // Lifecycle methods
    // =============================
    public void jspInit() {
        // Your override (if any)
    }
    
    public void jspDestroy() {
        // Your override (if any)
    }
    
    // =============================
    // Main service method (auto-generated)
    // =============================
    public void _jspService(HttpServletRequest request, 
                            HttpServletResponse response)
            throws java.io.IOException, ServletException {
        
        // Implicit objects setup
        PageContext pageContext = null;
        HttpSession session = null;
        ServletContext application = null;
        ServletConfig config = null;
        JspWriter out = null;
        Object page = this;
        
        try {
            // Initialize implicit objects
            response.setContentType("text/html");
            pageContext = _jspxFactory.getPageContext(this, request, response,
                    null, true, 8192, true);
            application = pageContext.getServletContext();
            config = pageContext.getServletConfig();
            session = pageContext.getSession();
            out = pageContext.getOut();
            
            // =============================
            // Your JSP content becomes code here
            // =============================
            out.write("<html>\n");
            out.write("<body>\n");
            out.print(getCounter());  // From <%= getCounter() %>
            out.write("</body>\n");
            out.write("</html>\n");
            
        } catch (Throwable t) {
            // Exception handling
        } finally {
            // Cleanup
            _jspxFactory.releasePageContext(pageContext);
        }
    }
}
```

---

## 7. Key Points to Remember

1. **JSP becomes Servlet** - Container translates JSP to servlet

2. **First request is slow** - Translation + compilation overhead

3. **Subsequent requests are fast** - Just _jspService() call

4. **Cannot override _jspService()** - Container generates it

5. **Can override jspInit/jspDestroy** - Use <%! declaration %>

6. **Generated files location** - Check work/ directory in Tomcat

7. **Changes detected** - Container recompiles modified JSPs

8. **Compilation errors** - Shown in browser on first request

9. **Implicit objects** - Created before your code runs

10. **Declaration = instance level** - Outside _jspService()

---

## 8. Interview Questions

### Basic Level

**Q1: What are the phases of JSP lifecycle?**
> 1. Translation (JSP → Java)
> 2. Compilation (Java → Class)
> 3. Loading (Class into memory)
> 4. Instantiation (Create object)
> 5. Initialization (jspInit)
> 6. Request handling (_jspService)
> 7. Destruction (jspDestroy)

**Q2: Why is the first JSP request slow?**
> Because the container must translate the JSP to Java source code and compile it to bytecode before executing. Subsequent requests skip these steps.

**Q3: Can you override _jspService()?**
> No! The container generates this method from your JSP code. Attempting to declare it in a scriptlet causes a compilation error.

### Intermediate Level

**Q4: Where does container store generated files?**
> In Tomcat: `TOMCAT_HOME/work/Catalina/localhost/[AppName]/org/apache/jsp/`
> You can view the generated .java and .class files there.

**Q5: How do you override jspInit()?**
```jsp
<%!
    public void jspInit() {
        // Your initialization code
    }
%>
```

**Q6: What's the difference between <%! %> and <% %>?**
| Declaration <%! %> | Scriptlet <% %> |
|-------------------|-----------------|
| Outside _jspService | Inside _jspService |
| Instance variables | Local variables |
| Method definitions | Executable code |
| Runs once (if static-like) | Runs per request |

### Advanced Level

**Q7: How would you pre-compile JSPs for production?**
> Several approaches:
> 1. Use IDE's build process
> 2. Use `jspc` (JSP compiler) at build time
> 3. Configure container to pre-compile on startup
> 4. First access all JSPs during deployment validation
> 
> Tomcat example in web.xml:
```xml
<servlet>
    <servlet-name>jsp</servlet-name>
    <servlet-class>org.apache.jasper.servlet.JspServlet</servlet-class>
    <init-param>
        <param-name>development</param-name>
        <param-value>false</param-value>
    </init-param>
</servlet>
```

**Q8: What happens when JSP is modified while server is running?**
> Container detects file modification (by timestamp), marks the old servlet for destruction, and re-translates/recompiles the JSP on the next request.

---

## Next Topic: [JSP Elements →](./21_JSP_Elements.md)
