# 📚 ASP.NET Core Web API Interview Questions

> **50+ Common Interview Questions with Detailed Answers**

---

## 🎯 Basic Questions

### Q1: What is ASP.NET Core Web API?
> **Answer:** ASP.NET Core Web API is a framework for building HTTP-based services that can be consumed by various clients like browsers, mobile apps, and IoT devices. It follows REST principles and uses JSON for data exchange.

### Q2: What is REST?
> **Answer:** REST (Representational State Transfer) is an architectural style for building web services. Key principles:
> - **Stateless**: Each request contains all information needed
> - **Resource-based**: URLs represent resources
> - **HTTP Methods**: GET (read), POST (create), PUT (update), DELETE (remove)
> - **Uniform Interface**: Standard HTTP status codes

### Q3: What is the difference between Web API and MVC?
> **Answer:**
> - **Web API**: Returns data (JSON/XML), uses `ControllerBase`, no views
> - **MVC**: Returns views (HTML), uses `Controller`, has view support
> - **Both**: Can coexist in same project using ASP.NET Core unified model

### Q4: What is the purpose of [ApiController] attribute?
> **Answer:** [ApiController] enables:
> - Automatic model validation (returns 400 for invalid models)
> - Automatic [FromBody] inference for complex types
> - Problem Details error format
> - Requires attribute routing

### Q5: What is the difference between ControllerBase and Controller?
> **Answer:**
> - `ControllerBase`: For APIs, no view methods (View(), PartialView())
> - `Controller`: For MVC, inherits ControllerBase + adds view support

---

## 🔧 HTTP Methods & Routing

### Q6: What HTTP methods are used in REST APIs?
> **Answer:**
> - **GET**: Retrieve data (safe, idempotent)
> - **POST**: Create new resource (not idempotent)
> - **PUT**: Update entire resource (idempotent)
> - **PATCH**: Partial update (idempotent)
> - **DELETE**: Remove resource (idempotent)

### Q7: What is idempotent?
> **Answer:** An operation is idempotent if calling it multiple times produces the same result. GET, PUT, DELETE are idempotent. POST is not (creates new resource each time).

### Q8: What is attribute routing?
> **Answer:** Defining routes using attributes on controllers and actions:
> ```csharp
> [Route("api/[controller]")]
> [HttpGet("{id:int}")]
> ```

### Q9: What are route constraints?
> **Answer:** Rules that validate URL parameters:
> - `{id:int}` - Must be integer
> - `{name:alpha}` - Must be alphabetic
> - `{age:range(18,65)}` - Must be in range

### Q10: What is the difference between [FromBody] and [FromQuery]?
> **Answer:**
> - `[FromBody]`: Binds from request body (JSON)
> - `[FromQuery]`: Binds from query string (?key=value)
> - `[FromRoute]`: Binds from URL path segment

---

## 📦 Dependency Injection

### Q11: What is Dependency Injection?
> **Answer:** A design pattern where objects receive their dependencies from an external source (DI container) rather than creating them. Benefits: loose coupling, testability, maintainability.

### Q12: What are the three DI lifetimes?
> **Answer:**
> - **Scoped**: One instance per HTTP request
> - **Transient**: New instance every time requested
> - **Singleton**: One instance for entire application

### Q13: When should you use Scoped vs Singleton?
> **Answer:**
> - **Scoped**: DbContext, Repositories (per-request state)
> - **Singleton**: Caching, Configuration (shared state, must be thread-safe)

### Q14: What is Constructor Injection?
> **Answer:** Dependencies are passed through the constructor:
> ```csharp
> public class MyController : ControllerBase
> {
>     private readonly IService _service;
>     public MyController(IService service) => _service = service;
> }
> ```

### Q15: What is a captive dependency?
> **Answer:** When a Singleton captures a Scoped service, causing it to live beyond its intended lifetime. This can cause bugs and memory issues.

---

## 🗃️ Entity Framework Core

### Q16: What is Entity Framework Core?
> **Answer:** An ORM (Object-Relational Mapper) that allows developers to work with databases using .NET objects, eliminating most data-access code.

### Q17: What is DbContext?
> **Answer:** DbContext represents a session with the database. It's a unit of work that tracks changes and persists them with SaveChanges().

### Q18: What is DbSet<T>?
> **Answer:** DbSet<T> represents a table in the database. It provides methods like Add(), Remove(), Find() and supports LINQ queries.

### Q19: What is the difference between Find() and FirstOrDefault()?
> **Answer:**
> - `Find()`: Uses primary key, checks local cache first
> - `FirstOrDefault()`: Uses any predicate, always queries database

### Q20: What is eager loading?
> **Answer:** Loading related data in the same query using Include():
> ```csharp
> context.Employees.Include(e => e.Department).ToList();
> ```
> Prevents N+1 query problem.

### Q21: What is the N+1 problem?
> **Answer:** Without Include(), EF executes 1 query for main entities, then N queries for each related entity. Eager loading solves this with a single JOIN query.

---

## 🔄 Async Programming

### Q22: Why use async/await in Web APIs?
> **Answer:** Async frees threads during I/O operations (database, HTTP calls), allowing fewer threads to handle more concurrent requests, improving scalability.

### Q23: What happens to the thread when you await?
> **Answer:** The thread returns to the thread pool. When the awaited operation completes, a thread (possibly different) continues execution.

