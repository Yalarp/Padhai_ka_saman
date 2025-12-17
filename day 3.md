# Design Patterns & Jakarta EE Notes (Day 2 & 3)

This document covers **Factory Design Patterns**, **Database Connection Pooling**, **JNDI**, **Servlet Parameters**, and **Request Dispatching**. It includes comprehensive explanations, code examples, and interview questions.

---

## 1. Design Patterns: Creational

### Simple Factory vs. Factory Method

#### The Problem with "Simple Factory"
Imagine you are building a UI framework. You have different users:
- **Windows Users**: Expect Windows-style buttons.
- **Linux Users**: Expect Linux-style buttons.
- **macOS Users**: Expect macOS-style buttons.

If you use a single "Simple Factory" class with a bunch of `if-else` statements:
```java
class ButtonFactory {
    public Button createButton(String os) {
        if (os.equals("Windows")) return new WindowsButton();
        else if (os.equals("Linux")) return new LinuxButton();
        // ... adding Mac requires modifying this code!
    }
}
```
**Problem:** This violates the **Open/Closed Principle** (Code should be open for extension but closed for modification). Every time you add a new OS, you break into the existing tested code to add another `else-if`.

#### The Solution: Factory Method Pattern
Instead of one central factory deciding everything, we define a **contract** (Interface/Abstract Class) for creating objects, but let **subclasses** decide which specific class to instantiate.

> **Core Idea**: Define an interface for creating an object, but let subclasses decide which class to instantiate.

**Structure:**
1.  **Product (Interface)**: `Button` (The common interface for all buttons).
2.  **Concrete Product**: `WindowsButton`, `LinuxButton` (The actual objects).
3.  **Creator (Abstract)**: `Dialog` (Declares the factory method `createButton()`).
4.  **Concrete Creator**: `WindowsDialog`, `LinuxDialog` (Implements the factory method to return the specific button).

**Benefit**: If you need to add "Mac Support", you simply create a `MacDialog` class. You do **not** touch the existing `WindowsDialog` or `LinuxDialog` code.

---

## 2. Jakarta EE Day 2: Connection Pooling & JNDI

### Database Connection Pooling

#### Why do we need it?
Connecting to a database manually (`DriverManager.getConnection()`) is an efficient process:
1.  Load Driver.
2.  Open physical TCP/IP connection (Handshake, Auth).
3.  Execute Query.
4.  Close Connection.

Doing this for **every single request** is slow and wastes resources (CPU, Memory).

#### What is a Connection Pool?
A **Connection Pool** is like a "Taxi Stand" or a cache of open database connections.
- **Analogy**:
    - **Without Pool**: every time a passenger needs a ride, you build a new car, drive them, and then scrap the car.
    - **With Pool**: Taxis (connections) are waiting at the stand. Passenger jumps in (borrows connection), goes to destination (runs query), and gets out. The taxi returns to the stand for the next passenger.

#### Key Differences

| Feature | Without Pool (`DriverManager`) | With Pool (`DataSource`) |
| :--- | :--- | :--- |
| **Creation** | New connection every time. | Reuses existing idle connections. |
| **Closing** | `con.close()` destroys connection. | `con.close()` **returns** it to pool. |
| **Performance** | Slow (Heavy overhead). | Fast (Already connected). |
| **Coupling** | Tightly coupled (Hardcoded DB creds). | Loosely coupled (Configured in Server). |

### JNDI (Java Naming and Directory Interface)

#### Concept
JNDI is an API that allows Java applications to look up data and objects via a **name**. It works like a generic "Search" interface for different systems.

- **Naming Service**: Binds a **Name** to an **Object** (Key-Value pair).
    - **DNS**: Maps `google.com` -> `142.250.x.x`
    - **File System**: Maps `C:\data.txt` -> File on disk
    - **LDAP**: Maps User ID -> User Info

#### How Jakarta EE uses JNDI
We don't want to hardcode database passwords in our Java code. Instead:
1.  **Administrator** configures the Connection Pool (DataSource) in the Server (Tomcat) and gives it a name (e.g., `jdbc/mypool`).
2.  **Server** binds this implementation to the JNDI tree under `java:comp/env/jdbc/mypool`.
3.  **Developer** asks for it by name.

#### Dependency Injection (DI) Example
Instead of writing complex JNDI lookup code manually, we use Annotations module to inject the resource.

