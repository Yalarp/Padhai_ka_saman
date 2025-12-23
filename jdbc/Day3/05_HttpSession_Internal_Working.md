# HttpSession Internal Working

## Table of Contents
1. [Introduction](#1-introduction)
2. [Session Creation Internals](#2-session-creation-internals)
3. [Session Retrieval Internals](#3-session-retrieval-internals)
4. [JSESSIONID Cookie](#4-jsessionid-cookie)
5. [Session Invalidation](#5-session-invalidation)
6. [Session Timeout Configuration](#6-session-timeout-configuration)
7. [SessionDemo Example](#7-sessiondemo-example)
8. [Key Points to Remember](#8-key-points-to-remember)
9. [Interview Questions](#9-interview-questions)

---

## 1. Introduction

Understanding how HttpSession works internally is crucial for debugging session-related issues and for interviews. This note explains the complete internal mechanism of session creation, storage, retrieval, and destruction.

---

## 2. Session Creation Internals

### What Happens When Session is Created?

When you call `request.getSession()` and no session exists, the container performs these steps:

```
┌────────────────────────────────────────────────────────────────────┐
│            INTERNAL STEPS WHEN SESSION IS CREATED                  │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│  Step 1: CREATE SESSION OBJECT                                     │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ Container creates implementation of HttpSession interface    │  │
│  │ (e.g., StandardSession in Tomcat)                           │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  Step 2: GENERATE UNIQUE SESSION ID                                │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ Container generates unique ID (e.g., "A1B2C3D4E5F6...")     │  │
│  │ Algorithm: Usually SecureRandom + Timestamp combination      │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  Step 3: STORE ATTRIBUTES                                          │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ session.setAttribute("book", "Complete_Reference")           │  │
│  │ Data stored in session object's internal Map                 │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  Step 4: CREATE JSESSIONID COOKIE                                  │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ Cookie cookie = new Cookie("JSESSIONID", sessionId);         │  │
│  │ cookie.setPath("/");                                         │  │
│  │ cookie.setHttpOnly(true);                                    │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  Step 5: ADD COOKIE TO RESPONSE                                    │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ response.addCookie(cookie);                                  │  │
│  │ HTTP Response Header: Set-Cookie: JSESSIONID=A1B2C3D4E5F6   │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  Step 6: STORE SESSION ON SERVER                                   │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ Server stores session in SessionManager (typically HashMap) │  │
│  │ Key = Session ID, Value = Session Object                    │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

### Code Flow

```java
// Developer writes:
HttpSession session = request.getSession();
session.setAttribute("book", "Complete_Reference");

// Container internally does (simplified):
// 1. String sessionId = generateUniqueId();  // "ABC123XYZ..."
// 2. HttpSession session = new StandardSession(sessionId);
// 3. sessionMap.put(sessionId, session);  // Store on server
// 4. Cookie jsessionid = new Cookie("JSESSIONID", sessionId);
// 5. jsessionid.setPath("/");
// 6. response.addCookie(jsessionid);  // Send to browser
```

---

## 3. Session Retrieval Internals

### What Happens When Session is Retrieved?

When you call `request.getSession(false)` and session exists:

```
┌────────────────────────────────────────────────────────────────────┐
│           INTERNAL STEPS WHEN SESSION IS RETRIEVED                 │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│  Step 1: RETRIEVE COOKIE FROM REQUEST                              │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ Browser sends: Cookie: JSESSIONID=ABC123XYZ                  │  │
│  │ Container reads cookies from request headers                 │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  Step 2: EXTRACT SESSION ID FROM COOKIE                            │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ for (Cookie c : request.getCookies()) {                      │  │
│  │     if (c.getName().equals("JSESSIONID")) {                  │  │
│  │         sessionId = c.getValue();  // "ABC123XYZ"            │  │
│  │     }                                                        │  │
│  │ }                                                            │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  Step 3: MATCH SESSION ID WITH SERVER SESSIONS                     │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ HttpSession session = sessionMap.get(sessionId);             │  │
│  │                                                              │  │
│  │ Server's Session Map:                                        │  │
│  │ ┌──────────────┬─────────────────────────┐                  │  │
│  │ │ Session ID   │ Session Object          │                  │  │
│  │ ├──────────────┼─────────────────────────┤                  │  │
│  │ │ ABC123XYZ    │ Session{book="..."} ✓   │ ← MATCH!         │  │
│  │ │ DEF456UVW    │ Session{user="..."}     │                  │  │
│  │ │ GHI789RST    │ Session{cart=[...]}     │                  │  │
│  │ └──────────────┴─────────────────────────┘                  │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  Step 4: RETURN SESSION OBJECT OR NULL                             │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ if (session found)                                           │  │
│  │     return session;  // Existing session object              │  │
│  │ else                                                         │  │
│  │     return null;     // Only for getSession(false)           │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

### Decision Tree for getSession()

```
                    ┌─────────────────────────────┐
                    │ request.getSession(false)   │
                    └─────────────┬───────────────┘
                                  │
                                  ▼
                    ┌─────────────────────────────┐
                    │ Read JSESSIONID cookie      │
                    └─────────────┬───────────────┘
                                  │
                    ┌─────────────┴───────────────┐
                    │                             │
                    ▼                             ▼
            ┌───────────────┐           ┌───────────────┐
            │ Cookie Exists │           │ No Cookie     │
            └───────┬───────┘           └───────┬───────┘
                    │                           │
                    ▼                           ▼
            ┌───────────────┐           ┌───────────────┐
            │ Find session  │           │ Return NULL   │
            │ in server map │           │               │
            └───────┬───────┘           └───────────────┘
                    │
          ┌─────────┴─────────┐
          │                   │
          ▼                   ▼
  ┌───────────────┐   ┌───────────────┐
  │ ID Matches    │   │ ID Not Found  │
  │ Return session│   │ (expired?)    │
  │               │   │ Return NULL   │
  └───────────────┘   └───────────────┘
```

---

## 4. JSESSIONID Cookie

### Cookie Details

```
Cookie Name: JSESSIONID
Cookie Value: ABC123XYZ789... (Unique session ID)
Path: / (accessible by all pages in the website)
HttpOnly: true (JavaScript cannot access it)
Secure: depends on configuration
```

### HTTP Headers Example

```
First Request (No Session):
GET /SessionServ1 HTTP/1.1
Host: localhost:8080
(No Cookie header)

First Response (Session Created):
HTTP/1.1 200 OK
Set-Cookie: JSESSIONID=ABC123XYZ789; Path=/; HttpOnly
Content-Type: text/html
...

Second Request (Session Exists):
GET /SessionServ2 HTTP/1.1
Host: localhost:8080
Cookie: JSESSIONID=ABC123XYZ789    ← Browser sends cookie back

Second Response:
HTTP/1.1 200 OK
Content-Type: text/html
...
(No Set-Cookie - session already exists)
```

### What if Cookies are Disabled?

```
Problem: Browser doesn't accept/send JSESSIONID cookie
Result: Every request is treated as new user → Session tracking fails

Solution: URL Rewriting
- Session ID embedded in URL instead of cookie
- Example: http://localhost:8080/MyApp/page.jsp;jsessionid=ABC123XYZ789

How to implement:
String url = response.encodeURL("page.jsp");
// Returns: "page.jsp;jsessionid=ABC123XYZ789" (if cookies disabled)
// Returns: "page.jsp" (if cookies enabled)
```

---

## 5. Session Invalidation

### The invalidate() Method

```java
session.invalidate();
```

### What Happens Internally:

```
┌────────────────────────────────────────────────────────────────────┐
│              INTERNAL STEPS WHEN SESSION IS INVALIDATED            │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│  Step 1: REMOVE ALL ATTRIBUTES                                     │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ All objects stored in session are removed                   │  │
│  │ session.removeAttribute("book");                             │  │
│  │ session.removeAttribute("user");                             │  │
│  │ ...                                                          │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  Step 2: REMOVE SESSION FROM SERVER MAP                            │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ sessionMap.remove(sessionId);                                │  │
│  │ Session object becomes eligible for garbage collection       │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  Step 3: INVALIDATE JSESSIONID COOKIE                              │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │ Cookie still exists in browser but ID no longer valid        │  │
│  │ Next request will not find matching session on server        │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

### Use Cases for invalidate()

1. **User Logout**: Destroy session when user logs out
2. **Security**: After password change, invalidate old session
3. **Timeout**: Manual session expiration

```java
// Logout implementation
protected void doGet(HttpServletRequest request, HttpServletResponse response) {
    HttpSession session = request.getSession(false);
    if (session != null) {
        session.invalidate();  // Destroy session
    }
    response.sendRedirect("login.html");  // Redirect to login
}
```

---

## 6. Session Timeout Configuration

### Method 1: Programmatic (In Java Code)

```java
HttpSession session = request.getSession();

// Set timeout to 30 minutes (1800 seconds)
session.setMaxInactiveInterval(1800);

// Get current timeout
int timeout = session.getMaxInactiveInterval();
System.out.println("Timeout: " + timeout + " seconds");
```

### Method 2: Declarative (In web.xml)

```xml
<web-app>
    <!-- Other configurations -->
    
    <session-config>
        <session-timeout>30</session-timeout>  <!-- In MINUTES, not seconds! -->
    </session-config>
    
</web-app>
```

### Important Difference

| Configuration | Unit | Example |
|---------------|------|---------|
| Java `setMaxInactiveInterval()` | Seconds | `session.setMaxInactiveInterval(1800)` = 30 min |
| web.xml `<session-timeout>` | Minutes | `<session-timeout>30</session-timeout>` = 30 min |

### What is "Inactive Interval"?

The session timeout is based on **inactivity**, not total time.

```
Session Created: 10:00:00
                     │
                     ▼
User makes request at 10:10:00  → Timer resets
                     │
                     ▼
User makes request at 10:20:00  → Timer resets
                     │
                     ▼
No request for 30 minutes...
                     │
                     ▼
10:50:00 → Session EXPIRED (auto-invalidated)
```

---

## 7. SessionDemo Example

### SessionDemo.java - Complete Implementation

```java
import java.io.*;                                    // Line 1: IO imports
import javax.servlet.*;                              // Line 2: Servlet imports
import javax.servlet.http.*;                         // Line 3: HTTP imports

public class SessionDemo extends HttpServlet         // Line 5: Servlet class
{
    // IMPORTANT: Instance variable shared by ALL requests
    int cnt = 0;                                     // Line 8: Counter (servlet-level)
    
    public void doGet(HttpServletRequest request,    // Line 10: Handle GET
                      HttpServletResponse response) 
                      throws ServletException, IOException
    {
        // Step 1: Increment counter for every request
        cnt++;                                       // Line 15: Increase count
        
        // Step 2: Create or get session
        HttpSession session = request.getSession();  // Line 18: Get session
        
        // Step 3: Get output writer
        PrintWriter pw = response.getWriter();       // Line 21: Output stream
        
        // Step 4: Check if session is new or existing
        if (session.isNew()) {                       // Line 24: New session?
            pw.println("it is new");                 // Line 25: First visit
        } else {
            pw.println("it is not new");             // Line 27: Returning user
        }
        
        // Step 5: Check if we should invalidate session
        if (cnt > 4) {                               // Line 31: After 4 hits
            session.invalidate();                    // Line 32: Destroy session
            cnt = 0;                                 // Line 33: Reset counter
        }
    }
}
```

### Execution Flow

```
Request 1: cnt=1, isNew()=true  → "it is new"
Request 2: cnt=2, isNew()=false → "it is not new"
Request 3: cnt=3, isNew()=false → "it is not new"
Request 4: cnt=4, isNew()=false → "it is not new"
Request 5: cnt=5, isNew()=false → "it is not new" + invalidate() + cnt=0
Request 6: cnt=1, isNew()=true  → "it is new" (new session created!)
```

### Visual Timeline

```
┌────────────────────────────────────────────────────────────────────┐
│                    SESSION DEMO TIMELINE                           │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│  Req 1    Req 2    Req 3    Req 4    Req 5        Req 6           │
│    │        │        │        │        │            │              │
│    ▼        ▼        ▼        ▼        ▼            ▼              │
│  ┌───┐    ┌───┐    ┌───┐    ┌───┐    ┌───┐      ┌───┐            │
│  │NEW│    │OLD│    │OLD│    │OLD│    │OLD│      │NEW│            │
│  └───┘    └───┘    └───┘    └───┘    └─┬─┘      └───┘            │
│                                        │                          │
│  cnt=1    cnt=2    cnt=3    cnt=4    cnt=5       cnt=1            │
│                                        │                          │
│                                   INVALIDATE                       │
│                                   cnt reset                        │
│                                                                    │
│  [──────── SESSION 1 ────────────]     [───── SESSION 2 ─────     │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

---

## 8. Key Points to Remember

1. **Session Creation Steps**:
   - Create session object
   - Generate unique ID
   - Create JSESSIONID cookie
   - Store session on server
   - Send cookie to browser

2. **Session Retrieval Steps**:
   - Read JSESSIONID from request
   - Find matching session on server
   - Return session or null

3. **JSESSIONID is automatic**: The container handles cookie creation and reading

4. **Timeout is Inactivity-based**: Each request resets the timeout timer

5. **invalidate() is permanent**: Session cannot be recovered after invalidation

6. **Cookie disabled = Session broken**: Use URL Rewriting as fallback

7. **Instance variables are shared**: Be careful with servlet-level variables like `cnt`

8. **isNew() for first request**: Returns true only for the very first request of that session

---

## 9. Interview Questions

### Basic Level

**Q1: What steps does the container perform when creating a session?**
> 1. Create HttpSession object
> 2. Generate unique session ID
> 3. Store attributes in session
> 4. Create JSESSIONID cookie with session ID
> 5. Add cookie to response
> 6. Store session in server's session map

**Q2: What is JSESSIONID?**
> JSESSIONID is a cookie created by the container to store the session ID. It links the browser to the correct session object on the server.

**Q3: What happens when invalidate() is called?**
> All session attributes are removed, the session is removed from the server's session map, and the session ID becomes invalid. A new session will be created on the next getSession() call.

### Intermediate Level

**Q4: How does the container retrieve an existing session?**
> 1. Read JSESSIONID cookie from request
> 2. Extract session ID from cookie
> 3. Look up session ID in server's session map
> 4. If found and not expired, return session object
> 5. If not found or expired, return null (for getSession(false)) or create new session (for getSession())

**Q5: What is the difference between timeout set in Java vs web.xml?**
> - Java `setMaxInactiveInterval()` takes seconds
> - web.xml `<session-timeout>` takes minutes
> - Both achieve the same result: session expires after specified inactivity period

**Q6: What is the meaning of "inactive interval"?**
> It's the time the server waits after the last request before automatically invalidating the session. Each new request from the user resets this timer.

### Advanced Level

**Q7: How would you implement URL Rewriting if cookies are disabled?**
```java
// Instead of hardcoded URLs
out.println("<a href='page2.jsp'>Next</a>");

// Use encoded URLs
out.println("<a href='" + response.encodeURL("page2.jsp") + "'>Next</a>");

// If cookies disabled, output becomes:
// <a href='page2.jsp;jsessionid=ABC123'>Next</a>
```

**Q8: Explain the thread-safety considerations for the SessionDemo example.**
> The `cnt` variable is an instance variable shared by all threads (requests). This is NOT thread-safe. Multiple concurrent requests could increment `cnt` simultaneously, causing race conditions. Solutions:
> 1. Use AtomicInteger instead of int
> 2. Synchronize access to cnt
> 3. Better: Use session-scoped counter per user

**Q9: What happens if two users share the same JSESSIONID?**
> They would share the same session! This is called "session hijacking" - a security vulnerability. Prevention:
> 1. Use HTTPS to encrypt cookies
> 2. Set HttpOnly flag (prevents JavaScript access)
> 3. Regenerate session ID after login
> 4. Use short session timeouts
> 5. Bind session to IP address (with caution)

---

## Next Topic: [URL Rewriting →](./06_URL_Rewriting.md)
