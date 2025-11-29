# 🗑️ **Java's New Garbage Collector (ZGC)**

**Z Garbage Collector (ZGC)** is an advanced **low-latency garbage collector** introduced in **Java 11** and **improved in subsequent releases** to handle **large heaps** with **minimal pause times**, even on heaps of **multi-terabyte sizes**. It is **scalable**, **concurrent**, and designed for applications requiring **consistent response times**.

Let's explore the key features, advantages, and how to enable and configure **ZGC** in Java.

---

## ✅ **Key Features of ZGC**

| **Feature**                     | **Description**                                                                            |
|---------------------------------|--------------------------------------------------------------------------------------------|
| ⚡ **Low Latency**               | Pause times typically **under 10ms**, regardless of heap size.                             |
| 🧩 **Scalable**                 | Handles **heaps from a few MB to multiple terabytes**.                                     |
| 📈 **Concurrent**               | Most of the garbage collection work is done **concurrently** with the application threads. |
| 📊 **Compacts Memory**          | Performs **concurrent compaction** to avoid heap fragmentation.                            |
| 🔄 **Supports Large Heaps**     | Designed to efficiently manage **very large heaps** (up to 16 TB).                         |
| 🚀 **No Stop-the-World Events** | No long stop-the-world pauses for GC cycles.                                               |

---

## 🚀 **Improvements in Recent Java Versions**

- **Java 11**: Initial release of ZGC as an **experimental feature**.
- **Java 15**: ZGC became **production-ready** and **enabled by default**.
- **Java 17**: Improved support for **memory management** and **performance tuning**.
- **Java 21**: Continued performance enhancements and reduced overhead.

---

## 🔧 **How to Enable ZGC**

You can enable ZGC by using the following JVM option:

```bash
-XX:+UseZGC
```

### ✅ **Example Command:**

```bash
java -XX:+UseZGC -Xmx16g -jar myapp.jar
```

- **`-Xmx16g`**: Sets the maximum heap size to 16 GB.
- **`-XX:+UseZGC`**: Enables ZGC as the garbage collector.

---

## 📚 **Example: Enabling ZGC in a Java Application**

```bash
java -XX:+UseZGC -Xmx32g -Xms32g -jar myapp.jar
```

In this example:

- **Heap Size**: 32 GB.
- **Garbage Collector**: ZGC.

---

## 🧪 **How ZGC Works**

ZGC operates **concurrently** with the application and splits its tasks into phases:

| **Phase**                   | **Description**                                                |
|-----------------------------|----------------------------------------------------------------|
| **Marking**                 | Identifies live objects in memory.                             |
| **Relocation (Compaction)** | Moves live objects to avoid fragmentation (done concurrently). |
| **Processing Roots**        | Ensures application references are updated during compaction.  |

### 🕒 **Pause Times**

The key strength of ZGC is that it keeps **pause times very short (typically under 10ms)**, regardless of:

- **Heap size** (even with multi-terabyte heaps).
- **Number of live objects**.

---

## 🛠️ **Tuning ZGC**

Here are some common tuning options for ZGC:

| **Option**                      | **Description**                                    |
|---------------------------------|----------------------------------------------------|
| `-XX:+UseZGC`                   | Enable ZGC.                                        |
| `-Xmx<size>`                    | Set the **maximum heap size**.                     |
| `-XX:SoftMaxHeapSize=<size>`    | Specify the **soft limit** on the heap size.       |
| `-XX:ZUncommitDelay=<time>`     | Set the time for **uncommitting unused memory**.   |
| `-XX:ZUncommitThreshold=<size>` | Specify the **threshold** for uncommitting memory. |

---

## 📋 **ZGC vs Other Garbage Collectors**

| **Feature**           | **ZGC**                  | **G1 GC**            | **Parallel GC**           |
|-----------------------|--------------------------|----------------------|---------------------------|
| Pause Time            | < 10ms                   | Up to hundreds of ms | Can exceed seconds        |
| Heap Size Support     | Up to 16 TB              | Up to a few TB       | Limited                   |
| Throughput            | Moderate                 | High                 | Very High                 |
| Concurrent Compaction | Yes                      | Partial              | No                        |
| Use Case              | Low-latency applications | General-purpose      | High-throughput workloads |

---

## 🌍 **Use Cases for ZGC**

| **Use Case**                 | **Description**                                       |
|------------------------------|-------------------------------------------------------|
| 🌐 **Web Services**          | Low-latency response times are critical.              |
| 📈 **Big Data Applications** | Large heaps (multi-terabyte) require efficient GC.    |
| 🧪 **Real-Time Systems**     | Consistent, predictable pause times are essential.    |
| 🎮 **Gaming Applications**   | Low-latency is required to avoid performance hiccups. |
| 🛠️ **Microservices**        | Fast startup and low memory overhead.                 |

---

## 🖥️ **Example Application with ZGC**

```java
public class ZGCExample {
    public static void main(String[] args) {
        System.out.println("Starting application with ZGC...");
        
        for (int i = 0; i < 1_000_000; i++) {
            String value = "Item " + i;
            System.out.println(value);
        }

        System.out.println("Application finished.");
    }
}
```

### ✅ **Run with ZGC:**

```bash
java -XX:+UseZGC -Xmx4g ZGCExample.java
```

---

## 🔍 **Monitoring ZGC**

You can monitor the performance of ZGC using **JVM logs**.

### ✅ **Enable GC Logs:**

```bash
java -XX:+UseZGC -Xlog:gc* -jar myapp.jar
```

---

## 📊 **Advantages of ZGC**

| **Advantage**           | **Description**                                                                    |
|-------------------------|------------------------------------------------------------------------------------|
| ⚡ **Ultra-Low Latency** | Pause times are typically under **10ms**.                                          |
| 🧩 **Scalable**         | Handles very **large heaps** (up to 16 TB).                                        |
| 🔄 **Concurrent GC**    | Most of the garbage collection work is done **concurrently** with the application. |
| 📈 **Compact Memory**   | Performs **concurrent compaction** to avoid fragmentation.                         |

---

## ⚠️ **Limitations of ZGC**

| **Limitation**                   | **Description**                                      |
|----------------------------------|------------------------------------------------------|
| 🛠️ **Requires Java 11+**        | Available only in **Java 11 and later versions**.    |
| 💻 **Not Ideal for Small Heaps** | ZGC’s benefits are more evident for **large heaps**. |
| 📉 **Moderate Throughput**       | Throughput is **lower** compared to **Parallel GC**. |

---

## 🆚 **When to Use ZGC?**

| **Use Case**                      | **Recommendation**    |
|-----------------------------------|-----------------------|
| Large, memory-intensive apps      | ✅ **Use ZGC**         |
| Low-latency real-time apps        | ✅ **Use ZGC**         |
| Apps with small heaps (< 1 GB)    | ❌ **Avoid ZGC**       |
| Apps requiring maximum throughput | ❌ **Use Parallel GC** |

---

## 🚀 **Summary**

| **Feature**             | **Description**                                   |
|-------------------------|---------------------------------------------------|
| **Introduced In**       | Java 11                                           |
| **Target Use Case**     | Low-latency, large-memory applications            |
| **Maximum Heap Size**   | Up to **16 TB**                                   |
| **Typical Pause Times** | **Under 10ms**                                    |
| **Enabled By**          | `-XX:+UseZGC`                                     |
| **Use Cases**           | Real-time systems, large-scale apps, web services |

---
