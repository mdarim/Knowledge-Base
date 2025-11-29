# 🌐 **Java Simple Web Server (Introduced in Java 18)**

The **Simple Web Server** is a lightweight, **built-in web server** introduced in **Java 18** to help developers quickly serve **static files** and test **web applications** without the need to set up complex web servers like **Tomcat**, **Jetty**, or **Spring Boot**.

This feature is **ideal for prototyping**, **testing**, or **serving static content** in a local environment.

---

## ✅ **Key Features of Java's Simple Web Server**

| **Feature**               | **Description**                                                           |
|---------------------------|---------------------------------------------------------------------------|
| 📦 **Built-in**           | No external libraries or configurations needed.                           |
| 🌐 **HTTP/1.1 Support**   | Supports basic HTTP/1.1 requests for static file serving.                 |
| 📁 **Static File Server** | Serves static files from a directory.                                     |
| 🔧 **Command-Line Tool**  | Easily start the server from the terminal using the `jwebserver` command. |
| 🚀 **Minimal Setup**      | No need to write Java code to run the server (CLI-based).                 |

---

## 🖥️ **How to Start the Simple Web Server Using the Command Line**

You can **start the server directly from the terminal** using the `jwebserver` command, available in **Java 18+**.

### ✅ **Command to Start the Web Server:**

```bash
jwebserver
```

### 🔧 **Default Behavior:**

- **Port:** `8000`
- **Directory:** Current working directory (files will be served from here).
- **Protocol:** HTTP/1.1

Once the server is started, open your browser and navigate to:

```
http://localhost:8000
```

---

## 📂 **Serving Files from a Specific Directory**

You can specify a **custom directory** to serve files from:

```bash
jwebserver -d /path/to/your/files
```

Example:

```bash
jwebserver -d /Users/username/website
```

---

## 📌 **Specify a Port Number**

To start the server on a **specific port**:

```bash
jwebserver -p 8080
```

Example:

```bash
jwebserver -d /Users/username/website -p 8080
```

Then visit:

```
http://localhost:8080
```

---

## 🧑‍💻 **How to Use the Simple Web Server Programmatically in Java**

You can also start the **Simple Web Server** using **Java code** through the **`SimpleFileServer`** and **`HttpServer`** classes.

### ✅ **Example Code to Start the Simple Web Server:**

```java
import com.sun.net.httpserver.HttpServer;
import com.sun.net.httpserver.SimpleFileServer;
import com.sun.net.httpserver.SimpleFileServer.OutputLevel;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.file.Path;

public class SimpleWebServerExample {
    public static void main(String[] args) throws IOException {
        // Define the directory to serve
        Path rootPath = Path.of("/path/to/your/files");

        // Create the server on port 8000
        HttpServer server = SimpleFileServer.createFileServer(
            new InetSocketAddress(8000),
            rootPath,
            OutputLevel.VERBOSE
        );

        // Start the server
        server.start();
        System.out.println("Server is running on http://localhost:8000");
    }
}
```

---

## 🧩 **Example: Serving Files from a Custom Directory**

```java
import com.sun.net.httpserver.HttpServer;
import com.sun.net.httpserver.SimpleFileServer;
import com.sun.net.httpserver.SimpleFileServer.OutputLevel;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.file.Path;

public class CustomDirectoryServer {
    public static void main(String[] args) throws IOException {
        // Serving files from /Users/username/website
        Path rootPath = Path.of("/Users/username/website");

        // Creating server on port 8080
        HttpServer server = SimpleFileServer.createFileServer(
            new InetSocketAddress(8080),
            rootPath,
            OutputLevel.INFO
        );

        server.start();
        System.out.println("Server is running on http://localhost:8080");
    }
}
```

---

## 🔄 **Real-Time Directory Changes**

The Simple Web Server **automatically detects changes** in the served directory. You don’t need to restart the server when you add or update files.

---

## 📚 **Common Use Cases**

| **Use Case**                | **Description**                                                 |
|-----------------------------|-----------------------------------------------------------------|
| 📦 **Serving Static Files** | Quickly serve HTML, CSS, JS, and images from a local directory. |
| 🛠️ **Prototyping**         | Test static websites or simple APIs locally.                    |
| 🚀 **Local Testing**        | Use it to test how files load in a browser before deployment.   |
| 🧪 **Educational Purposes** | Great for teaching basic web concepts and file serving.         |

---

## 🔐 **Security Considerations**

The Simple Web Server is designed for **local development** and **testing** purposes. It **should not be used in production environments** because:

- It lacks **advanced security features** like HTTPS.
- It does not support **authentication** or **access control**.
- It is intended to serve **static content only**.

---

## 🆕 **Additional Features in Java 20**

Starting from **Java 20**, the Simple Web Server supports:

- **Access Logs**: Log requests to the console.
- **Custom MIME Types**: Customize file type handling.

---

## 🚀 **Summary**

| **Feature**           | **Description**                                                  |
|-----------------------|------------------------------------------------------------------|
| **Introduced In**     | Java 18                                                          |
| **Default Port**      | 8000                                                             |
| **Default Directory** | Current working directory                                        |
| **Usage**             | Command-line (`jwebserver`) or programmatic (`SimpleFileServer`) |
| **Supports**          | Static file serving                                              |
| **Advanced Features** | Port configuration, directory specification                      |
| **Best For**          | Prototyping, local testing, educational purposes                 |

---

## 🎯 **Quick Commands for `jwebserver`**

| **Command**                      | **Description**                                  |
|----------------------------------|--------------------------------------------------|
| `jwebserver`                     | Start server on port 8000 (default).             |
| `jwebserver -p 8080`             | Start server on port 8080.                       |
| `jwebserver -d /path/to/files`   | Serve files from a specific directory.           |
| `jwebserver -p 9090 -d /my/site` | Start server on port 9090 with custom directory. |

---