```java
// We ask the server: "Give me the object listed under this name"
@Resource(lookup = "java:comp/env/jdbc/mypool")
private DataSource ds; 

// Later in doGet...
try (Connection con = ds.getConnection()) {
    // Use the connection
}
```

---

## 3. Jakarta EE Day 3: Parameters & Scopes

Parameters are used to pass data. The scope (lifetime) and visibility depend on the type of parameter.

### 1. Request Parameters
- **Source**: User input (HTML Forms, URL query strings like `?id=5`).
- **Scope**: **One Request**. Data is lost after response is sent.
- **Access**: `request.getParameter("username")`
- **Use Case**: Login form data, search queries.

### 2. Initialization Parameters (`<init-param>`)
- **Source**: `web.xml` (Deployment Descriptor) relative to a specific Servlet.
- **Scope**: **One Servlet**. Only visible to the Servlet it is defined for.
- **Access**: `getServletConfig().getInitParameter("adminEmail")`
- **Use Case**: Configuration specific to one module (e.g., File path for a specific report generator).

### 3. Context Parameters (`<context-param>`)
- **Source**: `web.xml` (Global level).
- **Scope**: **Entire Application**. Visible to ALL Servlets/JSPs.
- **Access**: `getServletContext().getInitParameter("dbURL")`
- **Use Case**: Global settings like Support Email, Company Name, Generic Database URL.

### ServletConfig vs. ServletContext

| Feature | ServletConfig | ServletContext |
| :--- | :--- | :--- |
| **Analogy** | **Personal Cubicle** (Only you see it) | **Office Notice Board** (Everyone sees it) |
| **Quantity** | One per **Servlet** | One per **Web App** |
| **Created** | During Servlet Initialization (`init()`) | At App Startup |
| **Used For** | Content specific to one servlet | Global app configuration |

---

## 4. Request Dispatching: Forward vs. Redirect

When a Servlet needs to pass control to another resource (Servlet/JSP).

### 1. Forward (`RequestDispatcher.forward()`)
- **Mechanism**: **Internal** hand-off. The server asks Servlet A, A calls B internally.
- **Browser URL**: **Does NOT change**. Code is secret to the user.
- **Request Object**: **Shared**. The same request object (with data) is passed along.
- **Speed**: Faster (1 Request/Response cycle).
- **Usage**: Passing data from Controller (Servlet) to View (JSP).

```java
RequestDispatcher rd = request.getRequestDispatcher("NextServlet");
rd.forward(request, response);
```

### 2. Redirect (`response.sendRedirect()`)
- **Mechanism**: **External** round-trip. Server tells Browser: "Go to this new URL". Browser makes a NEW request.
- **Browser URL**: **Changes** to the new URL.
- **Request Object**: **New**. Old request data is lost.
- **Speed**: Slower (2 Request/Response cycles).
- **Usage**: After a successful POST (like Payment or Login) to prevent form resubmission on refresh.

```java
response.sendRedirect("http://google.com"); // Can go to external sites
```

### 3. Include (`RequestDispatcher.include()`)
- Keeps the main Servlet in charge but "includes" the output of another resource (like a header or footer).
- The response is a mix of Servlet A + Servlet B.

---

## 5. Interview Questions (Viva Voce)

### Design Patterns
1.  **What is the core difference between Simple Factory and Factory Method?**
    *Answer: Simple Factory is a concrete class with hardcoded logic (violates Open/Closed). Factory Method is an interface/abstract pattern that lets subclasses decide modification.*
2.  **Why is the Factory Method pattern considered "loosely coupled"?**
    *Answer: The client code deals with the Interface (Creator), not the specific Concrete Classes.*
3.  **Give a real-world example of Factory Method.**
    *Answer: `java.util.Calendar.getInstance()` (returns specific calendar based on Locale) or UI toolkits returning OS-specific buttons.*
4.  **What problem does the Singleton pattern solve?**
    *Answer: Ensures a class has only one instance and provides a global point of access.*

### Connection Pooling
5.  **Why is `DriverManager.getConnection()` slow?**
    *Answer: It performs a TCP handshake and authentication for every single call.*
6.  **What happens when you close a connection in a pool (`con.close()`)?**
    *Answer: It does not close the physical socket; it returns the connection object to the pool for reuse.*
