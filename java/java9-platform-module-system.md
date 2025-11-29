# 🚀 **Java 9 Platform Module System (JPMS)**

The **Java Platform Module System (JPMS)**, introduced in **Java 9**, is one of the most significant changes to the Java language and platform. It allows developers to organize code into **reusable modules** with well-defined dependencies and strong encapsulation. The **JPMS** is also referred to as **Project Jigsaw**.

---

## ✅ **What is the Java Platform Module System (JPMS)?**

The **JPMS** introduces a way to **modularize** large applications, breaking them into **smaller, manageable modules**. It improves:

- **Encapsulation**: Only expose necessary APIs.
- **Dependency Management**: Declare and enforce module dependencies at compile-time and runtime.
- **Performance**: Reduce the footprint of applications by loading only the required modules.

---

## ✅ **Key Concepts in JPMS**

| **Term**             | **Description**                                                     |
|----------------------|---------------------------------------------------------------------|
| **Module**           | A group of related packages, classes, and resources.                |
| **`module-info.java`** | A descriptor file that defines the module’s metadata and dependencies. |
| **Requires**         | Specifies module dependencies.                                      |
| **Exports**          | Specifies which packages are visible to other modules.              |
| **Uses/Provides**    | Used for the **Service Loader** mechanism to implement services.     |

---

## ✅ **Why JPMS?**

- **Improved Security**: Modules can hide implementation details.
- **Easier Maintenance**: Smaller modules are easier to maintain.
- **Faster Startup Time**: Only necessary modules are loaded.
- **Reduced Memory Footprint**: Applications can run with a smaller memory footprint.

---

## ✅ **Creating a Module in Java 9**

A **module** is created by adding a **`module-info.java`** file to the root of the module’s directory.

### 🔷 **Structure of a Module**

```
mymodule
├── src
│   └── com.example.mymodule
│       └── Main.java
└── module-info.java
```

### 📄 **Example of `module-info.java`**

```java
module mymodule {
    requires java.base;   // Implicitly required by all modules
    requires java.sql;    // Requires another module
    exports com.example.mymodule; // Exports a package
}
```

---

### 📄 **Example of a Simple Module**

#### 🔧 **1. Create the `module-info.java`**

```java
module mymodule {
    exports com.example.mymodule;
}
```

#### 📄 **2. Create a Java Class**

```java
// File: src/com/example/mymodule/Main.java
package com.example.mymodule;

public class Main {
    public static void main(String[] args) {
        System.out.println("Hello, Java Modules!");
    }
}
```

---

## ✅ **Compiling and Running a Modular Application**

To compile and run a modular application, you need to use the **`javac`** and **`java`** commands with module-specific options.

### 🔧 **1. Compile the Module**

```bash
javac -d out --module-source-path src $(find src -name "*.java")
```

### 🔧 **2. Run the Module**

```bash
java --module-path out --module mymodule/com.example.mymodule.Main
```

---

## ✅ **`module-info.java` Directives**

| **Directive**    | **Description**                                             |
|------------------|-------------------------------------------------------------|
| `module`         | Declares a module.                                           |
| `requires`       | Specifies module dependencies.                               |
| `exports`        | Makes a package accessible to other modules.                 |
| `opens`          | Makes a package accessible via reflection.                   |
| `provides ... with` | Declares a service implementation.                        |
| `uses`           | Declares a service that the module consumes.                 |

---

## ✅ **Example: Multiple Modules**

### 🔧 **Module 1: `mymodule`**

**`module-info.java`**
```java
module mymodule {
    exports com.example.mymodule;
}
```

**`Main.java`**
```java
package com.example.mymodule;

public class Main {
    public static void printMessage() {
        System.out.println("Hello from mymodule!");
    }
}
```

---

### 🔧 **Module 2: `appmodule`**

**`module-info.java`**
```java
module appmodule {
    requires mymodule;
}
```

**`App.java`**
```java
package com.example.appmodule;

import com.example.mymodule.Main;

public class App {
    public static void main(String[] args) {
        Main.printMessage();
    }
}
```

---

### 🖥️ **Compile and Run the Application**

```bash
javac -d out --module-source-path src $(find src -name "*.java")
java --module-path out --module appmodule/com.example.appmodule.App
```

---

## ✅ **Service Loader with JPMS**

JPMS integrates with the **Service Loader** mechanism, allowing you to define and consume services.

### 🔧 **Step 1: Define a Service Interface**

```java
// File: src/mymodule/com/example/service/GreetingService.java
package com.example.service;

public interface GreetingService {
    void greet(String name);
}
```

---

### 🔧 **Step 2: Provide an Implementation**

```java
// File: src/mymodule/com/example/service/impl/EnglishGreetingService.java
package com.example.service.impl;

import com.example.service.GreetingService;

public class EnglishGreetingService implements GreetingService {
    @Override
    public void greet(String name) {
        System.out.println("Hello, " + name + "!");
    }
}
```

---

### 🔧 **Step 3: Update `module-info.java`**

```java
module mymodule {
    exports com.example.service;
    provides com.example.service.GreetingService
        with com.example.service.impl.EnglishGreetingService;
}
```

---

### 🔧 **Step 4: Consume the Service**

```java
// File: src/appmodule/com/example/appmodule/App.java
package com.example.appmodule;

import com.example.service.GreetingService;
import java.util.ServiceLoader;

public class App {
    public static void main(String[] args) {
        ServiceLoader<GreetingService> loader = ServiceLoader.load(GreetingService.class);
        for (GreetingService service : loader) {
            service.greet("John");
        }
    }
}
```

---

## ✅ **Benefits of JPMS**

| **Benefit**               | **Description**                                               |
|---------------------------|---------------------------------------------------------------|
| **Encapsulation**         | Modules can hide internal implementation details.             |
| **Dependency Management**  | Enforces module dependencies at both compile-time and runtime. |
| **Performance**           | Reduces startup time and memory footprint by loading only required modules. |
| **Security**               | Limits access to internal APIs, reducing the attack surface.  |

---

## ✅ **Summary**

| **Feature**         | **Description**                                      |
|---------------------|------------------------------------------------------|
| **JPMS**            | Java Platform Module System introduced in Java 9.    |
| **`module-info.java`** | The module descriptor file.                        |
| **Encapsulation**   | Hides implementation details and exposes only required packages. |
| **Service Loader**  | Supports dynamic discovery of services.              |

---

## ✅ **Conclusion**

The **Java 9 Platform Module System (JPMS)** brings modularity to Java, allowing developers to create more maintainable, secure, and efficient applications. By breaking applications into **modules**, developers can manage dependencies, improve encapsulation, and leverage the service loader mechanism for dynamic service discovery.
