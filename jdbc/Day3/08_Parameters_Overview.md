# Parameters Overview - Request, Init, and Context

## Table of Contents
1. [Introduction](#1-introduction)
2. [Three Types of Parameters](#2-three-types-of-parameters)
3. [Comparison Table](#3-comparison-table)
4. [Real-World Scenario](#4-real-world-scenario)
5. [Reading Parameters](#5-reading-parameters)
6. [Key Points to Remember](#6-key-points-to-remember)
7. [Interview Questions](#7-interview-questions)

---

## 1. Introduction

Parameters are used to send information to servlets. Understanding the different types of parameters and their scopes is essential for building well-designed web applications. Java EE provides three types of parameters, each with different scopes and use cases.

---

## 2. Three Types of Parameters

### Visual Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          WEB APPLICATION (Context)                           │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                      CONTEXT PARAMETERS                              │   │
│  │   Shared by ALL Servlets in the application                         │   │
│  │   Example: database URL, company name, admin email                  │   │
│  │   Defined in: <context-param> in web.xml                            │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ┌──────────────────────────┐    ┌──────────────────────────┐             │
│  │      BookServlet         │    │     EmployeeServlet      │             │
│  │  ┌──────────────────┐   │    │  ┌──────────────────┐   │             │
│  │  │ INIT PARAMETERS  │   │    │  │ INIT PARAMETERS  │   │             │
│  │  │ Specific to this │   │    │  │ Specific to this │   │             │
│  │  │ servlet only     │   │    │  │ servlet only     │   │             │
│  │  │ Ex: log file path│   │    │  │ Ex: page size    │   │             │
│  │  └──────────────────┘   │    │  └──────────────────┘   │             │
│  │                          │    │                          │             │
│  │  ┌──────────────────┐   │    │  ┌──────────────────┐   │             │
│  │  │REQUEST PARAMETERS│   │    │  │REQUEST PARAMETERS│   │             │
│  │  │From HTML form    │   │    │  │From HTML form    │   │             │
│  │  │Ex: module=Java   │   │    │  │Ex: empId=101     │   │             │
│  │  └──────────────────┘   │    │  └──────────────────┘   │             │
│  └──────────────────────────┘    └──────────────────────────┘             │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 1. Request Parameters

**Scope:** Single HTTP request only

**Source:** From HTML forms, query strings, or URLs

**Lifespan:** Valid only for one request

```java
// Reading request parameter
String name = request.getParameter("name");
```

**Example Sources:**
```html
<!-- From HTML form -->
<form action="BookServlet" method="post">
    <input type="text" name="module" value="Java">
</form>

<!-- From URL query string -->
<a href="BookServlet?module=Java">Click</a>

<!-- URL format -->
http://localhost:8080/app/BookServlet?module=Java&level=Advanced
```

### 2. Init/Config Parameters

**Scope:** Single servlet only

**Source:** Defined in web.xml under `<init-param>`

**Lifespan:** Entire lifetime of the servlet

```java
// Reading init parameter
String logFile = getServletConfig().getInitParameter("logFile");
// OR
String logFile = getInitParameter("logFile");
```

**Configuration in web.xml:**
```xml
<servlet>
    <servlet-name>BookServlet</servlet-name>
    <servlet-class>com.app.BookServlet</servlet-class>
    <init-param>
        <param-name>logFile</param-name>
        <param-value>/logs/book.log</param-value>
    </init-param>
</servlet>
```

### 3. Context Parameters

**Scope:** Entire web application (all servlets, JSPs)

**Source:** Defined in web.xml under `<context-param>`

**Lifespan:** Entire lifetime of the application

```java
// Reading context parameter
String dbUrl = getServletContext().getInitParameter("dbUrl");
```

**Configuration in web.xml:**
```xml
<context-param>
    <param-name>dbUrl</param-name>
    <param-value>jdbc:mysql://localhost:3306/mydb</param-value>
</context-param>

<context-param>
    <param-name>companyName</param-name>
    <param-value>CDAC</param-value>
</context-param>
```

---

## 3. Comparison Table

| Aspect | Request Parameter | Init Parameter | Context Parameter |
|--------|-------------------|----------------|-------------------|
| **Scope** | Single request | Single servlet | Entire application |
| **Set By** | Client (form/URL) | Developer (web.xml) | Developer (web.xml) |
| **Read Using** | HttpServletRequest | ServletConfig | ServletContext |
| **Method** | getParameter() | getInitParameter() | getInitParameter() |
| **Objects Per App** | Many (one per request) | Many (one per servlet) | One (per app) |
| **Lifespan** | Request duration | Servlet lifetime | App lifetime |
| **Common Use** | User input | Servlet config | App-wide config |

### Object Relationship

```
┌───────────────────────────────────────────────────────────────────┐
│                    OBJECT HIERARCHY                               │
├───────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ServletContext (ONE per web application)                         │
│       │                                                           │
│       ├── Servlet 1 → ServletConfig 1 (ONE per servlet)          │
│       │       │                                                   │
│       │       ├── Request 1 → HttpServletRequest (ONE per request)│
│       │       ├── Request 2 → HttpServletRequest                  │
│       │       └── Request 3 → HttpServletRequest                  │
│       │                                                           │
│       ├── Servlet 2 → ServletConfig 2 (ONE per servlet)          │
│       │       │                                                   │
│       │       └── Requests...                                     │
│       │                                                           │
│       └── Servlet 3 → ServletConfig 3 (ONE per servlet)          │
│               │                                                   │
│               └── Requests...                                     │
│                                                                   │
└───────────────────────────────────────────────────────────────────┘
```

---

## 4. Real-World Scenario

### BookServlet Example

```
Dynamic Web Project: MyBookStore (Context)
├── BookServlet
│   ├── Request Parameters:
│   │   - module (e.g., "Java", "Spring") ← from user selection
│   │   - quantity (e.g., "5") ← from form input
│   │
│   ├── Init Parameters:
│   │   - logFile: "/logs/book_module_log.txt" ← servlet-specific config
│   │   - cacheEnabled: "true" ← servlet-specific setting
│   │
│   └── Context Parameters:
│       - dbUrl: "jdbc:mysql://localhost/books" ← shared by all
│       - companyName: "BookMart" ← shared by all
│       - supportEmail: "support@bookmart.com" ← shared by all
│
├── EmployeeServlet
│   ├── Request Parameters:
│   │   - empId (e.g., "101") ← from user input
│   │   - department (e.g., "IT") ← from dropdown
│   │
│   ├── Init Parameters:
│   │   - pageSize: "20" ← items per page
│   │   - logFile: "/logs/employee.log" ← different from BookServlet!
│   │
│   └── Context Parameters:
│       - SAME as BookServlet (shared across application)
│
└── web.xml contains all init-param and context-param definitions
```

---

## 5. Reading Parameters

### Complete Code Example

#### ReadParamServ1.java - With Init Parameters

```java
import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;

public class ReadParamServ1 extends HttpServlet {
    
    public void doGet(HttpServletRequest request, 
                      HttpServletResponse response) 
                      throws ServletException, IOException {
        
        PrintWriter pw = response.getWriter();
        
        // Reading INIT parameter (servlet-specific)
        // This servlet has <init-param> for "file"
        String value = getServletConfig().getInitParameter("file");
        // OR simply: String value = getInitParameter("file");
        
        pw.println("Init Parameter 'file': " + value);
        // Output: Init Parameter 'file': /logs/servlet1.log
        
        // Reading CONTEXT parameter (application-wide)
        String value1 = getServletContext().getInitParameter("driver");
        
        pw.println("Context Parameter 'driver': " + value1);
        // Output: Context Parameter 'driver': Type4
    }
}
```

#### ReadParamServ2.java - Without Init Parameters

```java
import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;

public class ReadParamServ2 extends HttpServlet {
    
    public void doGet(HttpServletRequest request, 
                      HttpServletResponse response) 
                      throws ServletException, IOException {
        
        PrintWriter pw = response.getWriter();
        
        // Reading INIT parameter (servlet-specific)
        // This servlet does NOT have <init-param> for "file"
        String value = getServletConfig().getInitParameter("file");
        
        pw.println("Init Parameter 'file': " + value);
        // Output: Init Parameter 'file': null  ← NOT FOUND!
        
        // Reading CONTEXT parameter (application-wide)
        // This will work - context params are shared
        String value1 = getServletContext().getInitParameter("driver");
        
        pw.println("Context Parameter 'driver': " + value1);
        // Output: Context Parameter 'driver': Type4  ← FOUND!
    }
}
```

#### web.xml Configuration

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app>
    
    <!-- CONTEXT PARAMETER - Shared by all servlets -->
    <context-param>
        <param-name>driver</param-name>
        <param-value>Type4</param-value>
    </context-param>
    
    <!-- First Servlet with init parameter -->
    <servlet>
        <servlet-name>ReadParamServ1</servlet-name>
        <servlet-class>ReadParamServ1</servlet-class>
        
        <!-- INIT PARAMETER - Only for this servlet -->
        <init-param>
            <param-name>file</param-name>
            <param-value>/logs/servlet1.log</param-value>
        </init-param>
    </servlet>
    
    <!-- Second Servlet without init parameter -->
    <servlet>
        <servlet-name>ReadParamServ2</servlet-name>
        <servlet-class>ReadParamServ2</servlet-class>
        <!-- No init-param defined here -->
    </servlet>
    
    <!-- Servlet Mappings -->
    <servlet-mapping>
        <servlet-name>ReadParamServ1</servlet-name>
        <url-pattern>/ReadParamServ1</url-pattern>
    </servlet-mapping>
    
    <servlet-mapping>
        <servlet-name>ReadParamServ2</servlet-name>
        <url-pattern>/ReadParamServ2</url-pattern>
    </servlet-mapping>
    
</web-app>
```

### Output Comparison

```
ReadParamServ1:
┌──────────────────────────────────────────────────┐
│ Init Parameter 'file': /logs/servlet1.log       │
│ Context Parameter 'driver': Type4               │
└──────────────────────────────────────────────────┘

ReadParamServ2:
┌──────────────────────────────────────────────────┐
│ Init Parameter 'file': null                      │  ← No init-param!
│ Context Parameter 'driver': Type4               │  ← Shared works!
└──────────────────────────────────────────────────┘
```

---

## 6. Key Points to Remember

1. **Request parameters** are automatically set from forms/URLs
2. **Init and Context parameters** must be defined in web.xml
3. **One ServletContext per application** - shared by all servlets
4. **One ServletConfig per servlet** - unique to each servlet
5. **getInitParameter()** is used for both init and context parameters (but on different objects)
6. **Init parameters return null** if asked for non-existent parameter
7. **Context parameters are global** - accessible from any servlet in the application
8. **Parameters are always Strings** - convert if needed

### Method Summary

| To Read | Use Object | Method |
|---------|------------|--------|
| Request parameter | HttpServletRequest | request.getParameter("name") |
| Init parameter | ServletConfig | getServletConfig().getInitParameter("name") |
| Context parameter | ServletContext | getServletContext().getInitParameter("name") |

---

## 7. Interview Questions

### Basic Level

**Q1: What are the three types of parameters in servlets?**
> 1. Request parameters - from HTML forms or URL query strings
> 2. Init/Config parameters - defined in web.xml for specific servlet
> 3. Context parameters - defined in web.xml for entire application

**Q2: How do you read request parameters?**
> Using `request.getParameter("paramName")` method on HttpServletRequest object.

**Q3: What is the difference between ServletConfig and ServletContext?**
| ServletConfig | ServletContext |
|---------------|----------------|
| One per servlet | One per application |
| Servlet-specific parameters | Application-wide parameters |
| Created per servlet instance | Shared across all servlets |

### Intermediate Level

**Q4: Where are init and context parameters defined?**
> Both are defined in web.xml:
> - Init parameters: Inside `<servlet>` tag using `<init-param>`
> - Context parameters: Outside `<servlet>` tag using `<context-param>`

**Q5: Can a servlet access another servlet's init parameters?**
> No, init parameters are specific to each servlet. ServletConfig is unique to each servlet and cannot read another servlet's init-params. However, context parameters are shared across all servlets.

**Q6: What happens if you try to read a non-existent parameter?**
> All getParameter() and getInitParameter() methods return `null` if the parameter doesn't exist.

### Advanced Level

**Q7: How would you read init parameters in annotations (without web.xml)?**
```java
@WebServlet(
    urlPatterns = "/myServlet",
    initParams = {
        @WebInitParam(name = "file", value = "/logs/app.log"),
        @WebInitParam(name = "maxSize", value = "100")
    }
)
public class MyServlet extends HttpServlet {
    // Access using getInitParameter("file")
}
```

**Q8: What is the order of initialization for config and context?**
> 1. Context parameters are loaded first (when application starts)
> 2. Servlet config parameters are loaded when servlet is instantiated
> 3. Context is available throughout; config is available after init()

**Q9: How do you get all parameter names?**
```java
// All request parameter names
Enumeration<String> reqParams = request.getParameterNames();

// All init parameter names
Enumeration<String> initParams = getServletConfig().getInitParameterNames();

// All context parameter names
Enumeration<String> ctxParams = getServletContext().getInitParameterNames();
```

---

## Next Topic: [ServletConfig Parameters →](./09_ServletConfig_Parameters.md)