7.  **What is a "Connection Leak"?**
    *Answer: When code gets a connection but fails to close/return it. The pool eventually runs out of connections.*
8.  **Who manages the Connection Pool in a Jakarta EE app?**
    *Answer: Usually the Web Container / Application Server (e.g., Tomcat).*
9.  **What is `DataSource`?**
    *Answer: An interface (from `javax.sql`) acting as a factory for connections, often wrapping a pool.*
10. **How do you access a DataSource configured in the server?**
    *Answer: Using JNDI lookup or `@Resource` injection.*

### JNDI
11. **What does JNDI stand for?**
    *Answer: Java Naming and Directory Interface.*
12. **What is the purpose of JNDI?**
    *Answer: To decouple the application from resource creation/location by looking them up by name.*
13. **What is `java:comp/env`?**
    *Answer: The standard JNDI namespace for component-scoped environment variables.*
14. **Can JNDI be used for things other than Databases?**
    *Answer: Yes, for EJBs, JMS Queues, Environment variables, etc.*

### Servlet Parameters
15. **Difference between `getParameter` and `getAttribute`?**
    *Answer: `getParameter` returns String (from HTML/URL). `getAttribute` returns Object (set internally by code).*
16. **Where do you define Initialization Parameters?**
    *Answer: In `web.xml` inside the `<servlet>` tag.*
17. **How many `ServletContext` objects exist in an application?**
    *Answer: Exactly one per Web Application.*
18. **How many `ServletConfig` objects exist?**
    *Answer: One per Servlet.*
19. **If I have a global email for the whole app, where should I store it?**
    *Answer: In `<context-param>` (Context Parameter).*
20. **Can a Servlet read another Servlet's Init Parameters?**
    *Answer: No, they are private to that specific Servlet.*

### Request Dispatching
21. **What is the main difference between Forward and Redirect regarding the URL bar?**
    *Answer: Forward keeps the URL same; Redirect changes it.*
22. **Which is faster: Forward or Redirect? Why?**
    *Answer: Forward. It happens internally on the server (1 request). Redirect requires a client round-trip (2 requests).*
23. **If I store data in `request.setAttribute()` and use Redirect, is the data available?**
    *Answer: No. Redirect creates a new request, so the old attributes are lost.*
24. **Can `RequestDispatcher.forward()` send a user to Google.com?**
    *Answer: No, it works only within the same web application context.*
25. **What is `include()` used for?**
    *Answer: Merging content, like including a standard Header or Footer JSP in multiple pages.*

### General Jakarta EE
26. **What is the lifecycle of a Servlet?**
    *Answer: Load -> Instantiation -> `init()` -> `service()` (doGet/doPost) -> `destroy()`.*
27. **When is the `init()` method called?**
    *Answer: Once, when the Servlet is first created (either at startup or first request).*
28. **Is `Servlet` thread-safe?**
    *Answer: No. One instance handles multiple threads. Do not use instance variables for request data.*
29. **What annotation replaces `web.xml` servlet mapping?**
    *Answer: `@WebServlet("/url")`.*
30. **What is the default HTTP method for a Form?**
    *Answer: GET.*

### Scenarios
31. **Scenario: You need to pass a list of Users from a Servlet to a JSP to display. Which scope do you use?**
    *Answer: Request Scope (`request.setAttribute("users", list)`).*
32. **Scenario: You want to log the number of active users currently on the site. Which scope?**
    *Answer: Application/Context Scope.*
33. **Scenario: You have a payment page. After payment, where do you take the user and how?**
    *Answer: Redirect to a "Success" page (to prevent double payment on refresh).*
34. **Scenario: Login failed. What do you do?**
    *Answer: Forward back to the Login Page (usually with an error message).*

### Code-Based
35. **Write the line to get a Request Dispatcher.**
    *Answer: `RequestDispatcher rd = request.getRequestDispatcher("path");`*
36. **How do you retrieve a parameter named "age"?**
    *Answer: `String age = request.getParameter("age");`*
37. **How do you setting the content type of a response?**
    *Answer: `response.setContentType("text/html");`*
38. **What interface represents the connection pool in Java code?**
    *Answer: `javax.sql.DataSource`.*
39. **Syntax to inject a resource?**
    *Answer: `@Resource(name="...")`.*
40. **Does `destroy()` kill the servlet instance?**
    *Answer: Yes, it is the last method called before garbage collection.*
