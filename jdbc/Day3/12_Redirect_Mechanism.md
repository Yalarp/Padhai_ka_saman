# Redirect Mechanism - sendRedirect()

## Table of Contents
1. [Introduction](#1-introduction)
2. [How Redirect Works](#2-how-redirect-works)
3. [Syntax and Usage](#3-syntax-and-usage)
4. [Use Cases](#4-use-cases)
5. [Complete Code Examples](#5-complete-code-examples)
6. [Key Points to Remember](#6-key-points-to-remember)
7. [Interview Questions](#7-interview-questions)

---

## 1. Introduction

Redirect is a mechanism where the server tells the browser to make a **new request** to a different URL. The browser's address bar changes to show the new URL.

---

## 2. How Redirect Works

### Step-by-Step Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         REDIRECT MECHANISM                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Step 1: Client sends initial request                                       │
│  ┌──────────┐                              ┌──────────────┐                │
│  │  Browser │ ───── GET /servlet1 ──────►  │   Servlet1   │                │
│  └──────────┘                              └──────────────┘                │
│                                                    │                        │
│                                                    │ response.sendRedirect()│
│                                                    ▼                        │
│  Step 2: Server sends redirect response (302)                              │
│  ┌──────────┐                              ┌──────────────┐                │
│  │  Browser │ ◄─── HTTP 302               │   Servlet1   │                │
│  │          │      Location: /servlet2 ── │              │                │
│  └──────────┘                              └──────────────┘                │
│       │                                                                     │
│       │ Browser reads Location header                                       │
│       │ Makes NEW request automatically                                     │
│       ▼                                                                     │
│  Step 3: Client sends NEW request to new URL                               │
│  ┌──────────┐                              ┌──────────────┐                │
│  │  Browser │ ───── GET /servlet2 ──────►  │   Servlet2   │                │
│  │ (URL bar │                              │              │                │
│  │ changes!)│ ◄──── Response ────────────  │              │                │
│  └──────────┘                              └──────────────┘                │
│                                                                             │
│  KEY POINTS:                                                                │
│  • Two round trips (two request-response cycles)                           │
│  • URL in browser changes                                                  │
│  • Request attributes are LOST (new request object)                        │
│  • Can redirect to external URLs                                           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Syntax and Usage

### Basic Syntax

```java
response.sendRedirect(String location);
```

### Different URL Formats

```java
// Relative URL (same web application)
response.sendRedirect("success.jsp");

// Context-relative (recommended)
response.sendRedirect(request.getContextPath() + "/success.jsp");

// Absolute URL (external)
response.sendRedirect("https://www.google.com");

// With query parameters
response.sendRedirect("result.jsp?status=success&id=" + userId);
```

### Important Rules

```java
// WRONG: Writing after redirect
response.sendRedirect("page.jsp");
out.println("Hello");  // IllegalStateException or ignored!

// CORRECT: Return immediately after redirect
response.sendRedirect("page.jsp");
return;  // Stop further processing

// WRONG: Redirect after response committed
out.println("<html>");
out.flush();  // Response committed!
response.sendRedirect("page.jsp");  // IllegalStateException!
```

---

## 4. Use Cases

### When to Use Redirect

| Scenario | Reason |
|----------|--------|
| After form submission | Prevent duplicate submission on refresh (PRG pattern) |
| After login/logout | Clean URL in browser |
| External URL | Forward can't reach external sites |
| Different web app | Forward is limited to same app |
| Change browser URL | User should see new location |

### Post-Redirect-Get (PRG) Pattern

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         PRG PATTERN                                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  WITHOUT PRG (Problem):                                                     │
│  POST /submit ──► Process ──► Show result                                  │
│                                    │                                        │
│                   User hits refresh ──► Form resubmitted! (Duplicate!)     │
│                                                                             │
│  WITH PRG (Solution):                                                       │
│  POST /submit ──► Process ──► Redirect (302) ──► GET /result               │
│                                                       │                     │
│                                   User hits refresh ──► Just reloads page  │
│                                                       (No duplicate!)       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Complete Code Examples

### Basic Redirect Example

```java
@WebServlet("/login")
public class LoginServlet extends HttpServlet {
    
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        
        if (authenticate(username, password)) {
            // Create session
            HttpSession session = request.getSession();
            session.setAttribute("user", username);
            
            // Redirect to dashboard (PRG pattern)
            response.sendRedirect(request.getContextPath() + "/dashboard");
            return;
        } else {
            // Redirect back to login with error parameter
            response.sendRedirect("login.jsp?error=invalid");
            return;
        }
    }
    
    private boolean authenticate(String user, String pass) {
        // Authentication logic
        return "admin".equals(user) && "password".equals(pass);
    }
}
```

### Redirect with Parameters

```java
@WebServlet("/process")
public class ProcessServlet extends HttpServlet {
    
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // Process form data
        String name = request.getParameter("name");
        int orderId = createOrder(name);
        
        // Redirect with parameters (only way to pass data!)
        String url = String.format("confirmation.jsp?orderId=%d&name=%s",
                                   orderId, 
                                   URLEncoder.encode(name, "UTF-8"));
        
        response.sendRedirect(url);
    }
}
```

### Redirect to External URL

```java
@WebServlet("/external")
public class ExternalRedirectServlet extends HttpServlet {
    
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        String destination = request.getParameter("site");
        
        switch(destination) {
            case "google":
                response.sendRedirect("https://www.google.com");
                break;
            case "github":
                response.sendRedirect("https://www.github.com");
                break;
            default:
                response.sendRedirect(request.getContextPath() + "/home");
        }
    }
}
```

---

## 6. Key Points to Remember

1. **Two round trips** - Request goes to server, redirect response, new request

2. **New request object** - Request attributes are LOST

3. **URL changes** - Browser shows new URL in address bar

4. **Can redirect externally** - Works for any URL

5. **Must call before commit** - Before response.getWriter().flush()

6. **Return after redirect** - Stop further processing

7. **Use for PRG** - Prevent form resubmission

8. **Session survives** - Session attributes are preserved

9. **HTTP 302 by default** - Temporary redirect

10. **Encode special characters** - Use URLEncoder for parameters

---

## 7. Interview Questions

### Basic Level

**Q1: What is redirect in servlets?**
> Redirect tells the browser to make a new request to a different URL. The browser's address bar changes to show the new URL.

**Q2: What HTTP status code is sent for redirect?**
> HTTP 302 (Found/Temporary Redirect) by default. The Location header contains the new URL.

**Q3: What happens to request attributes on redirect?**
> They are LOST because a new request object is created for the new request.

### Intermediate Level

**Q4: What is the PRG pattern?**
> Post-Redirect-Get pattern: After processing a POST request, redirect to a GET page. This prevents form resubmission when user refreshes the browser.

**Q5: Can you redirect to external websites?**
> Yes! Unlike forward, redirect can go to any URL including external websites.
```java
response.sendRedirect("https://www.google.com");
```

**Q6: How do you pass data after redirect?**
> Since request attributes are lost, you can:
> 1. Use URL parameters: `redirect("page.jsp?msg=success")`
> 2. Use session attributes (remember to remove after reading)
> 3. Use cookies

### Advanced Level

**Q7: What happens if you write after sendRedirect?**
```java
response.sendRedirect("page.jsp");
out.println("Hello");  // What happens?
```
> If response is not committed, the output is ignored. If committed, you get IllegalStateException.

**Q8: How do you do a permanent redirect (301)?**
```java
response.setStatus(HttpServletResponse.SC_MOVED_PERMANENTLY);  // 301
response.setHeader("Location", "https://new-url.com");
```

---

## Next Topic: [Forward Mechanism →](./13_Forward_Mechanism.md)