### Q24: What is the difference between Task and void for async methods?
> **Answer:**
> - `Task`: Allows callers to await and catch exceptions
> - `async void`: Exceptions can't be caught, only for event handlers

### Q25: What is "async all the way"?
> **Answer:** If any method in the call chain is async, all callers should be async. Mixing sync and async can cause deadlocks.

---

## 🔐 Security

### Q26: What is JWT?
> **Answer:** JSON Web Token - a compact, self-contained token format with three parts: Header, Payload, Signature. Used for stateless authentication.

### Q27: How does JWT authentication work?
> **Answer:**
> 1. Client sends credentials
> 2. Server validates and returns JWT
> 3. Client includes JWT in Authorization header
> 4. Server validates JWT signature and claims

### Q28: What is the purpose of [Authorize] attribute?
> **Answer:** Restricts access to authenticated users. Can specify roles: `[Authorize(Roles = "Admin")]`

### Q29: What is the difference between Authentication and Authorization?
> **Answer:**
> - **Authentication**: Who are you? (Identity verification)
> - **Authorization**: What can you do? (Permission checking)

### Q30: Why must UseAuthentication() come before UseAuthorization()?
> **Answer:** Authentication identifies the user first, then authorization checks their permissions. Order matters in middleware pipeline.

---

## 📋 Model Validation

### Q31: What are Data Annotations?
> **Answer:** Attributes that define validation rules on model properties:
> - `[Required]` - Field must have value
> - `[MaxLength]` - Maximum length
> - `[EmailAddress]` - Email format

### Q32: Does [DataType(DataType.EmailAddress)] validate email format?
> **Answer:** No! [DataType] only provides metadata. Use [EmailAddress] attribute for validation.

### Q33: How does [ApiController] affect validation?
> **Answer:** Enables automatic validation. If model is invalid, returns 400 Bad Request with errors automatically without checking ModelState.

---

## 🏗️ Architecture

### Q34: What is the Repository Pattern?
> **Answer:** A design pattern that abstracts data access behind an interface, providing a collection-like API for accessing data. Benefits: testability, separation of concerns.

### Q35: What is the difference between Monolithic and Microservices?
> **Answer:**
> - **Monolithic**: Single deployable unit, all features together
> - **Microservices**: Small independent services, separate databases, independent deployment

### Q36: What is an API Gateway?
> **Answer:** Single entry point that routes requests to appropriate microservices, handles authentication, rate limiting, and can aggregate responses.

---

## ⚙️ Configuration

### Q37: What is the difference between appsettings.json and launchSettings.json?
> **Answer:**
> - `appsettings.json`: Runtime configuration (deployed with app)
> - `launchSettings.json`: Development settings (not deployed)

### Q38: How do you access configuration in .NET Core?
> **Answer:**
> ```csharp
> var value = builder.Configuration["MySetting"];
> var connString = builder.Configuration.GetConnectionString("Default");
> ```

---

## 🔧 Middleware

### Q39: What is Middleware?
> **Answer:** Components in the HTTP request pipeline that process requests and responses. Each can process, pass to next, or short-circuit.

### Q40: Why does middleware order matter?
> **Answer:** Middleware executes in registration order. Exception handling must be first to catch all errors. Authentication before Authorization.

---

## 📊 Return Types

### Q41: What is the difference between IActionResult and ActionResult<T>?
> **Answer:**
> - `IActionResult`: Full status code control, no type safety
> - `ActionResult<T>`: Type safety (Swagger knows type) + status codes

### Q42: When should POST return 201 vs 200?
> **Answer:** 201 Created is semantically correct for resource creation. Use CreatedAtAction() with Location header.

### Q43: What does void return type give?
> **Answer:** Void returns 204 No Content, appropriate for DELETE operations.

---

## 🌐 CORS

### Q44: What is CORS?
> **Answer:** Cross-Origin Resource Sharing - browser security feature that controls which domains can access your API.

### Q45: Why can't you use AllowAnyOrigin with AllowCredentials?
> **Answer:** Security restriction. Credentials require specific origins to prevent credential leakage to any domain.

---

## 📖 Swagger

### Q46: What is Swagger/OpenAPI?
> **Answer:** Specification for describing REST APIs. Swashbuckle auto-generates interactive documentation with "Try it out" functionality.

### Q47: Should Swagger be enabled in production?
> **Answer:** Generally no - it exposes API details. Enable only in Development environment.

---

## 💡 Best Practices

### Q48: What are some REST API best practices?
> **Answer:**
> - Use nouns for resources (api/products not api/getProducts)
> - Use proper HTTP methods
> - Return appropriate status codes
> - Version your APIs
> - Use consistent naming

### Q49: How do you handle errors globally?
> **Answer:** Use exception handling middleware that catches all exceptions, logs them, and returns appropriate error responses.

### Q50: What is content negotiation?
> **Answer:** Server returning data in format client prefers based on Accept header (JSON, XML).

---

## 🎯 Quick Revision Answers

| Topic | Key Point |
|-------|-----------|
| REST | Stateless, resource-based, HTTP methods |
| [ApiController] | Auto validation, binding inference |
| Scoped | One per HTTP request |
| DbContext | Database session, unit of work |
| Include() | Eager loading, prevents N+1 |
| async/await | Non-blocking I/O |
| JWT | Header.Payload.Signature |
| Middleware Order | Exception → Auth → AuthZ |

---

*Use these for quick revision before interviews!*
