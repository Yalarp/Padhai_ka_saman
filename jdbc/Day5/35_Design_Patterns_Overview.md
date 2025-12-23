# Design Patterns in Jakarta EE - Overview

## Table of Contents
1. [Introduction](#1-introduction)
2. [Abstract Factory Pattern](#2-abstract-factory-pattern)
3. [Builder Pattern](#3-builder-pattern)
4. [Bridge Pattern](#4-bridge-pattern)
5. [Proxy Pattern](#5-proxy-pattern)
6. [Patterns Comparison](#6-patterns-comparison)
7. [Interview Questions](#7-interview-questions)

---

## 1. Introduction

Design patterns are reusable solutions to common software design problems. Understanding these patterns is essential for Jakarta EE development and technical interviews.

---

## 2. Abstract Factory Pattern

### Definition

Abstract Factory provides an interface for creating **families of related objects** without specifying their concrete classes. It's a "factory of factories."

### When to Use

- Need to create related product families
- System should be independent of object creation
- Want to provide a library of products

### Visual Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      ABSTRACT FACTORY PATTERN                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│                   ┌─────────────────────┐                                  │
│                   │   AbstractFactory   │                                  │
│                   │   (Interface)       │                                  │
│                   │   + createProductA()│                                  │
│                   │   + createProductB()│                                  │
│                   └─────────┬───────────┘                                  │
│                             │                                               │
│              ┌──────────────┼──────────────┐                               │
│              │              │              │                               │
│              ▼              ▼              ▼                               │
│     ┌────────────┐  ┌────────────┐  ┌────────────┐                        │
│     │ Factory1   │  │ Factory2   │  │ Factory3   │                        │
│     │(Windows)   │  │(MacOS)     │  │(Linux)     │                        │
│     └──────┬─────┘  └──────┬─────┘  └──────┬─────┘                        │
│            │               │               │                               │
│            ▼               ▼               ▼                               │
│     ┌────────────┐  ┌────────────┐  ┌────────────┐                        │
│     │WinButton   │  │MacButton   │  │LinuxButton │                        │
│     │WinTextbox  │  │MacTextbox  │  │LinuxTextbox│                        │
│     └────────────┘  └────────────┘  └────────────┘                        │
│                                                                             │
│  Client uses AbstractFactory → gets family of compatible products          │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Code Example

```java
// Abstract Products
interface Button {
    void render();
}

interface TextBox {
    void render();
}

// Concrete Products - Windows
class WindowsButton implements Button {
    public void render() {
        System.out.println("Windows-style button");
    }
}

class WindowsTextBox implements TextBox {
    public void render() {
        System.out.println("Windows-style textbox");
    }
}

// Concrete Products - Mac
class MacButton implements Button {
    public void render() {
        System.out.println("Mac-style button");
    }
}

class MacTextBox implements TextBox {
    public void render() {
        System.out.println("Mac-style textbox");
    }
}

// Abstract Factory
interface GUIFactory {
    Button createButton();
    TextBox createTextBox();
}

// Concrete Factories
class WindowsFactory implements GUIFactory {
    public Button createButton() {
        return new WindowsButton();
    }
    public TextBox createTextBox() {
        return new WindowsTextBox();
    }
}

class MacFactory implements GUIFactory {
    public Button createButton() {
        return new MacButton();
    }
    public TextBox createTextBox() {
        return new MacTextBox();
    }
}

// Client
class Application {
    private Button button;
    private TextBox textBox;
    
    public Application(GUIFactory factory) {
        button = factory.createButton();
        textBox = factory.createTextBox();
    }
    
    public void render() {
        button.render();
        textBox.render();
    }
}
```

---

## 3. Builder Pattern

### Definition

Builder separates the **construction of complex objects** from their representation, allowing the same construction process to create different representations.

### When to Use

- Object has many optional parameters
- Want to avoid "telescoping constructor" problem
- Need to create immutable objects with many fields
- Construction involves multiple steps

### Visual Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         BUILDER PATTERN                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Traditional Constructor (Problem):                                         │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ new User(name, email, phone, age, address, city, state...)  │           │
│  │ // Hard to read, easy to make mistakes                       │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
│  Builder Pattern (Solution):                                                │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │ User user = new User.Builder()                               │           │
│  │     .name("John")                                            │           │
│  │     .email("john@mail.com")                                  │           │
│  │     .phone("123456")    // Optional                          │           │
│  │     .age(25)            // Optional                          │           │
│  │     .build();           // Creates the object                │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
│  Visual Flow:                                                               │
│  ┌────────┐    ┌────────┐    ┌────────┐    ┌─────────┐                    │
│  │ Client │───►│ Builder│───►│ Builder│───►│ Product │                    │
│  │        │    │.name() │    │.email()│    │ (User)  │                    │
│  └────────┘    └────────┘    └────────┘    └─────────┘                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Code Example

```java
public class User {
    // Required fields
    private final String name;
    private final String email;
    
    // Optional fields
    private final String phone;
    private final int age;
    private final String address;
    
    // Private constructor - can only be built via Builder
    private User(Builder builder) {
        this.name = builder.name;
        this.email = builder.email;
        this.phone = builder.phone;
        this.age = builder.age;
        this.address = builder.address;
    }
    
    // Static inner Builder class
    public static class Builder {
        // Required
        private final String name;
        private final String email;
        
        // Optional with defaults
        private String phone = "";
        private int age = 0;
        private String address = "";
        
        // Required fields in constructor
        public Builder(String name, String email) {
            this.name = name;
            this.email = email;
        }
        
        // Methods return Builder for chaining
        public Builder phone(String phone) {
            this.phone = phone;
            return this;
        }
        
        public Builder age(int age) {
            this.age = age;
            return this;
        }
        
        public Builder address(String address) {
            this.address = address;
            return this;
        }
        
        // Build method creates the object
        public User build() {
            return new User(this);
        }
    }
    
    // Getters
    public String getName() { return name; }
    public String getEmail() { return email; }
    public String getPhone() { return phone; }
    public int getAge() { return age; }
    public String getAddress() { return address; }
}

// Usage
User user = new User.Builder("John", "john@mail.com")
    .phone("123-456-7890")
    .age(25)
    .build();
```

---

## 4. Bridge Pattern

### Definition

Bridge decouples an **abstraction from its implementation** so that the two can vary independently. It uses composition instead of inheritance.

### When to Use

- Want to avoid permanent binding between abstraction and implementation
- Both abstraction and implementation should be extensible
- Changes in implementation shouldn't affect clients

### Visual Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          BRIDGE PATTERN                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│           WITHOUT Bridge (Explosion of classes):                            │
│           ┌────────────────────────────────────┐                           │
│           │              Shape                  │                           │
│           └────────────────┬───────────────────┘                           │
│                ┌───────────┼───────────┐                                   │
│                ▼           ▼           ▼                                   │
│           ┌────────┐  ┌────────┐  ┌────────┐                              │
│           │RedCircle│ │BlueCircle│ │RedSquare│ ...                         │
│           └────────┘  └────────┘  └────────┘                              │
│           (m shapes × n colors = m×n classes!)                             │
│                                                                             │
│           ─────────────────────────────────────────                        │
│                                                                             │
│           WITH Bridge (Separate hierarchies):                              │
│                                                                             │
│     Abstraction                        Implementation                       │
│     ┌────────────┐                    ┌────────────┐                       │
│     │   Shape    │ ──────────────────►│   Color    │                       │
│     │  +draw()   │    has-a           │  +fill()   │                       │
│     └─────┬──────┘                    └─────┬──────┘                       │
│           │                                 │                              │
│      ┌────┴────┐                       ┌────┴────┐                         │
│      ▼         ▼                       ▼         ▼                         │
│  ┌────────┐ ┌────────┐            ┌────────┐ ┌────────┐                   │
│  │Circle  │ │Square  │            │  Red   │ │ Blue   │                   │
│  └────────┘ └────────┘            └────────┘ └────────┘                   │
│                                                                             │
│  (m shapes + n colors = m+n classes!)                                      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Code Example

```java
// Implementor interface
interface Color {
    void applyColor();
}

// Concrete Implementors
class RedColor implements Color {
    public void applyColor() {
        System.out.print("Red");
    }
}

class BlueColor implements Color {
    public void applyColor() {
        System.out.print("Blue");
    }
}

// Abstraction
abstract class Shape {
    protected Color color;  // Bridge to implementation
    
    public Shape(Color color) {
        this.color = color;
    }
    
    abstract void draw();
}

// Refined Abstractions
class Circle extends Shape {
    public Circle(Color color) {
        super(color);
    }
    
    public void draw() {
        System.out.print("Drawing ");
        color.applyColor();  // Use implementation
        System.out.println(" Circle");
    }
}

class Square extends Shape {
    public Square(Color color) {
        super(color);
    }
    
    public void draw() {
        System.out.print("Drawing ");
        color.applyColor();  // Use implementation
        System.out.println(" Square");
    }
}

// Usage
Shape redCircle = new Circle(new RedColor());
redCircle.draw();  // Drawing Red Circle

Shape blueSquare = new Square(new BlueColor());
blueSquare.draw();  // Drawing Blue Square
```

---

## 5. Proxy Pattern

### Definition

Proxy provides a **surrogate or placeholder** for another object to control access to it. Common in distributed systems like RMI.

### Types of Proxies

| Type | Purpose |
|------|---------|
| **Remote Proxy** | Represents object in different address space (RMI) |
| **Virtual Proxy** | Lazy loading of expensive objects |
| **Protection Proxy** | Access control |
| **Caching Proxy** | Cache results |

### Visual Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          PROXY PATTERN                                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│                      ┌────────────────┐                                    │
│                      │   Client       │                                    │
│                      └────────┬───────┘                                    │
│                               │                                            │
│                               ▼                                            │
│                      ┌────────────────┐                                    │
│                      │   Subject      │ ◄─── Interface                    │
│                      │ + request()    │                                    │
│                      └────────┬───────┘                                    │
│                               │                                            │
│              ┌────────────────┴────────────────┐                          │
│              │                                 │                          │
│              ▼                                 ▼                          │
│     ┌────────────────┐                ┌────────────────┐                  │
│     │     Proxy      │───────────────►│  RealSubject   │                  │
│     │  + request()   │   delegates    │  + request()   │                  │
│     │  (checks, logs,│   to           │  (actual work) │                  │
│     │   caches...)   │                │                │                  │
│     └────────────────┘                └────────────────┘                  │
│                                                                             │
│  RMI Example:                                                              │
│  ┌────────┐     ┌────────────┐     ┌─────────────────┐     ┌────────────┐│
│  │ Client │────►│ Stub       │────►│ Network         │────►│ Skeleton   ││
│  │        │     │ (Proxy)    │     │ (TCP/IP)        │     │ (Proxy)    ││
│  └────────┘     └────────────┘     └─────────────────┘     └─────┬──────┘│
│                                                                   │       │
│                                                                   ▼       │
│                                                           ┌────────────┐ │
│                                                           │ Real Object│ │
│                                                           └────────────┘ │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Code Example

```java
// Subject interface
interface Image {
    void display();
}

// Real Subject - expensive to create
class RealImage implements Image {
    private String filename;
    
    public RealImage(String filename) {
        this.filename = filename;
        loadFromDisk();  // Expensive operation
    }
    
    private void loadFromDisk() {
        System.out.println("Loading image: " + filename);
    }
    
    public void display() {
        System.out.println("Displaying: " + filename);
    }
}

// Proxy - lazy loading
class ImageProxy implements Image {
    private String filename;
    private RealImage realImage;  // Reference to real subject
    
    public ImageProxy(String filename) {
        this.filename = filename;
        // Don't load image yet!
    }
    
    public void display() {
        // Load only when needed (lazy loading)
        if (realImage == null) {
            realImage = new RealImage(filename);
        }
        realImage.display();
    }
}

// Usage
Image image = new ImageProxy("photo.jpg");  // Image not loaded
System.out.println("Image object created");

image.display();  // NOW image is loaded and displayed
image.display();  // Uses cached image (no reload)
```

---

## 6. Patterns Comparison

| Pattern | Type | Purpose |
|---------|------|---------|
| **Abstract Factory** | Creational | Create families of related objects |
| **Builder** | Creational | Build complex objects step by step |
| **Bridge** | Structural | Separate abstraction from implementation |
| **Proxy** | Structural | Control access to an object |

---

## 7. Interview Questions

### Basic Level

**Q1: What is the Abstract Factory pattern?**
> Abstract Factory provides an interface for creating families of related objects without specifying concrete classes. Example: Creating Windows or Mac UI components.

**Q2: What problem does the Builder pattern solve?**
> Builder solves the "telescoping constructor" problem where constructors have many parameters. It allows step-by-step object construction with readable, fluent API.

**Q3: What is the Proxy pattern?**
> Proxy provides a surrogate for another object. Used for lazy loading, access control, logging, or remote access (like RMI stubs).

### Intermediate Level

**Q4: How is Bridge different from Adapter?**
| Bridge | Adapter |
|--------|---------|
| Designed upfront | Applied to existing code |
| Both sides can vary | Fixes incompatibility |
| Uses composition | Uses inheritance or composition |
| Separates abstraction | Makes interfaces compatible |

**Q5: When would you use Builder vs Factory?**
| Builder | Factory |
|---------|---------|
| Complex objects | Simple objects |
| Many optional parameters | Few parameters |
| Step-by-step construction | Single-step creation |
| Returns same type | May return different types |

**Q6: How does RMI use the Proxy pattern?**
> RMI (Remote Method Invocation) uses:
> - **Stub (Client-side Proxy)**: Represents remote object locally
> - **Skeleton (Server-side Proxy)**: Receives calls and forwards to real object
> 
> Client calls Stub → Network → Skeleton → Real Object

### Advanced Level

**Q7: Implement a thread-safe Singleton with Builder pattern.**
```java
public class Configuration {
    private static volatile Configuration instance;
    private final String dbUrl;
    private final int maxConnections;
    
    private Configuration(Builder builder) {
        this.dbUrl = builder.dbUrl;
        this.maxConnections = builder.maxConnections;
    }
    
    public static class Builder {
        private String dbUrl;
        private int maxConnections = 10;
        
        public Builder dbUrl(String url) {
            this.dbUrl = url;
            return this;
        }
        
        public Builder maxConnections(int max) {
            this.maxConnections = max;
            return this;
        }
        
        public Configuration build() {
            if (instance == null) {
                synchronized (Configuration.class) {
                    if (instance == null) {
                        instance = new Configuration(this);
                    }
                }
            }
            return instance;
        }
    }
}
```

**Q8: How would you implement a caching proxy?**
```java
class CachingProxy implements DataService {
    private RealDataService realService;
    private Map<String, Object> cache = new ConcurrentHashMap<>();
    
    public Object getData(String key) {
        // Check cache first
        if (cache.containsKey(key)) {
            return cache.get(key);
        }
        
        // Cache miss - get from real service
        if (realService == null) {
            realService = new RealDataService();
        }
        
        Object data = realService.getData(key);
        cache.put(key, data);
        return data;
    }
}
```

---

## Summary

These design patterns are fundamental to Jakarta EE development:

1. **Abstract Factory** - Useful for cross-platform UI, database abstraction
2. **Builder** - Essential for complex configuration objects
3. **Bridge** - Good for driver/implementation separation
4. **Proxy** - Foundation of RMI, JPA lazy loading, Spring AOP
