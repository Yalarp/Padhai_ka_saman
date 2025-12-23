# JSTL - JSP Standard Tag Library Complete Guide

## Table of Contents
1. [Introduction](#1-introduction)
2. [What is JSTL?](#2-what-is-jstl)
3. [JSTL Core Tags](#3-jstl-core-tags)
4. [forEach Tag](#4-foreach-tag)
5. [Conditional Tags](#5-conditional-tags)
6. [JSTL with Collections](#6-jstl-with-collections)
7. [Other Useful Tags](#7-other-useful-tags)
8. [Complete Code Examples](#8-complete-code-examples)
9. [Key Points to Remember](#9-key-points-to-remember)
10. [Interview Questions](#10-interview-questions)

---

## 1. Introduction

JSTL (JSP Standard Tag Library) is a collection of useful JSP tags that encapsulates core functionality common to many JSP applications. It eliminates the need for scriptlets and provides a cleaner, more maintainable way to write JSP pages.

---

## 2. What is JSTL?

### Definition

JSTL is a standardized collection of custom tags for JSP that provides tags for iteration, conditionals, XML processing, and more.

### Why JSTL?

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    WITHOUT JSTL (Scriptlets)                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  <%                                                                         │
│      List<String> items = (List<String>) request.getAttribute("items");    │
│      if (items != null) {                                                   │
│          for (String item : items) {                                        │
│              out.println("<li>" + item + "</li>");                         │
│          }                                                                  │
│      }                                                                      │
│  %>                                                                         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                    WITH JSTL (Clean)                                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  <c:forEach var="item" items="${items}">                                    │
│      <li>${item}</li>                                                       │
│  </c:forEach>                                                               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Taglib Directive

To use JSTL, you must include the taglib directive:

```jsp
<%-- For Tomcat 9 and earlier (javax) --%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<%-- For Tomcat 10+ (jakarta) --%>
<%@ taglib prefix="c" uri="jakarta.tags.core" %>
```

### JSTL Libraries

| Library | URI | Prefix | Description |
|---------|-----|--------|-------------|
| Core | `http://java.sun.com/jsp/jstl/core` | c | Loops, conditionals |
| Formatting | `http://java.sun.com/jsp/jstl/fmt` | fmt | Date, number formatting |
| Functions | `http://java.sun.com/jsp/jstl/functions` | fn | String functions |
| SQL | `http://java.sun.com/jsp/jstl/sql` | sql | Database operations |
| XML | `http://java.sun.com/jsp/jstl/xml` | x | XML processing |

---

## 3. JSTL Core Tags

### Core Tags Overview

| Tag | Purpose | Example |
|-----|---------|---------|
| `<c:out>` | Output value | `<c:out value="${name}"/>` |
| `<c:set>` | Set variable | `<c:set var="x" value="10"/>` |
| `<c:remove>` | Remove variable | `<c:remove var="x"/>` |
| `<c:if>` | Conditional | `<c:if test="${condition}">` |
| `<c:choose>` | Switch-case | `<c:choose><c:when>` |
| `<c:forEach>` | Loop | `<c:forEach items="${list}">` |
| `<c:forTokens>` | Split string | `<c:forTokens items="a,b,c" delims=",">` |
| `<c:import>` | Include URL | `<c:import url="header.jsp"/>` |
| `<c:url>` | Build URL | `<c:url value="/page.jsp"/>` |
| `<c:redirect>` | Redirect | `<c:redirect url="/login.jsp"/>` |

---

## 4. forEach Tag

### Basic Syntax

```jsp
<c:forEach var="item" items="${collection}">
    ${item}
</c:forEach>
```

### Loop with Index

```jsp
<c:forEach var="item" items="${list}" varStatus="status">
    Index: ${status.index}
    Count: ${status.count}
    First: ${status.first}
    Last: ${status.last}
    Item: ${item}
</c:forEach>
```

### Counting Loop

```jsp
<%-- Loop from 1 to 10 --%>
<c:forEach var="i" begin="1" end="10" step="1">
    ${i}<br>
</c:forEach>
```

### forEach Example

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<html>
<body>
    <h2>JSTL forEach Demo</h2>
    
    <%-- Count from 1 to 5 --%>
    <c:forEach var="i" begin="1" end="5">
        <p>Number: ${i}</p>
    </c:forEach>
    
</body>
</html>
```

---

## 5. Conditional Tags

### c:if Tag

```jsp
<c:if test="${user.age >= 18}">
    <p>You are an adult</p>
</c:if>

<c:if test="${empty cart}">
    <p>Your cart is empty</p>
</c:if>

<c:if test="${not empty user}">
    <p>Welcome, ${user.name}!</p>
</c:if>
```

### c:choose (switch-case)

```jsp
<c:choose>
    <c:when test="${score >= 90}">
        Grade: A
    </c:when>
    <c:when test="${score >= 80}">
        Grade: B
    </c:when>
    <c:when test="${score >= 70}">
        Grade: C
    </c:when>
    <c:otherwise>
        Grade: F
    </c:otherwise>
</c:choose>
```

---

## 6. JSTL with Collections

### Iterating List/Vector

```jsp
<%@ page import="java.util.*" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<%
    // Create and populate Vector
    Vector v = new Vector();
    v.addElement("first");
    v.addElement(new Integer(420));
    v.addElement(new Boolean(true));
    
    // Store in request scope
    request.setAttribute("myvect", v);
%>

<%-- Iterate with JSTL --%>
<c:forEach var="xy" items="${myvect}">
    ${xy}<br>
</c:forEach>
```

**Output:**
```
first
420
true
```

### Iterating Map

```jsp
<%@ page isELIgnored="false" import="java.util.*" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<%
    // Create and populate Map
    Map<Integer, String> mymap = new HashMap<>();
    mymap.put(1, "abc");
    mymap.put(2, "xyz");
    mymap.put(3, "pqr");
    
    // Store in pageContext
    pageContext.setAttribute("list", mymap);
%>

<table border="1">
    <tr>
        <th>Key</th>
        <th>Value</th>
    </tr>
    <c:forEach var="myvar" items="${list}">
        <tr>
            <td>${myvar.key}</td>
            <td>${myvar.value}</td>
        </tr>
    </c:forEach>
</table>
```

**Output:**
```
Key | Value
----|------
1   | abc
2   | xyz
3   | pqr
```

### Map Iteration Explanation

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         MAP ITERATION FLOW                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  <c:forEach var="myvar" items="${list}">                                    │
│                                                                             │
│  For each iteration:                                                        │
│  • myvar = one Map.Entry object                                             │
│  • ${myvar.key}   → calls getKey()   → returns Integer (1, 2, 3)           │
│  • ${myvar.value} → calls getValue() → returns String (abc, xyz, pqr)      │
│                                                                             │
│  Internally:                                                                │
│  ┌───────────────────────────────────────────────────────────────┐         │
│  │ Iteration 1: myvar → Entry{key=1, value="abc"}                │         │
│  │ Iteration 2: myvar → Entry{key=2, value="xyz"}                │         │
│  │ Iteration 3: myvar → Entry{key=3, value="pqr"}                │         │
│  └───────────────────────────────────────────────────────────────┘         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 7. Other Useful Tags

### c:set - Set Variables

```jsp
<%-- Set a variable in page scope --%>
<c:set var="name" value="John"/>

<%-- Set in specific scope --%>
<c:set var="user" value="${param.name}" scope="session"/>

<%-- Set bean property --%>
<c:set target="${employee}" property="name" value="John"/>
```

### c:out - Safe Output

```jsp
<%-- Safe output with default value --%>
<c:out value="${user.name}" default="Guest"/>

<%-- Escape XML special characters (default: true) --%>
<c:out value="${userInput}" escapeXml="true"/>
```

### c:url - Build URLs

```jsp
<%-- Build URL with parameters --%>
<c:url var="myUrl" value="/product.jsp">
    <c:param name="id" value="123"/>
    <c:param name="category" value="electronics"/>
</c:url>

<a href="${myUrl}">View Product</a>
<%-- Result: /product.jsp?id=123&category=electronics --%>
```

### c:import - Include External Content

```jsp
<%-- Include content from URL --%>
<c:import url="http://example.com/header.html"/>

<%-- Include local file --%>
<c:import url="/includes/footer.jsp"/>

<%-- Unlike include directive/action, can include from outside web container --%>
```

---

## 8. Complete Code Examples

### jstl.jsp - Basic Counting

```jsp
<%@ taglib uri="http://java.sun.com/jstl/core" prefix="d" %>  <%-- Line 1: Taglib --%>
<html>
<head>
    <title>Count to 10 Example (using JSTL)</title>
</head>
<body>
    <%-- Loop from 1 to 15 with step 1 --%>
    <d:forEach var="i" begin="1" end="15" step="1">          <%-- Line 8: forEach --%>
        ${i}                                                  <%-- Line 9: Output --%>
        <br/>
    </d:forEach>                                              <%-- Line 11: End loop --%>
</body>
</html>
```

### jstl1.jsp - Reading Checkbox Values

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>

all the values are:
<br>
<c:forEach var="str" items="${paramValues.animals}">    <%-- Line 4: Loop params --%>
    ${str}<br>                                           <%-- Line 5: Output each --%>
</c:forEach>
```

### call jstl1 jsp.html - Form

```html
<!DOCTYPE html>
<html>
<head>
    <title>JSTL Checkbox Demo</title>
</head>
<body>
    <form action="jstl1.jsp" method="post">
        Select Animals:<br>
        <input type="checkbox" name="animals" value="Dog"> Dog<br>
        <input type="checkbox" name="animals" value="Cat"> Cat<br>
        <input type="checkbox" name="animals" value="Bird"> Bird<br>
        <input type="submit" value="Submit">
    </form>
</body>
</html>
```

### jstl2.jsp - Vector Iteration

```jsp
<%@ page import="java.util.*" %>                          <%-- Line 1: Import --%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>  <%-- Line 2: Taglib --%>

<%
    // Create Vector with mixed types
    Vector v = new Vector();
    v.addElement("first");                                <%-- String --%>
    v.addElement(new Integer(420));                       <%-- Integer --%>
    v.addElement(new Boolean(true));                      <%-- Boolean --%>
    
    // Store in request scope
    request.setAttribute("myvect", v);
%>

<%-- Iterate and display each element --%>
<c:forEach var="xy" items="${myvect}">                    <%-- Line 13: Loop --%>
    ${xy}<br>                                             <%-- Line 14: Output --%>
</c:forEach>
```

### First_map.jsp - Map Iteration (Complete)

```jsp
<%@ page isELIgnored="false" import="java.util.*" %>      <%-- Line 1: Enable EL --%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>  <%-- Line 2: JSTL --%>

<%
    // Step 1: Create Map
    Map<Integer, String> mymap = new HashMap<>();
    
    // Step 2: Add key-value pairs
    mymap.put(1, "abc");
    mymap.put(2, "xyz");
    mymap.put(3, "pqr");
    
    // Step 3: Store in pageContext for EL access
    pageContext.setAttribute("list", mymap);
%>

<html>
<body>
    <h2>Map Contents:</h2>
    <table border="1">
        <tr>
            <th>Key</th>
            <th>Value</th>
        </tr>
        <%-- Step 4: Iterate using JSTL --%>
        <c:forEach var="myvar" items="${list}">
            <tr>
                <td>${myvar.key}</td>      <%-- Calls getKey() --%>
                <td>${myvar.value}</td>    <%-- Calls getValue() --%>
            </tr>
        </c:forEach>
    </table>
</body>
</html>
```

---

## 9. Key Points to Remember

1. **Taglib required**: Always include `<%@ taglib prefix="c" uri="..." %>`

2. **Prefix is alias**: The prefix (c, d, etc.) is just an alias for the library

3. **forEach for iteration**: Most commonly used tag

4. **EL inside JSTL**: Use `${expression}` for values

5. **c:if has no else**: Use `c:choose` for if-else logic

6. **c:import is powerful**: Can include external URLs (not just local)

7. **Map iteration**: `var` is Map.Entry with `.key` and `.value`

8. **Tomcat 10**: Use `jakarta.tags.core` instead of `http://java.sun.com`

9. **No scriptlets**: JSTL eliminates need for Java code in JSP

10. **Scope-aware**: JSTL searches all scopes for attributes

---

## 10. Interview Questions

### Basic Level

**Q1: What is JSTL?**
> JSTL (JSP Standard Tag Library) is a collection of custom tags for common JSP operations like iteration, conditionals, and URL manipulation.

**Q2: How do you include JSTL in a JSP page?**
```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

**Q3: How do you loop through a list with JSTL?**
```jsp
<c:forEach var="item" items="${myList}">
    ${item}
</c:forEach>
```

### Intermediate Level

**Q4: What is the difference between c:if and c:choose?**
| c:if | c:choose |
|------|----------|
| Simple if | If-else-if chain |
| No else support | Has c:when and c:otherwise |
| For single conditions | For multiple conditions |

**Q5: How do you iterate over a Map with JSTL?**
```jsp
<c:forEach var="entry" items="${myMap}">
    Key: ${entry.key}, Value: ${entry.value}
</c:forEach>
```

**Q6: What is the difference between c:import and include directive?**
| c:import | Include Directive |
|----------|-------------------|
| Runtime | Translation time |
| Can include external URLs | Local files only |
| Dynamic | Static |

### Advanced Level

**Q7: How do you handle null values in JSTL?**
```jsp
<%-- c:if with empty check --%>
<c:if test="${not empty user}">
    ${user.name}
</c:if>

<%-- c:out with default --%>
<c:out value="${user.name}" default="Unknown"/>

<%-- Ternary in EL --%>
${empty user.name ? 'Unknown' : user.name}
```

**Q8: How do you use varStatus in forEach?**
```jsp
<c:forEach var="item" items="${list}" varStatus="loop">
    ${loop.index}     <%-- 0-based index --%>
    ${loop.count}     <%-- 1-based count --%>
    ${loop.first}     <%-- true if first --%>
    ${loop.last}      <%-- true if last --%>
    ${loop.current}   <%-- current item (same as var) --%>
</c:forEach>
```

**Q9: What Maven dependency is needed for JSTL?**
```xml
<!-- For Tomcat 9 and earlier -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>

<!-- For Tomcat 10+ -->
<dependency>
    <groupId>jakarta.servlet.jsp.jstl</groupId>
    <artifactId>jakarta.servlet.jsp.jstl-api</artifactId>
    <version>3.0.0</version>
</dependency>
```

---

## Next Topic: [JSP Standard Actions →](./28_Standard_Actions.md)
