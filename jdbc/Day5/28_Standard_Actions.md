# JSP Standard Actions - Complete Guide

## Table of Contents
1. [Introduction](#1-introduction)
2. [What are Standard Actions?](#2-what-are-standard-actions)
3. [jsp:useBean](#3-jspusebean)
4. [jsp:setProperty](#4-jspsetproperty)
5. [jsp:getProperty](#5-jspgetproperty)
6. [jsp:forward](#6-jspforward)
7. [jsp:include](#7-jspinclude)
8. [jsp:param](#8-jspparam)
9. [Complete Code Examples](#9-complete-code-examples)
10. [Key Points to Remember](#10-key-points-to-remember)
11. [Interview Questions](#11-interview-questions)

---

## 1. Introduction

JSP Standard Actions are predefined tags that perform common operations without requiring scriptlets. They provide a cleaner, XML-based syntax for working with JavaBeans, forwarding requests, and including content.

---

## 2. What are Standard Actions?

### Definition

Standard Actions are XML-based tags that begin with the `jsp:` prefix and provide functionality for common tasks like:
- Instantiating JavaBeans
- Setting/getting bean properties
- Forwarding and including requests
- Working with applets

### List of Standard Actions

| Action | Purpose |
|--------|---------|
| `<jsp:useBean>` | Create or locate a JavaBean |
| `<jsp:setProperty>` | Set bean property value |
| `<jsp:getProperty>` | Get bean property value |
| `<jsp:forward>` | Forward request to another resource |
| `<jsp:include>` | Include another resource |
| `<jsp:param>` | Pass parameters |
| `<jsp:plugin>` | Embed applets/plugins |

---

## 3. jsp:useBean

### Purpose

The `<jsp:useBean>` tag instantiates or locates a JavaBean in a specified scope.

### Syntax

```jsp
<jsp:useBean id="beanName" 
             class="fully.qualified.ClassName" 
             scope="page|request|session|application" />
```

### Attributes

| Attribute | Required | Description |
|-----------|----------|-------------|
| `id` | Yes | Variable name for the bean |
| `class` | Yes* | Fully qualified class name |
| `scope` | No | Scope (default: page) |
| `type` | No | Interface or superclass type |
| `beanName` | No | For serialized beans |

### How It Works

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    jsp:useBean BEHAVIOR                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  <jsp:useBean id="user" class="com.example.User" scope="session"/>         │
│                                                                             │
│  Step 1: Check if bean exists in specified scope                           │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ User user = (User) session.getAttribute("user");            │           │
│  │                                                              │           │
│  │ if (user == null) {                                          │           │
│  │     // Step 2: Create new instance if not found              │           │
│  │     user = new User();                                        │           │
│  │     session.setAttribute("user", user);                       │           │
│  │ }                                                             │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
│  Result: 'user' variable is available in JSP                               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Example

```jsp
<%-- Create or find User bean in session scope --%>
<jsp:useBean id="myUser" class="com.example.User" scope="session"/>

<%-- Now myUser is available --%>
<p>User name: ${myUser.name}</p>
```

### Equivalent Java Code

```jsp
<%
    User myUser = (User) session.getAttribute("myUser");
    if (myUser == null) {
        myUser = new User();
        session.setAttribute("myUser", myUser);
    }
%>
```

---

## 4. jsp:setProperty

### Purpose

Sets the value of a bean property (calls setter method).

### Syntax

```jsp
<jsp:setProperty name="beanId" property="propertyName" value="value"/>
```

### Different Ways to Set Properties

```jsp
<%-- 1. Set specific value --%>
<jsp:setProperty name="user" property="name" value="John"/>

<%-- 2. Set from request parameter with same name --%>
<jsp:setProperty name="user" property="name" param="username"/>

<%-- 3. Set from request parameter (property name = param name) --%>
<jsp:setProperty name="user" property="name"/>

<%-- 4. Set ALL properties from matching request parameters --%>
<jsp:setProperty name="user" property="*"/>
```

### The Magic of property="*"

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    property="*" AUTO-POPULATION                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  HTML Form:                         JavaBean (User.java):                   │
│  ┌─────────────────────────┐       ┌─────────────────────────┐             │
│  │ name: [John          ]  │       │ private String name;    │             │
│  │ age:  [25            ]  │       │ private int age;        │             │
│  │ email:[john@mail.com ]  │       │ private String email;   │             │
│  └─────────────────────────┘       │                         │             │
│                                     │ + getters and setters   │             │
│                                     └─────────────────────────┘             │
│                                                                             │
│  <jsp:setProperty name="user" property="*"/>                               │
│                                                                             │
│  Container automatically calls:                                             │
│  • user.setName("John")                                                    │
│  • user.setAge(25)          ← Automatic type conversion!                   │
│  • user.setEmail("john@mail.com")                                          │
│                                                                             │
│  Matching is by form field name = bean property name                       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 5. jsp:getProperty

### Purpose

Gets and outputs a bean property value (calls getter method).

### Syntax

```jsp
<jsp:getProperty name="beanId" property="propertyName"/>
```

### Example

```jsp
<jsp:useBean id="user" class="com.example.User" scope="session"/>

<p>Name: <jsp:getProperty name="user" property="name"/></p>
<p>Age: <jsp:getProperty name="user" property="age"/></p>
```

### Equivalent to

```jsp
<p>Name: <%= user.getName() %></p>
<p>Age: <%= user.getAge() %></p>

<%-- Or with EL --%>
<p>Name: ${user.name}</p>
<p>Age: ${user.age}</p>
```

---

## 6. jsp:forward

### Purpose

Forwards the request to another resource (servlet, JSP, HTML).

### Syntax

```jsp
<jsp:forward page="targetPage.jsp"/>

<%-- With parameters --%>
<jsp:forward page="targetPage.jsp">
    <jsp:param name="paramName" value="paramValue"/>
</jsp:forward>
```

### Behavior

- Similar to `RequestDispatcher.forward()`
- URL in browser does NOT change
- Request and response objects are passed
- Original output buffer is cleared

### Comparison with RequestDispatcher

```jsp
<%-- Standard Action --%>
<jsp:forward page="result.jsp"/>

<%-- Equivalent Java code --%>
<%
    RequestDispatcher rd = request.getRequestDispatcher("result.jsp");
    rd.forward(request, response);
%>
```

---

## 7. jsp:include

### Purpose

Includes content from another resource at request time.

### Syntax

```jsp
<jsp:include page="includedPage.jsp"/>

<%-- With parameters --%>
<jsp:include page="header.jsp">
    <jsp:param name="title" value="Home Page"/>
</jsp:include>
```

### Include Directive vs Include Action

| Feature | Include Directive | Include Action |
|---------|-------------------|----------------|
| Syntax | `<%@ include %>` | `<jsp:include>` |
| Time | Translation time | Request time |
| Type | Static | Dynamic |
| Content | Merged at compile | Called at runtime |
| Changes | Requires recompile | Automatic |
| Parameters | Cannot pass | Can pass with jsp:param |

### Visual Comparison

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                INCLUDE DIRECTIVE (Static)                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  <%@ include file="header.html" %>                                          │
│                                                                             │
│  At TRANSLATION:                                                            │
│  ┌────────────┐    ┌────────────┐    ┌────────────────────┐               │
│  │ main.jsp   │ +  │ header.html│ =  │ main_jsp.java      │               │
│  │            │    │            │    │ (combined content) │               │
│  └────────────┘    └────────────┘    └────────────────────┘               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                INCLUDE ACTION (Dynamic)                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  <jsp:include page="header.jsp"/>                                           │
│                                                                             │
│  At RUNTIME:                                                                │
│  ┌────────────┐        ┌────────────┐                                      │
│  │ main.jsp   │ ─call─►│ header.jsp │                                      │
│  │            │        │            │                                      │
│  │            │◄─output─│            │                                      │
│  └────────────┘        └────────────┘                                      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 8. jsp:param

### Purpose

Passes parameters to included or forwarded pages.

### Syntax

```jsp
<jsp:include page="display.jsp">
    <jsp:param name="message" value="Hello World"/>
    <jsp:param name="count" value="10"/>
</jsp:include>
```

### Reading Parameters in Target Page

```jsp
<%-- In display.jsp --%>
Message: ${param.message}
Count: ${param.count}

<%-- Or with request --%>
<%= request.getParameter("message") %>
```

---

## 9. Complete Code Examples

### User.java - JavaBean

```java
package com.example;

public class User {
    private String name;
    private int age;
    private String email;
    
    // No-arg constructor (REQUIRED for useBean)
    public User() {}
    
    // Getters and Setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
    
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
}
```

### register.html - Form

```html
<!DOCTYPE html>
<html>
<head>
    <title>User Registration</title>
</head>
<body>
    <h2>Register User</h2>
    <form action="process.jsp" method="post">
        Name: <input type="text" name="name"><br><br>
        Age: <input type="text" name="age"><br><br>
        Email: <input type="text" name="email"><br><br>
        <input type="submit" value="Register">
    </form>
</body>
</html>
```

### process.jsp - Using Standard Actions

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<html>
<head>
    <title>Processing</title>
</head>
<body>
    <%-- Create or find User bean --%>
    <jsp:useBean id="user" class="com.example.User" scope="session"/>
    
    <%-- Auto-populate ALL properties from request parameters --%>
    <jsp:setProperty name="user" property="*"/>
    
    <h2>Registration Successful!</h2>
    
    <p>Name: <jsp:getProperty name="user" property="name"/></p>
    <p>Age: <jsp:getProperty name="user" property="age"/></p>
    <p>Email: <jsp:getProperty name="user" property="email"/></p>
    
    <%-- Or using EL (cleaner) --%>
    <h3>Using EL:</h3>
    <p>Name: ${user.name}</p>
    <p>Age: ${user.age}</p>
    <p>Email: ${user.email}</p>
    
    <a href="profile.jsp">View Profile</a>
</body>
</html>
```

### profile.jsp - Using Session Bean

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<html>
<head>
    <title>Profile</title>
</head>
<body>
    <%-- Bean already exists in session - just reference it --%>
    <jsp:useBean id="user" class="com.example.User" scope="session"/>
    
    <h2>User Profile</h2>
    <p>Welcome back, ${user.name}!</p>
    <p>Your registered email: ${user.email}</p>
</body>
</html>
```

### forward_demo.jsp - Forward with Parameters

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>

<%-- Forward to target with parameters --%>
<jsp:forward page="target.jsp">
    <jsp:param name="source" value="forward_demo"/>
    <jsp:param name="timestamp" value="<%= System.currentTimeMillis() %>"/>
</jsp:forward>

<%-- This code never executes --%>
<p>This will never be displayed</p>
```

### target.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<html>
<body>
    <h2>Target Page</h2>
    <p>Forwarded from: ${param.source}</p>
    <p>Timestamp: ${param.timestamp}</p>
</body>
</html>
```

---

## 10. Key Points to Remember

1. **useBean creates or finds**: Checks scope first, creates only if not found

2. **property="*" is powerful**: Auto-maps form fields to bean properties

3. **No-arg constructor required**: JavaBeans must have public no-arg constructor

4. **Scope matters**: page < request < session < application

5. **forward clears buffer**: Previous output is discarded

6. **include is dynamic**: Unlike include directive

7. **jsp:param adds parameters**: Available via request.getParameter()

8. **Standard actions are XML**: Must follow XML syntax (self-closing, lowercase)

9. **Type conversion automatic**: setProperty converts String to appropriate type

10. **EL is simpler**: Modern JSP prefers EL over getProperty

---

## 11. Interview Questions

### Basic Level

**Q1: What is jsp:useBean?**
> jsp:useBean is a standard action that creates or locates a JavaBean in a specified scope. If the bean exists, it returns the existing one; otherwise, it creates a new instance.

**Q2: What scopes are available for jsp:useBean?**
> Four scopes: page (default), request, session, and application.

**Q3: What is the difference between jsp:include and include directive?**
| Directive | Action |
|-----------|--------|
| Static (translation time) | Dynamic (request time) |
| Content merged | Content included at runtime |
| Cannot pass parameters | Can pass parameters |

### Intermediate Level

**Q4: What does property="*" do in jsp:setProperty?**
> It automatically populates all bean properties from matching request parameters. The parameter name must exactly match the property name.

**Q5: What are the requirements for a class to work with jsp:useBean?**
> 1. Public class
> 2. Public no-argument constructor
> 3. Getter/setter methods following naming conventions
> 4. (Optional) Implement Serializable

**Q6: What happens if jsp:forward is called after output is written?**
> An IllegalStateException is thrown if the response buffer has been flushed. The buffer must not be committed before forwarding.

### Advanced Level

**Q7: How does type conversion work in jsp:setProperty?**
> Container automatically converts String parameters to the property type:
> - String → int/Integer
> - String → boolean/Boolean
> - String → long/Long
> - String → double/Double
> 
> Invalid conversions throw NumberFormatException.

**Q8: Explain the type and beanName attributes of jsp:useBean.**
```jsp
<%-- type: Declare variable as interface/superclass --%>
<jsp:useBean id="list" type="java.util.List" class="java.util.ArrayList"/>

<%-- beanName: For serialized beans or factory methods --%>
<jsp:useBean id="data" beanName="com.example.DataFactory" type="Data"/>
```

**Q9: How would you conditionally set bean properties?**
```jsp
<jsp:useBean id="user" class="User" scope="session">
    <%-- This block ONLY executes when bean is CREATED (not found) --%>
    <jsp:setProperty name="user" property="role" value="guest"/>
</jsp:useBean>
```

---

## Next Topic: [Attributes in JSP Scopes →](./29_Attributes_Scopes.md)
