# Load On Startup - Servlet Initialization

## Table of Contents
1. [Introduction](#1-introduction)
2. [What is Load On Startup?](#2-what-is-load-on-startup)
3. [Configuration Methods](#3-configuration-methods)
4. [Priority and Loading Order](#4-priority-and-loading-order)
5. [Use Cases](#5-use-cases)
6. [Complete Code Examples](#6-complete-code-examples)
7. [Key Points to Remember](#7-key-points-to-remember)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

By default, servlets are loaded and initialized when the first request arrives (lazy loading). Load-on-startup allows you to change this behavior, initializing servlets when the server starts up instead.

---

## 2. What is Load On Startup?

### Default Behavior (Lazy Loading)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    DEFAULT SERVLET LOADING                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Server Starts                                                              │
│       │                                                                     │
│       ▼                                                                     │
│  ┌────────────────────┐                                                    │
│  │ Application Loaded │                                                    │
│  │ (web.xml parsed)   │                                                    │
│  │ Servlets NOT init  │ ◄─── Servlets waiting                             │
│  └────────────────────┘                                                    │
│       │                                                                     │
│       │ ... time passes ...                                                 │
│       │                                                                     │
│       ▼                                                                     │
│  First Request Arrives                                                      │
│       │                                                                     │
│       ▼                                                                     │
│  ┌────────────────────┐                                                    │
│  │ NOW servlet loads: │                                                    │
│  │ 1. Instantiate     │                                                    │
│  │ 2. init()          │ ◄─── Slow first request!                          │
│  │ 3. service()       │                                                    │
│  └────────────────────┘                                                    │
│                                                                             │
│  Problem: First user experiences delay!                                     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### With Load On Startup

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    LOAD-ON-STARTUP BEHAVIOR                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Server Starts                                                              │
│       │                                                                     │
│       ▼                                                                     │
│  ┌────────────────────┐                                                    │
│  │ Application Loaded │                                                    │
│  │ (web.xml parsed)   │                                                    │
│  │                    │                                                    │
│  │ Immediately:       │                                                    │
│  │ 1. Instantiate     │ ◄─── Happens at startup!                          │
│  │ 2. init()          │                                                    │
│  └────────────────────┘                                                    │
│       │                                                                     │
│       │ ... time passes ...                                                 │
│       │                                                                     │
│       ▼                                                                     │
│  First Request Arrives                                                      │
│       │                                                                     │
│       ▼                                                                     │
│  ┌────────────────────┐                                                    │
│  │ Immediately:       │                                                    │
│  │ service()          │ ◄─── Fast! Already initialized                    │
│  └────────────────────┘                                                    │
│                                                                             │
│  Benefit: First user gets fast response!                                   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Configuration Methods

### Method 1: web.xml Configuration

```xml
<servlet>
    <servlet-name>InitServlet</servlet-name>
    <servlet-class>com.example.InitServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>
```

### Method 2: Annotation Configuration

```java
@WebServlet(
    urlPatterns = "/init",
    loadOnStartup = 1
)
public class InitServlet extends HttpServlet {
    // ...
}
```

### Value Meanings

| Value | Meaning |
|-------|---------|
| **Negative or absent** | Lazy loading (default) |
| **0 or positive** | Load at startup |
| **Lower number** | Higher priority (loads first) |

---

## 4. Priority and Loading Order

### Multiple Servlets with Load Order

```xml
<servlet>
    <servlet-name>DatabaseServlet</servlet-name>
    <servlet-class>com.example.DatabaseServlet</servlet-class>
    <load-on-startup>1</load-on-startup>  <!-- Loads FIRST -->
</servlet>

<servlet>
    <servlet-name>CacheServlet</servlet-name>
    <servlet-class>com.example.CacheServlet</servlet-class>
    <load-on-startup>2</load-on-startup>  <!-- Loads SECOND -->
</servlet>

<servlet>
    <servlet-name>SchedulerServlet</servlet-name>
    <servlet-class>com.example.SchedulerServlet</servlet-class>
    <load-on-startup>3</load-on-startup>  <!-- Loads THIRD -->
</servlet>
```

### Loading Order Visualization

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    SERVLET LOADING ORDER                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Server Starts                                                              │
│       │                                                                     │
│       ▼                                                                     │
│  ┌────────────────────────────────────────────────────────┐                │
│  │  Order 1: DatabaseServlet                               │                │
│  │  - Establishes database connection pool                 │                │
│  │  - Must be ready before others                          │                │
│  └────────────────────────────────────────────────────────┘                │
│       │                                                                     │
│       ▼                                                                     │
│  ┌────────────────────────────────────────────────────────┐                │
│  │  Order 2: CacheServlet                                  │                │
│  │  - Loads frequently accessed data from DB               │                │
│  │  - Needs DB connection (hence order 2)                  │                │
│  └────────────────────────────────────────────────────────┘                │
│       │                                                                     │
│       ▼                                                                     │
│  ┌────────────────────────────────────────────────────────┐                │
│  │  Order 3: SchedulerServlet                              │                │
│  │  - Starts background jobs                               │                │
│  │  - May need cache and DB                                │                │
│  └────────────────────────────────────────────────────────┘                │
│       │                                                                     │
│       ▼                                                                     │
│  Application Ready!                                                         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Use Cases

### When to Use Load On Startup

| Use Case | Reason |
|----------|--------|
| Database connection pools | Need to be ready before requests |
| Configuration loading | Read properties/configs at startup |
| Cache warming | Pre-load frequently accessed data |
| Scheduler initialization | Start background jobs |
| Resource validation | Verify required resources exist |
| Expensive initialization | Move delay to startup, not first request |

### When NOT to Use Load On Startup

| Scenario | Reason |
|----------|--------|
| Rarely used servlets | Wastes memory if never accessed |
| Heavy memory consumers | Increases startup memory usage |
| Too many servlets | Slows down server startup |
| Optional features | May not be needed |

---

## 6. Complete Code Examples

### StartupServlet.java

```java
import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.annotation.*;
import java.io.*;

@WebServlet(
    urlPatterns = "/startup",
    loadOnStartup = 1  // Load first
)
public class StartupServlet extends HttpServlet {
    
    private static String configValue;
    
    @Override
    public void init() throws ServletException {
        System.out.println("StartupServlet: Initializing at server startup...");
        
        // Simulate loading configuration
        configValue = loadConfiguration();
        
        System.out.println("StartupServlet: Configuration loaded: " + configValue);
        System.out.println("StartupServlet: Initialization complete!");
    }
    
    private String loadConfiguration() {
        // Simulate reading from file/database
        return "Production Mode";
    }
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        PrintWriter out = response.getWriter();
        out.println("Configuration: " + configValue);
        out.println("This was loaded at startup, not at first request!");
    }
    
    @Override
    public void destroy() {
        System.out.println("StartupServlet: Shutting down...");
    }
}
```

### web.xml Configuration

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         version="4.0">
    
    <!-- Database Initialization - Priority 1 -->
    <servlet>
        <servlet-name>DbInitServlet</servlet-name>
        <servlet-class>com.example.DbInitServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    
    <!-- Cache Initialization - Priority 2 -->
    <servlet>
        <servlet-name>CacheInitServlet</servlet-name>
        <servlet-class>com.example.CacheInitServlet</servlet-class>
        <load-on-startup>2</load-on-startup>
    </servlet>
    
    <!-- Regular servlet - Lazy loading (no load-on-startup) -->
    <servlet>
        <servlet-name>UserServlet</servlet-name>
        <servlet-class>com.example.UserServlet</servlet-class>
        <!-- No load-on-startup = loads on first request -->
    </servlet>
    
    <!-- Servlet mappings -->
    <servlet-mapping>
        <servlet-name>DbInitServlet</servlet-name>
        <url-pattern>/db-status</url-pattern>
    </servlet-mapping>
    
    <servlet-mapping>
        <servlet-name>CacheInitServlet</servlet-name>
        <url-pattern>/cache-status</url-pattern>
    </servlet-mapping>
    
    <servlet-mapping>
        <servlet-name>UserServlet</servlet-name>
        <url-pattern>/users/*</url-pattern>
    </servlet-mapping>
    
</web-app>
```

### DbInitServlet.java

```java
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;
import java.sql.*;

public class DbInitServlet extends HttpServlet {
    
    private static Connection connection;
    
    @Override
    public void init() throws ServletException {
        System.out.println("=== DbInitServlet: Priority 1 ===");
        System.out.println("Initializing database connection pool...");
        
        try {
            // Simulate database connection setup
            Class.forName("com.mysql.cj.jdbc.Driver");
            connection = DriverManager.getConnection(
                "jdbc:mysql://localhost:3306/mydb", 
                "user", 
                "password"
            );
            System.out.println("Database connection established!");
            
        } catch (Exception e) {
            throw new ServletException("Failed to initialize database", e);
        }
    }
    
    public static Connection getConnection() {
        return connection;
    }
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        PrintWriter out = response.getWriter();
        out.println("Database Status: " + 
            (connection != null ? "Connected" : "Not Connected"));
    }
    
    @Override
    public void destroy() {
        System.out.println("Closing database connection...");
        try {
            if (connection != null && !connection.isClosed()) {
                connection.close();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

---

## 7. Key Points to Remember

1. **Default is lazy loading** - Servlets load on first request

2. **Value 0 or positive = startup loading** - Negative or absent = lazy

3. **Lower number = higher priority** - 1 loads before 2

4. **Same number = undefined order** - Container decides

5. **init() called at startup** - Not at first request

6. **Startup time increases** - More load-on-startup = slower startup

7. **Memory usage increases** - Servlets loaded even if unused

8. **Use for dependencies** - Load database before cache

9. **Annotation or web.xml** - Both work the same

10. **Container guarantee** - init() completes before any service()

---

## 8. Interview Questions

### Basic Level

**Q1: What is load-on-startup?**
> load-on-startup is a configuration that tells the container to initialize a servlet when the server starts, rather than waiting for the first request.

**Q2: What is the default loading behavior of servlets?**
> By default, servlets are lazy-loaded - they are only instantiated and initialized when the first request for that servlet arrives.

**Q3: What values can load-on-startup have?**
> - Negative or absent: Lazy loading (default)
> - 0 or positive: Load at startup
> - Lower positive number = higher priority

### Intermediate Level

**Q4: How do you set load-on-startup using annotations?**
```java
@WebServlet(
    urlPatterns = "/myServlet",
    loadOnStartup = 1
)
public class MyServlet extends HttpServlet {
    // ...
}
```

**Q5: What happens if two servlets have the same load-on-startup value?**
> The order is undefined and container-dependent. If order matters, use different values.

**Q6: When would you NOT use load-on-startup?**
> - Rarely accessed servlets (wastes memory)
> - Very heavy servlets (slows startup)
> - Non-critical optional features
> - When lazy loading is acceptable

### Advanced Level

**Q7: How would you use load-on-startup for dependency ordering?**
```xml
<!-- Database first (others depend on it) -->
<servlet>
    <servlet-name>Database</servlet-name>
    <servlet-class>DbServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>

<!-- Cache second (needs database) -->
<servlet>
    <servlet-name>Cache</servlet-name>
    <servlet-class>CacheServlet</servlet-class>
    <load-on-startup>2</load-on-startup>
</servlet>

<!-- Scheduler third (needs both) -->
<servlet>
    <servlet-name>Scheduler</servlet-name>
    <servlet-class>SchedulerServlet</servlet-class>
    <load-on-startup>3</load-on-startup>
</servlet>
```

**Q8: What happens if init() throws an exception?**
> The servlet is marked as unavailable. Container behavior varies:
> - Some containers fail the entire application deployment
> - Some mark just that servlet as unavailable
> - Always handle init() exceptions properly

**Q9: Is there an alternative to load-on-startup for initialization?**
> Yes, `ServletContextListener`:
```java
@WebListener
public class AppInitializer implements ServletContextListener {
    
    public void contextInitialized(ServletContextEvent sce) {
        // Runs when app starts - before any servlets
        // Good for application-wide initialization
    }
    
    public void contextDestroyed(ServletContextEvent sce) {
        // Cleanup when app shuts down
    }
}
```

---

## Next Topic: [Error Handling in JSP →](./17_Error_Handling_JSP.md)
