# ViewData and ViewBag in ASP.NET Core MVC

## Table of Contents
1. [Introduction](#1-introduction)
2. [ViewData](#2-viewdata)
3. [ViewBag](#3-viewbag)
4. [Comparison](#4-comparison)
5. [Best Practices](#5-best-practices)
6. [Quick Reference](#6-quick-reference)

---

## 1. Introduction

### Purpose
ViewData and ViewBag are mechanisms to pass data from a Controller to a View. They are alternatives to strongly-typed models for passing additional data.

### Key Characteristics
| Feature | ViewData | ViewBag |
|---------|----------|---------|
| Type | Dictionary | Dynamic object |
| Introduced | MVC 1.0 | MVC 3.0 |
| Type Casting | Required | Not required |
| Lifetime | Current request only | Current request only |

---

## 2. ViewData

### Definition
ViewData is a dictionary object (`ViewDataDictionary`) that stores data using string keys.

### Syntax

```csharp
// Controller - Setting ViewData
public ActionResult Index()
{
    ViewData["Message"] = "Welcome to the application";
    ViewData["CurrentDate"] = DateTime.Now;
    ViewData["Countries"] = new List<string> { "India", "US", "UK", "Canada" };
    
    return View();
}
```

```cshtml
@* View - Reading ViewData *@
<h1>@ViewData["Message"]</h1>
<p>Date: @ViewData["CurrentDate"]</p>

@* Type casting required for complex types *@
<ul>
@foreach (string country in (List<string>)ViewData["Countries"])
{
    <li>@country</li>
}
</ul>
```

### Line-by-Line Analysis
| Line | Code | Explanation |
|------|------|-------------|
| `ViewData["Message"]` | String key accessor | Access data by key name |
| `(List<string>)ViewData["Countries"]` | Type casting | Required for complex types |
| No compile-time checking | Key is string | Runtime error if key misspelled |

---

## 3. ViewBag

### Definition
ViewBag is a dynamic wrapper around ViewData. It uses C# 4.0's dynamic feature for property-like access.

### Syntax

```csharp
// Controller - Setting ViewBag
public ActionResult Index()
{
    ViewBag.Message = "Welcome to the application";
    ViewBag.CurrentDate = DateTime.Now;
    ViewBag.Countries = new List<string> { "India", "US", "UK", "Canada" };
    
    return View();
}
```

```cshtml
@* View - Reading ViewBag *@
<h1>@ViewBag.Message</h1>
<p>Date: @ViewBag.CurrentDate</p>

@* No type casting needed *@
<ul>
@foreach (string country in ViewBag.Countries)
{
    <li>@country</li>
}
</ul>
```

### Line-by-Line Analysis
| Line | Code | Explanation |
|------|------|-------------|
| `ViewBag.Message` | Dynamic property | Cleaner syntax than dictionary |
| `foreach (string country in ViewBag.Countries)` | No casting | Dynamic handles type |
| No compile-time checking | Property is dynamic | Runtime error if name misspelled |

---

## 4. Comparison

### ViewData vs ViewBag

| Aspect | ViewData | ViewBag |
|--------|----------|---------|
| **Type** | Dictionary<string, object> | dynamic object |
| **Syntax** | `ViewData["Key"]` | `ViewBag.Property` |
| **Type Casting** | Required for complex types | Not required |
| **Speed** | Faster | Slightly slower |
| **Compile-Time Check** | No | No |
| **IntelliSense** | No | No |
| **Null Check** | Required | Required |
| **Introduced** | MVC 1.0 | MVC 3.0 |

### Internal Relationship

```csharp
// ViewBag is a wrapper around ViewData
// Setting via ViewBag:
ViewBag.Title = "Home Page";

// Equivalent ViewData:
ViewData["Title"] = "Home Page";

// They access the same underlying data!
```

### Common Pitfall - No Compile-Time Errors

```csharp
// Controller
ViewBag.Message = "Hello";

// View - TYPO - No compile error!
<h1>@ViewBag.Mesage</h1>  @* Misspelled - returns null at runtime *@
```

---

## 5. When to Use ViewData/ViewBag vs ViewModel

### Use ViewData/ViewBag For:
- Small amounts of additional data
- Page titles
- Simple messages
- Layout-specific data

### Use ViewModel For:
- Main page data
- Forms with validation
- Complex data structures
- When IntelliSense is needed

### Preferred Approach: Strongly-Typed ViewModels

```csharp
// ViewModel - Strongly typed, IntelliSense, compile-time checking
public class HomeIndexViewModel
{
    public string Message { get; set; }
    public DateTime CurrentDate { get; set; }
    public List<string> Countries { get; set; }
}

// Controller
public ActionResult Index()
{
    var model = new HomeIndexViewModel
    {
        Message = "Welcome",
        CurrentDate = DateTime.Now,
        Countries = new List<string> { "India", "US", "UK" }
    };
    return View(model);
}
```

---

## 6. Best Practices

### DO ✅
| Practice | Reason |
|----------|--------|
| Use ViewBag for simple data (page title) | Cleaner syntax |
| Check for null before using | Prevent runtime errors |
| Prefer ViewModels for complex data | Type safety, IntelliSense |

### DON'T ❌
| Practice | Reason |
|----------|--------|
| Don't rely on for main view data | No compile-time safety |
| Don't store sensitive data | Easy to access in view |
| Don't expect data after redirect | Cleared on each request |

---

## 7. Quick Reference

### Null Check Pattern

```csharp
// Controller
ViewBag.Message = someValue; // Could be null
```

```cshtml
@* View - Safe null check *@
@if (ViewBag.Message != null)
{
    <h1>@ViewBag.Message</h1>
}

@* Or using null-conditional *@
<h1>@ViewBag.Message?.ToString()</h1>
```

### Common Usage Patterns

```csharp
// Page Title
ViewBag.Title = "Home Page";

// Success/Error Messages
ViewBag.SuccessMessage = "Record saved successfully";
ViewBag.ErrorMessage = "An error occurred";

// Dropdowns (though SelectList is better)
ViewBag.Categories = new SelectList(categories, "Id", "Name");
```

---

## 8. Interview Questions

1. **What is the difference between ViewData and ViewBag?**
   - ViewData is a dictionary requiring string keys and type casting. ViewBag is a dynamic wrapper with property-like syntax and no casting needed.

2. **Do ViewData and ViewBag persist across requests?**
   - No, they only last for the current request. Use TempData for cross-request data.

3. **Why prefer ViewModels over ViewBag?**
   - Type safety, IntelliSense support, compile-time error checking.

4. **What happens if you access a misspelled ViewBag property?**
   - Returns null at runtime with no compile-time error.

5. **Are ViewData and ViewBag independent?**
   - No, ViewBag is a wrapper around ViewData. They share the same underlying data.
