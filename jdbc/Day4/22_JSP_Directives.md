# JSP Directives - Complete Guide

## Table of Contents
1. [Introduction](#1-introduction)
2. [Page Directive](#2-page-directive)
3. [Include Directive](#3-include-directive)
4. [Taglib Directive](#4-taglib-directive)
5. [Comparison Table](#5-comparison-table)
6. [Code Examples](#6-code-examples)
7. [Interview Questions](#7-interview-questions)

---

## 1. Introduction

Directives provide instructions to the JSP container about how to process the page. They affect the entire page and are processed at translation time.

### Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         JSP DIRECTIVES                                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │  PAGE DIRECTIVE <%@ page ... %>                              │           │
│  │  • Import packages                                           │           │
│  │  • Set content type                                          │           │
│  │  • Error handling                                            │           │
│  │  • Session, buffer, threading settings                       │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │  INCLUDE DIRECTIVE <%@ include ... %>                        │           │
│  │  • Include file content at translation time                  │           │
│  │  • Static inclusion (copy-paste effect)                      │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │  TAGLIB DIRECTIVE <%@ taglib ... %>                          │           │
│  │  • Enable custom tag libraries (JSTL, custom)                │           │
│  │  • Define prefix for tags                                    │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Page Directive

### Syntax

```jsp
<%@ page attribute="value" attribute2="value2" %>
```

### All Page Directive Attributes

| Attribute | Description | Default |
|-----------|-------------|---------|
| `import` | Java classes to import | None |
| `contentType` | MIME type and charset | `text/html` |
| `pageEncoding` | Character encoding | ISO-8859-1 |
| `session` | Enable session | `true` |
| `isELIgnored` | Ignore EL expressions | `false` |
| `isErrorPage` | Is this an error page | `false` |
| `errorPage` | Error page URL | None |
| `buffer` | Output buffer size | `8kb` |
| `autoFlush` | Auto-flush buffer | `true` |
| `isThreadSafe` | Thread-safe (deprecated) | `true` |
| `extends` | Superclass | Container-specific |
| `info` | Page description | None |

### Import Attribute

```jsp
<%-- Single import --%>
<%@ page import="java.util.Date" %>

<%-- Multiple imports (comma-separated) --%>
<%@ page import="java.util.*, java.sql.*, com.example.model.*" %>

<%-- Or multiple directives --%>
<%@ page import="java.util.List" %>
<%@ page import="java.util.ArrayList" %>
```

### Content Type and Encoding

```jsp
<%-- Set content type and charset --%>
<%@ page contentType="text/html;charset=UTF-8" %>

<%-- Or separately --%>
<%@ page contentType="text/html" pageEncoding="UTF-8" %>

<%-- JSON response --%>
<%@ page contentType="application/json;charset=UTF-8" %>

<%-- XML response --%>
<%@ page contentType="text/xml;charset=UTF-8" %>
```

### Error Handling

```jsp
<%-- In normal page: specify error page --%>
<%@ page errorPage="error.jsp" %>

<%-- In error page: mark as error handler --%>
<%@ page isErrorPage="true" %>
<%
    // Can now use 'exception' implicit object
    out.println("Error: " + exception.getMessage());
%>
```

### Session Control

```jsp
<%-- Disable session (for performance) --%>
<%@ page session="false" %>

<%-- Default is true --%>
<%@ page session="true" %>
```

### Buffer Settings

```jsp
<%-- Disable buffering (immediately send output) --%>
<%@ page buffer="none" %>

<%-- Set buffer size --%>
<%@ page buffer="16kb" %>

<%-- Throw exception if buffer overflows --%>
<%@ page buffer="8kb" autoFlush="false" %>
```

---

## 3. Include Directive

### Syntax

```jsp
<%@ include file="filename" %>
```

### Purpose

Includes the content of another file **at translation time**. The content is literally copy-pasted into the JSP before compilation.

### Example

**header.jsp:**
```jsp
<header>
    <nav>
        <a href="home.jsp">Home</a>
        <a href="about.jsp">About</a>
    </nav>
</header>
```

**footer.jsp:**
```jsp
<footer>
    <p>&copy; 2024 MyCompany</p>
</footer>
```

**main.jsp:**
```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html>
<body>
    <%@ include file="header.jsp" %>
    
    <main>
        <h1>Welcome!</h1>
        <p>Main content here...</p>
    </main>
    
    <%@ include file="footer.jsp" %>
</body>
</html>
```

### Static vs Dynamic Include

```
┌─────────────────────────────────────────────────────────────────────────────┐
│              INCLUDE DIRECTIVE vs INCLUDE ACTION                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  <%@ include file="..." %>              vs    <jsp:include page="..." />    │
│                                                                             │
│  ┌─────────────────────────┐                 ┌─────────────────────────┐   │
│  │  TRANSLATION TIME       │                 │  REQUEST TIME           │   │
│  │  (STATIC)               │                 │  (DYNAMIC)              │   │
│  ├─────────────────────────┤                 ├─────────────────────────┤   │
│  │ • Content merged first  │                 │ • Separate compilation  │   │
│  │ • One compiled servlet  │                 │ • Two servlets          │   │
│  │ • Change = recompile    │                 │ • Change = works        │   │
│  │ • Faster execution      │                 │ • Slower (extra call)   │   │
│  │ • Variables shared      │                 │ • Variables NOT shared  │   │
│  └─────────────────────────┘                 └─────────────────────────┘   │
│                                                                             │
│  Use for: Headers, footers,                  Use for: Dynamic content,     │
│  static content, common vars                 separate components           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 4. Taglib Directive

### Syntax

```jsp
<%@ taglib uri="tagLibraryURI" prefix="prefix" %>
```

### JSTL Core Tags

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

<c:if test="${condition}">
    Content shown if true
</c:if>

<c:forEach var="item" items="${list}">
    ${item}
</c:forEach>
```

### JSTL Formatting Tags

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>

<fmt:formatNumber value="${price}" type="currency"/>
<fmt:formatDate value="${date}" pattern="yyyy-MM-dd"/>
```

### JSTL Functions

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/functions" prefix="fn" %>

Length: ${fn:length(list)}
Uppercase: ${fn:toUpperCase(name)}
```

### Custom Tags

```jsp
<%@ taglib uri="/WEB-INF/custom.tld" prefix="my" %>

<my:customTag attribute="value"/>
```

---

## 5. Comparison Table

| Feature | Page | Include | Taglib |
|---------|------|---------|--------|
| Purpose | Page settings | Include files | Enable tags |
| Syntax | `<%@ page %>` | `<%@ include %>` | `<%@ taglib %>` |
| Can repeat? | Yes (some attrs) | Yes (files) | Yes (libs) |
| When processed | Translation | Translation | Translation |
| Affects | Entire page | Insertion point | Tag usage |

---

## 6. Code Examples

### Complete Page Example

```jsp
<%-- Directives at top --%>
<%@ page contentType="text/html;charset=UTF-8" %>
<%@ page import="java.util.*, com.example.model.*" %>
<%@ page errorPage="error.jsp" %>
<%@ page session="true" %>

<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>

<!DOCTYPE html>
<html>
<head>
    <title>Product List</title>
</head>
<body>
    <%@ include file="header.jsp" %>
    
    <h1>Products</h1>
    
    <table>
        <c:forEach var="product" items="${products}">
            <tr>
                <td>${product.name}</td>
                <td><fmt:formatNumber value="${product.price}" type="currency"/></td>
            </tr>
        </c:forEach>
    </table>
    
    <%@ include file="footer.jsp" %>
</body>
</html>
```

### Error Page Setup

**main.jsp:**
```jsp
<%@ page errorPage="error.jsp" %>
<%@ page contentType="text/html;charset=UTF-8" %>

<%
    String value = request.getParameter("number");
    int num = Integer.parseInt(value);  // May throw exception
%>
<p>Number: <%= num %></p>
```

**error.jsp:**
```jsp
<%@ page isErrorPage="true" %>
<%@ page contentType="text/html;charset=UTF-8" %>

<!DOCTYPE html>
<html>
<body>
    <h1>Error Occurred</h1>
    <p><%= exception.getClass().getName() %></p>
    <p><%= exception.getMessage() %></p>
</body>
</html>
```

---

## 7. Interview Questions

### Basic Level

**Q1: What are the three JSP directives?**
> 1. Page (`<%@ page %>`) - Page settings
> 2. Include (`<%@ include %>`) - Include files at translation time
> 3. Taglib (`<%@ taglib %>`) - Enable custom tag libraries

**Q2: How do you import packages in JSP?**
```jsp
<%@ page import="java.util.*, java.sql.Connection" %>
```

**Q3: What is the default content type?**
> `text/html; charset=ISO-8859-1` - You should usually set `charset=UTF-8`.

### Intermediate Level

**Q4: What's the difference between include directive and include action?**
| Include Directive | Include Action |
|-------------------|----------------|
| `<%@ include file="" %>` | `<jsp:include page="" />` |
| Translation time | Request time |
| Static | Dynamic |
| One servlet | Two servlets |
| Variables shared | Variables not shared |

**Q5: What does isErrorPage="true" enable?**
> It enables the `exception` implicit object, which contains the exception thrown by the page that forwarded to this error page.

**Q6: Can you have multiple page directives?**
> Yes, but most attributes cannot be repeated. `import` is the only attribute that can appear multiple times.

### Advanced Level

**Q7: How do you disable EL for a page?**
```jsp
<%@ page isELIgnored="true" %>
```
> This treats `${...}` as literal text instead of EL expressions.

**Q8: What happens if buffer overflows with autoFlush="false"?**
> An exception is thrown: `java.io.IOException: Buffer overflow`.
> Use `autoFlush="true"` (default) to avoid this.

---

## Next Topic: [JSP Implicit Objects →](./23_JSP_Implicit_Objects.md)
