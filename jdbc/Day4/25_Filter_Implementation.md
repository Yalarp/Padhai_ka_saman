# Filter Implementation - Request and Response Filters

## Table of Contents
1. [Introduction](#1-introduction)
2. [Request Filters](#2-request-filters)
3. [Response Filters](#3-response-filters)
4. [Filter Chaining](#4-filter-chaining)
5. [Common Filter Patterns](#5-common-filter-patterns)
6. [Complete Code Examples](#6-complete-code-examples)
7. [Interview Questions](#7-interview-questions)

---

## 1. Introduction

Filters can modify both requests (before reaching servlets) and responses (before reaching clients). This enables cross-cutting concerns like logging, security, and compression.

---

## 2. Request Filters

### What Request Filters Can Do

- Validate/sanitize input
- Check authentication/authorization
- Log incoming requests
- Modify request headers
- Block malicious requests

### Request Filter Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    REQUEST FILTER PROCESSING                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Client Request                                                             │
│       │                                                                     │
│       ▼                                                                     │
│  ┌────────────────────────────────────────┐                                │
│  │         REQUEST FILTER                  │                                │
│  │  ┌──────────────────────────────────┐  │                                │
│  │  │ 1. Read request parameters       │  │                                │
│  │  │ 2. Validate input                │  │                                │
│  │  │ 3. Check authentication          │  │                                │
│  │  │ 4. Log request details           │  │                                │
│  │  │ 5. Modify if needed              │  │                                │
│  │  └──────────────────────────────────┘  │                                │
│  │                                         │                                │
│  │  if (valid) {                          │                                │
│  │      chain.doFilter(request,response); │ ───► Continue to servlet       │
│  │  } else {                              │                                │
│  │      response.sendError(403);          │ ───► Block request             │
│  │  }                                      │                                │
│  └────────────────────────────────────────┘                                │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Example: Authentication Filter

```java
@WebFilter("/*")
public class AuthenticationFilter implements Filter {
    
    private static final List<String> PUBLIC_PATHS = 
        Arrays.asList("/login", "/register", "/css/", "/js/");
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                         FilterChain chain) throws IOException, ServletException {
        
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        HttpServletResponse httpResponse = (HttpServletResponse) response;
        
        String path = httpRequest.getRequestURI();
        
        // Allow public paths
        if (isPublicPath(path)) {
            chain.doFilter(request, response);
            return;
        }
        
        // Check if user is logged in
        HttpSession session = httpRequest.getSession(false);
        if (session == null || session.getAttribute("user") == null) {
            // Not authenticated - redirect to login
            httpResponse.sendRedirect(httpRequest.getContextPath() + "/login");
            return;
        }
        
        // Authenticated - continue
        chain.doFilter(request, response);
    }
    
    private boolean isPublicPath(String path) {
        return PUBLIC_PATHS.stream().anyMatch(path::contains);
    }
}
```

---

## 3. Response Filters

### What Response Filters Can Do

- Compress response content
- Add response headers (CORS, security)
- Transform output (e.g., encrypt)
- Log response data
- Cache responses

### Response Filter Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    RESPONSE FILTER PROCESSING                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Servlet generates response                                                 │
│       │                                                                     │
│       ▼                                                                     │
│  ┌────────────────────────────────────────┐                                │
│  │        RESPONSE FILTER                  │                                │
│  │  ┌──────────────────────────────────┐  │                                │
│  │  │ 1. chain.doFilter() first!       │  │                                │
│  │  │    (let servlet write response)  │  │                                │
│  │  │                                   │  │                                │
│  │  │ 2. AFTER chain.doFilter():       │  │                                │
│  │  │    - Modify response content      │  │                                │
│  │  │    - Add headers                  │  │                                │
│  │  │    - Log response                 │  │                                │
│  │  │    - Compress output              │  │                                │
│  │  └──────────────────────────────────┘  │                                │
│  └────────────────────────────────────────┘                                │
│       │                                                                     │
│       ▼                                                                     │
│  Response sent to client                                                   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Example: CORS Filter

```java
@WebFilter("/*")
public class CORSFilter implements Filter {
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                         FilterChain chain) throws IOException, ServletException {
        
        HttpServletResponse httpResponse = (HttpServletResponse) response;
        
        // Add CORS headers BEFORE chain.doFilter()
        httpResponse.setHeader("Access-Control-Allow-Origin", "*");
        httpResponse.setHeader("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE");
        httpResponse.setHeader("Access-Control-Allow-Headers", "Content-Type, Authorization");
        
        chain.doFilter(request, response);
    }
}
```

### Example: Response Timing Filter

```java
@WebFilter("/*")
public class TimingFilter implements Filter {
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                         FilterChain chain) throws IOException, ServletException {
        
        long startTime = System.currentTimeMillis();
        
        // Process request
        chain.doFilter(request, response);
        
        // AFTER processing - calculate time
        long endTime = System.currentTimeMillis();
        long duration = endTime - startTime;
        
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        System.out.println(httpRequest.getRequestURI() + " took " + duration + "ms");
    }
}
```

---

## 4. Filter Chaining

### Multiple Filters

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    FILTER CHAIN EXECUTION                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Request                                                                    │
│     │                                                                       │
│     ▼                                                                       │
│  ┌──────────────────┐                                                      │
│  │ Filter 1 (pre)   │ ───┐                                                │
│  └──────────────────┘    │                                                │
│                          ▼                                                 │
│                    ┌──────────────────┐                                    │
│                    │ Filter 2 (pre)   │ ───┐                              │
│                    └──────────────────┘    │                              │
│                                            ▼                              │
│                                      ┌──────────────────┐                 │
│                                      │ Filter 3 (pre)   │                 │
│                                      └────────┬─────────┘                 │
│                                               │                           │
│                                               ▼                           │
│                                      ┌──────────────────┐                 │
│                                      │     SERVLET      │                 │
│                                      └────────┬─────────┘                 │
│                                               │                           │
│                                               ▼                           │
│                                      ┌──────────────────┐                 │
│                                      │ Filter 3 (post)  │                 │
│                                      └────────┬─────────┘                 │
│                          ┌────────────────────┘                           │
│                          ▼                                                 │
│                    ┌──────────────────┐                                    │
│                    │ Filter 2 (post)  │                                    │
│                    └────────┬─────────┘                                    │
│     ┌───────────────────────┘                                             │
│     ▼                                                                       │
│  ┌──────────────────┐                                                      │
│  │ Filter 1 (post)  │                                                      │
│  └──────────────────┘                                                      │
│     │                                                                       │
│     ▼                                                                       │
│  Response                                                                   │
│                                                                             │
│  Execution: 1→2→3→Servlet→3→2→1 (Stack-like behavior)                      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Filter Order Configuration

**web.xml (order matters!):**
```xml
<!-- First filter -->
<filter>
    <filter-name>LoggingFilter</filter-name>
    <filter-class>com.example.LoggingFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>LoggingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>

<!-- Second filter -->
<filter>
    <filter-name>AuthFilter</filter-name>
    <filter-class>com.example.AuthFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>AuthFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

---

## 5. Common Filter Patterns

### Logging Filter

```java
@WebFilter("/*")
public class LoggingFilter implements Filter {
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                         FilterChain chain) throws IOException, ServletException {
        
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        
        // Log request
        String logMessage = String.format(
            "[%s] %s %s from %s",
            new java.util.Date(),
            httpRequest.getMethod(),
            httpRequest.getRequestURI(),
            httpRequest.getRemoteAddr()
        );
        System.out.println(logMessage);
        
        // Continue processing
        chain.doFilter(request, response);
    }
}
```

### Encoding Filter

```java
@WebFilter("/*")
public class EncodingFilter implements Filter {
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                         FilterChain chain) throws IOException, ServletException {
        
        // Set encoding for all requests/responses
        request.setCharacterEncoding("UTF-8");
        response.setCharacterEncoding("UTF-8");
        
        chain.doFilter(request, response);
    }
}
```

### Security Headers Filter

```java
@WebFilter("/*")
public class SecurityHeadersFilter implements Filter {
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                         FilterChain chain) throws IOException, ServletException {
        
        HttpServletResponse httpResponse = (HttpServletResponse) response;
        
        // Add security headers
        httpResponse.setHeader("X-Content-Type-Options", "nosniff");
        httpResponse.setHeader("X-Frame-Options", "DENY");
        httpResponse.setHeader("X-XSS-Protection", "1; mode=block");
        httpResponse.setHeader("Strict-Transport-Security", "max-age=31536000");
        
        chain.doFilter(request, response);
    }
}
```

---

## 6. Complete Code Examples

### Combined Request/Response Filter

```java
@WebFilter(
    urlPatterns = "/*",
    initParams = {
        @WebInitParam(name = "logFile", value = "access.log")
    }
)
public class AccessLogFilter implements Filter {
    
    private PrintWriter logWriter;
    
    @Override
    public void init(FilterConfig config) throws ServletException {
        String logFile = config.getInitParameter("logFile");
        try {
            logWriter = new PrintWriter(new FileWriter(logFile, true));
        } catch (IOException e) {
            throw new ServletException("Cannot create log file", e);
        }
    }
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                         FilterChain chain) throws IOException, ServletException {
        
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        HttpServletResponse httpResponse = (HttpServletResponse) response;
        
        long startTime = System.currentTimeMillis();
        
        // Log request
        logWriter.printf("[%tF %<tT] REQUEST: %s %s%n",
            new java.util.Date(),
            httpRequest.getMethod(),
            httpRequest.getRequestURI()
        );
        
        // Process request
        chain.doFilter(request, response);
        
        // Log response
        long duration = System.currentTimeMillis() - startTime;
        logWriter.printf("[%tF %<tT] RESPONSE: %dms%n",
            new java.util.Date(),
            duration
        );
        logWriter.flush();
    }
    
    @Override
    public void destroy() {
        if (logWriter != null) {
            logWriter.close();
        }
    }
}
```

---

## 7. Interview Questions

### Basic Level

**Q1: What's the difference between request and response filters?**
> - Request filters process data BEFORE chain.doFilter()
> - Response filters process data AFTER chain.doFilter()
> - Same filter can do both!

**Q2: How do you add headers to all responses?**
```java
public void doFilter(...) {
    HttpServletResponse res = (HttpServletResponse) response;
    res.setHeader("Custom-Header", "value");
    chain.doFilter(request, response);
}
```

### Intermediate Level

**Q3: How does filter chain execution work?**
> Filters execute in a stack-like manner:
> - Pre-processing: Filter1 → Filter2 → Filter3 → Servlet
> - Post-processing: Filter3 → Filter2 → Filter1
> - Code after chain.doFilter() runs in reverse order

**Q4: How do you block a request in a filter?**
```java
public void doFilter(...) {
    if (!isValid(request)) {
        ((HttpServletResponse)response).sendError(403, "Forbidden");
        return;  // Don't call chain.doFilter()
    }
    chain.doFilter(request, response);
}
```

### Advanced Level

**Q5: How do you modify the request body in a filter?**
> Wrap the request using HttpServletRequestWrapper:
```java
public class CustomRequestWrapper extends HttpServletRequestWrapper {
    private final String modifiedBody;
    
    public CustomRequestWrapper(HttpServletRequest request, String body) {
        super(request);
        this.modifiedBody = body;
    }
    
    @Override
    public BufferedReader getReader() {
        return new BufferedReader(new StringReader(modifiedBody));
    }
}
```

**Q6: How do you capture and modify the response body?**
> Wrap the response using HttpServletResponseWrapper with a custom output stream, then modify the captured content.

---

## Next Topic: [Thread Safety →](./15_Thread_Safety_Servlets.md)
