# Cookies Fundamentals - State Management Technique

## Table of Contents
1. [Introduction](#1-introduction)
2. [What are Cookies?](#2-what-are-cookies)
3. [Cookie Anatomy and Types](#3-cookie-anatomy-and-types)
4. [Complete Code Example](#4-complete-code-example)
5. [Execution Flow](#5-execution-flow)
6. [Cookie Methods in Java](#6-cookie-methods-in-java)
7. [Advantages and Disadvantages](#7-advantages-and-disadvantages)
8. [Key Points to Remember](#8-key-points-to-remember)
9. [Interview Questions](#9-interview-questions)

---

## 1. Introduction

Cookies are small pieces of text data stored on the client's browser. They are one of the most commonly used mechanisms for maintaining state in web applications. When you see "Remember Me" on login pages or find your shopping cart items still there after closing the browser, cookies are often working behind the scenes.

---

## 2. What are Cookies?

### Definition

A cookie is a **small text file** that a web server sends to a user's browser. The browser stores the cookie and sends it back to the server with **every subsequent request** to that server.

### Visual Representation

```
STEP 1: First Request (No cookies yet)
┌──────────┐                        ┌──────────┐
│  Browser │ ──── Request ────────► │  Server  │
│          │ (no cookies)           │          │
│          │                        │          │
│          │ ◄── Response + Cookie ─│          │
│          │     (Set-Cookie: user=John)       │
└──────────┘                        └──────────┘
     │
     │ Browser stores cookie
     ▼
STEP 2: Subsequent Requests (Cookie sent automatically)
┌──────────┐                        ┌──────────┐
│  Browser │ ──── Request + Cookie ►│  Server  │
│  [user=  │     (Cookie: user=John)│          │
│   John]  │                        │          │
│          │ ◄── Response ──────────│  (Server │
│          │                        │ knows who│
└──────────┘                        │ you are) │
                                    └──────────┘
```

### Key Characteristics

| Property | Description |
|----------|-------------|
| Storage Location | Client's browser |
| Data Type | Text only (no Java objects) |
| Sent Automatically | With every request to the same domain |
| Created By | Server (using `response.addCookie()`) |
| Read From | Request (using `request.getCookies()`) |

---

## 3. Cookie Anatomy and Types

### Cookie Structure

```
Cookie Name: "username"
Cookie Value: "john_doe"
Max Age: 3600 (seconds) - Optional
Path: "/" - Optional  
Domain: "example.com" - Optional
Secure: true/false - Optional
HttpOnly: true/false - Optional
```

### Types of Cookies

#### 1. Session Cookies (Temporary)
```java
Cookie c = new Cookie("temp", "value");
// No setMaxAge() called - dies when browser closes
```

#### 2. Persistent Cookies (Permanent)
```java
Cookie c = new Cookie("perm", "value");
c.setMaxAge(86400); // Lives for 24 hours (86400 seconds)
```

### Visual: Cookie Lifecycle

```
SESSION COOKIE:
┌─────────────────────────────────────────────────────┐
│  Created ──► Lives in browser ──► Browser closes   │
│                                    Cookie DELETED   │
└─────────────────────────────────────────────────────┘

PERSISTENT COOKIE:
┌─────────────────────────────────────────────────────┐
│  Created ──► Lives in browser ──► MaxAge expires   │
│  (with MaxAge)                     Cookie DELETED   │
│                                                     │
│  OR                                                 │
│                                                     │
│  Created ──► Lives in browser ──► Manually deleted │
│                                    Cookie DELETED   │
└─────────────────────────────────────────────────────┘
```

---

## 4. Complete Code Example

### CustomCookieServ.java - Complete Implementation

```java
import jakarta.servlet.ServletException;                  // Line 1: Servlet exception handling
import jakarta.servlet.http.Cookie;                       // Line 2: Cookie class import
import jakarta.servlet.http.HttpServlet;                  // Line 3: Base HttpServlet class
import jakarta.servlet.http.HttpServletRequest;           // Line 4: Request object
import jakarta.servlet.http.HttpServletResponse;          // Line 5: Response object
import java.io.IOException;                               // Line 6: IO exception handling
import java.io.PrintWriter;                               // Line 7: For writing response

/**
 * Servlet that demonstrates cookie creation and reading
 */
public class CustomCookieServ extends HttpServlet {       // Line 11: Servlet class declaration
    
    private static final long serialVersionUID = 1L;      // Line 13: Serialization ID
    
    public void doGet(HttpServletRequest request,         // Line 15: Handle GET requests
                      HttpServletResponse response) 
                      throws ServletException, IOException {
        
        // STEP 1: Get PrintWriter to send output to browser
        PrintWriter out = response.getWriter();           // Line 20: Create output stream
        
        // STEP 2: Try to read existing cookies from the request
        Cookie[] cookielist = request.getCookies();       // Line 23: Get all cookies as array
        
        // Variables to store response information
        String user = null;                               // Line 26: Will hold username from cookie
        String responsestring = null;                     // Line 27: Message to display
        
        // STEP 3: Check if cookies exist
        if (cookielist != null) {                         // Line 30: Cookies found!
            
            // STEP 4: Loop through all cookies
            for (int x = 0; x < cookielist.length; x++) { // Line 33: Iterate each cookie
                
                // Get cookie name and value
                String name = cookielist[x].getName();    // Line 36: Get cookie's name
                String age = cookielist[x].getValue();    // Line 37: Get cookie's value
                
                // Print each cookie to browser
                out.println(name + "     " + age);        // Line 40: Display name-value pair
            }
        } 
        else {                                            // Line 43: No cookies - first visit
            
            // STEP 5: Create a new cookie for first-time visitor
            Cookie c = new Cookie("Scott", "420");        // Line 46: Create cookie (name, value)
            
            // Optional: Set cookie expiry time (commented out)
            // c.setMaxAge(120);                          // Line 49: Would make cookie live 120 seconds
            
            // STEP 6: Add cookie to response (sends to browser)
            response.addCookie(c);                        // Line 52: Cookie added to response
            
            // STEP 7: Prepare welcome message
            responsestring = new String("Welcome to our site, " +
                           "we have created a session for you ");  // Line 55-56: Welcome message
            
            // STEP 8: Write HTML response
            out.println("<html>");                        // Line 59: Start HTML
            out.println("<head><title>CookieServlet</title></head>"); // Line 60: Title
            out.println("<body>");                        // Line 61: Body start
            out.println(responsestring);                  // Line 62: Print welcome message
        }
        
        // STEP 9: Close the output stream
        out.close();                                      // Line 66: Close PrintWriter
    }
}
```

### Line-by-Line Explanation Table

| Line | Code | Purpose |
|------|------|---------|
| 2 | `import jakarta.servlet.http.Cookie` | Import Cookie class for cookie operations |
| 20 | `response.getWriter()` | Get output stream to write to browser |
| 23 | `request.getCookies()` | Retrieve all cookies sent by browser |
| 30 | `if (cookielist != null)` | Check if any cookies exist |
| 36 | `cookielist[x].getName()` | Get the name of current cookie |
| 37 | `cookielist[x].getValue()` | Get the value of current cookie |
| 46 | `new Cookie("Scott", "420")` | Create new cookie with name="Scott", value="420" |
| 52 | `response.addCookie(c)` | Add cookie to HTTP response (sent to browser) |

---

## 5. Execution Flow

### First Visit Flow (No Cookies)

```
┌────────────────────────────────────────────────────────────────────┐
│                       FIRST VISIT FLOW                             │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│  Step 1: User opens browser and visits CustomCookieServ            │
│          Browser sends: GET /CustomCookieServ                      │
│          (No cookies in request)                                   │
│                                                                    │
│  Step 2: Servlet executes                                          │
│          Cookie[] cookielist = request.getCookies();               │
│          → Returns NULL (no cookies exist)                         │
│                                                                    │
│  Step 3: Enters ELSE block                                         │
│          Cookie c = new Cookie("Scott", "420");                    │
│          response.addCookie(c);                                    │
│                                                                    │
│  Step 4: Response sent to browser                                  │
│          HTTP Response Headers:                                    │
│          Set-Cookie: Scott=420                                     │
│          Body: "Welcome to our site, we created a session..."     │
│                                                                    │
│  Step 5: Browser receives response                                 │
│          → Stores cookie: Scott=420                                │
│          → Displays: "Welcome to our site..."                      │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

### Second Visit Flow (Cookie Exists)

```
┌────────────────────────────────────────────────────────────────────┐
│                       SECOND VISIT FLOW                            │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│  Step 1: User visits CustomCookieServ again                        │
│          Browser sends: GET /CustomCookieServ                      │
│          Cookie: Scott=420  ← Browser sends cookie automatically   │
│                                                                    │
│  Step 2: Servlet executes                                          │
│          Cookie[] cookielist = request.getCookies();               │
│          → Returns array with at least 1 cookie                    │
│                                                                    │
│  Step 3: Enters IF block (cookielist != null)                      │
│          Loops through cookies:                                    │
│          - Cookie 0: name="Scott", value="420"                     │
│          Prints: "Scott     420"                                   │
│                                                                    │
│  Step 4: Response sent to browser                                  │
│          Body: "Scott     420"                                     │
│                                                                    │
│  Step 5: Browser displays: "Scott     420"                         │
│          → Server recognized the returning user!                   │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

### Visual State Diagram

```
                    ┌───────────────────┐
                    │   User visits     │
                    │   servlet         │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ Check cookies     │
                    │ request.getCookies│
                    └─────────┬─────────┘
                              │
              ┌───────────────┴───────────────┐
              │                               │
              ▼                               ▼
    ┌─────────────────┐             ┌─────────────────┐
    │ NULL            │             │ NOT NULL        │
    │ (First Visit)   │             │ (Return Visit)  │
    └────────┬────────┘             └────────┬────────┘
             │                               │
             ▼                               ▼
    ┌─────────────────┐             ┌─────────────────┐
    │ Create cookie   │             │ Read cookie     │
    │ Add to response │             │ Display values  │
    │ Show welcome    │             │                 │
    └─────────────────┘             └─────────────────┘
```

---

## 6. Cookie Methods in Java

### Creating Cookies

```java
// Create a cookie
Cookie cookie = new Cookie("name", "value");

// Set maximum age (in seconds)
cookie.setMaxAge(3600);        // Lives for 1 hour
cookie.setMaxAge(-1);          // Session cookie (default)
cookie.setMaxAge(0);           // Delete immediately

// Set path (which URLs can access this cookie)
cookie.setPath("/");           // All pages in website

// Set domain
cookie.setDomain(".example.com");  // All subdomains

// Make secure (HTTPS only)
cookie.setSecure(true);

// Make HttpOnly (JavaScript can't access)
cookie.setHttpOnly(true);

// Add cookie to response
response.addCookie(cookie);
```

### Reading Cookies

```java
// Get all cookies from request
Cookie[] cookies = request.getCookies();

// Check if cookies exist
if (cookies != null) {
    for (Cookie c : cookies) {
        String name = c.getName();     // Get cookie name
        String value = c.getValue();   // Get cookie value
        int maxAge = c.getMaxAge();    // Get max age
    }
}
```

### Deleting Cookies

```java
// To delete a cookie, set maxAge to 0
Cookie cookie = new Cookie("name", "");
cookie.setMaxAge(0);           // This deletes the cookie
cookie.setPath("/");           // Must match original path
response.addCookie(cookie);    // Send deletion to browser
```

### Methods Quick Reference

| Method | Purpose | Example |
|--------|---------|---------|
| `new Cookie(name, value)` | Create cookie | `new Cookie("user", "john")` |
| `setMaxAge(seconds)` | Set expiry time | `setMaxAge(3600)` = 1 hour |
| `getMaxAge()` | Get expiry time | Returns int (seconds) |
| `setPath(path)` | Set URL path | `setPath("/admin")` |
| `getPath()` | Get URL path | Returns String |
| `setSecure(boolean)` | HTTPS only | `setSecure(true)` |
| `setHttpOnly(boolean)` | No JavaScript access | `setHttpOnly(true)` |
| `setValue(value)` | Change value | `setValue("newValue")` |
| `response.addCookie(c)` | Send to browser | Required step! |

---

## 7. Advantages and Disadvantages

### Advantages ✓

| Advantage | Explanation |
|-----------|-------------|
| Persistent | Can survive browser closing (with maxAge) |
| Automatic | Browser sends with every request |
| No Server Memory | Data stored on client |
| Cross-Session | Data available across multiple visits |
| Simple API | Easy to create, read, and delete |

### Disadvantages ✗

| Disadvantage | Explanation |
|--------------|-------------|
| Can Be Disabled | Users can block cookies in browser settings |
| Text Only | Cannot store Java objects, only strings |
| Size Limited | Max ~4KB per cookie |
| Security Risk | Can be stolen (cookie hijacking) |
| Visible | Users can see/modify cookies |
| Client Rejection | Cookies may be rejected if full |

### Security Concerns

```
┌────────────────────────────────────────────────────────────────┐
│                    COOKIE SECURITY RISKS                       │
├────────────────────────────────────────────────────────────────┤
│                                                                │
│  1. COOKIE HIJACKING                                           │
│     Attacker steals JSESSIONID cookie → Impersonates user      │
│                                                                │
│  2. CROSS-SITE SCRIPTING (XSS)                                 │
│     Malicious script reads cookies via document.cookie         │
│     Prevention: Use HttpOnly flag                              │
│                                                                │
│  3. MAN-IN-THE-MIDDLE                                          │
│     Attacker intercepts cookie over HTTP                       │
│     Prevention: Use Secure flag + HTTPS                        │
│                                                                │
│  4. USER MODIFICATION                                          │
│     User changes cookie value directly                         │
│     Prevention: Validate/encrypt cookie values                 │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

---

## 8. Key Points to Remember

1. **Cookies are TEXT ONLY** - Cannot store Java objects directly

2. **Added to Response** - Cookies are ALWAYS added using `response.addCookie()`

3. **Retrieved from Request** - Cookies are ALWAYS read using `request.getCookies()`

4. **Automatic Transmission** - Browser sends cookies automatically with every request

5. **Domain Bound** - Cookies only sent to the domain that created them

6. **Session vs Persistent** - Without setMaxAge(), cookie dies when browser closes

7. **Array Return** - `getCookies()` returns an array, may be NULL

8. **Size Limit** - ~4KB per cookie, ~20 cookies per domain

9. **HttpOnly** - Prevents JavaScript from reading the cookie

10. **Secure Flag** - Cookie only sent over HTTPS

---

## 9. Interview Questions

### Basic Level

**Q1: What is a cookie?**
> A cookie is a small piece of text data sent by a server to a client's browser. The browser stores it and sends it back to the server with every subsequent request to that domain.

**Q2: How do you create a cookie in Java?**
```java
Cookie c = new Cookie("name", "value");
response.addCookie(c);
```

**Q3: How do you read cookies in a servlet?**
```java
Cookie[] cookies = request.getCookies();
if (cookies != null) {
    for (Cookie c : cookies) {
        String name = c.getName();
        String value = c.getValue();
    }
}
```

### Intermediate Level

**Q4: What is the difference between session cookies and persistent cookies?**
| Session Cookie | Persistent Cookie |
|---------------|-------------------|
| No maxAge set | maxAge > 0 |
| Deleted when browser closes | Lives until expiry |
| Stored in memory | Stored on disk |

**Q5: What happens if getCookies() returns null?**
> It means no cookies were sent with the request. This happens when:
> - It's the user's first visit
> - User deleted cookies
> - User has cookies disabled
> - Cookies expired

**Q6: How do you delete a cookie?**
```java
Cookie c = new Cookie("name", "");
c.setMaxAge(0);       // Setting to 0 deletes the cookie
c.setPath("/");       // Must match original path
response.addCookie(c);
```

### Advanced Level

**Q7: What is the HttpOnly flag and why is it important?**
> The HttpOnly flag prevents JavaScript from accessing the cookie via `document.cookie`. This protects against XSS (Cross-Site Scripting) attacks where malicious scripts try to steal session cookies.

**Q8: Can cookies work if the client disables them?**
> No, cookies require client browser support. If disabled:
> - Custom cookies won't work
> - HttpSession (which uses JSESSIONID cookie) will break
> - Solution: Use URL Rewriting as fallback

**Q9: What is the relationship between cookies and HttpSession?**
> HttpSession uses cookies internally:
> 1. When session is created, server generates unique session ID
> 2. Session ID stored in cookie named "JSESSIONID"
> 3. Browser sends this cookie with every request
> 4. Server matches ID to retrieve session object
> 
> If cookies are disabled, session breaks unless URL rewriting is used.

**Q10: What security measures should you take when using cookies?**
> 1. Use **HttpOnly** to prevent XSS attacks
> 2. Use **Secure** flag for cookies over HTTPS
> 3. Never store sensitive data (passwords) in cookies
> 4. Encrypt cookie values for sensitive information
> 5. Set appropriate **Max-Age** to minimize exposure
> 6. Validate cookie values on server side

---

## Next Topic: [HttpSession Introduction →](./04_HttpSession_Introduction.md)
