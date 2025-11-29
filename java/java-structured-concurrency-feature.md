# 🚀 **Java Structured Concurrency (Preview)** – Introduced in **Java 21**

Structured Concurrency is a **major new concurrency model** introduced in **Java 21** as a **preview feature**. It aims to simplify **multithreaded programming** by managing **lifecycle and scope** of tasks in a **structured way**, making it easier to reason about concurrent code and avoid common pitfalls such as **resource leaks** and **orphaned threads**.

---

## 🧐 **What is Structured Concurrency?**

Structured Concurrency ensures that:

1. **Threads are started and stopped in a structured, predictable way.**
2. **Child tasks are bounded by the scope of their parent task.**
3. **No threads are left running accidentally beyond their intended lifecycle.**

Think of it as **structured blocks of code** (like loops or try-catch), but for **managing threads**.

---

### 🤔 **Why Structured Concurrency?**

Traditional concurrency can result in:

- **Orphaned threads** (threads running beyond their intended lifecycle).
- **Difficult-to-manage task lifecycles**.
- **Resource leaks** due to threads not being properly shut down.

Structured Concurrency **avoids these issues** by ensuring that:

- Threads are **created, managed, and closed** within a **predictable scope**.
- Any **exception** in a task automatically **cancels related tasks**.

---

## 📦 **New Classes in Structured Concurrency**

Structured Concurrency introduces the following key classes in **`java.util.concurrent`** package:

| **Class**                                  | **Description**                                          |
|--------------------------------------------|----------------------------------------------------------|
| `StructuredTaskScope`                      | A base class to manage a **group of related tasks**.     |
| `StructuredTaskScope.ShutdownOnFailure`    | Automatically shuts down all tasks on **first failure**. |
| `StructuredTaskScope.ShutdownOnSuccess`    | Automatically shuts down all tasks on **first success**. |
| `StructuredTaskScope.ShutdownOnCompletion` | Waits for **all tasks to complete**.                     |

---

### ✅ **Structured Concurrency Scope Example**

Here’s a simple example of how **`StructuredTaskScope`** works:

```java
import java.util.concurrent.*;

public class StructuredConcurrencyExample {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
            Future<String> task1 = scope.fork(() -> fetchDataFromService1());
            Future<String> task2 = scope.fork(() -> fetchDataFromService2());

            // Wait for all tasks to complete or cancel them on failure
            scope.join();
            scope.throwIfFailed();

            // Get the results
            String result1 = task1.resultNow();
            String result2 = task2.resultNow();

            System.out.println("Results: " + result1 + ", " + result2);
        }
    }

    private static String fetchDataFromService1() {
        // Simulate fetching data
        return "Data from Service 1";
    }

    private static String fetchDataFromService2() {
        // Simulate fetching data
        return "Data from Service 2";
    }
}
```

---

### 📝 **Explanation:**

1. **`StructuredTaskScope.ShutdownOnFailure`**: Shuts down all tasks if **any task fails**.
2. **`fork()`**: Creates a **new task** and runs it asynchronously.
3. **`join()`**: Waits for **all tasks to complete**.
4. **`throwIfFailed()`**: Throws an **exception** if any task fails.

---

### 🔧 **Using `StructuredTaskScope.ShutdownOnSuccess`**

If you want to **cancel remaining tasks** after **one task completes successfully**, use **`ShutdownOnSuccess`**:

```java
try (var scope = new StructuredTaskScope.ShutdownOnSuccess<String>()) {
    Future<String> task1 = scope.fork(() -> fetchDataFromService1());
    Future<String> task2 = scope.fork(() -> fetchDataFromService2());

    scope.join(); // Wait for tasks to complete
    String result = scope.result(); // Get the first successful result

    System.out.println("First successful result: " + result);
}
```

---

### ⚠️ **Error Handling in Structured Concurrency**

Structured Concurrency handles **exceptions** automatically. If a task **throws an exception**, the **scope will cancel all related tasks**.

```java
try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
    Future<String> task1 = scope.fork(() -> fetchDataFromService1());
    Future<String> task2 = scope.fork(() -> fetchDataFromServiceWithError());

    scope.join(); // Wait for tasks
    scope.throwIfFailed(); // Throws exception if any task fails
}
```

---

### 🧪 **Advanced Example: Combining Results from Multiple Tasks**

```java
import java.util.concurrent.*;

public class AdvancedStructuredConcurrency {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        try (var scope = new StructuredTaskScope.ShutdownOnCompletion()) {
            Future<Integer> task1 = scope.fork(() -> computeValue1());
            Future<Integer> task2 = scope.fork(() -> computeValue2());

            scope.join(); // Wait for tasks to complete

            int result = task1.resultNow() + task2.resultNow();
            System.out.println("Combined Result: " + result);
        }
    }

    private static int computeValue1() {
        return 10;
    }

    private static int computeValue2() {
        return 20;
    }
}
```

---

### 📋 **Benefits of Structured Concurrency**

| **Benefit**                 | **Description**                                            |
|-----------------------------|------------------------------------------------------------|
| ✅ **Simplifies Code**       | Easier to manage and reason about concurrent code.         |
| ✅ **Automatic Cleanup**     | Tasks are automatically canceled when the scope is closed. |
| ✅ **Better Error Handling** | Propagates errors correctly, avoiding orphaned tasks.      |
| ✅ **Improves Reliability**  | Prevents resource leaks by ensuring proper task shutdown.  |

---

### 🌟 **Real-World Use Cases**

| **Use Case**           | **Description**                                 |
|------------------------|-------------------------------------------------|
| 🌐 **Web Services**    | Fetch data from multiple APIs in parallel.      |
| 📊 **Data Processing** | Process large datasets in parallel tasks.       |
| 🎮 **Gaming**          | Handle concurrent game events or computations.  |
| 🛠️ **Microservices**  | Manage concurrent tasks in distributed systems. |

---

### 📦 **Important Methods in StructuredTaskScope**

| **Method**                | **Description**                                               |
|---------------------------|---------------------------------------------------------------|
| `fork(Runnable/Callable)` | Creates a new task and runs it asynchronously.                |
| `join()`                  | Waits for all tasks to complete.                              |
| `throwIfFailed()`         | Throws an exception if any task fails.                        |
| `resultNow()`             | Returns the result of a completed task.                       |
| `result()`                | Returns the first successful result (in `ShutdownOnSuccess`). |

---

### 🔍 **Comparison with Traditional Thread Management**

| **Feature**                  | **Traditional Threads**     | **Structured Concurrency** |
|------------------------------|-----------------------------|----------------------------|
| Thread Lifecycle Management  | Manual                      | Automatic                  |
| Error Handling               | Complex                     | Simplified                 |
| Resource Management          | Prone to leaks              | Automatic cleanup          |
| Ease of Use                  | Hard to manage              | Easier to manage           |

---

## 🧪 **Summary**

| **Feature**       | **Description**                                       |
|-------------------|-------------------------------------------------------|
| **Introduced In** | Java 21 (Preview)                                     |
| **Key Class**     | `StructuredTaskScope`                                 |
| **Goal**          | Simplify and structure multithreaded programming.     |
| **Main Benefit**  | Automatic task management and error handling.         |
| **Use Cases**     | Web services, microservices, data processing, gaming. |

---

Would you like more examples on **real-world structured concurrency patterns**? 😊
