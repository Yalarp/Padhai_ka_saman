# 📚 ASP.NET Core Web API Complete Cheat Sheet

> **Quick Reference Guide for All Key Concepts**

---

## 🚀 Project Setup

### Create New Project
```bash
dotnet new webapi -n MyApi -f net8.0
cd MyApi
dotnet run
```

### Essential NuGet Packages
```bash
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Swashbuckle.AspNetCore
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```

---

## 📄 Program.cs Template

```csharp
var builder = WebApplication.CreateBuilder(args);

// Services
builder.Services.AddControllers();
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("Default")));
builder.Services.AddScoped<IMyService, MyService>();
builder.Services.AddSwaggerGen();

var app = builder.Build();

// Middleware Pipeline
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.UseAuthentication();  // Before Authorization
app.UseAuthorization();
app.MapControllers();
app.Run();
```

---

## 🎮 Controller Template

```csharp
[Route("api/[controller]")]
[ApiController]
public class ItemsController : ControllerBase
{
    private readonly IItemService _service;
    
    public ItemsController(IItemService service) => _service = service;

    [HttpGet]
    public async Task<ActionResult<IEnumerable<Item>>> GetAll()
        => Ok(await _service.GetAllAsync());

    [HttpGet("{id:int}")]
    public async Task<ActionResult<Item>> GetById(int id)
    {
        var item = await _service.GetByIdAsync(id);
        return item == null ? NotFound() : Ok(item);
    }

    [HttpPost]
    public async Task<ActionResult<Item>> Create(Item item)
    {
        var created = await _service.AddAsync(item);
        return CreatedAtAction(nameof(GetById), new { id = created.Id }, created);
    }

    [HttpPut("{id}")]
    public async Task<IActionResult> Update(int id, Item item)
    {
        if (id != item.Id) return BadRequest();
        await _service.UpdateAsync(item);
        return NoContent();
    }

    [HttpDelete("{id}")]
    public async Task<IActionResult> Delete(int id)
    {
        await _service.DeleteAsync(id);
        return NoContent();
    }
}
```

---

## 🔧 Quick Reference Tables

### HTTP Methods → CRUD

| Method | Action | Return Code | Has Body |
|--------|--------|-------------|----------|
| GET | Read | 200 OK | Response only |
| POST | Create | 201 Created | Both |
| PUT | Update | 204 No Content | Request only |
| DELETE | Delete | 204 No Content | Neither |
| PATCH | Partial Update | 200 OK | Both |

### Status Codes

| Code | Meaning | When to Use |
|------|---------|-------------|
| 200 | OK | Successful GET/PUT |
| 201 | Created | Successful POST |
| 204 | No Content | Successful DELETE/PUT |
| 400 | Bad Request | Invalid input |
| 401 | Unauthorized | Not authenticated |
| 403 | Forbidden | Not authorized |
| 404 | Not Found | Resource missing |
| 500 | Server Error | Unhandled exception |

### DI Lifetimes

| Lifetime | Scope | Use Case |
|----------|-------|----------|
| `AddScoped` | Per request | DbContext, Repositories |
| `AddTransient` | Every injection | Lightweight services |
| `AddSingleton` | App lifetime | Caching, Config |

### Binding Attributes

| Attribute | Source | Example |
|-----------|--------|---------|
| `[FromRoute]` | URL path | `/api/items/{id}` |
| `[FromQuery]` | Query string | `?page=1` |
| `[FromBody]` | JSON body | POST/PUT data |
| `[FromHeader]` | HTTP header | API keys |
| `[FromForm]` | Form data | File uploads |

---

## ✅ Validation Attributes

```csharp
public class Item
{
    [Key]
    public int Id { get; set; }
    
    [Required(ErrorMessage = "Name is required")]
    [MaxLength(100)]
    [MinLength(3)]
    public string Name { get; set; }
    
    [Range(0.01, 10000)]
    public decimal Price { get; set; }
    
    [EmailAddress]
    public string Email { get; set; }
    
    [RegularExpression(@"^\d{5}$")]
    public string ZipCode { get; set; }
}
```

---

## 🔒 Route Constraints

```csharp
[HttpGet("{id:int}")]              // Integer only
[HttpGet("{id:int:min(1)}")]       // Integer >= 1
[HttpGet("{name:alpha}")]          // Letters only
[HttpGet("{code:length(6)}")]      // Exactly 6 chars
[HttpGet("{code:regex(^[A-Z]{{2}}\\d{{4}}$)}")]  // Regex
```

---

## 📦 Entity Framework Core

### DbContext
```csharp
public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) 
        : base(options) { }
    
    public DbSet<Item> Items { get; set; }
    public DbSet<Category> Categories { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Item>()
            .HasOne(i => i.Category)
            .WithMany(c => c.Items)
            .HasForeignKey(i => i.CategoryId);
    }
}
```

