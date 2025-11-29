# 📚 **Java 23 Main Features and Enhancements**

Java 23 introduces several new **performance optimizations**, **language enhancements**, and **tooling updates** to improve developer productivity and application efficiency. Below are the key features and changes in **Java 23**.

---

## 🆕 **1. Record Patterns (Preview Feature)**

**Record Patterns** allow matching **records** (introduced in Java 16) within **pattern matching constructs** like `switch` and `if` statements. This feature simplifies working with **immutable data structures**.

### Example:

```java
record Point(int x, int y) {}

public class RecordPatternExample {
    public static void main(String[] args) {
        Point point = new Point(5, 10);

        if (point instanceof Point(int x, int y)) {
            System.out.println("X: " + x + ", Y: " + y);
        }
    }
}
```

---

## 🔐 **2. Sequenced Collections**

**Sequenced Collections** introduce a new interface that **unifies ordered collections**, such as **lists** and **deques**, and provides methods to access **first and last elements**.

### Key Methods:

- `getFirst()`
- `getLast()`
- `addFirst()`
- `addLast()`

### Example:

```java
import java.util.ArrayList;
import java.util.SequencedCollection;

public class SequencedCollectionsExample {
    public static void main(String[] args) {
        SequencedCollection<String> names = new ArrayList<>();
        names.add("Alice");
        names.add("Bob");

        System.out.println("First: " + names.getFirst());
        System.out.println("Last: " + names.getLast());
    }
}
```

---

## 🖥 **3. Simple Web Server (Production Feature)**

The **Simple Web Server**, introduced as an **incubator feature in Java 18**, is now **production-ready**. It allows you to quickly spin up a **lightweight HTTP server** for testing and development purposes.

### Example:

```bash
java -m jdk.httpserver
```

- **Default port:** 8000
- **Default directory:** Current working directory

---

## 📋 **4. UTF-8 by Default**

Starting from Java 23, **UTF-8** is the **default character encoding** for:

- **Java source files.**
- **String literals.**
- **File operations.**

This change ensures **consistent behavior across platforms**, making applications more **portable** and **internationalized**.

---

## 🔄 **5. Virtual Threads (Final Feature)**

**Virtual Threads** (introduced as a preview feature in Java 19) are now **finalized** in Java 23. Virtual threads are **lightweight threads** that make it easier to build **concurrent applications**.

### Key Benefits:

- **Efficient use of system resources.**
- **Scalable concurrency.**
- **Simplified thread management.**

### Example:

```java
public class VirtualThreadsExample {
    public static void main(String[] args) {
        try (var executor = java.util.concurrent.Executors.newVirtualThreadPerTaskExecutor()) {
            executor.submit(() -> {
                System.out.println("Running in a virtual thread");
            });
        }
    }
}
```

---

## 🧩 **6. Structured Concurrency API (Preview Feature)**

The **Structured Concurrency API** simplifies **multithreading** by treating a set of related tasks as a **single unit of work**. This makes it easier to manage **cancellation, error handling, and resource management** across multiple threads.

### Example:

```java
import java.util.concurrent.*;

public class StructuredConcurrencyExample {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
            Future<String> user = scope.fork(() -> fetchUserData());
            Future<String> order = scope.fork(() -> fetchOrderData());

            scope.join(); // Wait for all tasks to complete
            scope.throwIfFailed(); // Propagate exceptions

            System.out.println("User: " + user.resultNow());
            System.out.println("Order: " + order.resultNow());
        }
    }

    static String fetchUserData() {
        return "John Doe";
    }

    static String fetchOrderData() {
        return "Order #12345";
    }
}
```

---

## 🚀 **7. Advanced Concurrency Features**

Java 23 introduces new **concurrency features** to improve the performance of **multi-threaded applications**:

- **Thread-per-task Executors**: Automatically create virtual threads for each task.
- **Scoped Values API**: A safer and more efficient alternative to `ThreadLocal`.

---

## 🔄 **8. Updated Garbage Collectors**

Java 23 includes improvements to **ZGC** and **G1 Garbage Collectors** for:

- **Lower latency.**
- **Better performance on large heaps.**
- **Reduced pause times.**

---

## 📦 **9. Enhanced File Handling**

Java 23 introduces **new methods for file operations**, making it easier to **read, write, and manipulate files**.

### Example:

```java
import java.nio.file.*;

public class FileExample {
    public static void main(String[] args) throws Exception {
        Path path = Files.createTempFile("example", ".txt");
        Files.writeString(path, "Hello, Java 23!");
        System.out.println(Files.readString(path));
    }
}
```

---

## 🌐 **10. Foreign Function & Memory API (4th Preview)**

The **Foreign Function & Memory API** has been further refined to make it **more powerful and user-friendly**. It allows you to interact with **native code** and manage **native memory** without relying on **JNI**.

---

## 📋 **Summary of Java 23 Key Features**

| **Feature**                        | **Description**                                                |
|------------------------------------|----------------------------------------------------------------|
| **Record Patterns (Preview)**      | Simplifies pattern matching with record types.                 |
| **Sequenced Collections**          | Unified interface for ordered collections.                     |
| **Simple Web Server**              | Lightweight HTTP server for testing and development.           |
| **UTF-8 by Default**               | Default character encoding for source files and strings.       |
| **Virtual Threads (Final)**        | Lightweight threads for scalable concurrency.                  |
| **Structured Concurrency API**     | Simplifies multithreading by treating related tasks as a unit. |
| **Advanced Concurrency Features**  | Improved concurrency utilities like scoped values.             |
| **Garbage Collector Improvements** | Enhancements to ZGC and G1 for better performance.             |
| **Enhanced File Handling**         | New methods for working with files.                            |
| **Foreign Function & Memory API**  | Refined API for interacting with native code.                  |

---

## 📌 **How to Enable Preview Features (Virtual Threads, Structured Concurrency, etc.)**

### **Using Command Line:**

```bash
javac --enable-preview --release 23 Main.java
java --enable-preview Main
```

---
