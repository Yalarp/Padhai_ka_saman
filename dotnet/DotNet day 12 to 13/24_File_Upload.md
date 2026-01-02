# File Upload in ASP.NET Core MVC

## Table of Contents
1. [Introduction](#1-introduction)
2. [IFormFile Interface](#2-iformfile-interface)
3. [Single File Upload](#3-single-file-upload)
4. [Multiple File Upload](#4-multiple-file-upload)
5. [File Validation](#5-file-validation)
6. [Security Considerations](#6-security-considerations)
7. [Best Practices](#7-best-practices)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

### What is File Upload?
File upload in ASP.NET Core MVC uses the `IFormFile` interface to receive files sent via HTTP POST with `multipart/form-data` encoding.

### Upload Flow

```mermaid
flowchart LR
    A[Browser] -->|"multipart/form-data"| B[Controller]
    B -->|"IFormFile"| C[Validate]
    C -->|"Valid"| D[Generate Unique Name]
    D --> E[Save to wwwroot/uploads]
    E --> F[Return Success]
    C -->|"Invalid"| G[Return Error]
```

### Key Requirements
| Requirement | Value |
|------------|-------|
| Form encoding | `enctype="multipart/form-data"` |
| HTTP method | POST |
| Parameter type | `IFormFile` or `List<IFormFile>` |

---

## 2. IFormFile Interface

### Properties

| Property | Type | Description |
|----------|------|-------------|
| `FileName` | string | Original filename (from client) |
| `Name` | string | Form field name |
| `Length` | long | File size in bytes |
| `ContentType` | string | MIME type (e.g., "image/jpeg") |
| `ContentDisposition` | string | Content-Disposition header value |
| `Headers` | IHeaderDictionary | Request headers |

### Methods

| Method | Return | Description |
|--------|--------|-------------|
| `CopyTo(Stream)` | void | Synchronous copy to stream |
| `CopyToAsync(Stream)` | Task | Asynchronous copy to stream |
| `OpenReadStream()` | Stream | Open read-only stream |

---

## 3. Single File Upload

### Model

```csharp
public class FileUploadViewModel
{
    [Required(ErrorMessage = "Please select a file")]
    [Display(Name = "Select File")]
    public IFormFile File { get; set; }
    
    public string Description { get; set; }
}
```

### Controller

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;

public class FileController : Controller
{
    private readonly IWebHostEnvironment _environment;

    public FileController(IWebHostEnvironment environment)
    {
        _environment = environment;
    }

    [HttpGet]
    public IActionResult Upload()
    {
        return View();
    }

    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Upload(FileUploadViewModel model)
    {
        if (ModelState.IsValid && model.File != null && model.File.Length > 0)
        {
            // Generate unique filename to prevent overwriting
            string uniqueFileName = Guid.NewGuid().ToString() + "_" + 
                Path.GetFileName(model.File.FileName);
            
            // Get uploads folder path
            string uploadsFolder = Path.Combine(_environment.WebRootPath, "uploads");
            
            // Ensure directory exists
            if (!Directory.Exists(uploadsFolder))
            {
                Directory.CreateDirectory(uploadsFolder);
            }
            
            // Full file path
            string filePath = Path.Combine(uploadsFolder, uniqueFileName);
            
            // Save file asynchronously
            using (var fileStream = new FileStream(filePath, FileMode.Create))
            {
                await model.File.CopyToAsync(fileStream);
            }
            
            TempData["Success"] = $"File '{model.File.FileName}' uploaded successfully!";
            return RedirectToAction(nameof(Upload));
        }
        
        return View(model);
    }
}
```

### Line-by-Line Analysis

| Line | Code | Explanation |
|------|------|-------------|
| `IWebHostEnvironment` | Constructor injection | Provides WebRootPath (wwwroot) |
| `Guid.NewGuid().ToString()` | Unique prefix | Prevents filename conflicts |
| `Path.GetFileName(...)` | Safety | Removes path manipulation |
| `Path.Combine(...)` | Platform-safe path | Works on Windows/Linux |
| `CopyToAsync(fileStream)` | Async save | Non-blocking file I/O |

### View

```cshtml
@model FileUploadViewModel

<h2>Upload File</h2>

@if (TempData["Success"] != null)
{
    <div class="alert alert-success">@TempData["Success"]</div>
}

<form asp-action="Upload" 
      method="post" 
      enctype="multipart/form-data">
    
    <div asp-validation-summary="ModelOnly" class="text-danger"></div>
    
    <div class="form-group">
        <label asp-for="Description"></label>
        <input asp-for="Description" class="form-control" />
    </div>
    
    <div class="form-group">
        <label asp-for="File"></label>
        <input asp-for="File" class="form-control" type="file" />
        <span asp-validation-for="File" class="text-danger"></span>
    </div>
    
    <button type="submit" class="btn btn-primary">Upload</button>
</form>
```

> ⚠️ **Important:** `enctype="multipart/form-data"` is REQUIRED for file uploads!

---

## 4. Multiple File Upload

### Model

```csharp
public class MultiFileUploadViewModel
{
    [Display(Name = "Select Files")]
    public List<IFormFile> Files { get; set; }
}
```

### Controller

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> UploadMultiple(MultiFileUploadViewModel model)
{
    if (model.Files != null && model.Files.Count > 0)
    {
        string uploadsFolder = Path.Combine(_environment.WebRootPath, "uploads");
        
        if (!Directory.Exists(uploadsFolder))
        {
            Directory.CreateDirectory(uploadsFolder);
        }
        
        int successCount = 0;
        
        foreach (var file in model.Files)
        {
            if (file.Length > 0)
            {
                string uniqueFileName = $"{Guid.NewGuid()}_{Path.GetFileName(file.FileName)}";
                string filePath = Path.Combine(uploadsFolder, uniqueFileName);
                
                using var stream = new FileStream(filePath, FileMode.Create);
                await file.CopyToAsync(stream);
                
                successCount++;
            }
        }
        
        TempData["Success"] = $"{successCount} file(s) uploaded successfully!";
        return RedirectToAction(nameof(Upload));
    }
    
    ModelState.AddModelError("", "Please select at least one file");
    return View(model);
}
```

### View

```cshtml
@model MultiFileUploadViewModel

<form asp-action="UploadMultiple" method="post" enctype="multipart/form-data">
    <div class="form-group">
        <label asp-for="Files"></label>
        <input asp-for="Files" class="form-control" type="file" multiple />
    </div>
    
    <button type="submit" class="btn btn-primary">Upload All</button>
</form>
```

> **Note:** Add `multiple` attribute to allow selecting multiple files.

---

## 5. File Validation

### Custom Validation Attributes

```csharp
using System.ComponentModel.DataAnnotations;

// Validate allowed file extensions
public class AllowedExtensionsAttribute : ValidationAttribute
{
    private readonly string[] _extensions;

    public AllowedExtensionsAttribute(string[] extensions)
    {
        _extensions = extensions;
    }

    protected override ValidationResult IsValid(object value, ValidationContext context)
    {
        if (value is IFormFile file)
        {
            var extension = Path.GetExtension(file.FileName).ToLowerInvariant();
            
            if (!_extensions.Contains(extension))
            {
                return new ValidationResult(
                    $"Only {string.Join(", ", _extensions)} files are allowed");
            }
        }
        return ValidationResult.Success;
    }
}

// Validate maximum file size
public class MaxFileSizeAttribute : ValidationAttribute
{
    private readonly int _maxFileSize;

    public MaxFileSizeAttribute(int maxFileSize)
    {
        _maxFileSize = maxFileSize;
    }

    protected override ValidationResult IsValid(object value, ValidationContext context)
    {
        if (value is IFormFile file)
        {
            if (file.Length > _maxFileSize)
            {
                return new ValidationResult(
                    $"Maximum allowed file size is {_maxFileSize / 1024 / 1024} MB");
            }
        }
        return ValidationResult.Success;
    }
}
```

### Using Validation Attributes

```csharp
public class FileUploadViewModel
{
    [Required(ErrorMessage = "Please select a file")]
    [AllowedExtensions(new[] { ".jpg", ".jpeg", ".png", ".gif", ".pdf" })]
    [MaxFileSize(5 * 1024 * 1024)]  // 5 MB
    [Display(Name = "Select File")]
    public IFormFile File { get; set; }
}
```

### Manual Validation in Controller

```csharp
[HttpPost]
public async Task<IActionResult> Upload(FileUploadViewModel model)
{
    if (model.File != null)
    {
        // Check file size (5 MB limit)
        if (model.File.Length > 5 * 1024 * 1024)
        {
            ModelState.AddModelError("File", "File size cannot exceed 5 MB");
        }
        
        // Check file extension
        var allowedExtensions = new[] { ".jpg", ".png", ".pdf" };
        var extension = Path.GetExtension(model.File.FileName).ToLowerInvariant();
        
        if (!allowedExtensions.Contains(extension))
        {
            ModelState.AddModelError("File", "Invalid file type");
        }
        
        // Check content type (more secure)
        var allowedContentTypes = new[] { "image/jpeg", "image/png", "application/pdf" };
        
        if (!allowedContentTypes.Contains(model.File.ContentType))
        {
            ModelState.AddModelError("File", "Invalid content type");
        }
    }
    
    if (ModelState.IsValid)
    {
        // Save file...
    }
    
    return View(model);
}
```

---

## 6. Security Considerations

### Threats and Mitigations

| Threat | Mitigation |
|--------|------------|
| **Filename manipulation** | Use `Path.GetFileName()`, never use client filename in path |
| **Path traversal** | Never include user input in file paths |
| **Executable uploads** | Whitelist allowed extensions |
| **Content-Type spoofing** | Validate both extension AND content type |
| **Large file DoS** | Set maximum file size limits |
| **Overwriting files** | Generate unique filenames (GUID) |

### Secure Upload Pattern

```csharp
[HttpPost]
public async Task<IActionResult> SecureUpload(IFormFile file)
{
    // 1. Validate file exists
    if (file == null || file.Length == 0)
        return BadRequest("No file uploaded");
    
    // 2. Validate file size
    const long maxSize = 5 * 1024 * 1024; // 5 MB
    if (file.Length > maxSize)
        return BadRequest("File too large");
    
    // 3. Validate extension
    var allowedExtensions = new[] { ".jpg", ".png", ".pdf" };
    var extension = Path.GetExtension(file.FileName).ToLowerInvariant();
    if (!allowedExtensions.Contains(extension))
        return BadRequest("Invalid file type");
    
    // 4. Generate safe filename (GUID + safe extension)
    var safeFileName = $"{Guid.NewGuid()}{extension}";
    
    // 5. Store outside wwwroot for private files
    var privatePath = Path.Combine(_environment.ContentRootPath, "PrivateUploads");
    
    // 6. Ensure directory exists
    Directory.CreateDirectory(privatePath);
    
    // 7. Save file
    var filePath = Path.Combine(privatePath, safeFileName);
    using var stream = new FileStream(filePath, FileMode.Create);
    await file.CopyToAsync(stream);
    
    return Ok(new { fileName = safeFileName });
}
```

### Serving Private Files

```csharp
public IActionResult Download(string fileName)
{
    // Validate filename (no path characters)
    if (fileName.IndexOfAny(Path.GetInvalidFileNameChars()) >= 0)
        return BadRequest();
    
    var filePath = Path.Combine(_environment.ContentRootPath, "PrivateUploads", fileName);
    
    if (!System.IO.File.Exists(filePath))
        return NotFound();
    
    var contentType = GetContentType(fileName);
    return PhysicalFile(filePath, contentType, fileName);
}
```

---

## 7. Best Practices

### DO ✅

| Practice | Reason |
|----------|--------|
| Generate unique filenames | Prevent overwriting |
| Validate file type (extension + content type) | Security |
| Set max file size | Prevent DoS |
| Use async methods | Performance |
| Store private files outside wwwroot | Security |
| Use `Path.GetFileName()` | Prevent path traversal |

### DON'T ❌

| Practice | Reason |
|----------|--------|
| Don't trust client filename | Can be manipulated |
| Don't allow executable uploads | Security risk |
| Don't use client filename in paths | Path traversal attack |
| Don't skip validation | Security |
| Don't use synchronous file I/O | Blocks threads |

---

## 8. Interview Questions

1. **What interface represents uploaded files in ASP.NET Core?**
   - `IFormFile` interface.

2. **What form encoding is required for file upload?**
   - `enctype="multipart/form-data"` on the form tag.

3. **How do you prevent filename conflicts?**
   - Prefix with GUID: `Guid.NewGuid().ToString() + "_" + filename`

4. **Why use `Path.GetFileName()` on uploaded files?**
   - Prevents path traversal attacks by removing any path information.

5. **Where should private uploaded files be stored?**
   - Outside wwwroot, with controller actions to serve files with authorization.

6. **How do you validate file type securely?**
   - Validate both file extension AND ContentType property.

7. **What is the difference between CopyTo and CopyToAsync?**
   - CopyTo is synchronous (blocks thread), CopyToAsync is asynchronous (non-blocking).

8. **How do you upload multiple files?**
   - Use `List<IFormFile>` as parameter and add `multiple` attribute to input.

9. **What is IWebHostEnvironment used for?**
   - Provides WebRootPath (wwwroot) and ContentRootPath for file storage.

10. **How do you set maximum file size?**
    - Custom validation attribute or check `file.Length` in controller.
