# WAR File - Web Application Archive

## Table of Contents
1. [Introduction](#1-introduction)
2. [What is a WAR File?](#2-what-is-a-war-file)
3. [WAR File Structure](#3-war-file-structure)
4. [Creating a WAR File](#4-creating-a-war-file)
5. [Deploying WAR Files](#5-deploying-war-files)
6. [Complete Example](#6-complete-example)
7. [Key Points to Remember](#7-key-points-to-remember)
8. [Interview Questions](#8-interview-questions)

---

## 1. Introduction

WAR (Web Application Archive) files are the standard way to package and deploy Java web applications. Understanding WAR files is essential for deploying applications to production servers.

---

## 2. What is a WAR File?

### Definition

A WAR file is a compressed archive (like a ZIP file) that contains all components of a web application in a standardized directory structure.

### Key Characteristics

| Feature | Description |
|---------|-------------|
| **Extension** | `.war` |
| **Format** | JAR/ZIP format with specific structure |
| **Purpose** | Portable deployment unit |
| **Contains** | HTML, JSP, Servlets, JARs, config files |
| **Standard** | Java EE specification |

### Benefits

- **Portability** - Deploy to any compliant server
- **Single file** - Easy to transfer and backup
- **Standard structure** - Consistent across projects
- **Hot deployment** - Many servers auto-deploy

---

## 3. WAR File Structure

### Standard Directory Layout

```
MyApp.war
│
├── index.html              (Static files at root)
├── index.jsp
├── css/
│   └── style.css
├── js/
│   └── app.js
├── images/
│   └── logo.png
│
└── WEB-INF/               (Protected directory - NOT accessible via URL)
    │
    ├── web.xml            (Deployment descriptor - required pre-Java EE 6)
    │
    ├── classes/           (Compiled Java classes)
    │   └── com/
    │       └── example/
    │           ├── MyServlet.class
    │           └── User.class
    │
    └── lib/               (JAR dependencies)
        ├── mysql-connector.jar
        └── jstl.jar
```

### Visual Structure

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         WAR FILE STRUCTURE                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  MyWebApp.war                                                               │
│  ├── PUBLIC (Accessible via URL)                                           │
│  │   ├── *.html, *.jsp, *.css, *.js                                        │
│  │   └── images/, css/, js/ folders                                        │
│  │                                                                          │
│  └── WEB-INF/ (PROTECTED - NOT accessible via URL)                         │
│      │                                                                      │
│      ├── web.xml ─────────────────────── Deployment descriptor             │
│      │                                                                      │
│      ├── classes/ ────────────────────── Your compiled .class files        │
│      │   └── com/example/                Package structure                 │
│      │       ├── servlets/                                                 │
│      │       │   └── MyServlet.class                                       │
│      │       └── model/                                                    │
│      │           └── User.class                                            │
│      │                                                                      │
│      └── lib/ ────────────────────────── Third-party JAR files             │
│          ├── mysql-connector-java.jar                                      │
│          └── commons-io.jar                                                │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Important Directories

| Directory | Purpose | URL Accessible? |
|-----------|---------|-----------------|
| Root (/) | Static resources | ✅ Yes |
| /WEB-INF | Protected config & code | ❌ No |
| /WEB-INF/classes | Compiled Java classes | ❌ No |
| /WEB-INF/lib | JAR dependencies | ❌ No |

---

## 4. Creating a WAR File

### Method 1: Using JAR Command

```bash
# Navigate to web application directory
cd MyWebApp

# Create WAR file
jar -cvf MyWebApp.war .
```

**Options:**
- `-c` : Create new archive
- `-v` : Verbose output
- `-f` : Specify filename
- `.` : Include all files in current directory

### Method 2: Using Eclipse/IntelliJ

#### Eclipse:
1. Right-click on project
2. Export → WAR file
3. Choose destination
4. Click Finish

#### IntelliJ:
1. Build → Build Artifacts
2. Select WAR
3. Build

### Method 3: Using Maven

```xml
<!-- pom.xml -->
<project>
    <packaging>war</packaging>
    
    <build>
        <finalName>MyWebApp</finalName>
    </build>
</project>
```

```bash
# Build WAR
mvn clean package

# Output: target/MyWebApp.war
```

### Method 4: Using Gradle

```groovy
// build.gradle
plugins {
    id 'war'
}

war {
    archiveBaseName = 'MyWebApp'
}
```

```bash
# Build WAR
gradle war

# Output: build/libs/MyWebApp.war
```

---

## 5. Deploying WAR Files

### Tomcat Deployment

#### Method 1: Drop in webapps

```
TOMCAT_HOME/
└── webapps/
    └── MyWebApp.war  ← Just copy here!
```

Tomcat automatically:
1. Detects new WAR file
2. Extracts to `webapps/MyWebApp/`
3. Deploys the application
4. Available at `http://localhost:8080/MyWebApp/`

#### Method 2: Tomcat Manager

1. Open `http://localhost:8080/manager/html`
2. Enter credentials (configured in `tomcat-users.xml`)
3. Find "WAR file to deploy" section
4. Choose file and click "Deploy"

#### Method 3: Context Descriptor

Create `MyWebApp.xml` in `TOMCAT_HOME/conf/Catalina/localhost/`:

```xml
<Context docBase="/path/to/MyWebApp.war" reloadable="true"/>
```

### GlassFish Deployment

```bash
# Using asadmin
asadmin deploy MyWebApp.war

# Or copy to autodeploy directory
cp MyWebApp.war glassfish/domains/domain1/autodeploy/
```

### WildFly Deployment

```bash
# Copy to deployments
cp MyWebApp.war wildfly/standalone/deployments/
```

---

## 6. Complete Example

### Project Structure (Before WAR)

```
MyCalculator/
├── src/
│   └── com/
│       └── calc/
│           ├── CalculatorServlet.java
│           └── Calculator.java
├── web/
│   ├── index.html
│   ├── result.jsp
│   ├── css/
│   │   └── style.css
│   └── WEB-INF/
│       └── web.xml
└── pom.xml
```

### web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         version="4.0">
    
    <display-name>Calculator Application</display-name>
    
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
    </welcome-file-list>
    
    <servlet>
        <servlet-name>CalcServlet</servlet-name>
        <servlet-class>com.calc.CalculatorServlet</servlet-class>
    </servlet>
    
    <servlet-mapping>
        <servlet-name>CalcServlet</servlet-name>
        <url-pattern>/calculate</url-pattern>
    </servlet-mapping>
    
</web-app>
```

### WAR Contents (After Build)

```
MyCalculator.war
├── index.html
├── result.jsp
├── css/
│   └── style.css
└── WEB-INF/
    ├── web.xml
    ├── classes/
    │   └── com/
    │       └── calc/
    │           ├── CalculatorServlet.class
    │           └── Calculator.class
    └── lib/
        └── (any dependencies)
```

### Access URLs After Deployment

| Resource | URL |
|----------|-----|
| Home page | `http://localhost:8080/MyCalculator/` |
| Index | `http://localhost:8080/MyCalculator/index.html` |
| CSS file | `http://localhost:8080/MyCalculator/css/style.css` |
| Servlet | `http://localhost:8080/MyCalculator/calculate` |
| web.xml | ❌ NOT ACCESSIBLE (protected) |
| Classes | ❌ NOT ACCESSIBLE (protected) |

---

## 7. Key Points to Remember

1. **WAR = Web Application Archive** - Standard Java EE packaging

2. **ZIP format** - Can be extracted with any ZIP tool

3. **WEB-INF is protected** - Cannot be accessed via URL

4. **classes for .class files** - Your compiled code goes here

5. **lib for JARs** - Third-party libraries

6. **web.xml optional** - Since Java EE 6 (annotations work)

7. **Auto-deploy supported** - Most servers detect new WAR files

8. **Context path = WAR name** - `MyApp.war` → `/MyApp/`

9. **ROOT.war special** - Deploys to root context `/`

10. **Exploded deployment** - WAR extracted, or directory directly

---

## 8. Interview Questions

### Basic Level

**Q1: What is a WAR file?**
> WAR (Web Application Archive) is a compressed archive file that packages all components of a Java web application in a standardized structure for deployment.

**Q2: What is the purpose of WEB-INF directory?**
> WEB-INF is a protected directory that cannot be accessed directly via URL. It contains:
> - web.xml (deployment descriptor)
> - classes/ (compiled Java files)
> - lib/ (JAR dependencies)

**Q3: How do you create a WAR file?**
> Using command line: `jar -cvf myapp.war .`
> Using Maven: `mvn package`
> Using IDE: Export as WAR file

### Intermediate Level

**Q4: What is the difference between WAR and JAR?**
| WAR | JAR |
|-----|-----|
| Web Application Archive | Java Archive |
| Contains web resources | Contains Java classes |
| Has WEB-INF structure | No specific structure |
| Deployed to web server | Used as library |
| `.war` extension | `.jar` extension |

**Q5: How do you deploy to Tomcat without restarting?**
> - Drop WAR file in `webapps/` directory (hot deployment)
> - Use Tomcat Manager web interface
> - Use context XML in `conf/Catalina/localhost/`

**Q6: What happens if you name your WAR file ROOT.war?**
> It deploys to the root context path (`/`), so your app is accessible directly at `http://localhost:8080/` without an application name in the URL.

### Advanced Level

**Q7: How do you configure context path different from WAR name?**
> In Tomcat, create `META-INF/context.xml`:
```xml
<Context path="/mypath"/>
```
> Or configure in `server.xml` or use a context file in `conf/Catalina/localhost/`.

**Q8: What is an exploded WAR deployment?**
> Instead of deploying a compressed WAR file, you deploy the directory structure directly. Useful during development for faster deployment without re-packaging.

**Q9: How do you handle environment-specific configurations?**
> - JNDI resources in server config
> - System properties
> - External config files
> - Environment variables
> - Separate property files per environment

---

## Next Topic: [Session Management Summary →](./07_Session_Management_Summary.md)
