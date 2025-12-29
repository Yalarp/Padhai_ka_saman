# 📘 Server-Side Form Validation - Complete Guide

## Table of Contents
1. [Introduction to Validation](#introduction-to-validation)
2. [Why Server-Side Validation](#why-server-side-validation)
3. [Setting Up Validation](#setting-up-validation)
4. [Validation Annotations](#validation-annotations)
5. [@Valid and BindingResult](#valid-and-bindingresult)
6. [Displaying Errors in Thymeleaf](#displaying-errors-in-thymeleaf)
7. [Custom Error Messages](#custom-error-messages)
8. [Complete Code Examples](#complete-code-examples)
9. [Execution Flow](#execution-flow)
10. [Quick Reference](#quick-reference)

---

## Introduction to Validation

### What is Form Validation?

```
┌─────────────────────────────────────────────────────────────────┐
│              FORM VALIDATION CONCEPT                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Validation ensures user input meets application requirements  │
│   before processing.                                            │
│                                                                 │
│   Two Types:                                                    │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ CLIENT-SIDE (JavaScript)                                │   │
│   │ ├── Runs in browser                                     │   │
│   │ ├── Fast feedback                                       │   │
│   │ ├── Better UX                                           │   │
│   │ └── ⚠️ CAN BE BYPASSED!                                 │   │
│   │                                                         │   │
│   │ SERVER-SIDE (Java/Spring)                               │   │
│   │ ├── Runs on server                                      │   │
│   │ ├── Cannot be bypassed                                  │   │
│   │ ├── MANDATORY for security                              │   │
│   │ └── ✓ Always trustworthy                                │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Best Practice: Use BOTH client-side AND server-side           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Why Server-Side Validation

### Security Concern

```
┌─────────────────────────────────────────────────────────────────┐
│              WHY SERVER-SIDE IS MANDATORY                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Client-side validation can be bypassed by:                    │
│                                                                 │
│   1. Disabling JavaScript in browser                            │
│   2. Using browser developer tools                              │
│   3. Sending direct HTTP requests (Postman, curl)               │
│   4. Malicious scripts                                          │
│                                                                 │
│   Example Attack:                                               │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ # Bypassing client validation with curl                 │   │
│   │ curl -X POST http://localhost:8080/book                 │   │
│   │      -d "bookName=<script>alert('XSS')</script>"        │   │
│   │      -d "price=-999999"                                 │   │
│   │                                                         │   │
│   │ Without server validation:                              │   │
│   │ - Malicious data enters database                        │   │
│   │ - Negative prices could crash calculations              │   │
│   │ - XSS attacks possible                                  │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Server-side validation is your LAST LINE OF DEFENSE!          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Setting Up Validation

### Required Dependencies

Add to pom.xml:

```xml
<!-- Spring Boot Validation Starter (includes Hibernate Validator) -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>

<!-- Hibernate Validator (if not using starter) -->
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
</dependency>
```

### Validation Framework

```
┌─────────────────────────────────────────────────────────────────┐
│              VALIDATION FRAMEWORK STACK                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │            Your Application Code                        │   │
│   │        @Valid, @NotBlank, @Size, etc.                   │   │
│   └─────────────────────────────────────────────────────────┘   │
│                         ▲                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │          Spring Validation Integration                  │   │
│   │     Integrates Bean Validation with Spring MVC          │   │
│   └─────────────────────────────────────────────────────────┘   │
│                         ▲                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │            Hibernate Validator                          │   │
│   │    Reference implementation of Bean Validation          │   │
│   └─────────────────────────────────────────────────────────┘   │
│                         ▲                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │         Bean Validation API (JSR-380)                   │   │
│   │        Standard Java validation annotations             │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Validation Annotations

### Standard Bean Validation Annotations

```
┌─────────────────────────────────────────────────────────────────┐
│              VALIDATION ANNOTATIONS                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   STRING VALIDATIONS (jakarta.validation.constraints)           │
│   ─────────────────────────────────────────────────────         │
│   @NotNull        - Value cannot be null                        │
│   @NotEmpty       - Not null AND not empty ("")                 │
│   @NotBlank       - Not null, not empty, not whitespace only    │
│   @Size(min, max) - String length between min and max           │
│   @Pattern(regex) - Must match regex pattern                    │
│   @Email          - Valid email format                          │
│                                                                 │
│   NUMBER VALIDATIONS                                            │
│   ────────────────                                              │
│   @Min(value)     - Minimum numeric value                       │
│   @Max(value)     - Maximum numeric value                       │
│   @Positive       - Must be positive (> 0)                      │
│   @PositiveOrZero - Must be >= 0                                │
│   @Negative       - Must be negative (< 0)                      │
│   @Digits(int, frac) - Integer and fraction digit limits        │
│                                                                 │
│   HIBERNATE VALIDATOR EXTRAS (org.hibernate.validator)          │
│   ──────────────────────────────────────────────────            │
│   @Range(min, max) - Numeric value in range                     │
│   @Length(min, max) - String length (Hibernate specific)        │
│   @URL             - Valid URL format                           │
│   @CreditCardNumber - Valid credit card                         │
│                                                                 │
│   DATE VALIDATIONS                                              │
│   ────────────────                                              │
│   @Past           - Date must be in the past                    │
│   @Future         - Date must be in the future                  │
│   @PastOrPresent  - Past or present date                        │
│   @FutureOrPresent - Future or present date                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Annotation Comparison

| Annotation | null | "" | " " (spaces) |
|------------|------|--------|--------------|
| `@NotNull` | ❌ | ✓ | ✓ |
| `@NotEmpty` | ❌ | ❌ | ✓ |
| `@NotBlank` | ❌ | ❌ | ❌ |

### Example: Book.java with Validation

```java
package com.example.demo;

import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Pattern;
import jakarta.validation.constraints.Size;
import org.hibernate.validator.constraints.Range;

/**
 * Book.java - Model class with validation annotations
 */
public class Book 
{
    /**
     * @NotBlank - Cannot be null, empty, or whitespace only
     * @Size - Length must be between 2 and 30 characters
     * @Pattern - Must contain only letters and spaces
     */
    @NotBlank(message = "Book Name should not be empty")
    @Size(min = 2, max = 30, message = "Name must be 2-30 characters")
    @Pattern(regexp = "[a-zA-Z ]+", message = "Only letters and spaces allowed")
    private String bookName;
    
    /**
     * @Range - Value must be between 100 and 10000 (Hibernate specific)
     * Automatically handles type conversion from String to long
     */
    @Range(min = 100, max = 10000, message = "Price must be between 100 and 10000")
    private long price;
    
    // Default constructor (required for binding)
    public Book() {}
    
    // Getters and Setters
    public String getBookName() {
        return bookName;
    }
    
    public void setBookName(String bookName) {
        this.bookName = bookName;
    }
    
    public long getPrice() {
        return price;
    }
    
    public void setPrice(long price) {
        this.price = price;
    }
}
```

### Line-by-Line Annotation Explanation

| Annotation | Field | Validation Rule |
|------------|-------|-----------------|
| `@NotBlank(message="...")` | bookName | Cannot be null/empty/whitespace |
| `@Size(min=2, max=30)` | bookName | Length between 2 and 30 chars |
| `@Pattern(regexp="[a-zA-Z ]+")` | bookName | Only letters and spaces |
| `@Range(min=100, max=10000)` | price | Value between 100 and 10000 |

---

## @Valid and BindingResult

### How Validation Works in Controller

```
┌─────────────────────────────────────────────────────────────────┐
│              @Valid and BindingResult                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   @Valid                                                        │
│   ──────                                                        │
│   Triggers Bean Validation on the annotated object.             │
│   Spring validates all @NotBlank, @Size, etc. annotations.      │
│                                                                 │
│   BindingResult                                                 │
│   ─────────────                                                 │
│   Captures validation errors. MUST come immediately after       │
│   the @Valid parameter!                                         │
│                                                                 │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ @PostMapping("book")                                    │   │
│   │ public String submit(                                   │   │
│   │     @Valid Book book,           // ← Triggers validation│   │
│   │     BindingResult result) {     // ← Captures errors    │   │
│   │                                                         │   │
│   │     if (result.hasErrors()) {   // ← Check for errors   │   │
│   │         return "bookForm";      // ← Return to form     │   │
│   │     }                                                   │   │
│   │     return "success";           // ← Process if valid   │   │
│   │ }                                                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   ⚠️ IMPORTANT: BindingResult MUST be immediately after @Valid! │
│   ❌ WRONG: submit(@Valid Book, Model, BindingResult)           │
│   ✓ RIGHT: submit(@Valid Book, BindingResult, Model)            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Controller Example

```java
package com.example.demo;

import jakarta.validation.Valid;
import org.springframework.stereotype.Controller;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class BookNewController
{
    /**
     * GET /book - Display form
     */
    @GetMapping("book")
    public ModelAndView before()
    {
        Book book = new Book();  // Empty book for form binding
        return new ModelAndView("bookNew", "mybook", book);
    }
    
    /**
     * POST /book - Process form with validation
     * 
     * @Valid triggers validation on Book object
     * BindingResult captures validation errors
     */
    @PostMapping("book")
    public String afterSubmit(
        @Valid Book book,              // Triggers validation
        BindingResult result)          // Captures errors (MUST be next!)
    {
        // Check if there are validation errors
        if (result.hasErrors())
        {
            // Return to form - errors will be displayed
            return "bookNew";
        }
        
        // If no errors, proceed with business logic
        // book.getBookName() and book.getPrice() are valid
        return "success";
    }
}
```

### BindingResult Methods

| Method | Description |
|--------|-------------|
| `hasErrors()` | Returns true if any validation errors exist |
| `getErrorCount()` | Number of errors |
| `getAllErrors()` | List of all ObjectError |
| `getFieldErrors()` | List of FieldError only |
| `getFieldError("field")` | Get error for specific field |

---

## Displaying Errors in Thymeleaf

### Error Display Syntax

```
┌─────────────────────────────────────────────────────────────────┐
│              DISPLAYING ERRORS IN THYMELEAF                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Method 1: Using #fields.errors()                              │
│   ─────────────────────────────────                             │
│   <p th:each="err : ${#fields.errors('fieldName')}"             │
│      th:text="${err}">Error message</p>                         │
│                                                                 │
│   Method 2: Using #fields.hasErrors()                           │
│   ─────────────────────────────────────                         │
│   <div th:if="${#fields.hasErrors('fieldName')}"                │
│        class="error">                                           │
│       <p th:errors="*{fieldName}">Error</p>                     │
│   </div>                                                        │
│                                                                 │
│   Method 3: Using th:errors                                     │
│   ─────────────────────────                                     │
│   <span th:errors="*{fieldName}" class="error">Error</span>     │
│                                                                 │
│   Method 4: All errors for the form                             │
│   ──────────────────────────────────                            │
│   <ul th:if="${#fields.hasAnyErrors()}">                        │
│       <li th:each="err : ${#fields.allErrors()}"                │
│           th:text="${err}">Error</li>                           │
│   </ul>                                                         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Form with Error Display (bookNew.html)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="ISO-8859-1">
    <title>Add Book</title>
    <style>
        .error { color: red; font-size: 12px; }
        .field-error { border: 2px solid red; }
        table { border-collapse: collapse; }
        td { padding: 8px; }
    </style>
</head>
<body>

<h1>Add New Book</h1>

<!-- Show all errors at top (optional) -->
<div th:if="${#fields.hasAnyErrors()}" style="color: red; border: 1px solid red; padding: 10px;">
    <strong>Please fix the following errors:</strong>
    <ul>
        <li th:each="err : ${#fields.allErrors()}" th:text="${err}">Error</li>
    </ul>
</div>

<form th:action="@{/book}" th:object="${mybook}" method="post">
    <table border="1">
        <tr>
            <td>Enter Name</td>
            <td>
                <!-- Add error class if field has errors -->
                <input type="text" th:field="*{bookName}" 
                       th:classappend="${#fields.hasErrors('bookName')} ? 'field-error' : ''"/>
            </td>
            <td>
                <!-- Display errors for bookName field -->
                <span th:each="err : ${#fields.errors('bookName')}" 
                      th:text="${err}"
                      class="error">Error message</span>
            </td>
        </tr>
        <tr>
            <td>Enter Price</td>
            <td>
                <input type="number" th:field="*{price}"
                       th:classappend="${#fields.hasErrors('price')} ? 'field-error' : ''"/>
            </td>
            <td>
                <!-- Display errors for price field -->
                <span th:each="err : ${#fields.errors('price')}" 
                      th:text="${err}"
                      class="error">Error message</span>
            </td>
        </tr>
        <tr>
            <td colspan="2">
                <input type="submit" value="Submit"/>
            </td>
        </tr>
    </table>
</form>

</body>
</html>
```

### Thymeleaf Error Utilities

| Expression | Description |
|------------|-------------|
| `${#fields.errors('field')}` | List of errors for field |
| `${#fields.hasErrors('field')}` | True if field has errors |
| `${#fields.allErrors()}` | All errors in form |
| `${#fields.hasAnyErrors()}` | True if any errors exist |
| `${#fields.hasGlobalErrors()}` | True if object-level errors |

---

## Custom Error Messages

### Defining Custom Messages

```java
// Inline message (in annotation)
@NotBlank(message = "Book Name is required")
private String bookName;

// Using message code for i18n
@NotBlank(message = "{book.name.required}")
private String bookName;

// With parameters
@Size(min = 2, max = 30, message = "Name must be between {min} and {max} characters")
private String bookName;
```

### Message Interpolation

```
┌─────────────────────────────────────────────────────────────────┐
│              MESSAGE INTERPOLATION                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   {min}, {max}, {value} - Replaced with annotation values       │
│                                                                 │
│   Example:                                                      │
│   @Size(min = 2, max = 30,                                      │
│         message = "Must be between {min} and {max} chars")      │
│                                                                 │
│   Result: "Must be between 2 and 30 chars"                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Complete Code Examples

### Book.java (with all validations)

```java
package com.example.demo;

import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Pattern;
import jakarta.validation.constraints.Size;
import org.hibernate.validator.constraints.Range;

public class Book 
{
    @NotBlank(message = "Book Name should not be empty")
    @Size(min = 2, max = 30, message = "Name must be 2-30 characters")
    @Pattern(regexp = "[a-zA-Z ]+", message = "Only letters and spaces allowed")
    private String bookName;
    
    @Range(min = 100, max = 10000, message = "Price must be between 100 and 10000")
    private long price;
    
    // Default constructor
    public Book() {}
    
    // Getters and Setters
    public String getBookName() { return bookName; }
    public void setBookName(String bookName) { this.bookName = bookName; }
    public long getPrice() { return price; }
    public void setPrice(long price) { this.price = price; }
}
```

### BookNewController.java

```java
package com.example.demo;

import jakarta.validation.Valid;
import org.springframework.stereotype.Controller;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class BookNewController
{
    @GetMapping("book")
    public ModelAndView before()
    {
        Book book = new Book();
        return new ModelAndView("bookNew", "mybook", book);
    }
    
    @PostMapping("book")
    public String afterSubmit(@Valid Book book, BindingResult result)
    {
        if (result.hasErrors())
        {
            // Validation failed - return to form
            // Errors are automatically available to Thymeleaf
            return "bookNew";
        }
        
        // Validation passed - proceed
        return "success";
    }
}
```

### MvcValidationApplication.java

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MvcValidationApplication 
{
    public static void main(String[] args) 
    {
        SpringApplication.run(MvcValidationApplication.class, args);
    }
}
```

---

## Execution Flow

### Validation Process Flow

```
┌─────────────────────────────────────────────────────────────────┐
│              VALIDATION EXECUTION FLOW                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   1. User submits form                                          │
│      └── POST /book with data:                                  │
│          bookName: "" (empty)                                   │
│          price: 50 (too low)                                    │
│      │                                                          │
│      ▼                                                          │
│   2. Spring Data Binding                                        │
│      └── Creates new Book()                                     │
│      └── Calls book.setBookName("")                             │
│      └── Calls book.setPrice(50)                                │
│      │                                                          │
│      ▼                                                          │
│   3. @Valid triggers Bean Validation                            │
│      │                                                          │
│      ├── @NotBlank on bookName                                  │
│      │   └── "" is blank → FAILS                                │
│      │   └── Error: "Book Name should not be empty"             │
│      │                                                          │
│      ├── @Size on bookName                                      │
│      │   └── "" length is 0 → FAILS (min=2)                     │
│      │   └── Error: "Name must be 2-30 characters"              │
│      │                                                          │
│      └── @Range on price                                        │
│          └── 50 < 100 → FAILS                                   │
│          └── Error: "Price must be between 100 and 10000"       │
│      │                                                          │
│      ▼                                                          │
│   4. Errors stored in BindingResult                             │
│      │                                                          │
│      ▼                                                          │
│   5. Controller checks result.hasErrors()                       │
│      └── Returns true (3 errors)                                │
│      │                                                          │
│      ▼                                                          │
│   6. Returns "bookNew" to redisplay form                        │
│      │                                                          │
│      ▼                                                          │
│   7. Thymeleaf renders form with errors                         │
│      └── ${#fields.errors('bookName')} shows errors             │
│      └── ${#fields.errors('price')} shows errors                │
│      └── User's input preserved in form fields                  │
│                                                                 │
│   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ │
│                                                                 │
│   8. User corrects errors and resubmits                         │
│      └── bookName: "Java Guide" (valid)                         │
│      └── price: 500 (valid)                                     │
│      │                                                          │
│      ▼                                                          │
│   9. Validation passes                                          │
│      └── result.hasErrors() returns false                       │
│      │                                                          │
│      ▼                                                          │
│  10. Returns "success" view                                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

### Validation Annotations

| Annotation | Package | Description |
|------------|---------|-------------|
| `@NotNull` | jakarta.validation | Not null |
| `@NotEmpty` | jakarta.validation | Not null/empty |
| `@NotBlank` | jakarta.validation | Not null/empty/whitespace |
| `@Size` | jakarta.validation | String/collection size |
| `@Min/@Max` | jakarta.validation | Numeric bounds |
| `@Pattern` | jakarta.validation | Regex match |
| `@Email` | jakarta.validation | Email format |
| `@Range` | org.hibernate.validator | Numeric range |

### Controller Pattern

```java
@PostMapping("save")
public String save(
    @Valid MyObject obj,      // 1. @Valid triggers validation
    BindingResult result) {   // 2. MUST be immediately after
    
    if (result.hasErrors()) { // 3. Check for errors
        return "formView";    // 4. Return to form if errors
    }
    return "successView";     // 5. Process if valid
}
```

### Thymeleaf Error Display

```html
<!-- Single error -->
<span th:errors="*{field}" class="error"></span>

<!-- Multiple errors -->
<span th:each="err : ${#fields.errors('field')}" 
      th:text="${err}" class="error"></span>

<!-- Conditional styling -->
<input th:field="*{field}"
       th:classappend="${#fields.hasErrors('field')} ? 'error-border'"/>
```

### Required Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

---

*This note is part of the Advanced Java - Spring Boot & Spring MVC series*
