# JSTL with Collections - Complete Guide

## Table of Contents
1. [Introduction](#1-introduction)
2. [Iterating Arrays](#2-iterating-arrays)
3. [Iterating Lists](#3-iterating-lists)
4. [Iterating Maps](#4-iterating-maps)
5. [Loop Status Variable](#5-loop-status-variable)
6. [Nested Collections](#6-nested-collections)
7. [Complete Examples](#7-complete-examples)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

JSTL's `<c:forEach>` is powerful for iterating over various Java collection types. Understanding how to work with different collections is essential for data display.

---

## 2. Iterating Arrays

### String Array

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

<%
    String[] fruits = {"Apple", "Banana", "Orange", "Mango"};
    request.setAttribute("fruits", fruits);
%>

<ul>
    <c:forEach var="fruit" items="${fruits}">
        <li>${fruit}</li>
    </c:forEach>
</ul>
```

### Integer Array

```jsp
<%
    int[] numbers = {10, 20, 30, 40, 50};
    request.setAttribute("numbers", numbers);
%>

<c:forEach var="num" items="${numbers}">
    Number: ${num}<br/>
</c:forEach>
```

### Object Array

```jsp
<%
    Product[] products = {
        new Product("Laptop", 999.99),
        new Product("Phone", 499.99),
        new Product("Tablet", 299.99)
    };
    request.setAttribute("products", products);
%>

<table>
    <tr><th>Name</th><th>Price</th></tr>
    <c:forEach var="product" items="${products}">
        <tr>
            <td>${product.name}</td>
            <td>${product.price}</td>
        </tr>
    </c:forEach>
</table>
```

---

## 3. Iterating Lists

### ArrayList

```jsp
<%
    List<String> cities = new ArrayList<>();
    cities.add("New York");
    cities.add("London");
    cities.add("Tokyo");
    cities.add("Paris");
    request.setAttribute("cities", cities);
%>

<c:forEach var="city" items="${cities}">
    <p>${city}</p>
</c:forEach>
```

### List of Objects

```jsp
<%
    List<User> users = userService.getAllUsers();
    request.setAttribute("users", users);
%>

<table>
    <tr>
        <th>ID</th>
        <th>Name</th>
        <th>Email</th>
    </tr>
    <c:forEach var="user" items="${users}">
        <tr>
            <td>${user.id}</td>
            <td>${user.name}</td>
            <td>${user.email}</td>
        </tr>
    </c:forEach>
</table>
```

### Vector (Legacy)

```jsp
<%@ page import="java.util.*" %>

<%
    Vector<String> items = new Vector<>();
    items.addElement("Item 1");
    items.addElement("Item 2");
    items.addElement("Item 3");
    request.setAttribute("items", items);
%>

<c:forEach var="item" items="${items}">
    ${item}<br/>
</c:forEach>
```

---

## 4. Iterating Maps

### Simple HashMap

```jsp
<%
    Map<String, String> capitals = new HashMap<>();
    capitals.put("USA", "Washington DC");
    capitals.put("UK", "London");
    capitals.put("Japan", "Tokyo");
    capitals.put("France", "Paris");
    request.setAttribute("capitals", capitals);
%>

<table>
    <tr><th>Country</th><th>Capital</th></tr>
    <c:forEach var="entry" items="${capitals}">
        <tr>
            <td>${entry.key}</td>
            <td>${entry.value}</td>
        </tr>
    </c:forEach>
</table>
```

### Map with Object Values

```jsp
<%
    Map<Integer, User> userMap = new HashMap<>();
    userMap.put(1, new User("John", "john@mail.com"));
    userMap.put(2, new User("Jane", "jane@mail.com"));
    request.setAttribute("userMap", userMap);
%>

<c:forEach var="entry" items="${userMap}">
    ID: ${entry.key}, 
    Name: ${entry.value.name}, 
    Email: ${entry.value.email}<br/>
</c:forEach>
```

---

## 5. Loop Status Variable

### varStatus Attribute

The `varStatus` attribute provides information about the current iteration:

| Property | Description |
|----------|-------------|
| `index` | Current index (0-based) |
| `count` | Current iteration (1-based) |
| `first` | Is this the first iteration? |
| `last` | Is this the last iteration? |
| `current` | Current item |

### Example with varStatus

```jsp
<table>
    <tr>
        <th>#</th>
        <th>Index</th>
        <th>Name</th>
        <th>Status</th>
    </tr>
    <c:forEach var="user" items="${users}" varStatus="status">
        <tr class="${status.index % 2 == 0 ? 'even' : 'odd'}">
            <td>${status.count}</td>
            <td>${status.index}</td>
            <td>${user.name}</td>
            <td>
                <c:if test="${status.first}">★ First</c:if>
                <c:if test="${status.last}">★ Last</c:if>
            </td>
        </tr>
    </c:forEach>
</table>
```

### Alternating Row Colors

```jsp
<c:forEach var="item" items="${items}" varStatus="s">
    <div class="${s.index % 2 == 0 ? 'row-light' : 'row-dark'}">
        ${item}
    </div>
</c:forEach>
```

### Adding Separators (Not on Last)

```jsp
<c:forEach var="tag" items="${tags}" varStatus="s">
    ${tag}<c:if test="${!s.last}">, </c:if>
</c:forEach>

<%-- Output: tag1, tag2, tag3, tag4 (no trailing comma) --%>
```

---

## 6. Nested Collections

### List of Lists

```jsp
<%
    List<List<String>> matrix = new ArrayList<>();
    matrix.add(Arrays.asList("A", "B", "C"));
    matrix.add(Arrays.asList("D", "E", "F"));
    matrix.add(Arrays.asList("G", "H", "I"));
    request.setAttribute("matrix", matrix);
%>

<table>
    <c:forEach var="row" items="${matrix}">
        <tr>
            <c:forEach var="cell" items="${row}">
                <td>${cell}</td>
            </c:forEach>
        </tr>
    </c:forEach>
</table>
```

### Object with Collection Property

```jsp
<%
    // Order has List<OrderItem> items
    request.setAttribute("order", order);
%>

<h2>Order #${order.id}</h2>
<table>
    <c:forEach var="item" items="${order.items}">
        <tr>
            <td>${item.productName}</td>
            <td>${item.quantity}</td>
            <td>${item.price}</td>
        </tr>
    </c:forEach>
</table>
<p>Total: ${order.total}</p>
```

---

## 7. Complete Examples

### Product Catalog with Categories

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>

<%
    // Map<String, List<Product>> categoryProducts
    request.setAttribute("catalog", catalog);
%>

<c:forEach var="category" items="${catalog}">
    <h2>${category.key}</h2>
    
    <c:choose>
        <c:when test="${empty category.value}">
            <p>No products in this category.</p>
        </c:when>
        <c:otherwise>
            <ul>
                <c:forEach var="product" items="${category.value}">
                    <li>
                        ${product.name} - 
                        <fmt:formatNumber value="${product.price}" type="currency"/>
                    </li>
                </c:forEach>
            </ul>
        </c:otherwise>
    </c:choose>
</c:forEach>
```

### Pagination Example

```jsp
<%
    // From servlet
    // request.setAttribute("users", userList);
    // request.setAttribute("currentPage", pageNum);
    // request.setAttribute("totalPages", pages);
%>

<table>
    <tr><th>#</th><th>Name</th><th>Email</th></tr>
    <c:forEach var="user" items="${users}" varStatus="s">
        <tr>
            <td>${(currentPage - 1) * 10 + s.count}</td>
            <td>${user.name}</td>
            <td>${user.email}</td>
        </tr>
    </c:forEach>
</table>

<div class="pagination">
    <c:forEach begin="1" end="${totalPages}" var="page">
        <c:choose>
            <c:when test="${page == currentPage}">
                <span class="current">${page}</span>
            </c:when>
            <c:otherwise>
                <a href="?page=${page}">${page}</a>
            </c:otherwise>
        </c:choose>
    </c:forEach>
</div>
```

---

## 8. Interview Questions

### Basic Level

**Q1: How do you iterate over a List in JSTL?**
```jsp
<c:forEach var="item" items="${myList}">
    ${item}
</c:forEach>
```

**Q2: How do you access Map key and value?**
```jsp
<c:forEach var="entry" items="${myMap}">
    Key: ${entry.key}, Value: ${entry.value}
</c:forEach>
```

**Q3: What is varStatus used for?**
> It provides loop metadata: index, count, first, last, current.

### Intermediate Level

**Q4: How do you get the current index (0-based) vs count (1-based)?**
```jsp
<c:forEach var="item" items="${list}" varStatus="s">
    Index: ${s.index}  <%-- 0, 1, 2, ... --%>
    Count: ${s.count}  <%-- 1, 2, 3, ... --%>
</c:forEach>
```

**Q5: How do you create alternating row styles?**
```jsp
<c:forEach var="row" items="${data}" varStatus="s">
    <tr class="${s.index % 2 == 0 ? 'even' : 'odd'}">
        <td>${row}</td>
    </tr>
</c:forEach>
```

### Advanced Level

**Q6: How do you iterate over a specific range of a collection?**
```jsp
<%-- Items 5-10 only --%>
<c:forEach var="item" items="${list}" begin="4" end="9">
    ${item}
</c:forEach>
```

**Q7: How do you handle empty collections?**
```jsp
<c:choose>
    <c:when test="${empty myList}">
        <p>No items found.</p>
    </c:when>
    <c:otherwise>
        <c:forEach var="item" items="${myList}">
            ${item}
        </c:forEach>
    </c:otherwise>
</c:choose>
```

---

## Next Topic: [Accessing Business Logic →](./33_Accessing_Business_Logic.md)