### Repository Pattern
```csharp
// Interface
public interface IRepository<T>
{
    Task<T?> GetByIdAsync(int id);
    Task<List<T>> GetAllAsync();
    Task<T> AddAsync(T entity);
    Task UpdateAsync(T entity);
    Task DeleteAsync(int id);
}

// Implementation
public class Repository<T> : IRepository<T> where T : class
{
    private readonly AppDbContext _context;
    private readonly DbSet<T> _dbSet;
    
    public Repository(AppDbContext context)
    {
        _context = context;
        _dbSet = context.Set<T>();
    }
    
    public async Task<T?> GetByIdAsync(int id) 
        => await _dbSet.FindAsync(id);
    
    public async Task<List<T>> GetAllAsync() 
        => await _dbSet.ToListAsync();
    
    public async Task<T> AddAsync(T entity)
    {
        await _dbSet.AddAsync(entity);
        await _context.SaveChangesAsync();
        return entity;
    }
    
    public async Task UpdateAsync(T entity)
    {
        _dbSet.Update(entity);
        await _context.SaveChangesAsync();
    }
    
    public async Task DeleteAsync(int id)
    {
        var entity = await GetByIdAsync(id);
        if (entity != null)
        {
            _dbSet.Remove(entity);
            await _context.SaveChangesAsync();
        }
    }
}
```

---

## 🔐 JWT Quick Setup

### appsettings.json
```json
{
  "Jwt": {
    "Key": "YourSuperSecretKeyAtLeast32Chars!",
    "Issuer": "https://yourapp.com",
    "Audience": "https://yourapi.com"
  }
}
```

### Program.cs
```csharp
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = builder.Configuration["Jwt:Issuer"],
            ValidAudience = builder.Configuration["Jwt:Audience"],
            IssuerSigningKey = new SymmetricSecurityKey(
                Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"]!))
        };
    });

// Middleware order
app.UseAuthentication();
app.UseAuthorization();
```

### Protect Endpoints
```csharp
[Authorize]                        // Requires authentication
[Authorize(Roles = "Admin")]       // Requires Admin role
[AllowAnonymous]                   // Allows unauthenticated
```

---

## ⚡ Async Best Practices

```csharp
// ✅ Correct
public async Task<IActionResult> Get()
{
    var data = await _service.GetAsync();
    return Ok(data);
}

// ❌ Wrong - Blocking
public IActionResult Get()
{
    var data = _service.GetAsync().Result;  // DEADLOCK RISK!
    return Ok(data);
}

// ❌ Wrong - async void
public async void Get()  // Can't await, exceptions lost
{
    await _service.GetAsync();
}
```

---

## 🔄 Relationship Patterns

### One-to-Many
```csharp
// Parent
public class Category
{
    public int Id { get; set; }
    public string Name { get; set; }
    public ICollection<Item> Items { get; set; }  // Collection
}

// Child
public class Item
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int CategoryId { get; set; }           // FK
    [JsonIgnore]
    public Category? Category { get; set; }        // Navigation
}
```

### Eager Loading
```csharp
var items = await _context.Items
    .Include(i => i.Category)
    .ToListAsync();
```

---

## 📋 Common Patterns

### Return Types
```csharp
IEnumerable<T>          // Simple collection
IActionResult           // Full control, no type safety
ActionResult<T>         // Best: type safety + status codes
Task<ActionResult<T>>   // Async best practice
```

### Configuration Access
```csharp
// In Program.cs
var connString = builder.Configuration.GetConnectionString("Default");

// Via DI
public class MyService
{
    public MyService(IConfiguration config)
    {
        var value = config["MySetting"];
    }
}
```

### Model Binding
```csharp
// Route: /api/items/5
[HttpGet("{id}")]
public IActionResult Get([FromRoute] int id)

// Query: /api/items?page=1&size=10
[HttpGet]
public IActionResult Get([FromQuery] int page, [FromQuery] int size)

// Body: POST with JSON
[HttpPost]
public IActionResult Create([FromBody] Item item)
```

---

## 🎯 Key Concepts Summary

| Concept | Quick Description |
|---------|-------------------|
| **REST** | Stateless, resource-based API design |
| **[ApiController]** | Auto validation, binding inference |
| **ControllerBase** | API controller (no views) |
| **DbContext** | EF Core database session |
| **DbSet<T>** | Represents a table |
| **Include()** | Eager loading (JOIN) |
| **SaveChangesAsync()** | Persist changes to DB |
| **JWT** | Token-based authentication |
| **Middleware** | Request/response pipeline |
| **DI Container** | Manages object lifetimes |

---

## 📚 Interview Quick Answers

**REST vs SOAP?**
> REST is lightweight (JSON over HTTP), SOAP is protocol-heavy (XML with strict standards).

**Why Scoped for DbContext?**
> One context per request prevents tracking conflicts and ensures proper disposal.

**PUT vs PATCH?**
> PUT replaces entire resource, PATCH updates specific fields.

**Why async/await?**
> Frees threads during I/O, improving scalability without blocking.

**[ApiController] benefits?**
> Auto validation, binding inference, problem details for errors.

---

*This completes the ASP.NET Core Web API study notes!*
