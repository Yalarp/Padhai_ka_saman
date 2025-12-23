# Session Management - Complete Summary

## Table of Contents
1. [Overview](#1-overview)
2. [Comparison of All Techniques](#2-comparison-of-all-techniques)
3. [Quick Reference](#3-quick-reference)
4. [Decision Matrix](#4-decision-matrix)
5. [Best Practices](#5-best-practices)
6. [Interview Summary](#6-interview-summary)

---

## 1. Overview

HTTP is stateless - each request is independent. Session management techniques allow maintaining state across requests.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    SESSION MANAGEMENT TECHNIQUES                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │  1. HIDDEN FIELDS                                           │           │
│  │     • Data stored in HTML form                              │           │
│  │     • Visible in page source                                │           │
│  │     • Best for: Multi-step forms                            │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │  2. COOKIES                                                 │           │
│  │     • Data stored on client browser                         │           │
│  │     • Sent with every request                               │           │
│  │     • Best for: Preferences, remember me                    │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │  3. HTTP SESSION                                            │           │
│  │     • Data stored on server                                 │           │
│  │     • Session ID in cookie or URL                           │           │
│  │     • Best for: User login, shopping cart                   │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │  4. URL REWRITING                                           │           │
│  │     • Session ID appended to URL                            │           │
│  │     • Fallback when cookies disabled                        │           │
│  │     • Best for: Cookie-less clients                         │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Comparison of All Techniques

| Feature | Hidden Fields | Cookies | HttpSession | URL Rewriting |
|---------|--------------|---------|-------------|---------------|
| **Storage** | HTML form | Browser | Server | URL |
| **Visibility** | Page source | Browser tools | Server only | URL bar |
| **Size Limit** | Form size | ~4KB | Server memory | URL length |
| **Security** | Low | Medium | High | Low |
| **Persistence** | Page lifetime | Configurable | Session timeout | Session lifetime |
| **Works without cookies** | ✅ | ❌ | With URL rewriting | ✅ |
| **Server memory** | None | None | Required | Required |
| **Data type** | String | String | Any Object | String |

---

## 3. Quick Reference

### Hidden Fields
```html
<input type="hidden" name="userId" value="123">
```
```java
String userId = request.getParameter("userId");
```

### Cookies
```java
// Create
Cookie c = new Cookie("name", "value");
c.setMaxAge(3600);          // 1 hour
response.addCookie(c);

// Read
Cookie[] cookies = request.getCookies();
for (Cookie c : cookies) {
    if (c.getName().equals("name")) {
        String value = c.getValue();
    }
}
```

### HttpSession
```java
// Create/Get session
HttpSession session = request.getSession();      // Create if not exists
HttpSession session = request.getSession(false); // Return null if not exists

// Store/Retrieve
session.setAttribute("user", userObject);
User user = (User) session.getAttribute("user");

// Invalidate
session.invalidate();
```

### URL Rewriting
```java
// Encode URL (adds JSESSIONID if needed)
String url = response.encodeURL("nextPage.jsp");

// For redirects
String url = response.encodeRedirectURL("nextPage.jsp");
```

---

## 4. Decision Matrix

### When to Use What?

| Scenario | Recommended Technique |
|----------|----------------------|
| Multi-step form (wizard) | Hidden Fields |
| "Remember Me" login | Cookies |
| User login session | HttpSession |
| Shopping cart | HttpSession |
| User preferences | Cookies |
| Cookie-disabled clients | URL Rewriting |
| Sensitive data | HttpSession |
| Token-based auth | Cookies (HttpOnly) |
| Theme/Language | Cookies |
| Cart for logged-out users | Cookies + Session |

### Security Considerations

| Technique | Risk | Mitigation |
|-----------|------|------------|
| Hidden Fields | Easily modified by user | Validate server-side |
| Cookies | XSS attacks | Use HttpOnly flag |
| Cookies | Interception | Use Secure flag + HTTPS |
| HttpSession | Session hijacking | Regenerate ID on login |
| URL Rewriting | Session ID exposure | Use only as fallback |

---

## 5. Best Practices

### Hidden Fields
✅ Use for form continuation only  
✅ Always validate on server  
❌ Never store sensitive data  
❌ Don't use for authentication  

### Cookies
✅ Use HttpOnly for session cookies  
✅ Use Secure flag with HTTPS  
✅ Set appropriate expiration  
❌ Don't store passwords  
❌ Don't exceed 4KB  

### HttpSession
✅ Store minimal data in session  
✅ Invalidate on logout  
✅ Regenerate session ID after login  
✅ Set appropriate timeout  
❌ Don't store large objects  
❌ Don't share session across clusters without config  

### URL Rewriting
✅ Use encodeURL() consistently  
✅ Use only as fallback for cookies  
❌ Don't share URL with session ID  
❌ Don't bookmark sessions  

---

## 6. Interview Summary

### Top Interview Questions

**Q1: What techniques are available for session management?**
> Four main techniques:
> 1. Hidden Fields - Store in HTML forms
> 2. Cookies - Store on client browser
> 3. HttpSession - Store on server
> 4. URL Rewriting - Append to URLs

**Q2: What is the difference between session and cookies?**
| Session | Cookies |
|---------|---------|
| Server-side | Client-side |
| Any data type | String only |
| No size limit | ~4KB limit |
| More secure | Less secure |
| Memory cost | No server memory |

**Q3: How does HttpSession work internally?**
> 1. Server generates unique JSESSIONID
> 2. ID sent to client as cookie
> 3. Client sends ID with each request
> 4. Server uses ID to locate session data

**Q4: What is getSession() vs getSession(false)?**
> - `getSession()` or `getSession(true)`: Creates new session if none exists
> - `getSession(false)`: Returns null if no session exists

**Q5: How do you handle session when cookies are disabled?**
> Use URL Rewriting:
```java
String url = response.encodeURL("next.jsp");
// Appends ;jsessionid=ABC123 if cookies disabled
```

**Q6: What is session timeout and how to configure?**
> Time of inactivity before session expires.
```java
// Programmatically (seconds)
session.setMaxInactiveInterval(1800);  // 30 minutes
```
```xml
<!-- web.xml (minutes) -->
<session-config>
    <session-timeout>30</session-timeout>
</session-config>
```

**Q7: What is the difference between session.invalidate() and session.removeAttribute()?**
> - `invalidate()`: Destroys entire session
> - `removeAttribute()`: Removes specific attribute, session continues

**Q8: What are HttpOnly and Secure cookie flags?**
> - **HttpOnly**: Cookie cannot be accessed via JavaScript (prevents XSS)
> - **Secure**: Cookie only sent over HTTPS (prevents interception)

---

## Related Notes Reference

- [01_HTTP_Stateless_Problem.md](./01_HTTP_Stateless_Problem.md)
- [02_Hidden_Fields.md](./02_Hidden_Fields.md)
- [03_Cookies_Fundamentals.md](./03_Cookies_Fundamentals.md)
- [04_HttpSession_Introduction.md](./04_HttpSession_Introduction.md)
- [05_HttpSession_Internal_Working.md](./05_HttpSession_Internal_Working.md)
- [06_URL_Rewriting.md](./06_URL_Rewriting.md)
