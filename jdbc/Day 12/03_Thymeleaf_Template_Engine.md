# 📘 Thymeleaf Template Engine - Complete Guide

## Table of Contents
1. [Introduction to Thymeleaf](#introduction-to-thymeleaf)
2. [Thymeleaf vs JSP](#thymeleaf-vs-jsp)
3. [How Thymeleaf Works](#how-thymeleaf-works)
4. [Setting Up Thymeleaf](#setting-up-thymeleaf)
5. [Thymeleaf Namespace Declaration](#thymeleaf-namespace-declaration)
6. [Core Thymeleaf Expressions](#core-thymeleaf-expressions)
7. [Complete Code Examples](#complete-code-examples)
8. [Template Location and Configuration](#template-location-and-configuration)
9. [Quick Reference](#quick-reference)

---

## Introduction to Thymeleaf

### What is Thymeleaf?

```
┌─────────────────────────────────────────────────────────────────┐
│                    WHAT IS THYMELEAF?                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   "Thymeleaf is a modern server-side Java template engine       │
│    for both web and standalone environments, capable of         │
│    processing HTML, XML, JavaScript, CSS and even plain text."  │
│                                                                 │
│   Key Characteristics:                                          │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ ✓ Server-side rendering                                 │   │
│   │ ✓ Generates HTML dynamically                            │   │
│   │ ✓ Natural templating (HTML looks valid in browser)      │   │
│   │ ✓ More powerful expression language than JSP EL         │   │
│   │ ✓ Works with Spring Boot seamlessly                     │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Primary Use: "Mostly used to generate HTML views for          │
│                 web applications"                               │
│                                                                 │
│   Official Documentation: https://www.thymeleaf.org             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Main Goals of Thymeleaf

```
┌─────────────────────────────────────────────────────────────────┐
│                 THYMELEAF MAIN GOALS                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. ELEGANT TEMPLATES                                          │
│      "Provide an elegant, highly maintainable way of            │
│       creating templates"                                       │
│                                                                 │
│   2. NATURAL TEMPLATES                                          │
│      Templates can be opened directly in a browser              │
│      and still display correctly as valid HTML                  │
│                                                                 │
│   3. POWERFUL EXPRESSIONS                                       │
│      "Its expression language is more powerful than JSP's EL"   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Thymeleaf vs JSP

### Comparison Table

| Feature | Thymeleaf | JSP |
|---------|-----------|-----|
| File Extension | `.html` | `.jsp` |
| Template Validity | Valid HTML | Not valid HTML |
| Expression Language | More powerful | Standard EL |
| Natural Templates | Yes | No |
| Spring Boot Support | Primary choice | Requires extra config |
| Browser Preview | Works without server | Requires server |

### Visual Comparison

```
┌─────────────────────────────────────────────────────────────────┐
│                 THYMELEAF VS JSP                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   THYMELEAF (bookNew.html):                                     │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ <p th:text="${name}">Default Name</p>                   │   │
│   │                                                         │   │
│   │ • Opens in browser as valid HTML                        │   │
│   │ • Shows "Default Name" without server                   │   │
│   │ • Shows actual value with server                        │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   JSP (page.jsp):                                               │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ <p>${name}</p>                                          │   │
│   │ or                                                      │   │
│   │ <p><%= request.getAttribute("name") %></p>              │   │
│   │                                                         │   │
│   │ • Cannot open in browser without server                 │   │
│   │ • JSP tags visible as raw text                          │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## How Thymeleaf Works

### Server-Side Rendering Process

```
┌─────────────────────────────────────────────────────────────────┐
│              THYMELEAF RENDERING PROCESS                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   "Rendering of Thymeleaf happens on server side"               │
│                                                                 │
│   Step-by-Step Flow:                                            │
│                                                                 │
│   1. Browser Request                                            │
│      │                                                          │
│      ▼                                                          │
│   2. Controller Method Executes                                 │
│      ├── Processes business logic                               │
│      └── Adds data to Model                                     │
│      │                                                          │
│      ▼                                                          │
│   3. Returns View Name (e.g., "bookNew")                        │
│      │                                                          │
│      ▼                                                          │
│   4. View Resolver Finds Template                               │
│      └── templates/bookNew.html                                 │
│      │                                                          │
│      ▼                                                          │
│   5. THYMELEAF ENGINE PROCESSES TEMPLATE                        │
│      ├── Parses Thymeleaf expressions                           │
│      ├── Accesses Java objects from Model                       │
│      └── Replaces expressions with actual values                │
│      │                                                          │
│      ▼                                                          │
│   6. Pure HTML Sent to Browser                                  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Thymeleaf Engine

```
┌─────────────────────────────────────────────────────────────────┐
│                  THYMELEAF ENGINE                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   "Thymeleaf Engine will parse Thymeleaf template"              │
│                                                                 │
│   What can Thymeleaf Expressions Access?                        │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ • Java code                                             │   │
│   │ • Java objects (POJOs)                                  │   │
│   │ • Spring beans                                          │   │
│   │ • Model attributes                                      │   │
│   │ • Session attributes                                    │   │
│   │ • Request parameters                                    │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Example:                                                      │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ Inside HTML:  <p th:text="${name}">                     │   │
│   │                                                         │   │
│   │ "Thymeleaf engine will replace 'name' with its value    │   │
│   │  which it will read from some Java class."              │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Setting Up Thymeleaf

### Adding Thymeleaf Dependency

When creating a Spring Boot project, select **Thymeleaf** as a dependency, or add it to pom.xml:

```xml
<!-- pom.xml dependency -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

### File Extension Rule

```
┌─────────────────────────────────────────────────────────────────┐
│                 FILE EXTENSION RULE                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   "Thymeleaf generates HTML dynamically.                        │
│    When we use Thymeleaf, we have to save the file by .html"    │
│                                                                 │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ Template Type        │ File Extension                  │   │
│   ├──────────────────────┼─────────────────────────────────┤   │
│   │ Thymeleaf            │ .html                           │   │
│   │ JSP                  │ .jsp                            │   │
│   └──────────────────────┴─────────────────────────────────┘   │
│                                                                 │
│   Inside the HTML page you will have:                           │
│   "html + Thymeleaf expressions which is very important         │
│    and powerful"                                                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Thymeleaf Namespace Declaration

### The xmlns:th Declaration

Every Thymeleaf template must include the Thymeleaf namespace:

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
```

### Understanding the Namespace

```
┌─────────────────────────────────────────────────────────────────┐
│              THYMELEAF NAMESPACE                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   <html xmlns:th="http://www.thymeleaf.org">                    │
│                                                                 │
│   Breaking it down:                                             │
│   ┌─────────────┬───────────────────────────────────────────┐   │
│   │ xmlns       │ XML Namespace declaration                 │   │
│   │ th          │ Prefix used for Thymeleaf attributes      │   │
│   │ URL         │ Namespace identifier (not a real URL)     │   │
│   └─────────────┴───────────────────────────────────────────┘   │
│                                                                 │
│   Why is this needed?                                           │
│   ✓ Enables th:* attributes in your HTML                        │
│   ✓ IDE recognizes Thymeleaf syntax                             │
│   ✓ Keeps HTML valid for browser preview                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Core Thymeleaf Expressions

### Types of Expressions

```
┌─────────────────────────────────────────────────────────────────┐
│              THYMELEAF EXPRESSION TYPES                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. Variable Expressions: ${...}                               │
│      ├── Access model attributes                                │
│      └── Example: ${bookName}                                   │
│                                                                 │
│   2. Selection Expressions: *{...}                              │
│      ├── Access properties of selected object                   │
│      ├── Used with th:object                                    │
│      └── Example: *{price}                                      │
│                                                                 │
│   3. Link Expressions: @{...}                                   │
│      ├── Create URLs                                            │
│      └── Example: @{/book}                                      │
│                                                                 │
│   4. Message Expressions: #{...}                                │
│      └── Internationalization messages                          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Detailed Expression Examples

#### Variable Expressions ${...}

```html
<!-- Accessing simple attribute -->
<p th:text="${name}">Default Name</p>

<!-- Accessing object property -->
<p th:text="${mybook.bookName}">Book Title</p>

<!-- Accessing object method -->
<p th:text="${mybook.getPrice()}">0</p>
```

#### Selection Expressions *{...}

```html
<!-- First, select the object -->
<form th:object="${mybook}">
    
    <!-- Then use *{...} to access its properties -->
    <input type="text" th:field="*{bookName}" />
    <input type="text" th:field="*{price}" />
    
</form>
```

#### Link Expressions @{...}

```html
<!-- Simple link -->
<a th:href="@{/home}">Home</a>

<!-- Link with path variable -->
<a th:href="@{/book/{id}(id=${book.id})}">View Book</a>

<!-- Form action -->
<form th:action="@{/book}" method="post">
```

---

## Complete Code Examples

### Example 1: Basic Thymeleaf Template (bookNew.html)

```html
<!DOCTYPE html>
<!-- Line 1: HTML5 document type declaration -->

<html xmlns:th="http://www.thymeleaf.org">
<!-- Line 2: HTML tag with Thymeleaf namespace
     - xmlns:th enables th:* attributes
     - Required for all Thymeleaf templates -->

<head>
<!-- Line 3: Head section start -->

<meta charset="ISO-8859-1">
<!-- Line 4: Character encoding declaration -->

<title>Insert title here</title>
<!-- Line 5: Page title -->

</head>
<!-- Line 6: Head section end -->

<body>
<!-- Line 7: Body section start -->

<form th:action="@{/book}" method="post">
<!-- Line 8: Form element
     - th:action="@{/book}" creates URL /book for form submission
     - @{...} is link expression (context-aware URL)
     - method="post" sends data via POST request -->

    <table border="1">
    <!-- Line 9: Table for form layout -->
    
        <tr>
            <td>Enter Name</td>
            <td><input type="text" name="bookname" /></td>
            <!-- name="bookname" identifies the field for controller -->
        </tr>
        <tr>
            <td>Enter Price</td>
            <td><input type="text" name="price" /></td>
        </tr>
        <tr>
            <td><input type="submit" value="Submit" /></td>
        </tr>
    </table>
</form>

</body>
<!-- Line 22: Body section end -->

</html>
<!-- Line 23: HTML end -->
```

### Example 2: Displaying Data (success.html)

```html
<!DOCTYPE html>
<!-- Line 1: HTML5 document type -->

<html xmlns:th="http://www.thymeleaf.org">
<!-- Line 2: Thymeleaf namespace declaration -->

<head>
<meta charset="ISO-8859-1">
<title>Insert title here</title>
</head>

<body>
<!-- Line 7: Body start -->

<p th:text="${mybook.bookName}">
<!-- Line 8: Display book name
     - th:text replaces element content with expression value
     - ${mybook.bookName} accesses bookName property of mybook object
     - mybook is an attribute added to Model in controller -->

<br>
<!-- Line 9: Line break -->

<p th:text="${mybook.price}">
<!-- Line 10: Display book price
     - ${mybook.price} accesses price property -->

</body>
</html>
```

### Line-by-Line Execution Flow

```
┌─────────────────────────────────────────────────────────────────┐
│              EXECUTION FLOW: FORM TO DISPLAY                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   STEP 1: User visits http://localhost:8080/book               │
│           └── @GetMapping("book") method runs                   │
│           └── Returns "bookNew" (view name)                     │
│           └── bookNew.html is rendered                          │
│                                                                 │
│   STEP 2: User fills form and clicks Submit                    │
│           └── Form data sent to /book via POST                  │
│           └── @PostMapping("book") method runs                  │
│                                                                 │
│   STEP 3: Controller processes form data                       │
│           └── Creates Book object                               │
│           └── Sets bookName and price                           │
│           └── Adds Book to Model as "mybook"                    │
│           └── Returns "success" (view name)                     │
│                                                                 │
│   STEP 4: Thymeleaf renders success.html                       │
│           └── ${mybook.bookName} replaced with actual value     │
│           └── ${mybook.price} replaced with actual value        │
│           └── HTML sent to browser                              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Example 3: Form with Object Binding (th:object and th:field)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="ISO-8859-1">
<title>Book Form</title>
</head>
<body>

<form th:action="@{/book}" th:object="${mybook}" method="post">
<!-- th:object="${mybook}" binds the entire form to mybook object
     This allows using *{...} expressions for field binding -->

    <table border="1">
        <tr>
            <td>Enter Name</td>
            <td><input type="text" th:field="*{bookName}" /></td>
            <!-- th:field="*{bookName}" does three things:
                 1. Sets name="bookName"
                 2. Sets id="bookName"
                 3. Sets value="${mybook.bookName}" if exists -->
        </tr>
        <tr>
            <td>Enter Price</td>
            <td><input type="text" th:field="*{price}" /></td>
            <!-- th:field="*{price}" binds to mybook.price -->
        </tr>
        <tr>
            <td><input type="submit" value="Submit" /></td>
        </tr>
    </table>
</form>

</body>
</html>
```

### th:object and th:field Explained

```
┌─────────────────────────────────────────────────────────────────┐
│              th:object AND th:field                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   th:object="${mybook}"                                         │
│   ├── Binds form to a Java object                               │
│   ├── Object must be in Model                                   │
│   └── Enables *{...} expressions inside the form                │
│                                                                 │
│   th:field="*{bookName}"                                        │
│   ├── Selection variable expression (*{...})                    │
│   ├── Selects property from th:object                           │
│   └── Generates: name, id, and value attributes                 │
│                                                                 │
│   Without th:object (uses name attribute):                      │
│   <input type="text" name="bookname" />                         │
│                                                                 │
│   With th:object (uses th:field):                               │
│   <input type="text" th:field="*{bookName}" />                  │
│                                                                 │
│   Generated HTML:                                               │
│   <input type="text" name="bookName" id="bookName" value=""/>   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Template Location and Configuration

### Template Directory

```
┌─────────────────────────────────────────────────────────────────┐
│              TEMPLATE LOCATION                                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Both .html (Thymeleaf) or .jsp files need to be there inside: │
│                                                                 │
│   src/main/resources/templates/                                 │
│                                                                 │
│   Project Structure:                                            │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ src/                                                    │   │
│   │ └── main/                                               │   │
│   │     └── resources/                                      │   │
│   │         ├── templates/         ◀── THYMELEAF FILES HERE │   │
│   │         │   ├── bookNew.html                            │   │
│   │         │   ├── success.html                            │   │
│   │         │   └── Final.html                              │   │
│   │         ├── static/            ◀── CSS, JS, images      │   │
│   │         └── application.properties                      │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### View Resolver Configuration

```
┌─────────────────────────────────────────────────────────────────┐
│              VIEW RESOLVER SETTINGS                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   "Since we don't maintain spring bean configuration file       │
│    inside Spring Boot, View Resolver settings we have to        │
│    write inside application.properties file"                    │
│                                                                 │
│   # application.properties                                      │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ spring.mvc.view.prefix="/"                              │   │
│   │ spring.mvc.view.suffix=".html"                          │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   For Thymeleaf: suffix=".html"                                 │
│   For JSP:       suffix=".jsp"                                  │
│                                                                 │
│   How View Resolution Works:                                    │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ Controller returns: "bookNew"                           │   │
│   │ View Resolver adds: prefix + viewName + suffix          │   │
│   │ Result: "/" + "bookNew" + ".html" = "/bookNew.html"     │   │
│   │ File found at: templates/bookNew.html                   │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### application.properties Content

```properties
# application.properties - Line by Line Explanation

spring.mvc.view.prefix="/"
# Line 1: View prefix
# - "/" means look in the root of templates folder
# - Combined with suffix to create full path

spring.mvc.view.suffix=".html"
# Line 2: View suffix
# - ".html" for Thymeleaf templates
# - ".jsp" if using JSP views
```

---

## Quick Reference

### Thymeleaf Attributes Summary

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `th:text` | Set element text content | `th:text="${name}"` |
| `th:field` | Bind input to object property | `th:field="*{price}"` |
| `th:object` | Bind form to object | `th:object="${book}"` |
| `th:action` | Form submission URL | `th:action="@{/save}"` |
| `th:each` | Iterate over collection | `th:each="item : ${list}"` |
| `th:if` | Conditional rendering | `th:if="${condition}"` |
| `th:href` | Create hyperlink | `th:href="@{/page}"` |
| `th:src` | Image/script source | `th:src="@{/images/logo.png}"` |
| `th:value` | Set input value | `th:value="${item.id}"` |

### Expression Syntax Quick Reference

| Expression | Syntax | Use Case |
|------------|--------|----------|
| Variable | `${...}` | Access model attributes |
| Selection | `*{...}` | Access object properties (within th:object) |
| Link | `@{...}` | Create URLs |
| Message | `#{...}` | Internationalization |
| Fragment | `~{...}` | Include template fragments |

### Common Thymeleaf Patterns

```html
<!-- Display text -->
<p th:text="${message}">Default text</p>

<!-- Create link -->
<a th:href="@{/products}">View Products</a>

<!-- Form with binding -->
<form th:action="@{/save}" th:object="${item}" method="post">
    <input th:field="*{name}" />
</form>

<!-- Iterate collection -->
<tr th:each="item : ${items}">
    <td th:text="${item.name}"></td>
</tr>

<!-- Conditional -->
<div th:if="${user != null}">
    Welcome, <span th:text="${user.name}"></span>
</div>
```

---

## Next Steps

After understanding Thymeleaf, proceed to:
- **Note 04**: Spring MVC Controller - Learn about @Controller and Front Controller pattern

---

*This note is part of the Advanced Java - Spring Boot & Spring MVC series*
