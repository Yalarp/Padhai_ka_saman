# Servlet Init Methods - Parameterized Initialization

## Table of Contents
1. [Introduction](#1-introduction)
2. [init() vs init(ServletConfig)](#2-init-vs-initservletconfig)
3. [ServletConfig Object](#3-servletconfig-object)
4. [Configuring Init Parameters](#4-configuring-init-parameters)
5. [Complete Code Examples](#5-complete-code-examples)
6. [Key Points to Remember](#6-key-points-to-remember)
7. [Interview Questions](#7-interview-questions)

---

## 1. Introduction

Servlets have two versions of the init() method. Understanding when to use each is crucial for proper servlet initialization and configuration.

---

## 2. init() vs init(ServletConfig)

### The Two Init Methods

```java
// Method 1: No-argument init()
public void init() throws ServletException {
    // Override for simple initialization
}

// Method 2: Parameterized init(ServletConfig)
public void init(ServletConfig config) throws ServletException {
    super.init(config);  // IMPORTANT: Must call super!
    // Override for initialization needing config
}
```

### When to Use Which?

| Scenario | Use This |
|----------|----------|
| Simple initialization | `init()` |
| Need ServletConfig | `init(ServletConfig)` |
| Read init parameters | `init(ServletConfig)` |
| Database connection setup | Either (prefer init()) |

### Internal Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    INIT METHOD CALL FLOW                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Container calls init(ServletConfig)                                        │
│           │                                                                 │
│           ▼                                                                 │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ GenericServlet.init(ServletConfig config) {                 │           │
│  │     this.config = config;  // Store config reference         │           │
│  │     init();                // Call no-arg init()             │           │
│  │ }                                                            │           │
│  └─────────────────────────────────────────────────────────────┘           │
│           │                                                                 │
│           ▼                                                                 │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ Your override of init() {                                    │           │
│  │     // Your initialization code                              │           │
│  │ }                                                            │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
│  Result: You can override init() and still access config via getServletConfig()
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. ServletConfig Object

### What is ServletConfig?

ServletConfig is created by the container for each servlet and contains:
- Servlet name
- Init parameters
- ServletContext reference

### Key Methods

```java
public interface ServletConfig {
    String getServletName();
    ServletContext getServletContext();
    String getInitParameter(String name);
    Enumeration<String> getInitParameterNames();
}
```

### Accessing ServletConfig

```java
// Method 1: From init(ServletConfig)
public void init(ServletConfig config) throws ServletException {
    super.init(config);
    String value = config.getInitParameter("paramName");
}

// Method 2: From any method using getServletConfig()
public void doGet(HttpServletRequest req, HttpServletResponse res) {
    String value = getServletConfig().getInitParameter("paramName");
}
```

---

## 4. Configuring Init Parameters

### web.xml Configuration

```xml
<servlet>
    <servlet-name>ConfigServlet</servlet-name>
    <servlet-class>com.example.ConfigServlet</servlet-class>
    
    <!-- Init parameters -->
    <init-param>
        <param-name>dbUrl</param-name>
        <param-value>jdbc:mysql://localhost:3306/mydb</param-value>
    </init-param>
    <init-param>
        <param-name>maxConnections</param-name>
        <param-value>10</param-value>
    </init-param>
</servlet>
```

### Annotation Configuration

```java
@WebServlet(
    urlPatterns = "/config",
    initParams = {
        @WebInitParam(name = "dbUrl", value = "jdbc:mysql://localhost:3306/mydb"),
        @WebInitParam(name = "maxConnections", value = "10")
    }
)
public class ConfigServlet extends HttpServlet {
    // ...
}
```

---

## 5. Complete Code Examples

### Using init()

```java
import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.annotation.*;
import java.io.*;

@WebServlet("/simple-init")
public class SimpleInitServlet extends HttpServlet {
    
    private String message;
    
    @Override
    public void init() throws ServletException {
        // Simple initialization - no config needed
        message = "Servlet initialized at: " + new java.util.Date();
        System.out.println(message);
        
        // Can still access config via getServletConfig()
        String param = getServletConfig().getInitParameter("anyParam");
    }
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        response.getWriter().println(message);
    }
}
```

### Using init(ServletConfig)

```java
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;
import java.sql.*;

public class DatabaseServlet extends HttpServlet {
    
    private Connection connection;
    private String dbUrl;
    private String username;
    private String password;
    
    @Override
    public void init(ServletConfig config) throws ServletException {
        // CRITICAL: Must call super.init(config)!
        super.init(config);
        
        // Read init parameters
        dbUrl = config.getInitParameter("dbUrl");
        username = config.getInitParameter("username");
        password = config.getInitParameter("password");
        
        // Initialize database connection
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            connection = DriverManager.getConnection(dbUrl, username, password);
            System.out.println("Database connection established in init()");
        } catch (Exception e) {
            throw new ServletException("Failed to initialize database", e);
        }
    }
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        PrintWriter out = response.getWriter();
        out.println("Database URL: " + dbUrl);
        out.println("Connection status: " + (connection != null ? "Connected" : "Not Connected"));
    }
    
    @Override
    public void destroy() {
        try {
            if (connection != null && !connection.isClosed()) {
                connection.close();
                System.out.println("Database connection closed");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

### web.xml for DatabaseServlet

```xml
<servlet>
    <servlet-name>DatabaseServlet</servlet-name>
    <servlet-class>com.example.DatabaseServlet</servlet-class>
    <init-param>
        <param-name>dbUrl</param-name>
        <param-value>jdbc:mysql://localhost:3306/mydb</param-value>
    </init-param>
    <init-param>
        <param-name>username</param-name>
        <param-value>root</param-value>
    </init-param>
    <init-param>
        <param-name>password</param-name>
        <param-value>secret</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
```

### Common Mistake

```java
// WRONG: Forgetting to call super.init(config)
public void init(ServletConfig config) throws ServletException {
    // super.init(config) is MISSING!
    String param = config.getInitParameter("param");  // OK here
}

// Later in doGet():
String param = getServletConfig().getInitParameter("param");
// NullPointerException! Because config was never stored!
```

```java
// CORRECT: Always call super.init(config)
public void init(ServletConfig config) throws ServletException {
    super.init(config);  // This stores config for later use
    String param = config.getInitParameter("param");
}
```

---

## 6. Key Points to Remember

1. **Two init methods** - init() and init(ServletConfig)

2. **Container calls init(ServletConfig)** - This then calls init()

3. **Override init() for simplicity** - When you don't need config directly

4. **Call super.init(config)** - If overriding init(ServletConfig)

5. **getServletConfig()** - Available after init() completes

6. **Init params are per-servlet** - Different from context params

7. **Called once** - At servlet instantiation

8. **Throws ServletException** - If initialization fails

9. **UnavailableException** - To make servlet temporarily unavailable

10. **Thread-safe** - Container ensures single-threaded init

---

## 7. Interview Questions

### Basic Level

**Q1: What is the difference between init() and init(ServletConfig)?**
> - `init()`: No-argument version, simpler to override
> - `init(ServletConfig)`: Has access to ServletConfig directly
> 
> Container calls `init(ServletConfig)`, which internally calls `init()`.

**Q2: Why must you call super.init(config)?**
> `super.init(config)` stores the ServletConfig reference in the GenericServlet class. Without it, `getServletConfig()` returns null.

**Q3: How do you read init parameters?**
```java
String value = getServletConfig().getInitParameter("paramName");
```

### Intermediate Level

**Q4: When would you prefer init(ServletConfig) over init()?**
> When you need the ServletConfig reference immediately in the init method itself, such as reading configuration before setting up resources.

**Q5: What happens if init() throws an exception?**
> - Servlet is marked as unavailable
> - Container may try to reinitialize later
> - Requests to this servlet will fail with appropriate error

**Q6: How are init parameters different from context parameters?**
| Init Parameters | Context Parameters |
|-----------------|-------------------|
| Per-servlet | Application-wide |
| In `<servlet>` | In `<context-param>` |
| `getInitParameter()` | `getServletContext().getInitParameter()` |

### Advanced Level

**Q7: How would you implement a servlet that pre-loads data on startup?**
```java
@WebServlet(urlPatterns = "/data", loadOnStartup = 1)
public class DataServlet extends HttpServlet {
    private List<Product> products;
    
    @Override
    public void init() throws ServletException {
        // Pre-load data from database
        products = loadProductsFromDatabase();
        // Store in application scope for other servlets
        getServletContext().setAttribute("products", products);
    }
    
    private List<Product> loadProductsFromDatabase() {
        // Database access code
    }
}
```

**Q8: Can you have multiple servlets with same init parameter names?**
> Yes! Init parameters are scoped to individual servlets. Each servlet has its own ServletConfig with its own init parameters.

---

## Next Topic: [ServletContext Parameters →](./10_ServletContext_Parameters.md)
