# ServletContext Parameters - Application-Wide Configuration

## Table of Contents
1. [Introduction](#1-introduction)
2. [ServletContext vs ServletConfig](#2-servletcontext-vs-servletconfig)
3. [Configuring Context Parameters](#3-configuring-context-parameters)
4. [Accessing Context Parameters](#4-accessing-context-parameters)
5. [Complete Code Examples](#5-complete-code-examples)
6. [Key Points to Remember](#6-key-points-to-remember)
7. [Interview Questions](#7-interview-questions)

---

## 1. Introduction

ServletContext parameters are application-wide configuration values accessible by all servlets and JSPs in the application. They're ideal for database URLs, API keys, and shared settings.

---

## 2. ServletContext vs ServletConfig

### Comparison

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    SERVLETCONTEXT vs SERVLETCONFIG                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ServletConfig (Per-Servlet)             ServletContext (Application-Wide)  │
│  ┌──────────────────────────┐           ┌──────────────────────────┐       │
│  │ Servlet A               │           │       APPLICATION        │       │
│  │ ┌────────────────────┐  │           │                          │       │
│  │ │ dbUrl = "db1"      │  │           │  appName = "MyApp"       │       │
│  │ │ maxConn = 5        │  │           │  version = "1.0"         │       │
│  │ └────────────────────┘  │           │  adminEmail = "..."      │       │
│  └──────────────────────────┘           │                          │       │
│                                         │  Accessible by ALL:      │       │
│  ┌──────────────────────────┐           │  • Servlet A             │       │
│  │ Servlet B               │           │  • Servlet B             │       │
│  │ ┌────────────────────┐  │           │  • Servlet C             │       │
│  │ │ dbUrl = "db2"      │  │◄─────────►│  • All JSPs              │       │
│  │ │ cacheSize = 100    │  │           │  • Filters               │       │
│  │ └────────────────────┘  │           │  • Listeners             │       │
│  └──────────────────────────┘           └──────────────────────────┘       │
│                                                                             │
│  Each servlet has own config           One context for entire app          │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Quick Comparison Table

| Feature | ServletConfig | ServletContext |
|---------|--------------|----------------|
| Scope | Single servlet | Entire application |
| Configured in | `<servlet>` | `<context-param>` |
| Number per app | One per servlet | One per application |
| Use for | Servlet-specific config | Shared config |
| Access | `getServletConfig()` | `getServletContext()` |

---

## 3. Configuring Context Parameters

### web.xml Configuration

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee" version="4.0">
    
    <!-- Context Parameters (Application-Wide) -->
    <context-param>
        <param-name>appName</param-name>
        <param-value>My Enterprise Application</param-value>
    </context-param>
    
    <context-param>
        <param-name>adminEmail</param-name>
        <param-value>admin@example.com</param-value>
    </context-param>
    
    <context-param>
        <param-name>environment</param-name>
        <param-value>production</param-value>
    </context-param>
    
    <context-param>
        <param-name>dbUrl</param-name>
        <param-value>jdbc:mysql://localhost:3306/maindb</param-value>
    </context-param>
    
    <!-- Servlets below can access all context-params -->
    <servlet>
        <servlet-name>MyServlet</servlet-name>
        <servlet-class>com.example.MyServlet</servlet-class>
    </servlet>
    
</web-app>
```

---

## 4. Accessing Context Parameters

### In Servlet

```java
// Method 1: Via getServletContext()
ServletContext context = getServletContext();
String appName = context.getInitParameter("appName");
String dbUrl = context.getInitParameter("dbUrl");

// Method 2: Via ServletConfig
String value = getServletConfig().getServletContext().getInitParameter("paramName");
```

### In JSP

```jsp
<%-- Method 1: Using implicit 'application' object --%>
<%= application.getInitParameter("appName") %>

<%-- Method 2: Using EL --%>
${initParam.appName}
${initParam.dbUrl}
${initParam.adminEmail}
```

### In Filter

```java
public void init(FilterConfig filterConfig) throws ServletException {
    ServletContext context = filterConfig.getServletContext();
    String env = context.getInitParameter("environment");
}
```

### In Listener

```java
public void contextInitialized(ServletContextEvent sce) {
    ServletContext context = sce.getServletContext();
    String dbUrl = context.getInitParameter("dbUrl");
}
```

---

## 5. Complete Code Examples

### ContextDemoServlet.java

```java
package com.example;

import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.annotation.*;
import java.io.*;

@WebServlet("/context-demo")
public class ContextDemoServlet extends HttpServlet {
    
    private String appName;
    private String adminEmail;
    private String environment;
    
    @Override
    public void init() throws ServletException {
        // Read context parameters at initialization
        ServletContext context = getServletContext();
        
        appName = context.getInitParameter("appName");
        adminEmail = context.getInitParameter("adminEmail");
        environment = context.getInitParameter("environment");
        
        System.out.println("Application: " + appName);
        System.out.println("Environment: " + environment);
    }
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        
        out.println("<html><body>");
        out.println("<h2>Application Configuration</h2>");
        out.println("<p><strong>App Name:</strong> " + appName + "</p>");
        out.println("<p><strong>Admin Email:</strong> " + adminEmail + "</p>");
        out.println("<p><strong>Environment:</strong> " + environment + "</p>");
        
        // Get all context parameters
        out.println("<h3>All Context Parameters:</h3>");
        out.println("<ul>");
        
        java.util.Enumeration<String> params = getServletContext().getInitParameterNames();
        while (params.hasMoreElements()) {
            String name = params.nextElement();
            String value = getServletContext().getInitParameter(name);
            out.println("<li>" + name + " = " + value + "</li>");
        }
        
        out.println("</ul>");
        out.println("</body></html>");
    }
}
```

### context_demo.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>Context Parameters Demo</title>
</head>
<body>
    <h2>Reading Context Parameters in JSP</h2>
    
    <h3>Using Scriptlet</h3>
    <p>App Name: <%= application.getInitParameter("appName") %></p>
    <p>Environment: <%= application.getInitParameter("environment") %></p>
    
    <h3>Using EL (Expression Language)</h3>
    <p>App Name: ${initParam.appName}</p>
    <p>Admin Email: ${initParam.adminEmail}</p>
    <p>Environment: ${initParam.environment}</p>
    <p>DB URL: ${initParam.dbUrl}</p>
    
    <h3>Conditional Display Based on Environment</h3>
    <%
        String env = application.getInitParameter("environment");
        if ("development".equals(env)) {
    %>
        <p style="color: orange;">⚠️ Development Mode - Debug info enabled</p>
    <% } else if ("production".equals(env)) { %>
        <p style="color: green;">✓ Production Mode</p>
    <% } %>
</body>
</html>
```

### AppInitListener.java

```java
package com.example;

import javax.servlet.*;
import javax.servlet.annotation.*;

@WebListener
public class AppInitListener implements ServletContextListener {
    
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        ServletContext context = sce.getServletContext();
        
        // Read context parameters
        String dbUrl = context.getInitParameter("dbUrl");
        String env = context.getInitParameter("environment");
        
        System.out.println("=== Application Starting ===");
        System.out.println("Database: " + dbUrl);
        System.out.println("Environment: " + env);
        
        // Initialize shared resources based on config
        if ("development".equals(env)) {
            context.setAttribute("debugEnabled", true);
        } else {
            context.setAttribute("debugEnabled", false);
        }
    }
    
    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("=== Application Shutting Down ===");
    }
}
```

---

## 6. Key Points to Remember

1. **One per application** - Unlike ServletConfig (one per servlet)

2. **Configured in `<context-param>`** - Outside any `<servlet>` tag

3. **Accessible everywhere** - Servlets, JSPs, filters, listeners

4. **EL access via `${initParam}`** - Clean JSP syntax

5. **Read-only at runtime** - Set in web.xml, can't change

6. **For shared configuration** - DB URLs, API keys, environment

7. **String values only** - Convert as needed

8. **Case-sensitive names** - "appName" ≠ "AppName"

9. **Available after context init** - Before any servlet init

10. **Prefer over hardcoding** - External configuration is better

---

## 7. Interview Questions

### Basic Level

**Q1: What is ServletContext?**
> ServletContext is an interface that represents the entire web application. There's one ServletContext per application, and all servlets can access it.

**Q2: How do you define context parameters?**
```xml
<context-param>
    <param-name>paramName</param-name>
    <param-value>paramValue</param-value>
</context-param>
```

**Q3: How do you access context parameters in a servlet?**
```java
String value = getServletContext().getInitParameter("paramName");
```

### Intermediate Level

**Q4: What's the difference between context and init parameters?**
| Context Parameters | Init Parameters |
|-------------------|-----------------|
| Application-wide | Servlet-specific |
| `<context-param>` | `<init-param>` in `<servlet>` |
| One set for all | Different per servlet |
| `getServletContext().getInitParameter()` | `getServletConfig().getInitParameter()` |

**Q5: How do you access context parameters in JSP using EL?**
```jsp
${initParam.parameterName}
```

**Q6: Can you modify context parameters at runtime?**
> No, context parameters are defined in web.xml and are read-only at runtime. However, you can set/modify context *attributes* using `setAttribute()`.

### Advanced Level

**Q7: What's the difference between context parameter and context attribute?**
| Context Parameter | Context Attribute |
|-------------------|-------------------|
| Defined in web.xml | Set programmatically |
| Read-only | Read/write |
| String values only | Any Object |
| `getInitParameter()` | `getAttribute()` |
| For configuration | For runtime data |

**Q8: How would you use context parameters for environment-specific config?**
```java
@WebListener
public class EnvConfigListener implements ServletContextListener {
    
    public void contextInitialized(ServletContextEvent sce) {
        ServletContext ctx = sce.getServletContext();
        String env = ctx.getInitParameter("environment");
        
        // Load environment-specific properties
        Properties props = new Properties();
        String propsFile = env + ".properties";  // e.g., "production.properties"
        
        try (InputStream is = ctx.getResourceAsStream("/WEB-INF/" + propsFile)) {
            props.load(is);
            ctx.setAttribute("config", props);
        } catch (Exception e) {
            throw new RuntimeException("Failed to load config", e);
        }
    }
}
```

---

## Next Topic: [Redirect vs Forward →](./11_Redirect_Forward.md)
