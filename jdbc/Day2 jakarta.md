# JAKARTA EE - SERVLETS & MAVEN COMPLETE NOTES

> **COURSE:** Advanced Java - Day 2  
> **TOPIC:** Jakarta EE, Servlets, and Maven  
> **ROOT PATH:** `D:\CDAC\Advanced java\Day_2_Jakarta_EE`

================================================================================
TABLE OF CONTENTS
================================================================================

1.  [Design Patterns - Factory Pattern](#1-design-patterns---factory-pattern)
2.  [Static vs Dynamic Content](#2-static-vs-dynamic-content)
3.  [Introduction to Servlets](#3-introduction-to-servlets)
4.  [Servlet Hierarchy](#4-servlet-hierarchy)
5.  [Servlet Lifecycle](#5-servlet-lifecycle)
6.  [HTTP Methods - doGet() and doPost()](#6-http-methods---doget-and-dopost)
7.  [Creating Your First Servlet](#7-creating-your-first-servlet)
8.  [Servlet with HTML Form](#8-servlet-with-html-form)
9.  [Servlet with Database Connectivity](#9-servlet-with-database-connectivity)
10. [Using Singleton Pattern for Database Connection](#10-using-singleton-pattern-for-database-connection)
11. [Introduction to Maven](#11-introduction-to-maven)
12. [Maven Project Setup](#12-maven-project-setup)
13. [Interview & Viva Questions](#13-interview--viva-questions)
14. [Markdown Guide for Notepad](#14-markdown-guide-for-notepad)

--------------------------------------------------------------------------------

## 1. DESIGN PATTERNS - FACTORY PATTERN

📍 **Reference Files:**
   - `Design pattern\creational\Simple_Factory_and_Factory_Method\Problem without SimpleFactory.txt`
   - `Design pattern\creational\Simple_Factory_and_Factory_Method\Simple Factory.doc`
   - `Design pattern\creational\Simple_Factory_and_Factory_Method\GUIApp.txt`
   - `Design pattern\creational\Simple_Factory_and_Factory_Method\GUIApp1.txt`

### Problem Without Simple Factory
- When object creation logic is scattered throughout the codebase.
- Changes to object creation require modifications in multiple places.
- Violates the Open/Closed Principle.
- Tight coupling between client code and concrete classes.

### Simple Factory Pattern
- Encapsulates object creation logic in a single place.
- Provides a central point for creating objects.
- Client code doesn't need to know about concrete classes.
- Makes the code more maintainable and testable.

### Factory Method Pattern
- Defines an interface for creating objects.
- Lets subclasses decide which class to instantiate.
- Follows the Open/Closed Principle.
- Promotes loose coupling.

--------------------------------------------------------------------------------

## 2. STATIC VS DYNAMIC CONTENT

📍 **Reference File:**
   - `Day_2_Jakarta_EE\Servlet\Static Content vs Dynamic Content.ppt`

### Static Content
- **Definition:** No matter what the request is, all clients receive the same response.
- Also known as the **Request-Response Model**.
- Client sends a request → Server sends the same response for all.
- Examples: HTML files, CSS files, images, static web pages.
- Client clicks a hyperlink → Server sends that file/content.

### Dynamic Content
- **Definition:** Response varies based on the request.
- Known as the **Request-Response-Process Model**.
- Requires server-side programming.
- Each client gets a different page based on their request.
- Examples: Personalized dashboards, search results, user profiles.

> **Key Point:** Web servers alone cannot perform programming - they need server-side technologies like Servlets, JSP, PHP, etc.

--------------------------------------------------------------------------------

## 3. INTRODUCTION TO SERVLETS

📍 **Reference Files:**
   - `Day_2_Jakarta_EE\Servlet\Servlet infot.txt`
   - `Day_2_Jakarta_EE\Servlet\Java-Enabled Web and Application Server.ppt`

### What is a Servlet?
A Servlet is a Java class that extends the capabilities of servers to handle requests from web clients. It runs on a web server (container) and processes HTTP requests.

### CGI vs Servlet

| Feature | CGI (Common Gateway Interface) | Servlet |
|---------|-------------------------------|---------|
| Process Handling | Creates a new process for each request | Uses threads from a thread pool |
| Resource Usage | Heavy - high memory consumption | Lightweight - efficient memory usage |
| Platform | Platform-dependent | Platform-independent (Java) |
| Performance | Slow startup time | Fast - JVM is already running |
| Scalability | Poor | Excellent |

### Servlet Packages
```java
jakarta.servlet         // Core servlet interfaces and classes
jakarta.servlet.http    // HTTP-specific servlet classes
```
> **Note:** Older versions used `javax.servlet` package. Jakarta EE uses `jakarta.servlet`.

--------------------------------------------------------------------------------

## 4. SERVLET HIERARCHY

📍 **Reference File:**
   - `Day_2_Jakarta_EE\Servlet\Servlet infot.txt`

```
        Servlet (Interface)
            │
            │ implements
            ▼
    GenericServlet (Abstract Class)
            │
            │ extends
            ▼
     HttpServlet (Abstract Class)
            │
            │ extends
            ▼
    YourCustomServlet (Concrete Class)
```

### Servlet Interface
- **Purpose:** Provides the contract (blueprint) for all servlets.
- Defines lifecycle methods: `init()`, `service()`, `destroy()`.
- All methods are abstract.

### GenericServlet
- **Type:** Abstract class.
- **Purpose:** Designed to handle any type of request (HTTP, FTP, SMTP, etc.).
- Implements most methods of Servlet interface.
- Still has some abstract methods left unimplemented.

```java
// Logic: Custom servlet inheriting from GenericServlet
public class MyServlet extends GenericServlet {
    // You MUST implement the abstract "service" method here
    public void service(ServletRequest req, ServletResponse res) {
        // Code to handle request goes here...
    }
}
```

### HttpServlet
- **Type:** Abstract class (even though all methods are implemented).
- **Purpose:** Specifically designed to handle HTTP requests only.
- Implements all remaining abstract methods from parent classes.
- Provides HTTP-specific methods: `doGet()`, `doPost()`, `doPut()`, `doDelete()`, etc.

> **Important:** HttpServlet is declared abstract to prevent direct instantiation - you must extend it and override the required methods.

--------------------------------------------------------------------------------

## 5. SERVLET LIFECYCLE

📍 **Reference File:**
   - `Day_2_Jakarta_EE\Servlet\Servlet infot.txt`

The servlet lifecycle is managed by the **Servlet Container** (e.g., Apache Tomcat). Since servlets are server-side applications, they don't have a `main()` method.

### Lifecycle Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                        SERVLET LIFECYCLE                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. Loading & Instantiation                                         │
│     └── Container loads servlet class and creates instance          │
│         using no-arg constructor (via Reflection API)               │
│                           │                                         │
│                           ▼                                         │
│  2. Initialization                                                  │
│     └── init(ServletConfig) is called ONCE                          │
│         • Create database connections                               │
│         • Lookup remote objects/EJB components                      │
│         • Initialize resources                                      │
│                           │                                         │
│                           ▼                                         │
│  3. Request Handling (Called multiple times)                        │
│     └── service(request, response)                                  │
│         └── Dispatches to doGet() or doPost()                       │
│         • One thread per request from thread pool                   │
│                           │                                         │
│                           ▼                                         │
│  4. Destruction                                                     │
│     └── destroy() is called ONCE when:                              │
│         • Server is shut down                                       │
│         • Application is undeployed                                 │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Lifecycle Methods

#### 1. `init(ServletConfig config)`
```java
// Called BY THE SERVER immediately after creating the servlet instance
// This happens ONLY ONCE in the servlet's life.
public void init(ServletConfig config) throws ServletException {
    // Example Initialization tasks:
    // 1. Establish connection to the database
    // 2. Read configuration files
    // 3. Set up initial values
}
```

#### 2. `service(HttpServletRequest request, HttpServletResponse response)`
```java
// Called for EVERY SINGLE REQUEST coming from a client (browser)
// It checks the request type (GET, POST, etc.) and calls the matching method
protected void service(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    // The Container calls this automatically.
    // If request is GET -> calls doGet()
    // If request is POST -> calls doPost()
    // Note: You usually DO NOT override this method in HttpServlet.
}
```

#### 3. `destroy()`
```java
// Called ONLY ONCE when the server is shutting down or removing the servlet
public void destroy() {
    // Cleanup tasks:
    // 1. Close active database connections
    // 2. Release memory resources
    // 3. Save any final data
}
```

### DD (Deployment Descriptor)
- The `web.xml` file that configures servlet mappings.
- Can also use annotations (`@WebServlet`) as an alternative.

--------------------------------------------------------------------------------

## 6. HTTP METHODS - doGet() and doPost()

📍 **Reference File:**
   - `Day_2_Jakarta_EE\Servlet\Servlet infot.txt`

### Key Interfaces

| Interface | Parent | Description |
|-----------|--------|-------------|
| `HttpServletRequest` | `ServletRequest` | Contains request data from client |
| `HttpServletResponse` | `ServletResponse` | Used to send response to client |

### doGet() Method
```java
// Logic: Handles standard requests (like typing a URL in a browser or clicking a link)
protected void doGet(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    // 1. Browsers send detailed info in "request" (parameters, headers)
    // 2. We write our reply into "response" object
    
    // Characteristics:
    // - Data sent in URL bar (Query String)
    // - NOT secure (visible to everyone)
    // - Size limited (~2KB)
}
```

### doPost() Method
```java
// Logic: Handles form submissions where method="post"
protected void doPost(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    // 1. Used for submitting forms (Login, Register)
    // 2. Data is HIDDEN inside the request body
    
    // Characteristics:
    // - Data NOT visible in URL
    // - Unlimited size (good for file uploads)
    // - Secure for passwords
}
```

### GET vs POST Comparison

| Feature | GET | POST |
|---------|-----|------|
| Data Location | URL query string | Request body |
| Visibility | Visible in URL | Hidden |
| Data Size | Limited (~2KB) | Unlimited |
| Bookmarkable | Yes | No |
| Security | Less secure | More secure |
| Caching | Can be cached | Not cached |
| Idempotent | Yes | No |

--------------------------------------------------------------------------------

## 7. CREATING YOUR FIRST SERVLET

📍 **Reference Files:**
   - `Day_2_Jakarta_EE\Servlet\Web Application Strucure as per JavaEE Spec\`
   - `Day_2_Jakarta_EE\Servlet\Java-Enabled Web and Application Server.ppt`

### Creating the Project
1. Open Eclipse → Window → Show View → Servers.
2. If no server, create new **Apache Tomcat v10.1**.
3. Create **Dynamic Web Project** (Module version 5.0).
4. Right-click project → New → **Servlet**.

### Code: FirstServlet.java
```java
// Import necessary Jakarta EE classes (formerly javax)
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

// @WebServlet annotation maps this class to the URL path "/firstserv"
// So, accessing http://localhost:8080/ProjectName/firstserv will run this code
@WebServlet("/firstserv")
public class FirstServlet extends HttpServlet {
    
    // Serial version ID for serialization (best practice for Serializable classes)
    private static final long serialVersionUID = 1L;

    /**
     * Handles HTTP GET requests.
     * Triggered when you visit the URL in a browser.
     */
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // --- Method 1: Using PrintWriter (Standard for Text/HTML) ---
        // getWriter() returns a character stream to write text responses
        PrintWriter pw = response.getWriter();
        
        // Write HTML or Text to the client (browser)
        pw.println("Welcome to the First Servlet!");
        
        // --- Method 2: Using ServletOutputStream (For binary data like images) ---
        // ServletOutputStream sos = response.getOutputStream();
        // sos.println("Welcome to the First Servlet!");
        
        // Note: You can only use ONE of them (Writer or OutputStream), not both.
    }
}
```

--------------------------------------------------------------------------------

## 8. SERVLET WITH HTML FORM

📍 **Reference Files:**
   - `Day_2_Jakarta_EE\Servlet\person.html`
   - `Day_2_Jakarta_EE\Servlet\PersonServ.java`

### Form Code: person.html
```html
<!DOCTYPE html>
<html>
<head>
    <title>Person Form</title>
</head>
<body>
    <h2>Enter Person Details</h2>
    <!-- 
        action="PersonServ" -> connects to the Servlet mapped to "/PersonServ"
        method="post"       -> sends data securely in request body (calls doPost)
    -->
    <form action="PersonServ" method="post">
        Enter name: <input type="text" name="name"/>
        <br><br>
        Enter age: <input type="text" name="age"/>
        <br><br>
        <input type="submit" value="Submit">
    </form>
</body>
</html>
```

### Servlet Code: PersonServ.java
```java
import java.io.*;
import jakarta.servlet.*;
import jakarta.servlet.http.*;
import jakarta.servlet.annotation.WebServlet;

@WebServlet("/PersonServ")
public class PersonServ extends HttpServlet {
    
    // Overriding doPost() because the HTML form uses method="post"
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws IOException, ServletException {
        
        // 1. Tell browser we are sending back HTML text
        response.setContentType("text/html");
        
        // 2. Get the output stream to write the response
        PrintWriter pw = response.getWriter();

        // 3. Capture data sent from the HTML form inputs
        String name = request.getParameter("name"); // gets value from input name="name"
        
        // getParameter always returns String, so we must parse it to int for calculations
        // .trim() removes any accidental spaces user might have typed
        int age = Integer.parseInt(request.getParameter("age").trim());

        // 4. Send response back to the user
        pw.println("<html><body>");
        pw.println("<h2>Welcome, " + name + "!</h2>");
        pw.println("<p>Your age is: " + age + "</p>");
        pw.println("</body></html>");
    }
}
```

--------------------------------------------------------------------------------

## 9. SERVLET WITH DATABASE CONNECTIVITY

📍 **Reference File:**
   - `Day_2_Jakarta_EE\Servlet\PersonServ.java` (JDBC version)

### Database Setup (MySQL)
```sql
CREATE DATABASE IF NOT EXISTS mydb;
USE mydb;
CREATE TABLE person (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(30),
    age INT
);
```

### JDBC Servlet Code
```java
import jakarta.servlet.ServletConfig;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

@WebServlet("/PersonServ")
public class PersonServ extends HttpServlet {
    private static final long serialVersionUID = 1L;
    
    // Declare Connection object as a member so it can be reused
    private Connection con = null;

    /**
     * init() method: Best place for DB connection code.
     * It runs ONLY ONCE when the servlet starts.
     */
    @Override
    public void init(ServletConfig config) throws ServletException {
        super.init(config);
        try {
            // 1. Load the MySQL JDBC Driver class dynamically
            Class.forName("com.mysql.cj.jdbc.Driver");
            
            // 2. Establish the connection string (Path to DB)
            String url = "jdbc:mysql://localhost:3306/mydb";
            
            // 3. Connect using username and password
            con = DriverManager.getConnection(url, "root", "Root");
            
            System.out.println("Database connection established!");
        } catch (Exception e) {
            e.printStackTrace();
            // Wrap exception in ServletException to inform the container
            throw new ServletException("Database connection failed", e);
        }
    }

    /**
     * doPost(): Handles the form submission and saves data to DB
     */
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        response.setContentType("text/html");
        PrintWriter pw = response.getWriter();
        
        // Get user input
        String name = request.getParameter("name");
        int age = Integer.parseInt(request.getParameter("age").trim());
        
        try {
            // Using PreparedStatement to prevent SQL Injection
            String sql = "INSERT INTO person(name, age) VALUES(?, ?)";
            PreparedStatement pst = con.prepareStatement(sql);
            
            // Set values for the '?' placeholders
            pst.setString(1, name); // First ?
            pst.setInt(2, age);     // Second ?
            
            // Execute the update (returns number of rows affected)
            int rowsAffected = pst.executeUpdate();
            
            // Feedback to user
            if (rowsAffected > 0) {
                pw.println("<h3>✅ Record added to the database successfully!</h3>");
            } else {
                pw.println("<h3>❌ Record not added!</h3>");
            }
            
            // Note: We close the PreparedStatement, but NOT the Connection 'con'
            // 'con' is kept open for future requests (until destroy)
            pst.close();
        } catch (Exception e) {
            e.printStackTrace();
            pw.println("<h3>Error: " + e.getMessage() + "</h3>");
        }
    }

    /**
     * destroy(): Runs when server stops. Close the shared connection here.
     */
    @Override
    public void destroy() {
        try {
            if (con != null && !con.isClosed()) {
                con.close();
                System.out.println("Database connection closed.");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

--------------------------------------------------------------------------------

## 10. USING SINGLETON PATTERN FOR DATABASE CONNECTION

📍 **Reference Files:**
   - `Day_2_Jakarta_EE\Servlet\JDBC_using_Properties\SingletonCon.java`
   - `Day_2_Jakarta_EE\Servlet\JDBC_using_Properties\FourthServ.java`

### SingletonCon.java
```java
package mypack;

import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.util.Properties;

// Singleton Class: Ensures only ONE instance of the Connection exists
public class SingletonCon {
    // Static variable to hold the single connection instance
    private static Connection con = null;

    // Private constructor prevents others from saying 'new SingletonCon()'
    private SingletonCon() {
    }

    // Static method to get the connection
    public static Connection getCon() throws Exception {
        // Only create a new connection if one doesn't exist or is closed
        if (con == null || con.isClosed()) {
            
            // Load configuration from 'db.properties' file
            Properties props = new Properties();
            
            // getResourceAsStream looks for the file in the classpath (src/main/resources)
            InputStream is = SingletonCon.class.getResourceAsStream("/mypack/db.properties");
            props.load(is);

            // Extract credentials
            String driver = props.getProperty("driver");
            String url = props.getProperty("url");
            String username = props.getProperty("username");
            String password = props.getProperty("password");

            // Create the connection
            Class.forName(driver);
            con = DriverManager.getConnection(url, username, password);
        }
        // Return existing connection
        return con;
    }
}
```

### db.properties
```properties
# Key=Value pairs for database configuration
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/mydb
username=root
password=Root
```

--------------------------------------------------------------------------------

## 11. INTRODUCTION TO MAVEN

📍 **Reference Folder:**
   - `Day_2_Jakarta_EE\Maven_Folder\`

### What is Maven?
Maven is a **build automation and dependency management tool** primarily used for Java projects.

### Key Features
- Automatic dependency download and management
- Standardized project structure
- Build lifecycle management
- Plugin-based architecture

### Maven Architecture
```
┌─────────────────────────────────────────────────────────────────┐
│                     MAVEN ARCHITECTURE                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌─────────────────────┐                                       │
│   │   Your Application  │                                       │
│   │      (pom.xml)      │                                       │
│   └──────────┬──────────┘                                       │
│              │                                                  │
│              ▼                                                  │
│   ┌─────────────────────┐      Not found      ┌───────────────┐│
│   │    Local Repository │◄────────────────────│    Central    ││
│   │  (C:\Users\.m2)     │      download       │   Repository  ││
│   └──────────┬──────────┘                     │  (internet)   ││
│              │                                └───────────────┘│
│              ▼                                                  │
│   ┌─────────────────────┐                                       │
│   │   Application Build │                                       │
│   │      (Success)      │                                       │
│   └─────────────────────┘                                       │
└─────────────────────────────────────────────────────────────────┘
```

--------------------------------------------------------------------------------

## 12. MAVEN PROJECT SETUP

📍 **Reference Folder:**
   - `Day_2_Jakarta_EE\Maven_Folder\`

### Sample pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    
    <!-- Maven Model Version -->
    <modelVersion>4.0.0</modelVersion>
    
    <!-- Project Identifiers -->
    <groupId>com.example</groupId>
    <artifactId>mywebapp</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>
    
    <dependencies>
        <!-- 1. Jakarta Servlet API -->
        <dependency>
            <groupId>jakarta.servlet</groupId>
            <artifactId>jakarta.servlet-api</artifactId>
            <version>6.0.0</version>
            <scope>provided</scope>
        </dependency>
        
        <!-- 2. MySQL Connector -->
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <version>8.0.33</version>
        </dependency>
    </dependencies>
</project>
```

--------------------------------------------------------------------------------

## 13. INTERVIEW & VIVA QUESTIONS

(Listed below are top questions for your viva)

1.  **What is a Servlet?**
    A Servlet is a Java class that runs on a web server and handles HTTP requests, generating dynamic content.

2.  **CGI vs Servlet?**
    CGI creates a new process per request (slow/heavy). Servlet uses threads (fast/lightweight).

3.  **Servlet Lifecycle?**
    Init() -> Service() -> Destroy().

4.  **GenericServlet vs HttpServlet?**
    GenericServlet handles any protocol. HttpServlet is specifically for HTTP.

5.  **doGet vs doPost?**
    GET puts data in URL (insecure, limited). POST puts data in body (secure, unlimited).

(Refer to previous sections for the full list of 50 questions)

--------------------------------------------------------------------------------

## 14. MARKDOWN GUIDE FOR NOTEPAD

Since you are editing in a text editor like Notepad, here is how the formatting symbols work:

- **# Space Text**   -> Heading 1 (Main Title)
- **## Space Text**  -> Heading 2 (Section Title)
- **### Space Text** -> Heading 3 (Subsection)
- ****Text****       -> Bold Text
- ***Text***         -> Italic Text
- **- Space Text**   -> Bullet Point
- **`Code`**         -> Inline Code
- **```java ... ```** -> Code Block
- **> Text**         -> Quote/Note
- **---**            -> Horizontal Line

================================================================================
END OF NOTES
================================================================================
