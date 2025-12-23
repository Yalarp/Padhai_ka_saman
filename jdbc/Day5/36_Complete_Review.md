# Jakarta EE Days 3-5 Complete Review

## Quick Reference Summary

---

## Day 3: HTTP Stateful Mechanisms & Parameters

### Session Management Techniques

| Technique | Storage | Persistence | Security | Best For |
|-----------|---------|-------------|----------|----------|
| Hidden Fields | HTML | Page-to-page | Low | Wizard forms |
| Cookies | Browser | Configurable | Medium | Preferences |
| HttpSession | Server | Session lifetime | High | User data |
| URL Rewriting | URL | Session | Low | No-cookie fallback |

### Key Methods

```java
// Cookies
Cookie c = new Cookie("name", "value");
response.addCookie(c);
Cookie[] cookies = request.getCookies();

// Session
HttpSession session = request.getSession();
session.setAttribute("key", value);
Object obj = session.getAttribute("key");
session.invalidate();

// URL Rewriting
String url = response.encodeURL("page.jsp");
```

### Parameters

| Type | Scope | Configuration | Access |
|------|-------|---------------|--------|
| Request | Single request | Client form/URL | `request.getParameter()` |
| Init | Single servlet | `<init-param>` | `getServletConfig().getInitParameter()` |
| Context | Application | `<context-param>` | `getServletContext().getInitParameter()` |

### Redirect vs Forward

| Feature | Redirect | Forward |
|---------|----------|---------|
| URL changes | Yes | No |
| Request attrs | Lost | Preserved |
| Round trips | 2 | 1 |
| Method | `response.sendRedirect()` | `RequestDispatcher.forward()` |

---

## Day 4: Filters, JSP, and Advanced Topics

### Filter Lifecycle

```java
public interface Filter {
    void init(FilterConfig config);
    void doFilter(ServletRequest req, ServletResponse res, FilterChain chain);
    void destroy();
}
```

### JSP Lifecycle

1. Translation (JSP → Java)
2. Compilation (Java → Class)
3. Loading
4. Instantiation
5. jspInit()
6. _jspService() (per request)
7. jspDestroy()

### JSP Elements

| Element | Syntax | Purpose |
|---------|--------|---------|
| Declaration | `<%! %>` | Instance variables, methods |
| Expression | `<%= %>` | Output value |
| Scriptlet | `<% %>` | Java code |
| Directive | `<%@ %>` | Page settings |
| Comment | `<%-- --%>` | Hidden comment |

### JSP Implicit Objects

| Object | Type | Scope |
|--------|------|-------|
| request | HttpServletRequest | Request |
| response | HttpServletResponse | Page |
| session | HttpSession | Session |
| application | ServletContext | Application |
| out | JspWriter | Page |
| config | ServletConfig | Page |
| pageContext | PageContext | Page |
| page | Object | Page |
| exception | Throwable | Page (error) |

### Load On Startup

```xml
<servlet>
    <load-on-startup>1</load-on-startup>
</servlet>
```

### Thread Safety

- Servlets are multi-threaded by default
- Use local variables (thread-safe)
- Avoid instance variables for request data
- Synchronize shared resources

---

## Day 5: EL, JSTL, and Standard Actions

### Expression Language

```jsp
${expression}                    // Basic syntax
${param.name}                    // Request parameter
${sessionScope.user.name}        // Session attribute property
${empty list}                    // Check empty
${a + b}                         // Arithmetic
${a > b ? 'yes' : 'no'}         // Ternary
```

### EL Implicit Objects

| Object | Purpose |
|--------|---------|
| pageScope | Page attributes |
| requestScope | Request attributes |
| sessionScope | Session attributes |
| applicationScope | Application attributes |
| param | Request parameters (single) |
| paramValues | Request parameters (array) |
| header | HTTP headers |
| cookie | Cookies |
| initParam | Context init parameters |
| pageContext | PageContext access |

### JSTL Core Tags

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

<c:out value="${var}"/>
<c:set var="x" value="10"/>
<c:remove var="x"/>
<c:if test="${condition}">...</c:if>
<c:choose>
    <c:when test="${cond1}">...</c:when>
    <c:otherwise>...</c:otherwise>
</c:choose>
<c:forEach var="item" items="${list}">...</c:forEach>
<c:forEach var="i" begin="1" end="10">...</c:forEach>
<c:forTokens var="token" items="${str}" delims=",">...</c:forTokens>
```

### Standard Actions

```jsp
<jsp:useBean id="bean" class="pkg.Bean" scope="request"/>
<jsp:setProperty name="bean" property="*"/>
<jsp:getProperty name="bean" property="prop"/>
<jsp:forward page="other.jsp"/>
<jsp:include page="header.jsp"/>
<jsp:param name="key" value="val"/>
```

### Four Scopes

| Scope | Object | EL | Lifetime |
|-------|--------|-----|----------|
| Page | pageContext | pageScope | Single page |
| Request | request | requestScope | Single request |
| Session | session | sessionScope | User session |
| Application | application | applicationScope | App lifetime |

---

## Design Patterns Covered

| Pattern | Type | Purpose |
|---------|------|---------|
| Abstract Factory | Creational | Family of related objects |
| Builder | Creational | Complex object construction |
| Bridge | Structural | Separate abstraction/implementation |
| Proxy | Structural | Control access (RMI) |

---

## Common Interview Topics

1. **Session Management**: getSession() vs getSession(false)
2. **Redirect vs Forward**: When to use each
3. **Filter Chain**: Execution order
4. **JSP Lifecycle**: Translation vs Runtime
5. **EL vs Scriptlet**: Why EL is preferred
6. **Scopes**: Choosing appropriate scope
7. **Thread Safety**: Servlet instance variables
8. **JSTL**: forEach with varStatus
9. **Standard Actions**: useBean with property="*"
10. **Design Patterns**: Proxy in RMI

---

## Quick Tips

1. **Always validate input** - Both client and server side
2. **Minimize session data** - Memory consumption
3. **Use EL over scriptlets** - Cleaner, safer
4. **Choose smallest scope** - Only use session/application when needed
5. **Thread-safe servlets** - Local variables, synchronized blocks
6. **Filter order matters** - Defined in web.xml sequence
7. **Pre-compile JSPs** - For production performance
8. **Handle nulls** - EL shows empty, scriptlet shows "null"
9. **Secure cookies** - HttpOnly, Secure flags
10. **Use JSTL** - Avoid Java code in JSP
