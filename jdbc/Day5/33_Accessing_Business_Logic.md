# Accessing Business Logic in Web Applications

## Table of Contents
1. [Introduction](#1-introduction)
2. [MVC Architecture](#2-mvc-architecture)
3. [Separating Business Logic](#3-separating-business-logic)
4. [POJO for Business Logic](#4-pojo-for-business-logic)
5. [Complete Code Example](#5-complete-code-example)
6. [Data Flow](#6-data-flow)
7. [Key Points to Remember](#7-key-points-to-remember)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

Business logic should never be directly in servlets or JSPs. Following the MVC pattern, business logic belongs in separate Java classes (Model layer), enabling better code organization, reusability, and testability.

---

## 2. MVC Architecture

### Model-View-Controller Pattern

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         MVC ARCHITECTURE                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│     ┌──────────┐              ┌──────────────┐                             │
│     │  User    │              │   MODEL      │                             │
│     │ (Browser)│              │  (POJOs)     │                             │
│     └────┬─────┘              │              │                             │
│          │                    │ • User.java  │                             │
│          │ HTTP Request       │ • Product.java│                            │
│          │                    │ • Order.java │                             │
│          ▼                    │              │                             │
│     ┌──────────┐              │ + Business   │                             │
│     │CONTROLLER│◄────────────►│   Services   │                             │
│     │(Servlet) │  calls/uses  │              │                             │
│     │          │              └──────────────┘                             │
│     │ Decides  │                                                           │
│     │ what to  │                                                           │
│     │ do       │                                                           │
│     └────┬─────┘                                                           │
│          │                                                                 │
│          │ forwards to                                                     │
│          ▼                                                                 │
│     ┌──────────┐                                                           │
│     │  VIEW    │                                                           │
│     │  (JSP)   │──────────────────────────────────────────► Response       │
│     │          │                                                           │
│     │ Renders  │                                                           │
│     │ HTML     │                                                           │
│     └──────────┘                                                           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Responsibilities

| Layer | Component | Responsibility |
|-------|-----------|----------------|
| **Model** | POJOs, Services | Business logic, data access |
| **View** | JSP | Display data, user interface |
| **Controller** | Servlet | Handle requests, coordinate flow |

---

## 3. Separating Business Logic

### Wrong Way (Logic in Servlet/JSP)

```jsp
<%-- BAD: Business logic in JSP --%>
<%
    Connection conn = DriverManager.getConnection(...);
    PreparedStatement ps = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
    ps.setInt(1, userId);
    ResultSet rs = ps.executeQuery();
    
    if (rs.next()) {
        String name = rs.getString("name");
        double salary = rs.getDouble("salary");
        double tax = salary * 0.3;  // Business logic!
        double bonus = salary > 50000 ? salary * 0.1 : 0;  // More logic!
    }
%>
```

### Right Way (Logic in POJO)

```java
// Model: User.java
public class User {
    private String name;
    private double salary;
    
    public double calculateTax() {
        return salary * 0.3;  // Business logic in model
    }
    
    public double calculateBonus() {
        return salary > 50000 ? salary * 0.1 : 0;  // Business logic in model
    }
}

// Service: UserService.java
public class UserService {
    public User findById(int id) {
        // Database access logic
    }
}
```

---

## 4. POJO for Business Logic

### What is a POJO?

POJO (Plain Old Java Object) is a simple Java class without any special restrictions:
- No need to extend specific classes
- No need to implement specific interfaces
- Just regular properties, getters, setters, and methods

### MyClass.java - Business Logic POJO

```java
package pack1;

public class MyClass {
    
    private int x;
    private int y;
    
    // Constructor
    public MyClass() {}
    
    public MyClass(int x, int y) {
        this.x = x;
        this.y = y;
    }
    
    // Getters and Setters
    public int getX() { return x; }
    public void setX(int x) { this.x = x; }
    
    public int getY() { return y; }
    public void setY(int y) { this.y = y; }
    
    // Business Logic Methods
    public int add() {
        return x + y;
    }
    
    public int subtract() {
        return x - y;
    }
    
    public int multiply() {
        return x * y;
    }
    
    public double divide() {
        if (y == 0) {
            throw new ArithmeticException("Cannot divide by zero");
        }
        return (double) x / y;
    }
    
    // Display method
    public void disp() {
        System.out.println("x = " + x + ", y = " + y);
    }
}
```

---

## 5. Complete Code Example

### Project Structure

```
WebApp/
├── src/
│   ├── pack1/
│   │   └── MyClass.java        (Model - Business Logic)
│   └── servlets/
│       └── CalculatorServlet.java  (Controller)
├── webapp/
│   ├── index.html              (User Input)
│   ├── result.jsp              (View)
│   └── WEB-INF/
│       └── web.xml
```

### index.html - Input Form

```html
<!DOCTYPE html>
<html>
<head>
    <title>Calculator</title>
</head>
<body>
    <h2>Calculator using MVC</h2>
    <form action="calculate" method="post">
        Number 1: <input type="text" name="num1"><br><br>
        Number 2: <input type="text" name="num2"><br><br>
        Operation: 
        <select name="operation">
            <option value="add">Add</option>
            <option value="subtract">Subtract</option>
            <option value="multiply">Multiply</option>
            <option value="divide">Divide</option>
        </select><br><br>
        <input type="submit" value="Calculate">
    </form>
</body>
</html>
```

### CalculatorServlet.java - Controller

```java
package servlets;

import pack1.MyClass;
import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.annotation.*;
import java.io.*;

@WebServlet("/calculate")
public class CalculatorServlet extends HttpServlet {
    
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // Step 1: Get input from request
        int num1 = Integer.parseInt(request.getParameter("num1"));
        int num2 = Integer.parseInt(request.getParameter("num2"));
        String operation = request.getParameter("operation");
        
        // Step 2: Create business object (Model)
        MyClass calculator = new MyClass(num1, num2);
        
        // Step 3: Call business logic
        double result = 0;
        switch (operation) {
            case "add":
                result = calculator.add();
                break;
            case "subtract":
                result = calculator.subtract();
                break;
            case "multiply":
                result = calculator.multiply();
                break;
            case "divide":
                result = calculator.divide();
                break;
        }
        
        // Step 4: Set data for view
        request.setAttribute("num1", num1);
        request.setAttribute("num2", num2);
        request.setAttribute("operation", operation);
        request.setAttribute("result", result);
        request.setAttribute("calculator", calculator);  // Pass object to JSP
        
        // Step 5: Forward to view (JSP)
        RequestDispatcher rd = request.getRequestDispatcher("result.jsp");
        rd.forward(request, response);
    }
}
```

### result.jsp - View

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>Result</title>
    <style>
        .result-box {
            background: #e8f5e9;
            padding: 20px;
            border-radius: 10px;
            max-width: 400px;
            margin: 50px auto;
        }
    </style>
</head>
<body>
    <div class="result-box">
        <h2>Calculation Result</h2>
        
        <%-- Using EL to access request attributes --%>
        <p>Number 1: ${num1}</p>
        <p>Number 2: ${num2}</p>
        <p>Operation: ${operation}</p>
        <p><strong>Result: ${result}</strong></p>
        
        <hr>
        
        <%-- Using jsp:useBean to access the object --%>
        <jsp:useBean id="calculator" class="pack1.MyClass" scope="request"/>
        <p>X value from bean: <jsp:getProperty name="calculator" property="x"/></p>
        <p>Y value from bean: <jsp:getProperty name="calculator" property="y"/></p>
        
        <hr>
        
        <%-- Direct method call (not recommended but possible) --%>
        <p>Add result: ${calculator.add()}</p>
        <p>Multiply result: ${calculator.multiply()}</p>
        
        <p><a href="index.html">Calculate Again</a></p>
    </div>
</body>
</html>
```

---

## 6. Data Flow

### Request Flow Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    COMPLETE DATA FLOW                                        │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. User fills form in index.html                                           │
│     ┌─────────────────────────────────┐                                    │
│     │ num1: 10                        │                                    │
│     │ num2: 5                         │                                    │
│     │ operation: add                  │                                    │
│     └───────────────┬─────────────────┘                                    │
│                     │                                                       │
│                     ▼ POST /calculate                                       │
│                                                                             │
│  2. CalculatorServlet (Controller) receives request                         │
│     ┌─────────────────────────────────┐                                    │
│     │ Extract parameters              │                                    │
│     │ num1 = 10, num2 = 5             │                                    │
│     └───────────────┬─────────────────┘                                    │
│                     │                                                       │
│                     ▼                                                       │
│                                                                             │
│  3. Servlet creates MyClass object (Model)                                  │
│     ┌─────────────────────────────────┐                                    │
│     │ MyClass calc = new MyClass(10,5)│                                    │
│     │ result = calc.add(); // 15      │                                    │
│     └───────────────┬─────────────────┘                                    │
│                     │                                                       │
│                     ▼                                                       │
│                                                                             │
│  4. Servlet sets request attributes                                         │
│     ┌─────────────────────────────────┐                                    │
│     │ request.setAttribute("result",15)│                                   │
│     │ request.setAttribute("calc",calc)│                                   │
│     └───────────────┬─────────────────┘                                    │
│                     │                                                       │
│                     ▼ forward                                               │
│                                                                             │
│  5. result.jsp (View) displays data                                         │
│     ┌─────────────────────────────────┐                                    │
│     │ ${result}  → 15                 │                                    │
│     │ ${calc.x}  → 10                 │                                    │
│     │ ${calc.y}  → 5                  │                                    │
│     └───────────────┬─────────────────┘                                    │
│                     │                                                       │
│                     ▼ HTML Response                                         │
│                                                                             │
│  6. User sees result in browser                                             │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 7. Key Points to Remember

1. **Business logic in Model** - Not in Servlet or JSP

2. **Servlet is Controller** - Receives request, calls model, forwards to view

3. **JSP is View** - Only displays data, minimal Java code

4. **POJO = Plain Old Java Object** - Simple class with no special requirements

5. **Forward not Redirect** - To preserve request attributes

6. **EL accesses model** - `${object.property}` calls getProperty()

7. **useBean can access** - Objects set in request scope

8. **Separation benefits**:
   - Testability (test model without web container)
   - Reusability (use model in different apps)
   - Maintainability (change view without changing logic)

9. **Services for complex logic** - Separate from POJOs

10. **DAO for data access** - Separate from business logic

---

## 8. Interview Questions

### Basic Level

**Q1: What is MVC?**
> MVC (Model-View-Controller) is a design pattern that separates an application into three components:
> - Model: Business logic and data
> - View: User interface (JSP)
> - Controller: Request handling (Servlet)

**Q2: What is a POJO?**
> POJO (Plain Old Java Object) is a simple Java class with properties, getters, setters, and methods. It has no special requirements like implementing interfaces or extending specific classes.

**Q3: Why should business logic not be in JSP?**
> - Hard to test
> - Not reusable
> - Mixed concerns
> - Difficult to maintain
> - Can't be used in non-web applications

### Intermediate Level

**Q4: How do you pass data from servlet to JSP?**
```java
// In Servlet
request.setAttribute("user", userObject);
RequestDispatcher rd = request.getRequestDispatcher("view.jsp");
rd.forward(request, response);

// In JSP
${user.name}  // Access using EL
```

**Q5: What's the difference between using forward vs redirect to show results?**
| Forward | Redirect |
|---------|----------|
| Request attributes preserved | Request attributes lost |
| Same request | New request |
| URL unchanged | URL changes |
| Faster | Extra round trip |

**Q6: How do you access a POJO method in JSP using EL?**
```jsp
<%-- Property access (calls getX()) --%>
${myObject.x}

<%-- Method call (EL 3.0+) --%>
${myObject.calculateTotal()}
```

### Advanced Level

**Q7: Explain the layered architecture in enterprise applications.**
```
┌────────────────────────────────┐
│        Presentation Layer      │  (Servlets, JSP)
├────────────────────────────────┤
│         Service Layer          │  (Business Logic)
├────────────────────────────────┤
│        DAO/Repository Layer    │  (Data Access)
├────────────────────────────────┤
│         Database               │  (Persistence)
└────────────────────────────────┘
```

**Q8: How would you implement dependency injection without a framework?**
```java
// Service class
public class UserService {
    private UserDAO userDAO;
    
    // Constructor injection
    public UserService(UserDAO userDAO) {
        this.userDAO = userDAO;
    }
}

// In Servlet
UserDAO dao = new UserDAOImpl();
UserService service = new UserService(dao);
```

**Q9: What is the Front Controller pattern?**
> A single servlet that handles all requests and delegates to specific handlers:
```java
@WebServlet("/app/*")
public class FrontController extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) {
        String path = request.getPathInfo();
        
        if ("/users".equals(path)) {
            new UserController().handle(request, response);
        } else if ("/products".equals(path)) {
            new ProductController().handle(request, response);
        }
    }
}
```

---

## Next Topic: [WAR File Deployment →](./35_WAR_File_Deployment.md)
