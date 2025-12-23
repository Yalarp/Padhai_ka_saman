# URL Rewriting - Fallback State Management

## Table of Contents
1. [Introduction](#1-introduction)
2. [What is URL Rewriting?](#2-what-is-url-rewriting)
3. [When to Use URL Rewriting](#3-when-to-use-url-rewriting)
4. [Complete Code Example](#4-complete-code-example)
5. [Execution Flow](#5-execution-flow)
6. [Pros and Cons](#6-pros-and-cons)
7. [Key Points to Remember](#7-key-points-to-remember)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

URL Rewriting is a fallback technique for session management when cookies are disabled by the client. It works by appending the session ID directly to the URL, ensuring session tracking works even without cookies.

---

## 2. What is URL Rewriting?

### Definition

URL Rewriting is a technique where the **session ID is appended at the end of every URL** instead of being stored in a cookie. This ensures the server can identify the user even when cookies are blocked.

### Visual Representation

```
WITH COOKIES (Normal):
┌──────────────────────────────────────────────────────────────────┐
│ URL: http://localhost:8080/MyApp/SecondServlet                   │
│ Cookie: JSESSIONID=ABC123XYZ                                     │
└──────────────────────────────────────────────────────────────────┘

WITHOUT COOKIES (URL Rewriting):
┌──────────────────────────────────────────────────────────────────┐
│ URL: http://localhost:8080/MyApp/Second;jsessionid=ABC123XYZ     │
│ No Cookie needed!                                                 │
└──────────────────────────────────────────────────────────────────┘
```

### The encodeURL() Method

```java
String encodedURL = response.encodeURL("SecondServlet");
```

This method:
- Returns the **original URL** if cookies are enabled
- Returns the **URL + session ID** if cookies are disabled

### How encodeURL() Works

```
┌────────────────────────────────────────────────────────────────────┐
│                    encodeURL() Decision Logic                      │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│  response.encodeURL("SecondServlet")                               │
│                      │                                             │
│                      ▼                                             │
│         ┌────────────────────────────┐                            │
│         │ Are cookies enabled?       │                            │
│         └────────────┬───────────────┘                            │
│                      │                                             │
│         ┌────────────┴────────────┐                               │
│         │                         │                               │
│         ▼                         ▼                               │
│   ┌──────────────┐        ┌──────────────────────────────┐       │
│   │ YES          │        │ NO                           │       │
│   │              │        │                              │       │
│   │ Returns:     │        │ Returns:                     │       │
│   │"SecondServlet│        │"SecondServlet;jsessionid=    │       │
│   │"             │        │ ABC123XYZ"                   │       │
│   └──────────────┘        └──────────────────────────────┘       │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

---

## 3. When to Use URL Rewriting

### Primary Use Case

URL Rewriting is used when:
1. Your application needs to support users who have **disabled cookies**
2. You want **maximum compatibility** across different browsers
3. You're building a **legacy system** that must work everywhere

### When Cookies Might Be Disabled

| Scenario | Reason |
|----------|--------|
| Privacy-conscious users | Disable cookies for privacy |
| Corporate firewalls | Block cookies for security |
| Old browsers | Poor cookie support |
| Public computers | Users don't trust cookies |
| Mobile apps with WebView | Cookie handling issues |

---

## 4. Complete Code Example

### First.java - Create Session with URL Rewriting

```java
import java.io.*;                                    // Line 1: IO imports
import javax.servlet.http.*;                         // Line 2: HTTP servlet imports
import javax.servlet.*;                              // Line 3: Servlet imports

public class First extends HttpServlet               // Line 5: Servlet class
{
    public void doGet(HttpServletRequest req,        // Line 7: Handle GET request
                      HttpServletResponse res) 
                      throws ServletException, IOException
    {
        // STEP 1: Create or get session
        HttpSession s = req.getSession();            // Line 12: Get/create session
        
        // STEP 2: Get output writer
        PrintWriter out = res.getWriter();           // Line 15: Get output stream
        
        // STEP 3: Store data in session
        s.setAttribute("gender", "male");            // Line 18: Store attribute
        
        // STEP 4: Generate HTML with encoded URL
        out.println("<html><body>");                 // Line 21: Start HTML
        
        // STEP 5: Create link using encodeURL()
        // This is the KEY step for URL Rewriting!
        out.println("<a href=\"" +                   // Line 25: Start anchor tag
                    res.encodeURL("Second") +        // Line 26: Encode URL with session ID
                    "\">click me for second</a>");   // Line 27: End anchor tag
        
        out.println("</body></html>");               // Line 29: End HTML
    }
}
```

### Line-by-Line Explanation

| Line | Code | Purpose |
|------|------|---------|
| 12 | `req.getSession()` | Creates new session or returns existing one |
| 18 | `s.setAttribute("gender", "male")` | Stores "gender=male" in session |
| 26 | `res.encodeURL("Second")` | **KEY LINE**: Encodes URL with session ID if cookies disabled |

### What encodeURL() Returns:

```
If cookies ENABLED:
res.encodeURL("Second") → "Second"
Final HTML: <a href="Second">click me for second</a>

If cookies DISABLED:
res.encodeURL("Second") → "Second;jsessionid=A1B2C3D4E5"
Final HTML: <a href="Second;jsessionid=A1B2C3D4E5">click me for second</a>
```

### Second.java - Retrieve Session from URL

```java
import java.io.*;                                    // Line 1: IO imports
import javax.servlet.http.*;                         // Line 2: HTTP servlet imports
import javax.servlet.*;                              // Line 3: Servlet imports

public class Second extends HttpServlet              // Line 5: Servlet class
{
    public void doGet(HttpServletRequest req,        // Line 7: Handle GET request
                      HttpServletResponse res) 
                      throws ServletException, IOException
    {
        // STEP 1: Get output writer
        PrintWriter pw = res.getWriter();            // Line 12: Get output stream
        
        // STEP 2: Print initial message
        pw.println("i am second");                   // Line 15: Confirm servlet reached
        
        // STEP 3: Try to get existing session
        // getSession(false) = DO NOT create new session
        HttpSession session = req.getSession(false); // Line 19: Get existing session only
        
        // STEP 4: Check if session exists
        if (session != null)                         // Line 22: Session found?
        {
            // STEP 5: Retrieve and print attribute
            pw.println(session.getAttribute("gender")); // Line 25: Print "male"
        }
        else                                         // Line 27: No session found
        {
            pw.println("session does not exists");   // Line 29: Print error message
        }
    }
}
```

### Line-by-Line Explanation

| Line | Code | Purpose |
|------|------|---------|
| 19 | `req.getSession(false)` | Gets session WITHOUT creating new one. Returns null if no session. |
| 22 | `if (session != null)` | Check if session was successfully retrieved |
| 25 | `session.getAttribute("gender")` | Gets value stored in First servlet |
| 29 | `"session does not exists"` | Error message if session not found |

---

## 5. Execution Flow

### Complete Flow Diagram

```
┌────────────────────────────────────────────────────────────────────┐
│                    URL REWRITING COMPLETE FLOW                     │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│  STEP 1: User visits First servlet                                 │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ Browser → GET /First HTTP/1.1                                │  │
│  │                                                              │  │
│  │ Server executes First.java:                                  │  │
│  │   - Creates session (ID = ABC123)                            │  │
│  │   - Stores gender = "male"                                   │  │
│  │   - res.encodeURL("Second") called                           │  │
│  │                                                              │  │
│  │ Response HTML (if cookies disabled):                         │  │
│  │   <a href="Second;jsessionid=ABC123">click me</a>            │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  STEP 2: User clicks "click me for second"                         │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ Browser → GET /Second;jsessionid=ABC123 HTTP/1.1             │  │
│  │                                ▲                             │  │
│  │                                │                             │  │
│  │                   Session ID in URL!                         │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  STEP 3: Server processes Second servlet                           │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ Container extracts session ID from URL path                  │  │
│  │   jsessionid = "ABC123"                                      │  │
│  │                                                              │  │
│  │ Container finds session with ID "ABC123" on server           │  │
│  │                                                              │  │
│  │ Second.java executes:                                        │  │
│  │   - session = req.getSession(false) → Returns session ABC123 │  │
│  │   - session.getAttribute("gender") → Returns "male"          │  │
│  │                                                              │  │
│  │ Output: "i am second" + "male"                               │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

### Session ID Extraction

```
URL: /Second;jsessionid=ABC123

Container parses:
┌────────────────────────────────────────────┐
│ Servlet Path: /Second                      │
│ Session ID:   ABC123                       │
│ Query String: (none)                       │
└────────────────────────────────────────────┘

Container then:
1. Looks up session "ABC123" in session map
2. Associates request with that session
3. getSession(false) returns the session
```

---

## 6. Pros and Cons

### Advantages ✓

| Advantage | Explanation |
|-----------|-------------|
| Works without cookies | Main purpose - fallback for disabled cookies |
| No client configuration | User doesn't need to enable anything |
| Browser independent | Works on all browsers equally |
| Simple implementation | Just use encodeURL() for all links |

### Disadvantages ✗

| Disadvantage | Explanation |
|--------------|-------------|
| Ugly URLs | URLs become long and complex |
| Security risk | Session ID visible in URL |
| Bookmarking issues | Bookmarked URLs contain session ID |
| Sharing URLs | Shared links can hijack sessions |
| All links must be encoded | Miss one link = session lost |
| Server logs exposure | Session IDs logged in access logs |
| Referrer header leakage | Session ID sent to external sites |

### Security Concerns

```
┌────────────────────────────────────────────────────────────────────┐
│                    URL REWRITING SECURITY RISKS                    │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│  1. URL VISIBLE IN BROWSER BAR                                     │
│     - Someone looking over shoulder can see session ID             │
│     - Users might share URLs not realizing the risk                │
│                                                                    │
│  2. BROWSER HISTORY EXPOSURE                                       │
│     - Session IDs stored in browser history                        │
│     - Anyone using the same computer can find them                 │
│                                                                    │
│  3. REFERRER HEADER LEAKAGE                                        │
│     - When clicking external link, referrer contains session ID    │
│     - External site receives your session ID                       │
│                                                                    │
│  4. SERVER ACCESS LOGS                                             │
│     - URLs are logged in web server access logs                    │
│     - Session IDs visible in log files                             │
│                                                                    │
│  5. BOOKMARKING                                                    │
│     - User bookmarks page with session ID                          │
│     - Session might expire, causing confusion                      │
│     - Or worse, someone else uses the bookmark                     │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

---

## 7. Key Points to Remember

1. **Fallback mechanism**: URL Rewriting is primarily a fallback when cookies are disabled

2. **encodeURL() is essential**: Always use `response.encodeURL()` for all URLs in your application

3. **Automatic detection**: encodeURL() automatically detects if cookies are enabled or disabled

4. **All links must be encoded**: If you miss encoding one link, the session is lost on that navigation

5. **Forms need encodeURL too**: For form actions: `<form action="${pageContext.response.encodeURL('process')}">`

6. **Session ID format**: `url;jsessionid=ABC123` (semicolon separator, not query parameter)

7. **Security risk**: Always use HTTPS when using URL Rewriting to encrypt the session ID

8. **Prefer cookies**: Use URL Rewriting only as fallback, not as primary method

9. **encodeRedirectURL()**: For redirects, use `response.encodeRedirectURL()` instead

10. **Dynamic HTML required**: You must generate HTML dynamically to use encodeURL()

---

## 8. Interview Questions

### Basic Level

**Q1: What is URL Rewriting?**
> URL Rewriting is a session tracking technique where the session ID is appended to the URL instead of using cookies. It's used when cookies are disabled.

**Q2: What method is used for URL Rewriting?**
> `response.encodeURL()` method encodes a URL with the session ID if cookies are disabled.

**Q3: What is the format of a rewritten URL?**
> The session ID is appended with a semicolon: `http://example.com/page;jsessionid=ABC123XYZ`

### Intermediate Level

**Q4: When does encodeURL() add the session ID to the URL?**
> encodeURL() adds the session ID only when:
> 1. The user has cookies disabled in their browser
> 2. A session exists for the current user
> 3. The client hasn't accepted cookies yet (first request)

**Q5: What are the security risks of URL Rewriting?**
> 1. Session ID visible in browser address bar
> 2. Session ID stored in browser history
> 3. Session ID leaked via Referrer header to external sites
> 4. Session ID logged in server access logs
> 5. Bookmarked URLs may contain session IDs

**Q6: What is the difference between encodeURL() and encodeRedirectURL()?**
| encodeURL() | encodeRedirectURL() |
|-------------|---------------------|
| Used for links in HTML | Used for redirect URLs |
| For `<a href="">` tags | For `response.sendRedirect()` |
| Returns encoded URL for links | Returns encoded URL for redirects |

### Advanced Level

**Q7: How can you completely disable URL Rewriting for security?**
```xml
<!-- In web.xml -->
<session-config>
    <tracking-mode>COOKIE</tracking-mode>
</session-config>

<!-- This prevents URL-based session tracking -->
```

**Q8: Explain the first request paradox with URL Rewriting.**
> On the very first request, the server doesn't know if the client accepts cookies. So encodeURL() will add the session ID to URLs. On the second request:
> - If client sent JSESSIONID cookie → server knows cookies work → stops URL rewriting
> - If no cookie received → continues URL rewriting
> 
> This means URLs might contain session IDs even for users with cookies enabled, on their first page.

**Q9: How would you implement URL Rewriting with forms?**
```html
<!-- Wrong - session lost on form submit -->
<form action="ProcessServlet" method="post">

<!-- Correct - session preserved -->
<form action="<%=response.encodeURL("ProcessServlet")%>" method="post">

<!-- With JSTL -->
<form action="${pageContext.response.encodeURL('ProcessServlet')}" method="post">
```

**Q10: What happens if you mix encoded and non-encoded URLs?**
> If you have one non-encoded link (`<a href="page2">`), clicking it will lose the session:
> 1. User on page1.jsp (session ID in URL)
> 2. Clicks non-encoded link to page2.jsp
> 3. Request to page2.jsp has no session ID (not in URL, no cookie)
> 4. Server creates NEW session
> 5. All data from original session is lost!
> 
> **Always encode ALL URLs** when using URL Rewriting.

---

## Next Topic: [Session Management Advanced →](./07_Session_Management_Advanced.md)
