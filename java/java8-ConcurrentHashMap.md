# 🚀 **Concurrent Additions Using ConcurrentHashMap in Java 8**

In Java 8, **`ConcurrentHashMap`** is a **thread-safe** and **high-performance** map for managing concurrent access to a shared map. It efficiently handles **concurrent additions, updates, and retrievals** without blocking the entire map.

Let's explore **how to perform concurrent additions in a `ConcurrentHashMap` using Java 8 features** such as **lambda expressions**, **`compute()`**, and **`merge()`** methods.

---

## ✅ **Code Example: Concurrent Additions Using `ConcurrentHashMap`**

Here’s a practical example of using **`ConcurrentHashMap`** for concurrent additions in a multi-threaded environment:

### 📄 **Concurrent Additions Example**
```java
import java.util.concurrent.ConcurrentHashMap;

public class ConcurrentHashMapExample {
    public static void main(String[] args) {
        // Create a ConcurrentHashMap
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

        // Adding initial values
        map.put("Java", 1);
        map.put("Python", 1);

        // Concurrently update values
        map.compute("Java", (key, value) -> value + 1);
        map.compute("Python", (key, value) -> value + 1);
        map.computeIfAbsent("JavaScript", key -> 1);

        // Print the map
        map.forEach((key, value) -> System.out.println(key + ": " + value));
    }
}
```

### **Output:**
```
Java: 2
Python: 2
JavaScript: 1
```

---

## ✅ **Key Methods for Concurrent Additions in `ConcurrentHashMap`**

Java 8 introduced new methods in **`ConcurrentHashMap`** that make concurrent updates easier:

| **Method**               | **Description**                                                        |
|--------------------------|------------------------------------------------------------------------|
| `compute(key, remappingFunction)` | Computes a new value for the given key using the specified function. |
| `computeIfAbsent(key, mappingFunction)` | Computes a value if the key is not already present.                |
| `computeIfPresent(key, remappingFunction)` | Computes a new value if the key is already present.              |
| `merge(key, value, remappingFunction)` | Merges a new value with the existing value using the provided function. |

---

### 🔷 **Example: Using `compute()` for Concurrent Additions**

The **`compute()`** method is used to **update a value atomically**.

```java
import java.util.concurrent.ConcurrentHashMap;

public class ComputeExample {
    public static void main(String[] args) {
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
        map.put("Java", 1);

        // Update the value atomically
        map.compute("Java", (key, value) -> value + 1);

        System.out.println(map); // Output: {Java=2}
    }
}
```

---

### 🔷 **Example: Using `merge()` for Concurrent Additions**

The **`merge()`** method is used to **combine a new value with the existing value**.

```java
import java.util.concurrent.ConcurrentHashMap;

public class MergeExample {
    public static void main(String[] args) {
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
        map.put("Java", 1);

        // Merge the new value with the existing value
        map.merge("Java", 2, Integer::sum);
        map.merge("Python", 1, Integer::sum);

        System.out.println(map); // Output: {Java=3, Python=1}
    }
}
```

---

### 🔷 **Example: Using `computeIfAbsent()`**

The **`computeIfAbsent()`** method adds a value only if the key is **not already present**.

```java
import java.util.concurrent.ConcurrentHashMap;

public class ComputeIfAbsentExample {
    public static void main(String[] args) {
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

        // Add a value only if the key is absent
        map.computeIfAbsent("Java", key -> 1);

        System.out.println(map); // Output: {Java=1}
    }
}
```

---

### 🔷 **Example: Using `computeIfPresent()`**

The **`computeIfPresent()`** method updates the value only if the key **is already present**.

```java
import java.util.concurrent.ConcurrentHashMap;

public class ComputeIfPresentExample {
    public static void main(String[] args) {
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
        map.put("Java", 1);

        // Update the value only if the key is present
        map.computeIfPresent("Java", (key, value) -> value + 1);

        System.out.println(map); // Output: {Java=2}
    }
}
```

---

## ✅ **Concurrency with Multiple Threads Example**

Here's an example to show **how `ConcurrentHashMap` handles concurrent additions** from multiple threads.

### 📄 **Multi-Threaded Example**
```java
import java.util.concurrent.ConcurrentHashMap;

public class MultiThreadExample {
    public static void main(String[] args) {
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

        // Adding initial value
        map.put("Java", 0);

        // Create two threads that update the map concurrently
        Thread thread1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                map.compute("Java", (key, value) -> value + 1);
            }
        });

        Thread thread2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                map.compute("Java", (key, value) -> value + 1);
            }
        });

        // Start both threads
        thread1.start();
        thread2.start();

        // Wait for both threads to finish
        try {
            thread1.join();
            thread2.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        // Print the final value
        System.out.println("Final Value: " + map.get("Java")); // Output: 2000
    }
}
```

---

## ✅ **Summary of Best Practices for Concurrent Additions**

| **Method**               | **Use Case**                                 |
|--------------------------|----------------------------------------------|
| `compute()`              | When you want to update the value for a key atomically. |
| `computeIfAbsent()`       | When you want to add a value only if the key is absent. |
| `computeIfPresent()`      | When you want to update a value only if the key is present. |
| `merge()`                | When you want to combine a new value with the existing value. |

---

## ✅ **Benefits of Using `ConcurrentHashMap`**

- **Thread-safe**: Allows concurrent access without explicit synchronization.
- **Efficient**: Uses internal **segmentation** to reduce contention.
- **Non-blocking Reads**: Read operations are non-blocking and highly performant.

---

## ✅ **Conclusion**

Using **`ConcurrentHashMap`** in Java 8 is an efficient way to handle **concurrent additions** and updates in multi-threaded applications. The newly introduced methods like **`compute()`**, **`computeIfAbsent()`**, and **`merge()`** simplify concurrent updates without the need for external synchronization, ensuring **thread-safety** and **high performance**.
