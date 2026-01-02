# 📚 Service Lifetimes - Singleton, Scoped, Transient

## 🎯 Introduction

Understanding service lifetimes is crucial for building efficient and correct ASP.NET Core applications. The **lifetime** of a service determines how and when instances are created and disposed by the IoC container.

---

## 📋 Table of Contents
1. [Overview of Service Lifetimes](#overview-of-service-lifetimes)
2. [Singleton Lifetime](#singleton-lifetime)
3. [Scoped Lifetime](#scoped-lifetime)
4. [Transient Lifetime](#transient-lifetime)
5. [Comparison Table](#comparison-table)
6. [When to Use What](#when-to-use-what)
7. [DbContext Pooling](#dbcontext-pooling)
8. [Key Takeaways](#key-takeaways)

---

## 🔷 Overview of Service Lifetimes

ASP.NET Core provides **three service lifetimes** that control when instances are created and shared:

```mermaid
flowchart TB
    subgraph Lifetimes["Service Lifetimes"]
        S[Singleton]
        SC[Scoped]
        T[Transient]
    end
    
    S --> S1["One instance for<br/>entire app lifetime"]
    SC --> SC1["One instance per<br/>HTTP request"]
    T --> T1["New instance<br/>every time"]
    
    style S fill:#90EE90
    style SC fill:#87CEEB
    style T fill:#FFB6C1
```

### Registration Methods

```csharp
// Three ways to register services with different lifetimes
builder.Services.AddSingleton<IService, MyService>();   // Singleton
builder.Services.AddScoped<IService, MyService>();      // Scoped
builder.Services.AddTransient<IService, MyService>();   // Transient
```

---

## 🔷 Singleton Lifetime

### Definition
When you register a service as **Singleton**, only **ONE instance** is created and shared throughout the application's lifetime.

### Characteristics
- Created **once** when first requested
- **Same instance** used for all requests and all users
- Disposed when **application shuts down**
- Similar to having a **static object**

### Visual Representation

```mermaid
sequenceDiagram
    participant User1 as User 1
    participant User2 as User 2
    participant App as Application
    participant Service as Singleton Service
    
    Note over Service: Created once
    User1->>App: Request 1
    App->>Service: Get Instance
    Service-->>App: Instance A
    
    User2->>App: Request 2
    App->>Service: Get Instance
    Service-->>App: Instance A (Same!)
    
    User1->>App: Request 3
    App->>Service: Get Instance
    Service-->>App: Instance A (Same!)
```

### Code Example

```csharp
// Registration
builder.Services.AddSingleton<IConfigurationService, ConfigurationService>();

// The ConfigurationService
public class ConfigurationService : IConfigurationService
{
    private readonly Dictionary<string, string> _settings;
    
    public ConfigurationService()
    {
        Console.WriteLine($"ConfigurationService created at {DateTime.Now}");
        _settings = new Dictionary<string, string>
        {
            { "AppName", "My Application" },
            { "Version", "1.0.0" }
        };
    }
    
    public string GetSetting(string key) => _settings[key];
}
```

### When Created

| Scenario | When Instance is Created |
|----------|--------------------------|
| First Request | First time the service is requested |
| Application Start | If registered with instance |
| Subsequent Requests | Uses existing instance |

---

## 🔷 Scoped Lifetime

### Definition
When you register a service as **Scoped**, **ONE instance** is created per **HTTP request** and shared across all components within that same request.

### Characteristics
- Created **once per HTTP request**
- **Same instance** within a single request
- **Different instance** for different requests
- Disposed at the **end of the request**

### Visual Representation

```mermaid
sequenceDiagram
    participant U1 as Request 1
    participant U2 as Request 2
    participant App as Application
    participant S1 as Instance A
    participant S2 as Instance B
    
    U1->>App: Start Request 1
    App->>S1: Create Instance A
    Note over S1: Lives during Request 1
    
    U2->>App: Start Request 2
    App->>S2: Create Instance B
    Note over S2: Lives during Request 2
    
    U1->>App: Controller Action
    App->>S1: Use Instance A
    
    U1->>App: Another Service
    App->>S1: Use Instance A (Same!)
    
    U1->>App: End Request 1
    App->>S1: Dispose Instance A
    
    U2->>App: End Request 2
    App->>S2: Dispose Instance B
```

### Code Example

```csharp
// Registration - Scoped is RECOMMENDED for DbContext
builder.Services.AddScoped<IOrderService, OrderService>();
builder.Services.AddDbContextPool<AppDbContext>(options =>
    options.UseSqlServer(connectionString));

// The OrderService - uses scoped DbContext
public class OrderService : IOrderService
{
    private readonly AppDbContext _context;
    private readonly Guid _instanceId;
    
    public OrderService(AppDbContext context)
    {
        _context = context;
        _instanceId = Guid.NewGuid();
        Console.WriteLine($"OrderService created: {_instanceId}");
    }
    
    public async Task<Order> GetOrderAsync(int id)
    {
        Console.WriteLine($"Getting order using instance: {_instanceId}");
        return await _context.Orders.FindAsync(id);
    }
}
```

### Request Flow

```mermaid
flowchart LR
    subgraph "HTTP Request 1"
        C1[Controller] --> S1[Service A]
        R1[Repository] --> S1
        S1 --> DB1[(Database)]
    end
    
    subgraph "HTTP Request 2"
        C2[Controller] --> S2[Service B]
        R2[Repository] --> S2
        S2 --> DB2[(Database)]
    end
    
    style S1 fill:#87CEEB
    style S2 fill:#90EE90
```

---

## 🔷 Transient Lifetime

### Definition
When you register a service as **Transient**, a **NEW instance** is created **every time** the service is requested, regardless of the request.

### Characteristics
- Created **every time** requested
- **Never shared** between components
- Disposed at the **end of the request**
- Lightweight and stateless services

### Visual Representation

```mermaid
sequenceDiagram
    participant C as Controller
    participant S1 as Service (Instance 1)
    participant S2 as Service (Instance 2)
    participant S3 as Service (Instance 3)
    
    Note over C: Same Request
    C->>S1: Get Service
    Note over S1: New Instance Created
    
    C->>S2: Get Service Again
    Note over S2: New Instance Created
    
    C->>S3: Yet Another Get
    Note over S3: New Instance Created
```

### Code Example

```csharp
// Registration
builder.Services.AddTransient<IEmailService, EmailService>();

// The EmailService - stateless
public class EmailService : IEmailService
{
    private readonly Guid _instanceId;
    
    public EmailService()
    {
        _instanceId = Guid.NewGuid();
        Console.WriteLine($"EmailService created: {_instanceId}");
    }
    
    public void SendEmail(string to, string subject, string body)
    {
        Console.WriteLine($"Sending email using instance: {_instanceId}");
        // Send email logic
    }
}
```

### Use Case: Parallel Processing

```csharp
// Transient is ideal for parallel processing
public class BatchProcessor
{
    private readonly IServiceProvider _serviceProvider;
    
    public BatchProcessor(IServiceProvider serviceProvider)
    {
        _serviceProvider = serviceProvider;
    }
    
    public void ProcessInParallel(List<Item> items)
    {
        Parallel.ForEach(items, item =>
        {
            // Each parallel thread gets its own instance
            using var scope = _serviceProvider.CreateScope();
            var processor = scope.ServiceProvider.GetRequiredService<IItemProcessor>();
            processor.Process(item);
        });
    }
}
```

---

## 🔷 Comparison Table

| Parameter | Singleton | Scoped | Transient |
|-----------|-----------|--------|-----------|
| **Instance** | Same for all requests/users | One per request | New every time |
| **Created** | First request | Start of request | Each injection |
| **Disposed** | App shutdown | End of request | End of request |
| **Memory** | Low (one instance) | Moderate | Higher (many instances) |
| **Thread-Safe** | Must be thread-safe | Safe within request | Always safe |
| **Use Case** | Configuration, caching | Database contexts | Stateless operations |

### Visual Comparison

```mermaid
flowchart TB
    subgraph Request1["Request 1"]
        R1C1[Controller] --> R1S[Service]
        R1R1[Repository] --> R1S
    end
    
    subgraph Request2["Request 2"]
        R2C1[Controller] --> R2S[Service]
        R2R1[Repository] --> R2S
    end
    
    subgraph Singleton["Singleton"]
        SingleS[Single Instance]
        R1S -.-> SingleS
        R2S -.-> SingleS
    end
    
    subgraph Scoped["Scoped"]
        ScopedS1[Instance 1]
        ScopedS2[Instance 2]
        R1S -.-> ScopedS1
        R2S -.-> ScopedS2
    end
    
    subgraph Transient["Transient"]
        TransS1[Instance 1]
        TransS2[Instance 2]
        TransS3[Instance 3]
        TransS4[Instance 4]
    end
```

---

## 🔷 When to Use What

### AddSingleton() - Use When:

```csharp
// ✅ Application-wide configuration
builder.Services.AddSingleton<IAppConfiguration, AppConfiguration>();

// ✅ Caching service
builder.Services.AddSingleton<ICacheService, MemoryCacheService>();

// ✅ Logger factory
builder.Services.AddSingleton<ILoggerFactory, LoggerFactory>();
```

> [!WARNING]
> Singleton services must be **thread-safe** since they're shared across multiple requests!

### AddScoped() - Use When:

```csharp
// ✅ Database contexts - RECOMMENDED by Microsoft
builder.Services.AddScoped<AppDbContext>();

// ✅ Per-request services
builder.Services.AddScoped<IUserContext, UserContext>();

// ✅ Unit of Work pattern
builder.Services.AddScoped<IUnitOfWork, UnitOfWork>();
```

### AddTransient() - Use When:

```csharp
// ✅ Lightweight, stateless services
builder.Services.AddTransient<IEmailService, EmailService>();

// ✅ Services used in parallel processing
builder.Services.AddTransient<IDocumentProcessor, DocumentProcessor>();

// ✅ Services that don't hold state
builder.Services.AddTransient<IValidator, InputValidator>();
```

### Decision Flowchart

```mermaid
flowchart TD
    Start[Choose Lifetime] --> Q1{Does service hold state?}
    
    Q1 -->|No| Transient[Use Transient]
    Q1 -->|Yes| Q2{Is state per-request?}
    
    Q2 -->|Yes| Scoped[Use Scoped]
    Q2 -->|No| Q3{Is state app-wide?}
    
    Q3 -->|Yes| Q4{Is it thread-safe?}
    Q3 -->|No| Scoped
    
    Q4 -->|Yes| Singleton[Use Singleton]
    Q4 -->|No| MakeThreadSafe[Make Thread-Safe<br/>Then Use Singleton]
    
    style Singleton fill:#90EE90
    style Scoped fill:#87CEEB
    style Transient fill:#FFB6C1
```

---

## 🔷 DbContext Pooling

### AddDbContext vs AddDbContextPool

| Method | Description | Performance |
|--------|-------------|-------------|
| `AddDbContext<T>()` | Creates new context each time | Standard |
| `AddDbContextPool<T>()` | Reuses context from pool | Better in high-scale |

### AddDbContext

```csharp
// Creates a new DbContext instance for each request
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(connectionString));
```

### AddDbContextPool

```csharp
// Reuses DbContext instances from a pool
builder.Services.AddDbContextPool<AppDbContext>(options =>
    options.UseSqlServer(connectionString));
```

### How Pooling Works

```mermaid
sequenceDiagram
    participant R1 as Request 1
    participant R2 as Request 2
    participant Pool as DbContext Pool
    participant Ctx1 as Context A
    participant Ctx2 as Context B
    
    R1->>Pool: Request Context
    Pool->>Ctx1: Get from Pool (or Create)
    Ctx1-->>R1: Use Context A
    
    R1->>Pool: Release Context
    Pool->>Pool: Reset & Return to Pool
    
    R2->>Pool: Request Context
    Pool->>Ctx1: Reuse Context A
    Ctx1-->>R2: Use Context A (Reused!)
```

### Key Differences

| Feature | AddDbContext | AddDbContextPool |
|---------|--------------|------------------|
| Instance per request | New instance | From pool (reused) |
| Initialization cost | Every request | Only when pool grows |
| Pool size | N/A | Default: 1024 (EF Core 6.0) |
| State reset | N/A | Automatic |

### When NOT to Use Pooling

```csharp
// ❌ Don't use pooling if you have private state in DbContext
public class MyDbContext : DbContext
{
    // This private field could leak between requests!
    private string _currentUserId;  // ❌ Don't do this with pooling
    
    public void SetUser(string userId)
    {
        _currentUserId = userId;
    }
}
```

> [!CAUTION]
> EF Core only resets the state it knows about. Private fields in your DbContext will persist between pooled instances!

---

## 🔷 Key Takeaways

> [!IMPORTANT]
> **Must Remember Points:**

### Quick Reference Table

| Question | Singleton | Scoped | Transient |
|----------|-----------|--------|-----------|
| When created? | First request | Each request | Each injection |
| Same instance? | Yes, always | Yes, within request | Never |
| Disposed when? | App shutdown | Request ends | Request ends |
| Thread-safe? | Must be | Not required | Not required |
| Best for? | Config, cache | DbContext | Stateless ops |

### Code Summary

```csharp
// Program.cs - Service Registration

// Singleton - One instance for entire app
builder.Services.AddSingleton<IConfigService, ConfigService>();

// Scoped - One instance per HTTP request (DbContext default)
builder.Services.AddScoped<IUserService, UserService>();
builder.Services.AddDbContextPool<AppDbContext>(options => 
    options.UseSqlServer(connectionString));

// Transient - New instance every time
builder.Services.AddTransient<IEmailService, EmailService>();
```

### Common Mistakes to Avoid

| Mistake | Problem | Solution |
|---------|---------|----------|
| Injecting Scoped into Singleton | Scoped service doesn't get updated | Use IServiceScopeFactory |
| Non-thread-safe Singleton | Race conditions | Make thread-safe or use Scoped |
| Using Singleton for DbContext | Concurrency issues | Use Scoped or Pooled |
| Overusing Transient | Memory overhead | Use Scoped when possible |

### Entity Framework Context Best Practice

```csharp
// ✅ RECOMMENDED - Scoped with Pooling
builder.Services.AddDbContextPool<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

// Then inject into any service
public class EmployeeService
{
    private readonly AppDbContext _context;
    
    public EmployeeService(AppDbContext context)
    {
        _context = context;  // Scoped - same instance throughout request
    }
}
```

---

## 📝 Practice Questions

1. What is the difference between Singleton and Scoped lifetime?
2. Why is Scoped lifetime recommended for Entity Framework DbContext?
3. When would you use Transient lifetime?
4. What happens if you inject a Scoped service into a Singleton service?
5. Explain the difference between AddDbContext and AddDbContextPool.
6. Which lifetime should be used for caching services and why?

---

*Previous: [02 - Dependency Injection in ASP.NET Core](./02_Dependency_Injection_ASPNETCore.md)*

*Next: [04 - Repository Pattern with Entity Framework Core](./04_Repository_Pattern_EFCore.md)*
