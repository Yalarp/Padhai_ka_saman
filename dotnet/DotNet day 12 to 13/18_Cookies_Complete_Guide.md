# Cookies Complete Guide in ASP.NET Core MVC

## Table of Contents
1. [Introduction](#1-introduction)
2. [Types of Cookies](#2-types-of-cookies)
3. [Creating Cookies](#3-creating-cookies)
4. [Reading and Deleting Cookies](#4-reading-and-deleting-cookies)
5. [Remember Me Implementation](#5-remember-me-implementation)
6. [Accessing Cookies in Views](#6-accessing-cookies-in-views)
7. [Best Practices](#7-best-practices)
8. [Quick Reference](#8-quick-reference)

---

## 1. Introduction

### What are Cookies?
Cookies are small pieces of data stored on the client's browser, used to remember information about the user between requests.

### Cookie Characteristics
| Feature | Description |
|---------|-------------|
| **Created By** | Server |
| **Stored At** | Client browser |
| **Scope** | Browser-specific, user-specific |
| **Size Limit** | ~4KB per cookie |

---

## 2. Types of Cookies

### Persistent Cookies
Remain on client even after browser closes. Have explicit expiration date.

```csharp
CookieOptions options = new CookieOptions();
options.Expires = DateTime.Now.AddDays(7);  // Persistent for 7 days
Response.Cookies.Append("Key", "Value", options);
```

### Non-Persistent (Session) Cookies
Deleted when browser closes. No expiration set.

```csharp
Response.Cookies.Append("Key", "Value");  // No expiration = session cookie
```

### Comparison

| Type | Expires Property | Persistence |
|------|-----------------|-------------|
| **Persistent** | Set to future date | Survives browser close |
| **Non-Persistent** | Not set | Deleted on browser close |

---

## 3. Creating Cookies

### Basic Cookie

```csharp
public IActionResult Create()
{
    string key = "DemoCookie";
    string value = DateTime.Now.ToString();

    CookieOptions options = new CookieOptions();
    options.Expires = DateTime.Now.AddDays(7);
    
    Response.Cookies.Append(key, value, options);
    
    return View();
}
```

### Cookie with All Options

```csharp
CookieOptions options = new CookieOptions()
{
    Domain = "localhost",           // Cookie domain
    Path = "/",                     // Available entire app
    Expires = DateTime.Now.AddDays(7),  // Expiration
    Secure = false,                 // HTTPS only (true in production)
    HttpOnly = true,                // No JavaScript access
    IsEssential = true,             // GDPR essential cookie
    SameSite = SameSiteMode.Strict  // CSRF protection
};

Response.Cookies.Append("userId", "12345", options);
```

### Cookie Options Explained

| Option | Purpose | Recommended |
|--------|---------|-------------|
| `Domain` | Which domain can access | Current domain |
| `Path` | Which paths can access | "/" for all |
| `Expires` | When cookie expires | Based on need |
| `Secure` | HTTPS only | `true` in production |
| `HttpOnly` | Block JavaScript access | `true` for security |
| `IsEssential` | GDPR compliance | `true` if necessary |
| `SameSite` | CSRF protection | `Strict` or `Lax` |

---

## 4. Reading and Deleting Cookies

### Reading Cookie

```csharp
public IActionResult Read()
{
    string key = "DemoCookie";
    var cookieValue = Request.Cookies[key];
    
    if (cookieValue != null)
    {
        ViewBag.CookieValue = cookieValue;
    }
    
    return View();
}
```

### Deleting Cookie (Set Past Expiration)

```csharp
public IActionResult Remove()
{
    string key = "DemoCookie";
    string value = "";

    CookieOptions options = new CookieOptions();
    options.Expires = DateTime.Now.AddDays(-1);  // Past date!
    
    Response.Cookies.Append(key, value, options);

    return View();
}
```

### Deleting Cookie (Using Delete Method)

```csharp
public IActionResult Remove()
{
    CookieOptions options = new CookieOptions
    {
        Domain = "localhost",
        Path = "/"
    };
    
    Response.Cookies.Delete("DemoCookie", options);
    
    return View();
}
```

---

## 5. Remember Me Implementation

### Model

```csharp
public class UserLogin
{
    public string Email { get; set; }
    public string Password { get; set; }
    public bool Remember { get; set; }
}
```

### Controller

```csharp
public class UserLoginController : Controller
{
    public ActionResult Create()
    {
        UserLogin obj = null;
        var cookieValue = Request.Cookies["Ukey"]?.ToString();
        
        if (cookieValue != null)
        {
            obj = new UserLogin();
            obj.Email = cookieValue;  // Pre-fill email from cookie
        }
        
        return View(obj);
    }

    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create(UserLogin userData)
    {
        try
        {
            if (ModelState.IsValid)
            {
                // Validate user credentials here...
                
                if (userData.Remember == true)
                {
                    CookieOptions options = new CookieOptions();
                    options.Expires = DateTime.Now.AddDays(7);
                    Response.Cookies.Append("Ukey", userData.Email, options);
                }
            }
            return RedirectToAction(nameof(Index));
        }
        catch
        {
            return View();
        }
    }

    public ActionResult Index()
    {
        var cookieValue = Request.Cookies["Ukey"]?.ToString();
        ViewBag.CookieValue = cookieValue;
        return View();
    }
}
```

### View (Create.cshtml)

```cshtml
@model UserLogin

<form asp-action="Create">
    <div class="form-group">
        <label asp-for="Email"></label>
        <input asp-for="Email" class="form-control" />
    </div>
    
    <div class="form-group">
        <label asp-for="Password"></label>
        <input asp-for="Password" class="form-control" />
    </div>
    
    <div class="form-group form-check">
        <input class="form-check-input" asp-for="Remember" />
        <label class="form-check-label" asp-for="Remember">Remember Me</label>
    </div>
    
    <button type="submit" class="btn btn-primary">Login</button>
</form>
```

---

## 6. Accessing Cookies in Views

### Inject IHttpContextAccessor

```cshtml
@using Microsoft.AspNetCore.Http
@inject IHttpContextAccessor HttpContextAccessor

@{
    ViewData["Title"] = "Home Page";
}

<div class="text-left">
    <b>User Name:</b> @HttpContextAccessor?.HttpContext?.Request.Cookies["UserName"]
    <br />
    <b>User Id:</b> @HttpContextAccessor?.HttpContext?.Request.Cookies["UserId"]
</div>
```

### Register IHttpContextAccessor in Program.cs

```csharp
builder.Services.AddSingleton<IHttpContextAccessor, HttpContextAccessor>();
```

---

## 7. Best Practices

### DO ✅
| Practice | Reason |
|----------|--------|
| Use HttpOnly for sensitive cookies | Prevent XSS stealing cookies |
| Use Secure in production | Prevent transmission over HTTP |
| Set appropriate expiration | Don't keep cookies forever |
| Use SameSite attribute | CSRF protection |

### DON'T ❌
| Practice | Reason |
|----------|--------|
| Don't store sensitive data in cookies | Can be read/modified by user |
| Don't exceed 4KB | Size limit |
| Don't assume cookies exist | User may clear them |
| Don't rely solely on client validation | Security |

---

## 8. Quick Reference

### Cookie Operations Summary

| Operation | Code |
|-----------|------|
| **Create** | `Response.Cookies.Append(key, value, options)` |
| **Read** | `Request.Cookies[key]` |
| **Delete** | `Response.Cookies.Delete(key)` or set past expiration |

### Cookie Use Cases

| Use Case | Description |
|----------|-------------|
| **Remember Me** | Store user identifier for auto-login |
| **Preferences** | User theme, language settings |
| **Shopping Cart** | Cart items (small data) |
| **Analytics** | Tracking identifiers |

---

## 9. Interview Questions

1. **What are cookies and where are they stored?**
   - Small data pieces created by server, stored on client's browser.

2. **Difference between persistent and non-persistent cookies?**
   - Persistent: Have expiration date, survive browser close. Non-persistent: No expiration, deleted on browser close.

3. **How do you secure cookies?**
   - Use HttpOnly (no JavaScript access), Secure (HTTPS only), SameSite (CSRF protection).

4. **Can cookies be shared across browsers?**
   - No, cookies are browser-specific and user-specific.

5. **What is the HttpOnly flag?**
   - Prevents JavaScript from accessing the cookie, protecting against XSS attacks.
