# EL Implicit Objects - Complete Reference

## Table of Contents
1. [Introduction](#1-introduction)
2. [All EL Implicit Objects](#2-all-el-implicit-objects)
3. [Scope Objects](#3-scope-objects)
4. [Request Objects](#4-request-objects)
5. [Context Objects](#5-context-objects)
6. [pageContext Object](#6-pagecontext-object)
7. [Code Examples](#7-code-examples)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

Expression Language provides its own set of implicit objects that simplify accessing various data in JSP pages without using scriptlets.

---

## 2. All EL Implicit Objects

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    EL IMPLICIT OBJECTS                                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  SCOPE OBJECTS:                                                             │
│  ├── pageScope        Map of page-scoped attributes                        │
│  ├── requestScope     Map of request-scoped attributes                     │
│  ├── sessionScope     Map of session-scoped attributes                     │
│  └── applicationScope Map of application-scoped attributes                 │
│                                                                             │
│  REQUEST OBJECTS:                                                           │
│  ├── param            Map of request parameters (single value)             │
│  ├── paramValues      Map of request parameters (all values)               │
│  ├── header           Map of HTTP headers (single value)                   │
│  ├── headerValues     Map of HTTP headers (all values)                     │
│  └── cookie           Map of cookies                                        │
│                                                                             │
│  CONTEXT OBJECTS:                                                           │
│  ├── initParam        Map of context init parameters                       │
│  └── pageContext      The PageContext object                               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Scope Objects

### pageScope

```jsp
<%-- Set attribute in page scope --%>
<% pageContext.setAttribute("localVar", "Page Value"); %>

<%-- Access with EL --%>
${pageScope.localVar}
${pageScope["localVar"]}
```

### requestScope

```jsp
<%-- Set in servlet --%>
<% request.setAttribute("message", "Hello from request"); %>

<%-- Access with EL --%>
${requestScope.message}

<%-- Without scope (auto-searches) --%>
${message}
```

### sessionScope

```jsp
<%-- Access session attributes --%>
${sessionScope.loggedInUser}
${sessionScope.cartItems}

<%-- Access nested properties --%>
${sessionScope.user.name}
${sessionScope.user.email}
```

### applicationScope

```jsp
<%-- Access application attributes --%>
${applicationScope.siteConfig}
${applicationScope.visitorCount}
```

### Auto-Search Order

When you use `${variableName}` without specifying scope:

```
Page → Request → Session → Application
```

```jsp
<%-- These are equivalent if "user" is in request scope --%>
${user}
${requestScope.user}
```

---

## 4. Request Objects

### param - Single Value Parameters

```jsp
<%-- URL: ?name=John&age=25 --%>

Name: ${param.name}           <%-- John --%>
Age: ${param.age}             <%-- 25 --%>
Missing: ${param.missing}     <%-- empty string (not null!) --%>

<%-- Alternative bracket notation --%>
${param["name"]}
${param['name']}
```

### paramValues - Multiple Values

```jsp
<%-- URL: ?color=red&color=blue&color=green --%>

First Color: ${paramValues.color[0]}    <%-- red --%>
Second Color: ${paramValues.color[1]}   <%-- blue --%>
Third Color: ${paramValues.color[2]}    <%-- green --%>

<%-- Iterate with JSTL --%>
<c:forEach var="c" items="${paramValues.color}">
    ${c}<br/>
</c:forEach>
```

### header - HTTP Headers

```jsp
User-Agent: ${header["User-Agent"]}
Accept: ${header.Accept}
Host: ${header.Host}
Content-Type: ${header["Content-Type"]}

<%-- Note: Use bracket notation for headers with special characters --%>
```

### headerValues - Multiple Header Values

```jsp
<%-- For headers that may have multiple values --%>
${headerValues["Accept-Language"][0]}
```

### cookie - Cookies

```jsp
<%-- Access cookie by name --%>
${cookie.JSESSIONID.value}
${cookie.username.value}

<%-- Access cookie name (key) --%>
${cookie.username.name}

<%-- Check if cookie exists --%>
<c:if test="${not empty cookie.rememberMe}">
    Welcome back!
</c:if>
```

---

## 5. Context Objects

### initParam - Context Init Parameters

```xml
<!-- web.xml -->
<context-param>
    <param-name>appName</param-name>
    <param-value>My Application</param-value>
</context-param>
```

```jsp
<%-- Access in JSP --%>
App Name: ${initParam.appName}
Database: ${initParam.dbUrl}
```

---

## 6. pageContext Object

The `pageContext` EL object provides access to JSP implicit objects.

### Accessing Request Properties

```jsp
<%-- Request info --%>
Method: ${pageContext.request.method}
URI: ${pageContext.request.requestURI}
Context Path: ${pageContext.request.contextPath}
Remote Address: ${pageContext.request.remoteAddr}
Protocol: ${pageContext.request.protocol}
Secure: ${pageContext.request.secure}
```

### Accessing Session Properties

```jsp
Session ID: ${pageContext.session.id}
Is New: ${pageContext.session.new}
Creation Time: ${pageContext.session.creationTime}
```

### Accessing ServletContext Properties

```jsp
Server Info: ${pageContext.servletContext.serverInfo}
Context Path: ${pageContext.servletContext.contextPath}
```

### Accessing Response Properties

```jsp
Content Type: ${pageContext.response.contentType}
```

---

## 7. Code Examples

### Complete Demo Page

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
    <title>EL Implicit Objects Demo</title>
</head>
<body>
    <h2>EL Implicit Objects Demo</h2>
    
    <%-- Set some attributes --%>
    <% 
        pageContext.setAttribute("pageVar", "I'm in page scope");
        request.setAttribute("reqVar", "I'm in request scope");
        session.setAttribute("sessVar", "I'm in session scope");
        application.setAttribute("appVar", "I'm in application scope");
    %>
    
    <h3>Scope Objects</h3>
    <p>Page: ${pageScope.pageVar}</p>
    <p>Request: ${requestScope.reqVar}</p>
    <p>Session: ${sessionScope.sessVar}</p>
    <p>Application: ${applicationScope.appVar}</p>
    
    <h3>Request Parameters</h3>
    <p>Name: ${param.name}</p>
    <p>Age: ${param.age}</p>
    
    <h3>Headers</h3>
    <p>User-Agent: ${header["User-Agent"]}</p>
    <p>Accept-Language: ${header["Accept-Language"]}</p>
    
    <h3>Cookies</h3>
    <p>Session ID: ${cookie.JSESSIONID.value}</p>
    
    <h3>Init Parameters</h3>
    <p>App Name: ${initParam.appName}</p>
    
    <h3>Page Context Info</h3>
    <p>Request Method: ${pageContext.request.method}</p>
    <p>Request URI: ${pageContext.request.requestURI}</p>
    <p>Context Path: ${pageContext.request.contextPath}</p>
    <p>Session ID: ${pageContext.session.id}</p>
</body>
</html>
```

### Form Processing with EL

**form.html:**
```html
<form action="process.jsp" method="post">
    Name: <input type="text" name="name"><br>
    Email: <input type="email" name="email"><br>
    
    Languages:
    <input type="checkbox" name="lang" value="Java"> Java
    <input type="checkbox" name="lang" value="Python"> Python
    <input type="checkbox" name="lang" value="JavaScript"> JavaScript
    <br>
    <input type="submit" value="Submit">
</form>
```

**process.jsp:**
```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

<h2>Form Data</h2>
<p>Name: ${param.name}</p>
<p>Email: ${param.email}</p>

<p>Languages:</p>
<ul>
    <c:forEach var="language" items="${paramValues.lang}">
        <li>${language}</li>
    </c:forEach>
</ul>
```

---

## 8. Interview Questions

### Basic Level

**Q1: What are the EL scope objects?**
> pageScope, requestScope, sessionScope, applicationScope - they provide access to attributes in each scope.

**Q2: How do you access a request parameter in EL?**
```jsp
${param.parameterName}
```

**Q3: What's the difference between param and paramValues?**
> - `param` returns a single value (first if multiple)
> - `paramValues` returns an array of all values

### Intermediate Level

**Q4: How does EL handle null values?**
> EL displays an empty string for null values instead of "null". This is a key advantage over `<%= %>`.

**Q5: What is the search order for ${variableName}?**
> Page → Request → Session → Application. First match is returned.

**Q6: How do you access HTTP headers with special characters?**
```jsp
${header["Content-Type"]}
${header["User-Agent"]}
```

### Advanced Level

**Q7: How do you access nested bean properties?**
```jsp
<%-- If user has address.city property --%>
${sessionScope.user.address.city}

<%-- Equivalent to --%>
<%= ((User)session.getAttribute("user")).getAddress().getCity() %>
```

**Q8: What's the difference between JSP and EL implicit objects?**
| JSP Implicit | EL Equivalent |
|--------------|---------------|
| `request` | `pageContext.request` |
| `session` | `pageContext.session` |
| `application` | `pageContext.servletContext` |
| `request.getParameter()` | `param.x` |
| `request.getAttribute()` | `requestScope.x` or `x` |

---

## Next Topic: [JSTL Core Tags →](./32_JSTL_Core_Tags.md)
