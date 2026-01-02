# ASP.NET Core MVC Complete Cheat Sheet

## Table of Contents
1. [Project Structure](#1-project-structure)
2. [Program.cs Configuration](#2-programcs-configuration)
3. [Controller Patterns](#3-controller-patterns)
4. [Model and Validation](#4-model-and-validation)
5. [View Patterns](#5-view-patterns)
6. [Tag Helpers](#6-tag-helpers)
7. [State Management](#7-state-management)
8. [Caching](#8-caching)
9. [Session Configuration](#9-session-configuration)
10. [Cookie Configuration](#10-cookie-configuration)
11. [Error Handling](#11-error-handling)
12. [Action Results](#12-action-results)

---

## 1. Project Structure

```
ProjectName/
├── Controllers/
│   ├── HomeController.cs
│   └── EmployeeController.cs
├── Models/
│   ├── Employee.cs
│   └── Department.cs
├── ViewModels/
│   └── EmployeeViewModel.cs
├── Services/
│   ├── IEmployeeService.cs
│   └── SqlEmployeeService.cs
├── Repository/
│   └── AppDbContext.cs
├── Views/
│   ├── _ViewImports.cshtml
│   ├── _ViewStart.cshtml
│   ├── Shared/
│   │   ├── _Layout.cshtml
│   │   ├── _ValidationScriptsPartial.cshtml
│   │   └── _LoginPartial.cshtml
│   └── Home/
│       └── Index.cshtml
├── wwwroot/
│   ├── css/
│   ├── js/
│   ├── lib/
│   └── uploads/
├── appsettings.json
└── Program.cs
```

---

## 2. Program.cs Configuration

```csharp
var builder = WebApplication.CreateBuilder(args);

// === SERVICES ===
builder.Services.AddControllersWithViews(options =>
{
    // Cache Profiles
    options.CacheProfiles.Add("Hourly", new CacheProfile { Duration = 3600 });
    options.CacheProfiles.Add("NoCache", new CacheProfile 
    { 
        Location = ResponseCacheLocation.None, 
        NoStore = true 
    });
});

// Database
builder.Services.AddDbContextPool<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("Default")));

// Dependency Injection
builder.Services.AddScoped<IEmployeeService, SqlEmployeeService>();

// Caching
builder.Services.AddMemoryCache();
builder.Services.AddResponseCaching();

// Session
builder.Services.AddDistributedMemoryCache();  // or AddDistributedSqlServerCache()
builder.Services.AddSession(options =>
{
    options.IdleTimeout = TimeSpan.FromMinutes(30);
    options.Cookie.HttpOnly = true;
    options.Cookie.IsEssential = true;
});

// For accessing HttpContext in views
builder.Services.AddSingleton<IHttpContextAccessor, HttpContextAccessor>();

var app = builder.Build();

// === MIDDLEWARE PIPELINE ===
if (app.Environment.IsDevelopment())
    app.UseDeveloperExceptionPage();
else
    app.UseExceptionHandler("/Home/Error");

app.UseStatusCodePagesWithReExecute("/Error/{0}");
app.UseHttpsRedirection();
app.UseStaticFiles();
app.UseResponseCaching();
app.UseRouting();
app.UseSession();
app.UseAuthorization();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

app.Run();
```

---

## 3. Controller Patterns

```csharp
public class EmployeeController : Controller
{
    private readonly IEmployeeService _service;
    private readonly IMemoryCache _cache;
    
    // Constructor Injection
    public EmployeeController(IEmployeeService service, IMemoryCache cache)
    {
        _service = service;
        _cache = cache;
    }
    
    // GET: /Employee
    public ActionResult Index() => View(_service.GetAll());
    
    // GET: /Employee/Details/5
    public ActionResult Details(int id)
    {
        var emp = _service.Get(id);
        return emp == null ? NotFound() : View(emp);
    }
    
    // GET: /Employee/Create
    [HttpGet]
    public ActionResult Create()
    {
        LoadDropdowns();
        return View();
    }
    
    // POST: /Employee/Create - PRG Pattern
    [HttpPost, ValidateAntiForgeryToken]
    public ActionResult Create(Employee emp)
    {
        if (ModelState.IsValid)
        {
            _service.Add(emp);
            _cache.Remove("EmployeeList");  // Invalidate cache
            TempData["Success"] = "Created!";
            return RedirectToAction(nameof(Index));
        }
        LoadDropdowns();
        return View(emp);
    }
    
    // Method Injection
    public IActionResult Display([FromServices] IEmployeeService es)
    {
        return Json(es.GetAll());
    }
    
    private void LoadDropdowns() => 
        ViewBag.Departments = new SelectList(_service.GetDepartments(), "Id", "Name");
}
```

---

## 4. Model and Validation

```csharp
public class Employee
{
    public int Id { get; set; }
    
    [Required(ErrorMessage = "{0} is required")]
    [StringLength(50, MinimumLength = 2)]
    [Display(Name = "Full Name")]
    public string Name { get; set; }
    
    [Required, EmailAddress]
    public string Email { get; set; }
    
    [Range(18, 65, ErrorMessage = "Age must be 18-65")]
    public int Age { get; set; }
    
    [Required(ErrorMessage = "Select a department")]
    public int? DepartmentId { get; set; }  // Nullable for dropdown validation
    
    [DataType(DataType.Password)]
    [MinLength(8)]
    public string Password { get; set; }
    
    [Compare("Password", ErrorMessage = "Passwords don't match")]
    public string ConfirmPassword { get; set; }
    
    public Department Department { get; set; }
}
```

### Validation Attributes Quick Reference

| Attribute | Usage |
|-----------|-------|
| `[Required]` | Field required |
| `[StringLength(max, MinimumLength = min)]` | Length range |
| `[Range(min, max)]` | Numeric range |
| `[RegularExpression(pattern)]` | Regex match |
| `[Compare("Property")]` | Match another property |
| `[EmailAddress]` | Email format |
| `[Phone]` | Phone format |
| `[Url]` | URL format |
| `[DataType(DataType.X)]` | Input type hint |
| `[Display(Name = "X")]` | Label text |

---

## 5. View Patterns

### _ViewImports.cshtml
```cshtml
@using MyApp.Models
@using MyApp.ViewModels
@using Microsoft.AspNetCore.Http
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@inject IHttpContextAccessor HttpContextAccessor
```

### Form Pattern
```cshtml
@model Employee

<form asp-action="Create" method="post">
    <div asp-validation-summary="ModelOnly" class="text-danger"></div>
    
    <div class="mb-3">
        <label asp-for="Name" class="form-label"></label>
        <input asp-for="Name" class="form-control" />
        <span asp-validation-for="Name" class="text-danger"></span>
    </div>
    
    <div class="mb-3">
        <label asp-for="DepartmentId"></label>
        <select asp-for="DepartmentId" asp-items="ViewBag.Departments" class="form-control">
            <option value="">Select...</option>
        </select>
        <span asp-validation-for="DepartmentId" class="text-danger"></span>
    </div>
    
    <button type="submit" class="btn btn-primary">Submit</button>
</form>

@section Scripts {
    @{await Html.RenderPartialAsync("_ValidationScriptsPartial");}
}
```

---

## 6. Tag Helpers

### Common Tag Helpers

| Tag Helper | Usage | Example |
|------------|-------|---------|
| `asp-controller` | Controller name | `<a asp-controller="Home">` |
| `asp-action` | Action name | `<a asp-action="Index">` |
| `asp-route-{param}` | Route parameter | `<a asp-route-id="5">` |
| `asp-for` | Model binding | `<input asp-for="Name" />` |
| `asp-items` | Dropdown items | `<select asp-items="ViewBag.List">` |
| `asp-validation-for` | Validation message | `<span asp-validation-for="Name">` |
| `asp-validation-summary` | All errors | `<div asp-validation-summary="All">` |
| `asp-append-version` | Cache busting | `<img asp-append-version="true">` |

### Environment Tag Helper
```cshtml
<environment include="Development">
    <link href="~/css/site.css" rel="stylesheet" />
</environment>
<environment exclude="Development">
    <link href="https://cdn.example.com/css/site.min.css" rel="stylesheet"
          asp-fallback-href="~/css/site.min.css" />
</environment>
```

---

## 7. State Management

| Type | Set | Get |
|------|-----|-----|
| **ViewBag** | `ViewBag.X = val` | `@ViewBag.X` |
| **ViewData** | `ViewData["X"] = val` | `@ViewData["X"]` |
| **TempData** | `TempData["X"] = val` | `TempData["X"]` |
| **TempData Peek** | - | `TempData.Peek("X")` |
| **TempData Keep** | `TempData.Keep("X")` | - |
| **Session** | `Session.SetString("X", val)` | `Session.GetString("X")` |
| **Session Int** | `Session.SetInt32("X", val)` | `Session.GetInt32("X")` |
| **Cookie** | `Response.Cookies.Append(k, v, opts)` | `Request.Cookies[k]` |

---

## 8. Caching

### In-Memory Cache
```csharp
var data = _cache.GetOrCreate("key", entry => {
    entry.SlidingExpiration = TimeSpan.FromMinutes(5);
    entry.AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(30);
    return GetExpensiveData();
});

// Remove cache
_cache.Remove("key");
```

### Response Cache
```csharp
[ResponseCache(Duration = 60)]
public IActionResult Index() => View();

[ResponseCache(CacheProfileName = "Hourly")]
public IActionResult About() => View();

[ResponseCache(Location = ResponseCacheLocation.None, NoStore = true)]
public IActionResult Login() => View();
```

### Cache Tag Helper
```cshtml
<cache expires-after="@TimeSpan.FromMinutes(5)" vary-by-user="true">
    <p>Cached at: @DateTime.Now</p>
</cache>

<cache expires-sliding="@TimeSpan.FromMinutes(2)" vary-by-query="page,sort">
    @await Html.PartialAsync("_ProductList")
</cache>
```

---

## 9. Session Configuration

```csharp
// In-Memory (Development)
builder.Services.AddDistributedMemoryCache();

// SQL Server (Production)
builder.Services.AddDistributedSqlServerCache(options =>
{
    options.ConnectionString = connectionString;
    options.SchemaName = "dbo";
    options.TableName = "MySessions";
});

// Session Options
builder.Services.AddSession(options =>
{
    options.IdleTimeout = TimeSpan.FromMinutes(30);
    options.Cookie.Name = ".MyApp.Session";
    options.Cookie.HttpOnly = true;
    options.Cookie.IsEssential = true;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
});
```

---

## 10. Cookie Configuration

```csharp
CookieOptions options = new CookieOptions
{
    Domain = "localhost",
    Path = "/",
    Expires = DateTime.Now.AddDays(7),
    Secure = true,           // HTTPS only
    HttpOnly = true,         // No JavaScript access
    IsEssential = true,      // GDPR
    SameSite = SameSiteMode.Strict  // CSRF protection
};

Response.Cookies.Append("userId", "12345", options);
Response.Cookies.Delete("userId", options);
```

---

## 11. Error Handling

```csharp
// Program.cs
if (app.Environment.IsDevelopment())
    app.UseDeveloperExceptionPage();
else
    app.UseExceptionHandler("/Error");

app.UseStatusCodePagesWithReExecute("/Error/{0}");

// Controller
public IActionResult Error(int statusCode)
{
    switch (statusCode)
    {
        case 404: ViewBag.Message = "Page not found"; break;
        case 500: ViewBag.Message = "Server error"; break;
    }
    return View();
}
```

---

## 12. Action Results

| Result | Method | Status |
|--------|--------|--------|
| ViewResult | `View()` | 200 |
| JsonResult | `Json(data)` | 200 |
| RedirectResult | `Redirect(url)` | 302 |
| RedirectToActionResult | `RedirectToAction()` | 302 |
| ContentResult | `Content(str)` | 200 |
| FileResult | `File(bytes, type)` | 200 |
| OkResult | `Ok()` | 200 |
| BadRequestResult | `BadRequest()` | 400 |
| UnauthorizedResult | `Unauthorized()` | 401 |
| NotFoundResult | `NotFound()` | 404 |
| StatusCodeResult | `StatusCode(code)` | Custom |

---

## HTTP Methods

| Method | CRUD | Attribute | Use |
|--------|------|-----------|-----|
| GET | Read | `[HttpGet]` | Retrieve data |
| POST | Create | `[HttpPost]` | Submit forms |
| PUT | Update | `[HttpPut]` | Full update |
| PATCH | Partial | `[HttpPatch]` | Partial update |
| DELETE | Delete | `[HttpDelete]` | Remove data |
