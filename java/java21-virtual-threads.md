## 📌 **Java 21: Virtual Threads (Project Loom)**

**Virtual Threads** were introduced in **Java 21** as part of **Project Loom** to provide **lightweight, high-concurrency threading** capabilities. They are a revolutionary improvement in Java's concurrency model, allowing developers to handle **millions of concurrent tasks** efficiently with **minimal memory usage**.

---

## 🔎 **What Are Virtual Threads?**

A **Virtual Thread** is a **lightweight thread** that is managed by the **Java Virtual Machine (JVM)** rather than the operating system. Unlike traditional **platform threads**, virtual threads are **cheap to create and manage**, enabling **scalable concurrency** without the usual limitations of thread pools.

**Key Differences:**

| **Traditional Platform Threads**    | **Virtual Threads**                           |
|-------------------------------------|-----------------------------------------------|
| Managed by the **operating system** | Managed by the **JVM**                        |
| **Expensive** to create and manage  | **Cheap** to create and manage                |
| Limited to a few thousand threads   | Can handle **millions** of concurrent threads |
| Requires **thread pools**           | No need for complex thread pooling            |

---

### ✅ **Why Virtual Threads?**

Java's traditional concurrency model uses **platform threads**, which are **mapped to OS threads**. This approach has limitations:

- OS threads are **expensive to create** and **consume a lot of memory**.
- **Blocking I/O** operations (e.g., network calls) cause **thread starvation**.
- Thread pools require **complex management** to avoid bottlenecks.

With **virtual threads**, these issues are mitigated:

- Threads become **cheap and plentiful**.
- Blocking operations no longer cause thread starvation.
- **Simpler code** without worrying about thread pool sizing.

---

### ✅ **Key Features of Virtual Threads:**

1. **Lightweight and scalable**: Supports **millions of concurrent threads**.
2. **Efficient blocking**: Virtual threads handle **blocking I/O** efficiently.
3. **No need for thread pools**: Virtual threads eliminate the need for **thread pool management**.
4. **Works with existing APIs**: Compatible with traditional **synchronous** and **blocking** Java APIs.

---

### 🛠 **How to Create Virtual Threads:**

Java 21 introduces **new methods** in the `Thread` class to work with virtual threads.

#### **1. Using `Thread.ofVirtual().start()`:**

```java
public class VirtualThreadExample {
    public static void main(String[] args) {
        Thread virtualThread = Thread.ofVirtual().start(() -> {
            System.out.println("Hello from a virtual thread!");
        });

        // Wait for the virtual thread to finish
        try {
            virtualThread.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

#### **2. Using `Executors.newVirtualThreadPerTaskExecutor()`:**

```java
import java.util.concurrent.ExecutorService;

public class VirtualThreadExecutorExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor();

        for (int i = 1; i <= 10; i++) {
            int taskNumber = i;
            executor.submit(() -> {
                System.out.println("Running task " + taskNumber + " in a virtual thread.");
            });
        }

        executor.shutdown();
    }
}
```

---

### 🔄 **Migrating from Platform Threads to Virtual Threads:**

#### **Before (Using Platform Threads):**

```java
ExecutorService executor = Executors.newFixedThreadPool(10);

for (int i = 1; i <= 1000; i++) {
    executor.submit(() -> {
        // Simulate blocking I/O
        Thread.sleep(1000);
        System.out.println("Task completed by " + Thread.currentThread().getName());
    });
}

executor.shutdown();
```

#### **After (Using Virtual Threads):**

```java
ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor();

for (int i = 1; i <= 1000; i++) {
    executor.submit(() -> {
        // Simulate blocking I/O
        Thread.sleep(1000);
        System.out.println("Task completed by " + Thread.currentThread().getName());
    });
}

executor.shutdown();
```

---

### 🧪 **Example: Handling Millions of Concurrent Connections**

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class VirtualThreadsExample {
    public static void main(String[] args) {
        try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
            for (int i = 0; i < 1_000_000; i++) {
                executor.submit(() -> {
                    System.out.println("Handling request in: " + Thread.currentThread());
                });
            }
        }
    }
}
```

---

### 📋 **Virtual Threads vs Platform Threads:**

| **Aspect**             | **Platform Threads** | **Virtual Threads** |
|------------------------|----------------------|---------------------|
| **Creation Cost**      | High                 | Low                 |
| **Memory Consumption** | High                 | Low                 |
| **Blocking I/O**       | Inefficient          | Efficient           |
| **Concurrency Limit**  | Thousands            | Millions            |
| **Thread Pools**       | Required             | Not needed          |

---

### 📋 **New Methods in `Thread` Class (Java 21):**

| **Method**                                    | **Description**                                                      |
|-----------------------------------------------|----------------------------------------------------------------------|
| `Thread.ofVirtual()`                          | Returns a virtual thread builder.                                    |
| `Executors.newVirtualThreadPerTaskExecutor()` | Returns an executor that creates a new virtual thread for each task. |

---

### 🧩 **Use Cases for Virtual Threads:**

1. **Handling high-concurrency workloads**: Web servers, chat applications, etc.
2. **Blocking I/O operations**: Database queries, file operations, etc.
3. **Simplifying thread management**: No need to manage thread pools manually.
4. **Microservices**: Virtual threads make it easier to handle **thousands of requests** simultaneously.

---

### 🔧 **Best Practices for Virtual Threads:**

1. **Use virtual threads for I/O-bound tasks**.
2. **Avoid long-running CPU-bound tasks** in virtual threads.
3. Use **structured concurrency** to manage virtual threads efficiently.
4. **Leverage existing synchronous APIs** without worrying about blocking.

---

### 🛠 **Structured Concurrency (Preview Feature)**

Java 21 also introduces **Structured Concurrency** as a **preview feature** to improve the management of **concurrent tasks**.

```java
import java.util.concurrent.*;

public class StructuredConcurrencyExample {
    public static void main(String[] args) {
        try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
            Future<String> task1 = scope.fork(() -> fetchDataFromService1());
            Future<String> task2 = scope.fork(() -> fetchDataFromService2());

            scope.join();
            scope.throwIfFailed();

            String result1 = task1.resultNow();
            String result2 = task2.resultNow();

            System.out.println("Results: " + result1 + ", " + result2);
        }
    }

    static String fetchDataFromService1() {
        return "Data from Service 1";
    }

    static String fetchDataFromService2() {
        return "Data from Service 2";
    }
}
```

---

### 📋 **Summary of Virtual Threads:**

| **Aspect**                 | **Details**                                                         |
|----------------------------|---------------------------------------------------------------------|
| **Introduced in**          | Java 21                                                             |
| **Project**                | Project Loom                                                        |
| **Purpose**                | To handle **millions of concurrent tasks** efficiently.             |
| **New Methods**            | `Thread.ofVirtual()`, `Executors.newVirtualThreadPerTaskExecutor()` |
| **Use Cases**              | High-concurrency workloads, web servers, chat apps, etc.            |
| **Structured Concurrency** | Simplifies managing multiple virtual threads.                       |

---

### 🎯 **Advantages of Virtual Threads:**

- **Efficient I/O handling**.
- **Simplifies concurrency code**.
- Reduces the need for **thread pools**.
- Enables **high-concurrency** applications.

---

Let me know if you need more examples or further clarification! 😊
