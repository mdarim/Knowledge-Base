# ✅ **Java 11 HTTP Client API: Main Features & Examples**

Java 11 introduced a brand-new **`HttpClient` API** to handle **HTTP requests and responses** in a more modern, efficient, and feature-rich way. It replaces the legacy **`HttpURLConnection`** and provides an easier and more flexible way to interact with web services.

---

## 🎯 **Key Features of Java 11 HTTP Client API:**

| **Feature**                   | **Description**                                                      |
|--------------------------------|----------------------------------------------------------------------|
| **Synchronous & Asynchronous** | Supports both **blocking (synchronous)** and **non-blocking (asynchronous)** calls. |
| **Supports HTTP/1.1 and HTTP/2** | Built-in support for both **HTTP/1.1** and **HTTP/2** protocols.   |
| **Immutable Requests**         | HTTP requests are immutable and thread-safe.                         |
| **WebSocket Support**          | Native **WebSocket** support to handle real-time, two-way communication. |
| **Response Handling**          | Simplifies handling of responses with support for **JSON**, **text**, etc. |
| **Built-in Timeout and Redirection** | Easily set timeouts and configure redirection policies.          |
| **Cookies Management**         | Allows automatic handling of cookies.                                |

---

## 🛠️ **1. Creating an HTTP Client**

The **`HttpClient`** is the core class used to send HTTP requests and receive responses.

### ✅ **Example: Creating a Simple HttpClient**

```java
import java.net.http.HttpClient;

public class Main {
    public static void main(String[] args) {
        HttpClient client = HttpClient.newHttpClient();
        System.out.println("HTTP Client created successfully!");
    }
}
```

---

## 🛠️ **2. Sending a Synchronous GET Request**

The **`HttpClient`** can send **synchronous** (blocking) requests using **`send()`**.

### ✅ **Example: Sending a GET Request**

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class Main {
    public static void main(String[] args) throws Exception {
        // Create an HTTP client
        HttpClient client = HttpClient.newHttpClient();

        // Create an HTTP request
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("https://jsonplaceholder.typicode.com/posts/1"))
                .GET()
                .build();

        // Send the request and get the response
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

        // Print the response
        System.out.println("Status Code: " + response.statusCode());
        System.out.println("Response Body: " + response.body());
    }
}
```

### **Output:**
```text
Status Code: 200
Response Body: {
  "userId": 1,
  "id": 1,
  "title": "Sample Title",
  "body": "Sample Body"
}
```

---

## 🛠️ **3. Sending an Asynchronous GET Request**

You can send **non-blocking (asynchronous)** requests using **`sendAsync()`**.

### ✅ **Example: Asynchronous GET Request**

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.concurrent.CompletableFuture;

public class Main {
    public static void main(String[] args) {
        // Create an HTTP client
        HttpClient client = HttpClient.newHttpClient();

        // Create an HTTP request
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("https://jsonplaceholder.typicode.com/posts/1"))
                .GET()
                .build();

        // Send the request asynchronously
        CompletableFuture<HttpResponse<String>> response = client.sendAsync(request, HttpResponse.BodyHandlers.ofString());

        // Print the response when available
        response.thenAccept(res -> {
            System.out.println("Status Code: " + res.statusCode());
            System.out.println("Response Body: " + res.body());
        });

        // Keep the main thread alive to see the result
        response.join();
    }
}
```

---

## 🛠️ **4. Sending a POST Request with JSON Body**

The **`HttpClient`** can send **POST** requests with a **JSON body** using the **`POST()`** method.

### ✅ **Example: Sending a POST Request**

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class Main {
    public static void main(String[] args) throws Exception {
        // Create an HTTP client
        HttpClient client = HttpClient.newHttpClient();

        // Create a JSON body
        String json = """
                {
                  "title": "foo",
                  "body": "bar",
                  "userId": 1
                }
                """;

        // Create an HTTP POST request
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("https://jsonplaceholder.typicode.com/posts"))
                .header("Content-Type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(json))
                .build();

        // Send the request and get the response
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

        // Print the response
        System.out.println("Status Code: " + response.statusCode());
        System.out.println("Response Body: " + response.body());
    }
}
```

---

## 🛠️ **5. Using HTTP/2 Protocol**

The **`HttpClient`** in Java 11 supports the **HTTP/2** protocol by default. To ensure you're using HTTP/2, you can explicitly set it in the builder.

### ✅ **Example: Using HTTP/2**

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpClient.Version;

public class Main {
    public static void main(String[] args) throws Exception {
        // Create an HTTP client with HTTP/2
        HttpClient client = HttpClient.newBuilder()
                .version(Version.HTTP_2)
                .build();

        // Create an HTTP request
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("https://jsonplaceholder.typicode.com/posts/1"))
                .GET()
                .build();

        // Send the request and get the response
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

        // Print the response
        System.out.println("Status Code: " + response.statusCode());
        System.out.println("Response Body: " + response.body());
    }
}
```

---

## 🛠️ **6. Using WebSockets (Real-Time Communication)**

The **`HttpClient`** also supports **WebSocket** connections for **real-time, two-way communication**.

---

## 🔧 **Key Components of Java 11 HTTP Client API**

| **Class/Interface**         | **Description**                                                   |
|-----------------------------|-------------------------------------------------------------------|
| `HttpClient`                | Main class for sending requests and receiving responses.          |
| `HttpRequest`               | Represents an HTTP request (GET, POST, etc.).                     |
| `HttpResponse`              | Represents an HTTP response (status code, body, headers, etc.).   |
| `HttpResponse.BodyHandler`  | Defines how the response body is handled (String, JSON, etc.).    |
| `WebSocket`                 | Interface for handling WebSocket connections.                     |

---

## 🎯 **Summary of Key Features:**

| **Feature**                   | **Description**                                                |
|--------------------------------|----------------------------------------------------------------|
| **Synchronous & Asynchronous** | Supports both blocking and non-blocking HTTP requests.         |
| **HTTP/2 Support**             | Uses HTTP/2 by default for faster performance.                 |
| **POST with JSON**             | Supports sending POST requests with JSON payloads.             |
| **WebSocket Support**          | Handles real-time, two-way communication using WebSockets.     |
| **Built-in Timeout and Redirection** | Easily configure request timeouts and redirection policies. |

---

This **modern API** simplifies HTTP communication in Java applications. 
