# Expression Language (EL) - Complete Guide

## Table of Contents
1. [Introduction](#1-introduction)
2. [What is Expression Language?](#2-what-is-expression-language)
3. [EL Syntax](#3-el-syntax)
4. [EL Implicit Objects](#4-el-implicit-objects)
5. [Reading Attributes with EL](#5-reading-attributes-with-el)
6. [Reading Parameters with EL](#6-reading-parameters-with-el)
7. [Complete Code Examples](#7-complete-code-examples)
8. [Key Points to Remember](#8-key-points-to-remember)
9. [Interview Questions](#9-interview-questions)

---

## 1. Introduction

Expression Language (EL) is a powerful feature in JSP that simplifies access to data stored in JavaBeans, scopes, and parameters. It eliminates the need for scriptlets and makes JSP pages cleaner and more maintainable.

---

## 2. What is Expression Language?

### Definition

EL (Expression Language) is a scripting language used in JSP to access data from various sources like attributes and parameters. It provides a simple syntax to read values without writing Java code.

### Key Features

| Feature | Description |
|---------|-------------|
| **Simple Syntax** | `${expression}` instead of Java code |
| **Null-Friendly** | Doesn't throw NullPointerException |
| **No Scriptlets** | Reduces Java code in JSP |
| **Implicit Objects** | Built-in access to scopes and parameters |
| **Property Navigation** | Access bean properties easily |

### Visual: EL vs JSP Expression

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    JSP EXPRESSION vs EXPRESSION LANGUAGE                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   JSP Expression (old way):                                                 │
│   ┌───────────────────────────────────────────────────────────────┐        │
│   │ <%= session.getAttribute("user") %>                           │        │
│   │ <%= request.getParameter("name") %>                           │        │
│   │ <%= ((User)session.getAttribute("user")).getName() %>         │        │
│   └───────────────────────────────────────────────────────────────┘        │
│                                                                             │
│   Expression Language (new way):                                            │
│   ┌───────────────────────────────────────────────────────────────┐        │
│   │ ${sessionScope.user}                                          │        │
│   │ ${param.name}                                                 │        │
│   │ ${sessionScope.user.name}                                     │        │
│   └───────────────────────────────────────────────────────────────┘        │
│                                                                             │
│   Benefits:                                                                 │
│   ✓ Shorter and cleaner                                                    │
│   ✓ No casting required                                                    │
│   ✓ Null-safe                                                              │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. EL Syntax

### Basic Syntax

```jsp
${expression}
```

### Null-Friendly Behavior

```jsp
<%-- JSP Expression - throws NullPointerException if null --%>
<%= request.getAttribute("nonExistent").toString() %>  <%-- ERROR! --%>

<%-- EL - returns empty string if null --%>
${requestScope.nonExistent}  <%-- Safe, outputs nothing --%>
```

### Accessing Properties

```jsp
<%-- Access object properties --%>
${user.name}          <%-- Calls user.getName() --%>
${user.email}         <%-- Calls user.getEmail() --%>
${user.address.city}  <%-- Nested property access --%>

<%-- Access map values --%>
${myMap.key}          <%-- myMap.get("key") --%>
${myMap["key"]}       <%-- Same as above --%>

<%-- Access list/array elements --%>
${myList[0]}          <%-- First element --%>
${myArray[2]}         <%-- Third element --%>
```

### EL Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `.` | Property access | `${user.name}` |
| `[]` | Collection/Map access | `${list[0]}` |
| `+`, `-`, `*`, `/` | Arithmetic | `${a + b}` |
| `==` (eq), `!=` (ne) | Equality | `${a == b}` |
| `<` (lt), `>` (gt) | Comparison | `${a < b}` |
| `&&` (and), `||` (or) | Logical | `${a && b}` |
| `empty` | Check null/empty | `${empty name}` |
| `? :` | Ternary | `${a > b ? a : b}` |

---

## 4. EL Implicit Objects

### Complete List

EL provides **11 implicit objects** to access different scopes and data:

| Implicit Object | Description | Example |
|-----------------|-------------|---------|
| `pageScope` | Page scope attributes | `${pageScope.myVar}` |
| `requestScope` | Request scope attributes | `${requestScope.user}` |
| `sessionScope` | Session scope attributes | `${sessionScope.cart}` |
| `applicationScope` | Application scope attributes | `${applicationScope.config}` |
| `param` | Request parameters (single) | `${param.name}` |
| `paramValues` | Request parameters (multiple) | `${paramValues.hobbies[0]}` |
| `cookie` | Cookies | `${cookie.JSESSIONID.value}` |
| `initParam` | Context init parameters | `${initParam.dbUrl}` |
| `header` | Request headers | `${header["User-Agent"]}` |
| `headerValues` | Multiple header values | `${headerValues.Accept[0]}` |
| `pageContext` | PageContext object | `${pageContext.request.method}` |

### Visual: Scope Objects

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        EL SCOPE IMPLICIT OBJECTS                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                     applicationScope                                 │   │
│  │  Entire application lifetime                                        │   │
│  │  ┌─────────────────────────────────────────────────────────────┐   │   │
│  │  │                      sessionScope                            │   │   │
│  │  │  User session lifetime                                       │   │   │
│  │  │  ┌─────────────────────────────────────────────────────┐   │   │   │
│  │  │  │                    requestScope                      │   │   │   │
│  │  │  │  Single request lifetime                             │   │   │   │
│  │  │  │  ┌─────────────────────────────────────────────┐   │   │   │   │
│  │  │  │  │                 pageScope                    │   │   │   │   │
│  │  │  │  │  Single page lifetime                        │   │   │   │   │
│  │  │  │  └─────────────────────────────────────────────┘   │   │   │   │
│  │  │  └─────────────────────────────────────────────────────┘   │   │   │
│  │  └─────────────────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  Scope Priority (when no scope specified):                                  │
│  ${myVar} searches: page → request → session → application                 │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Reading Attributes with EL

### Setting Attributes (in Java/JSP)

```jsp
<%
    // Page scope
    pageContext.setAttribute("one", "PAGE");
    
    // Request scope
    request.setAttribute("two", "REQUEST");
    
    // Session scope
    session.setAttribute("three", "SESSION");
    
    // Application scope
    application.setAttribute("four", "APPLICATION");
%>
```

### Reading Attributes with Scope Specified

```jsp
<%-- Explicit scope --%>
${pageScope.one}           <%-- Output: PAGE --%>
${requestScope.two}        <%-- Output: REQUEST --%>
${sessionScope.three}      <%-- Output: SESSION --%>
${applicationScope.four}   <%-- Output: APPLICATION --%>
```

### Reading Attributes without Scope

```jsp
<%-- EL searches in order: page → request → session → application --%>
${one}    <%-- Finds in pageScope: PAGE --%>
${two}    <%-- Finds in requestScope: REQUEST --%>
${three}  <%-- Finds in sessionScope: SESSION --%>
${four}   <%-- Finds in applicationScope: APPLICATION --%>
```

### Comparison: JSP Expression vs EL

```jsp
<%-- JSP Expression (verbose) --%>
<%= pageContext.getAttribute("one") %><br>
<%= request.getAttribute("two") %><br>
<%= session.getAttribute("three") %><br>
<%= application.getAttribute("four") %><br>

<%-- Expression Language (clean) --%>
${pageScope.one}<br>
${requestScope.two}<br>
${sessionScope.three}<br>
${applicationScope.four}<br>

<%-- Even simpler with auto-search --%>
${one}<br>
${two}<br>
${three}<br>
${four}<br>
```

---

## 6. Reading Parameters with EL

### param - Single Values

```jsp
<%-- HTML form --%>
<form>
    <input type="text" name="name">
    <input type="text" name="age">
</form>

<%-- Read in JSP with EL --%>
Name: ${param.name}
Age: ${param.age}

<%-- Equivalent Java code --%>
Name: <%= request.getParameter("name") %>
Age: <%= request.getParameter("age") %>
```

### paramValues - Multiple Values

```jsp
<%-- HTML form with checkboxes --%>
<form>
    <input type="checkbox" name="modules" value="Java"> Java
    <input type="checkbox" name="modules" value="Spring"> Spring
    <input type="checkbox" name="modules" value="Hibernate"> Hibernate
</form>

<%-- Read in JSP with EL --%>
First module: ${paramValues.modules[0]}
Second module: ${paramValues.modules[1]}

<%-- Equivalent Java code --%>
<% String[] modules = request.getParameterValues("modules"); %>
First module: <%= modules[0] %>
```

---

## 7. Complete Code Examples

### el_1.jsp - All Scopes Demonstration

```jsp
<%-- Set attributes in all four scopes --%>
<% pageContext.setAttribute("one", "PAGE"); %>       <%-- Line 1: Page scope --%>
<% request.setAttribute("two", "REQUEST"); %>        <%-- Line 2: Request scope --%>
<% session.setAttribute("three", "SESSION"); %>      <%-- Line 3: Session scope --%>
<% application.setAttribute("four", "APPLICATION"); %> <%-- Line 4: App scope --%>

<%-- Read using JSP Expression --%>
<%= pageContext.getAttribute("one") %><br>           <%-- Line 6: JSP way --%>

<%-- Read using EL with explicit scope --%>
${pageScope.one}<br>                                  <%-- Line 8: EL with scope --%>

<%-- Read using EL without scope (auto-search) --%>
${one}<br>                                            <%-- Line 10: EL auto-search --%>

<%-- Same for request scope --%>
<%= request.getAttribute("two") %><br>
${requestScope.two}<br>
${two}<br>

<%-- Same for session scope --%>
<%= session.getAttribute("three") %><br>
${sessionScope.three}<br>
${three}<br>

<%-- Same for application scope --%>
<%= application.getAttribute("four") %><br>
${applicationScope.four}<br>
${four}<br>
```

### Line-by-Line Output

```
PAGE
PAGE
PAGE
REQUEST
REQUEST
REQUEST
SESSION
SESSION
SESSION
APPLICATION
APPLICATION
APPLICATION
```

### el_4.jsp - Parameter Reading

```jsp
<%@ page isELIgnored="false" %>                      <%-- Line 1: Enable EL --%>

<%-- Read single parameter --%>
Name: ${param.name}<br>                              <%-- Line 4: Single param --%>

<%-- Read multiple parameters --%>
First Module: ${paramValues.modules[0]}<br>          <%-- Line 7: First checkbox --%>
Second Module: ${paramValues.modules[1]}<br>         <%-- Line 8: Second checkbox --%>
```

### callEL4.html - Form for el_4.jsp

```html
<!DOCTYPE html>
<html>
<head>
    <title>EL Demo Form</title>
</head>
<body>
    <form action="el_4.jsp" method="post">
        Name: <input type="text" name="name"><br><br>
        
        Select Modules:<br>
        <input type="checkbox" name="modules" value="Java"> Java<br>
        <input type="checkbox" name="modules" value="Spring"> Spring<br>
        <input type="checkbox" name="modules" value="Hibernate"> Hibernate<br><br>
        
        <input type="submit" value="Submit">
    </form>
</body>
</html>
```

---

## 8. Key Points to Remember

1. **EL Syntax**: Always use `${expression}`

2. **Null-Friendly**: EL doesn't throw NullPointerException, returns empty string

3. **Auto-Search Order**: page → request → session → application

4. **No Casting**: EL automatically handles type conversion

5. **param vs paramValues**: 
   - `param` for single values
   - `paramValues` for checkbox/multi-select

6. **All EL objects are Maps**: Internally, all implicit objects are Map-based

7. **Enable EL**: Some containers need `isELIgnored="false"`

8. **Property syntax**: `${object.property}` calls `object.getProperty()`

9. **Scope Objects**: pageScope, requestScope, sessionScope, applicationScope

10. **initParam**: Reads from `<context-param>` in web.xml

---

## 9. Interview Questions

### Basic Level

**Q1: What is Expression Language (EL)?**
> EL is a scripting language in JSP that provides easy access to data stored in JavaBeans, scopes, and request parameters. It uses the `${expression}` syntax.

**Q2: What is the syntax for EL?**
> `${expression}` - The expression is evaluated and the result is output to the page.

**Q3: What are the scope implicit objects in EL?**
> pageScope, requestScope, sessionScope, and applicationScope.

### Intermediate Level

**Q4: What is the search order when scope is not specified?**
> When you write `${myVar}` without scope:
> 1. pageScope
> 2. requestScope
> 3. sessionScope
> 4. applicationScope
> 
> First match is returned.

**Q5: What is the difference between param and paramValues?**
| param | paramValues |
|-------|-------------|
| Single value | Multiple values |
| `${param.name}` | `${paramValues.hobbies[0]}` |
| For text inputs | For checkboxes, multi-select |
| Returns String | Returns String[] |

**Q6: How is EL null-friendly?**
> If you access a null value:
> - JSP Expression throws NullPointerException
> - EL returns empty string ""
> 
> Example: `${nonExistent}` outputs nothing (no error)

### Advanced Level

**Q7: How do you access nested properties?**
```jsp
<%-- Java object: user.getAddress().getCity() --%>
${user.address.city}

<%-- Map inside Map --%>
${outerMap.innerMap.key}

<%-- List inside object --%>
${user.orders[0].productName}
```

**Q8: How do you disable EL for a specific page?**
```jsp
<%@ page isELIgnored="true" %>

<%-- Now ${expression} is output literally, not evaluated --%>
${this.will.be.printed.as.is}
```

**Q9: What is the empty operator?**
```jsp
<%-- Check if value is null, empty string, or empty collection --%>
${empty name}           <%-- true if name is null or "" --%>
${not empty list}       <%-- true if list has elements --%>

<%-- Conditional output --%>
${empty user ? "Guest" : user.name}
```

**Q10: How do EL implicit objects work internally?**
> All EL implicit objects are implementations of Map interface:
> - `pageScope` - Map of page attributes
> - `param` - Map of request parameters
> - `cookie` - Map of cookies
> 
> When you write `${param.name}`, EL calls `param.get("name")`.

---

## Next Topic: [JSTL Introduction →](./31_JSTL_Introduction.md)
