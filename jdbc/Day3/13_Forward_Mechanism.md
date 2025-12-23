# Forward Mechanism - RequestDispatcher

## Table of Contents
1. [Introduction](#1-introduction)
2. [How Forward Works](#2-how-forward-works)
3. [Syntax and Usage](#3-syntax-and-usage)
4. [Use Cases](#4-use-cases)
5. [Complete Code Examples](#5-complete-code-examples)
6. [Include vs Forward](#6-include-vs-forward)
7. [Key Points to Remember](#7-key-points-to-remember)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

Forward is a server-side mechanism where the request is passed internally from one resource to another. The browser is unaware of this transfer - the URL in the address bar does not change.

---

## 2. How Forward Works

### Step-by-Step Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         FORWARD MECHANISM                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Step 1: Client sends request                                               │
│  ┌──────────┐                              ┌──────────────┐                │
│  │  Browser │ ───── GET /servlet1 ──────►  │   Servlet1   │                │
│  │          │                              │              │                │
│  │ URL bar: │                              │ Processes... │                │
│  │ /servlet1│                              └──────────────┘                │
│  └──────────┘                                     │                        │
│                                                   │ forward()              │
│                                                   ▼                        │
│  Step 2: Server internally forwards (SAME request object!)                 │
│                                            ┌──────────────┐                │
│                                            │   Servlet2   │                │
│                                            │ or JSP       │                │
│                                            │              │                │
│                                            │ Uses same    │                │
│                                            │ request obj  │                │
│                                            └──────────────┘                │
│                                                   │                        │
│  Step 3: Response sent back                       │                        │
│  ┌──────────┐                                     │                        │
│  │  Browser │ ◄───────── Response ────────────────┘                        │
│  │          │                                                              │
│  │ URL bar: │  (URL still shows /servlet1!)                               │
│  │ /servlet1│                                                              │
│  └──────────┘                                                              │
│                                                                             │
│  KEY POINTS:                                                                │
│  • Single round trip (one request-response cycle)                          │
│  • URL in browser does NOT change                                          │
│  • Request attributes are PRESERVED                                        │
│  • Cannot forward to external URLs                                         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Syntax and Usage

### Getting RequestDispatcher

```java
// Method 1: From ServletRequest (relative path)
RequestDispatcher rd = request.getRequestDispatcher("view.jsp");

// Method 2: From ServletContext (absolute path, must start with /)
RequestDispatcher rd = getServletContext().getRequestDispatcher("/view.jsp");
```

### Forward Method

```java
rd.forward(request, response);
```

### Complete Example

```java
// Get dispatcher
RequestDispatcher rd = request.getRequestDispatcher("result.jsp");

// Forward (passes same request and response objects)
rd.forward(request, response);

// IMPORTANT: Don't write after forward!
```

---

## 4. Use Cases

### When to Use Forward

| Scenario | Reason |
|----------|--------|
| MVC pattern | Controller servlet forwards to JSP view |
| Error handling | Forward to error page |
| Same application | Internal page navigation |
| Pass data to view | Request attributes preserved |
| Hide implementation | User doesn't see .jsp URLs |

### MVC Pattern with Forward

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         MVC WITH FORWARD                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Browser ──► Controller (Servlet) ──► View (JSP)                           │
│                    │                      ▲                                 │
│                    │                      │                                 │
│                    ▼                      │                                 │
│              Model (POJO) ────────────────┘                                │
│                                     (forward with data)                     │
│                                                                             │
│  1. Browser requests /products                                             │
│  2. ProductServlet (Controller):                                            │
│     - Gets data from ProductService (Model)                                │
│     - Sets request attribute                                               │
│     - Forwards to products.jsp (View)                                      │
│  3. JSP displays data                                                       │
│  4. Browser sees URL: /products (not products.jsp)                         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Complete Code Examples

### Basic MVC Example

**ProductServlet.java (Controller):**
```java
@WebServlet("/products")
public class ProductServlet extends HttpServlet {
    
    private ProductService productService = new ProductService();
    
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // Get data from Model
        List<Product> products = productService.getAllProducts();
        
        // Set data as request attribute
        request.setAttribute("products", products);
        request.setAttribute("title", "Product Catalog");
        
        // Forward to View (JSP)
        RequestDispatcher rd = request.getRequestDispatcher("products.jsp");
        rd.forward(request, response);
    }
}
```

**products.jsp (View):**
```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
    <title>${title}</title>
</head>
<body>
    <h1>${title}</h1>
    
    <table>
        <tr><th>Name</th><th>Price</th></tr>
        <c:forEach var="product" items="${products}">
            <tr>
                <td>${product.name}</td>
                <td>${product.price}</td>
            </tr>
        </c:forEach>
    </table>
</body>
</html>
```

### Error Handling with Forward

```java
@WebServlet("/process")
public class ProcessServlet extends HttpServlet {
    
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        try {
            // Process form
            String data = request.getParameter("data");
            processData(data);
            
            // Success - forward to success page
            request.setAttribute("message", "Data processed successfully!");
            request.getRequestDispatcher("success.jsp").forward(request, response);
            
        } catch (Exception e) {
            // Error - forward to error page with error info
            request.setAttribute("error", e.getMessage());
            request.getRequestDispatcher("error.jsp").forward(request, response);
        }
    }
}
```

### Chained Forwarding

```java
@WebServlet("/step1")
public class Step1Servlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        request.setAttribute("step1Data", "From Step 1");
        request.getRequestDispatcher("/step2").forward(request, response);
    }
}

@WebServlet("/step2")
public class Step2Servlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // Can access step1Data!
        String step1 = (String) request.getAttribute("step1Data");
        request.setAttribute("step2Data", "From Step 2, received: " + step1);
        
        request.getRequestDispatcher("final.jsp").forward(request, response);
    }
}
```

---

## 6. Include vs Forward

### RequestDispatcher Methods

```java
// Forward: Transfers control completely
rd.forward(request, response);  // Target generates entire response

// Include: Includes output of another resource
rd.include(request, response);  // Both can write to response
```

### Comparison

| Feature | forward() | include() |
|---------|-----------|-----------|
| Control | Transfers completely | Returns after inclusion |
| Output | Only target writes | Both can write |
| Use case | MVC view delegation | Include partial content |
| After call | Code after is ignored | Code after executes |

### Include Example

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    PrintWriter out = response.getWriter();
    
    out.println("<html><body>");
    
    // Include header
    request.getRequestDispatcher("header.jsp").include(request, response);
    
    // Main content
    out.println("<h1>Main Content</h1>");
    
    // Include footer
    request.getRequestDispatcher("footer.jsp").include(request, response);
    
    out.println("</body></html>");
}
```

---

## 7. Key Points to Remember

1. **Single round trip** - Server-side transfer, efficient

2. **Same request object** - Attributes are preserved

3. **URL doesn't change** - Browser unaware of forward

4. **Cannot forward externally** - Same web application only

5. **Must not commit response** - Before forward() call

6. **Don't write after forward** - Code is ignored

7. **Use for MVC** - Controller forwards to View

8. **Request.getRequestDispatcher** - Relative paths OK

9. **ServletContext.getRequestDispatcher** - Must start with /

10. **Forward is final** - Control doesn't return

---

## 8. Interview Questions

### Basic Level

**Q1: What is forward in servlets?**
> Forward is a server-side mechanism that passes the request from one resource to another within the same application. The URL in the browser doesn't change.

**Q2: What happens to request attributes on forward?**
> They are PRESERVED because the same request object is passed to the forwarded resource.

**Q3: How do you forward a request?**
```java
RequestDispatcher rd = request.getRequestDispatcher("page.jsp");
rd.forward(request, response);
```

### Intermediate Level

**Q4: What is the difference between forward and redirect?**
| Forward | Redirect |
|---------|----------|
| Server-side | Client-side |
| URL unchanged | URL changes |
| Single request | Two requests |
| Attributes preserved | Attributes lost |
| Same app only | Can go external |
| Faster | Slower |

**Q5: What is the difference between forward and include?**
| forward() | include() |
|-----------|-----------|
| Transfers control entirely | Includes and returns |
| Only target writes response | Both write response |
| Code after ignored | Code after executes |
| Use for main delegation | Use for partial content |

**Q6: Can you forward to an external URL?**
> No! Forward only works within the same web application. Use redirect for external URLs.

### Advanced Level

**Q7: What happens if you call forward after response is committed?**
> You get `IllegalStateException: Cannot forward after response has been committed`. Always forward before any output is sent.

**Q8: What is the difference between getting RequestDispatcher from request vs ServletContext?**
```java
// From request - relative path allowed
request.getRequestDispatcher("page.jsp");
request.getRequestDispatcher("../other.jsp");

// From ServletContext - must be absolute (start with /)
getServletContext().getRequestDispatcher("/page.jsp");
```

---

## Related Notes
- [Redirect Mechanism →](./12_Redirect_Mechanism.md)
- [Redirect vs Forward Comparison →](./11_Redirect_Forward.md)
