# 📘 Creating Executable JAR - Complete Guide

## Table of Contents
1. [What is an Executable JAR](#what-is-an-executable-jar)
2. [Spring Boot Fat JAR vs Traditional JAR](#spring-boot-fat-jar-vs-traditional-jar)
3. [JAR Structure](#jar-structure)
4. [Building the JAR with Maven](#building-the-jar-with-maven)
5. [Running the JAR](#running-the-jar)
6. [Advanced Options](#advanced-options)
7. [Troubleshooting](#troubleshooting)
8. [Complete Deployment Guide](#complete-deployment-guide)
9. [Quick Reference](#quick-reference)

---

## What is an Executable JAR

### Understanding Executable JAR

```
┌─────────────────────────────────────────────────────────────────┐
│              EXECUTABLE JAR (FAT JAR / UBER JAR)                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   An executable JAR is a self-contained archive that includes:  │
│                                                                 │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │  ✓ Your compiled application code (.class files)       │   │
│   │  ✓ All dependency JARs (Spring, Thymeleaf, etc.)        │   │
│   │  ✓ Embedded web server (Tomcat by default)              │   │
│   │  ✓ Configuration files (application.properties)        │   │
│   │  ✓ Static resources (CSS, JS, images)                   │   │
│   │  ✓ Template files (Thymeleaf HTML templates)            │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Why "Fat" JAR?                                                │
│   ───────────────                                               │
│   Regular JARs are small - contain only your code.              │
│   Fat JARs are large - contain EVERYTHING needed to run.        │
│                                                                 │
│   Example sizes:                                                │
│   Regular JAR: ~500 KB (just your code)                         │
│   Fat JAR: ~50 MB (all dependencies included)                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Benefits of Executable JAR

```
┌─────────────────────────────────────────────────────────────────┐
│              BENEFITS OF EXECUTABLE JAR                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ✓ SIMPLE DEPLOYMENT                                           │
│     Just copy one file and run - no complex setup               │
│                                                                 │
│   ✓ NO EXTERNAL SERVER REQUIRED                                 │
│     Tomcat is embedded - no need to install/configure servers   │
│                                                                 │
│   ✓ CONSISTENT ENVIRONMENT                                      │
│     Same JAR runs identically everywhere                        │
│                                                                 │
│   ✓ EASY VERSION MANAGEMENT                                     │
│     One file = one version of your app                          │
│                                                                 │
│   ✓ CONTAINER-READY                                             │
│     Perfect for Docker, Kubernetes deployments                  │
│                                                                 │
│   ✓ CLOUD-NATIVE                                                │
│     Ideal for cloud platforms (AWS, GCP, Azure, Heroku)         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Spring Boot Fat JAR vs Traditional JAR

### Comparison

```
┌─────────────────────────────────────────────────────────────────┐
│         TRADITIONAL JAR vs SPRING BOOT FAT JAR                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   TRADITIONAL JAR (Plain Java)                                  │
│   ────────────────────────────                                  │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ myapp.jar                                               │   │
│   │ ├── com/example/MyClass.class                           │   │
│   │ └── META-INF/MANIFEST.MF                                │   │
│   └─────────────────────────────────────────────────────────┘   │
│   • Contains only your code                                     │
│   • Must specify classpath with dependencies                    │
│   • Cannot run with just "java -jar"                            │
│                                                                 │
│   SPRING BOOT FAT JAR                                           │
│   ───────────────────────                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ myapp.jar                                               │   │
│   │ ├── BOOT-INF/                                           │   │
│   │ │   ├── classes/ (your code)                            │   │
│   │ │   └── lib/ (all dependencies)                         │   │
│   │ ├── META-INF/MANIFEST.MF                                │   │
│   │ └── org/springframework/boot/loader/                    │   │
│   └─────────────────────────────────────────────────────────┘   │
│   • Contains your code AND all dependencies                     │
│   • Self-contained executable                                   │
│   • Run with: java -jar myapp.jar                               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Traditional Deployment vs Modern

```
┌─────────────────────────────────────────────────────────────────┐
│         DEPLOYMENT COMPARISON                                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   TRADITIONAL WAY (WAR + External Tomcat)                       │
│   ────────────────────────────────────────                      │
│   1. Install Java on server                                     │
│   2. Download and install Tomcat                                │
│   3. Configure Tomcat (ports, memory, etc.)                     │
│   4. Build WAR file                                             │
│   5. Copy WAR to Tomcat's webapps folder                        │
│   6. Start Tomcat                                               │
│   7. Manage Tomcat lifecycle                                    │
│                                                                 │
│   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ │
│                                                                 │
│   SPRING BOOT WAY (Executable JAR)                              │
│   ─────────────────────────────────                             │
│   1. Install Java on server                                     │
│   2. Copy JAR file to server                                    │
│   3. Run: java -jar myapp.jar                                   │
│   Done! ✓                                                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## JAR Structure

### Inside a Spring Boot JAR

```
┌─────────────────────────────────────────────────────────────────┐
│              SPRING BOOT JAR STRUCTURE                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   myapp-0.0.1-SNAPSHOT.jar                                      │
│   │                                                             │
│   ├── BOOT-INF/                     Application content         │
│   │   ├── classes/                  Your compiled code          │
│   │   │   ├── com/                                              │
│   │   │   │   └── example/                                      │
│   │   │   │       └── demo/                                     │
│   │   │   │           ├── DemoApplication.class                 │
│   │   │   │           ├── BookController.class                  │
│   │   │   │           └── Book.class                            │
│   │   │   ├── templates/            Thymeleaf templates         │
│   │   │   │   ├── bookNew.html                                  │
│   │   │   │   └── success.html                                  │
│   │   │   ├── static/               CSS, JS, images             │
│   │   │   └── application.properties                            │
│   │   │                                                         │
│   │   └── lib/                      All dependencies (JARs)     │
│   │       ├── spring-boot-3.x.jar                               │
│   │       ├── spring-web-6.x.jar                                │
│   │       ├── thymeleaf-3.x.jar                                 │
│   │       ├── tomcat-embed-core-10.x.jar                        │
│   │       └── ... (100+ JARs)                                   │
│   │                                                             │
│   ├── META-INF/                     Metadata                    │
│   │   └── MANIFEST.MF              Execution configuration      │
│   │                                                             │
│   └── org/springframework/boot/loader/                          │
│       ├── JarLauncher.class        Spring Boot launcher         │
│       └── ...                      Loader classes               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### MANIFEST.MF Content

```
Manifest-Version: 1.0
Main-Class: org.springframework.boot.loader.JarLauncher
Start-Class: com.example.demo.DemoApplication
Spring-Boot-Version: 3.2.1
Spring-Boot-Classes: BOOT-INF/classes/
Spring-Boot-Lib: BOOT-INF/lib/
```

| Entry | Description |
|-------|-------------|
| Main-Class | Spring Boot's launcher (not your class!) |
| Start-Class | Your @SpringBootApplication class |
| Spring-Boot-Classes | Location of your compiled code |
| Spring-Boot-Lib | Location of dependencies |

---

## Building the JAR with Maven

### Method 1: Command Line

```
┌─────────────────────────────────────────────────────────────────┐
│              BUILDING WITH MAVEN CLI                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Step 1: Open command prompt in project root                   │
│           (where pom.xml is located)                            │
│                                                                 │
│   Step 2: Run Maven package command                             │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ mvn clean package                                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   • clean: Deletes target folder (fresh build)                  │
│   • package: Compiles code, runs tests, creates JAR             │
│                                                                 │
│   Step 3: Find JAR in target folder                             │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ target/                                                 │   │
│   │ ├── myapp-0.0.1-SNAPSHOT.jar        ← Executable JAR    │   │
│   │ ├── myapp-0.0.1-SNAPSHOT.jar.original ← Before repack   │   │
│   │ └── classes/                                            │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Maven Commands

```bash
# Clean build (recommended)
mvn clean package

# Skip tests (faster, but not recommended for production)
mvn clean package -DskipTests

# Skip test compilation and execution
mvn clean package -Dmaven.test.skip=true

# Production build with specific profile
mvn clean package -P production

# Verbose output for debugging
mvn clean package -X
```

### Method 2: Using IDE (Eclipse/STS)

```
┌─────────────────────────────────────────────────────────────────┐
│              BUILDING IN IDE                                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Eclipse / Spring Tool Suite (STS):                            │
│   ──────────────────────────────────                            │
│   1. Right-click on project                                     │
│   2. Run As → Maven build...                                    │
│   3. In "Goals" field, enter: clean package                     │
│   4. Click "Run"                                                │
│   5. JAR created in target/ folder                              │
│                                                                 │
│   IntelliJ IDEA:                                                │
│   ──────────────                                                │
│   1. Open Maven tool window (View → Tool Windows → Maven)       │
│   2. Expand project → Lifecycle                                 │
│   3. Double-click "package" (or "clean" then "package")         │
│   4. JAR created in target/ folder                              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Build Output

```
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< com.example:demo >------------------------
[INFO] Building demo 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:3.3.1:clean (default-clean) @ demo ---
[INFO] Deleting target
[INFO] 
[INFO] --- maven-resources-plugin:3.3.1:resources ---
[INFO] Copying 1 resource
[INFO] Copying 3 resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.11.0:compile ---
[INFO] Compiling 5 source files to target/classes
[INFO] 
[INFO] --- maven-surefire-plugin:3.1.2:test ---
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
[INFO] 
[INFO] --- maven-jar-plugin:3.3.0:jar ---
[INFO] Building jar: target/demo-0.0.1-SNAPSHOT.jar
[INFO] 
[INFO] --- spring-boot-maven-plugin:3.2.1:repackage ---
[INFO] Replacing main artifact target/demo-0.0.1-SNAPSHOT.jar
[INFO] 
[INFO] BUILD SUCCESS
[INFO] Total time: 15.234 s
```

---

## Running the JAR

### Basic Execution

```bash
# Navigate to target folder
cd target

# Run the JAR
java -jar demo-0.0.1-SNAPSHOT.jar
```

### What Happens on Startup

```
┌─────────────────────────────────────────────────────────────────┐
│              JAR STARTUP PROCESS                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   java -jar demo-0.0.1-SNAPSHOT.jar                             │
│        │                                                        │
│        ▼                                                        │
│   1. JarLauncher starts (from MANIFEST Main-Class)              │
│        │                                                        │
│        ▼                                                        │
│   2. Creates custom ClassLoader for BOOT-INF structure          │
│        │                                                        │
│        ▼                                                        │
│   3. Calls DemoApplication.main() (from Start-Class)            │
│        │                                                        │
│        ▼                                                        │
│   4. SpringApplication.run() initializes Spring context         │
│        │                                                        │
│        ▼                                                        │
│   5. Auto-configuration detects web app setup                   │
│        │                                                        │
│        ▼                                                        │
│   6. Embedded Tomcat starts on port 8080                        │
│        │                                                        │
│        ▼                                                        │
│   7. Application ready! Access at http://localhost:8080         │
│                                                                 │
│   Console output:                                               │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │   .   ____          _            __ _ _                │   │
│   │  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \               │   │
│   │ ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \              │   │
│   │  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )             │   │
│   │   '  |____| .__|_| |_|_| |_\__, | / / / /              │   │
│   │  =========|_|==============|___/=/_/_/_/               │   │
│   │  :: Spring Boot ::                (v3.2.1)             │   │
│   │                                                        │   │
│   │ Started DemoApplication in 3.456 seconds               │   │
│   │ Tomcat started on port(s): 8080 (http)                 │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Advanced Options

### Command Line Arguments

```bash
# Different port
java -jar demo.jar --server.port=9090

# Different profile
java -jar demo.jar --spring.profiles.active=production

# Custom configuration location
java -jar demo.jar --spring.config.location=/etc/myapp/

# Debug mode
java -jar demo.jar --debug

# Multiple options
java -jar demo.jar --server.port=9090 --spring.profiles.active=prod
```

### JVM Options

```bash
# Memory settings
java -Xms256m -Xmx512m -jar demo.jar

# Garbage collector
java -XX:+UseG1GC -jar demo.jar

# Remote debugging
java -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005 -jar demo.jar

# Complete production example
java -Xms512m -Xmx1024m \
     -XX:+UseG1GC \
     -Dspring.profiles.active=production \
     -jar demo.jar \
     --server.port=80
```

### Running as Background Process

```bash
# Linux/Mac - Run in background
nohup java -jar demo.jar > app.log 2>&1 &

# Windows - Run in background
start /B java -jar demo.jar > app.log 2>&1

# Get process ID
echo $! > app.pid

# Stop the application
kill $(cat app.pid)
```

---

## Troubleshooting

### Common Issues

```
┌─────────────────────────────────────────────────────────────────┐
│              TROUBLESHOOTING                                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Issue: "Port already in use"                                  │
│   ─────────────────────────────                                 │
│   Error: Web server failed to start. Port 8080 was in use.      │
│                                                                 │
│   Solutions:                                                    │
│   • Use different port: --server.port=8081                      │
│   • Find and kill process using port:                           │
│     Windows: netstat -ano | findstr :8080                       │
│              taskkill /PID <pid> /F                             │
│     Linux: lsof -i :8080                                        │
│            kill -9 <pid>                                        │
│                                                                 │
│   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ │
│                                                                 │
│   Issue: "'java' is not recognized"                             │
│   ──────────────────────────────────                            │
│   Error: 'java' is not recognized as internal or external cmd   │
│                                                                 │
│   Solutions:                                                    │
│   • Install Java JDK                                            │
│   • Add Java to PATH environment variable                       │
│   • Use full path: "C:\Program Files\Java\jdk-17\bin\java"      │
│                                                                 │
│   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ │
│                                                                 │
│   Issue: "OutOfMemoryError"                                     │
│   ─────────────────────────                                     │
│   Error: java.lang.OutOfMemoryError: Java heap space            │
│                                                                 │
│   Solutions:                                                    │
│   • Increase heap size: java -Xmx512m -jar demo.jar             │
│   • Check for memory leaks in application                       │
│                                                                 │
│   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ │
│                                                                 │
│   Issue: "No main manifest attribute"                           │
│   ─────────────────────────────────                             │
│   Error: no main manifest attribute, in demo.jar                │
│                                                                 │
│   Solutions:                                                    │
│   • Ensure spring-boot-maven-plugin is in pom.xml               │
│   • Run mvn clean package (not just mvn compile)                │
│   • Check you're running the correct JAR (not .original)        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Complete Deployment Guide

### Step-by-Step Deployment

```
┌─────────────────────────────────────────────────────────────────┐
│              COMPLETE DEPLOYMENT STEPS                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   LOCAL DEVELOPMENT                                             │
│   ─────────────────                                             │
│                                                                 │
│   Step 1: Open Terminal in project folder                       │
│   c:\myproject>                                                 │
│                                                                 │
│   Step 2: Build the JAR                                         │
│   c:\myproject> mvn clean package                               │
│                                                                 │
│   Step 3: Navigate to target                                    │
│   c:\myproject> cd target                                       │
│                                                                 │
│   Step 4: Run the JAR                                           │
│   c:\myproject\target> java -jar demo-0.0.1-SNAPSHOT.jar        │
│                                                                 │
│   Step 5: Test in browser                                       │
│   http://localhost:8080                                         │
│                                                                 │
│   Step 6: Stop the application                                  │
│   Press Ctrl + C                                                │
│                                                                 │
│   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ │
│                                                                 │
│   PRODUCTION SERVER                                             │
│   ─────────────────                                             │
│                                                                 │
│   Step 1: Copy JAR to server                                    │
│   scp target/demo.jar user@server:/opt/myapp/                   │
│                                                                 │
│   Step 2: SSH into server                                       │
│   ssh user@server                                               │
│                                                                 │
│   Step 3: Run with production settings                          │
│   java -Xmx512m -jar demo.jar --spring.profiles.active=prod     │
│                                                                 │
│   Step 4: (Optional) Create systemd service for auto-start      │
│   See systemd service configuration below                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

### Maven Commands

| Command | Description |
|---------|-------------|
| `mvn clean` | Delete target folder |
| `mvn compile` | Compile source code |
| `mvn test` | Run unit tests |
| `mvn package` | Create JAR in target/ |
| `mvn clean package` | Clean build from scratch |
| `mvn package -DskipTests` | Build without tests |

### Java Run Commands

| Command | Description |
|---------|-------------|
| `java -jar app.jar` | Run with defaults |
| `java -jar app.jar --server.port=9090` | Custom port |
| `java -Xmx512m -jar app.jar` | Custom memory |
| `java -jar app.jar --debug` | Debug mode |

### Useful Options Summary

| Option | Example | Purpose |
|--------|---------|---------|
| Port | `--server.port=9090` | Change server port |
| Profile | `--spring.profiles.active=prod` | Activate profile |
| Memory | `-Xmx512m` | Set max heap |
| Debug | `--debug` | Enable debug logging |

### Deployment Checklist

```
□ Java installed on server
□ JAR file copied to server
□ Correct memory settings configured
□ Production profile activated
□ Firewall ports opened
□ Application tested
□ Monitoring configured
□ Log rotation set up
```

---

*This note is part of the Advanced Java - Spring Boot & Spring MVC series*
