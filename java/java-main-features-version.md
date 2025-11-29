Here’s a **comprehensive list of key features introduced in each major Java version** to
help you stay up-to-date with the evolution of Java from **Java 8** to the latest version.
These features focus on performance improvements, language enhancements, APIs, and more.

---

## **Java 8 (March 2014) - The Game-Changer**

### 🔑 **Main Features:**

- [**Lambda Expressions**](java8-lambda-expression.md): Functional programming style.
- [**Stream API**](java8-stream-api.md): Processing collections of data.
- [**Optional Class**](java8-optional-class.md): Avoid `null` references.
- [**Default Methods**](java8-default-method.md): Methods in interfaces with default
  implementation.
- [**Date and Time API (java.time)**](java8-date-time-api.md): Improved date and time
  handling.
- [**Functional Interfaces**](java8-functional-interfaces.md): Simplified functional
  programming.
- [**Nashorn JavaScript Engine**](java8-nashorn-javaScript-engine.md): Run JavaScript
  within Java.
- [**Concurrent Additions**](java8-ConcurrentHashMap.md): New methods in
  `ConcurrentHashMap`.

---

## 🧩 **Java 9 (September 2017) - The Modular Java**

### 🔑 **Main Features:**

- [**Java Platform Module System (Project Jigsaw)**](java9-platform-module-system.md):
  Modularization of the JDK.
- [**JShell**](java9-jshell.md): Interactive REPL (Read-Eval-Print Loop) for Java.
- [**Improved Stream API**](java9-Improved-Stream-api-and-add-new-methods.md): New methods
  like `takeWhile()`, `dropWhile()`.
- [**Factory Methods for Collections**](java9-factory-methods-for-collections.md):
  `List.of()`, `Set.of()`, `Map.of()`.
- [**Private Methods in Interfaces**](java9-private-methods-in-interfaces.md): Reusable
  methods in interfaces.
- [**HTTP/2 Client (Incubator)**](java11-http-client-api.md): New HTTP client API.

---

## 🧩 **Java 10 (March 2018) - Local Type Inference**

### 🔑 **Main Features:**

- [**`var` Keyword**](java10-var-keyword.md): Local variable type inference.
- [**Garbage Collector Improvements**](java10-garbage-collector-improvements.md): Experimental garbage collectors.
- [**Application Class-Data Sharing**](java10-application-class-data-sharing.md): Improves startup time.
- **Parallel Full GC for G1**: Improves G1 GC performance.

---

## 🧩 **Java 11 (September 2018) - Long-Term Support (LTS)**

### 🔑 **Main Features:**

- [**HTTP Client API**](java11-http-client-api.md): Fully implemented and non-incubator.
- [**String Methods**](java11-string-methods.md): `isBlank()`, `lines()`, `repeat()`.
- [**File Methods**](advanced-java-file-contents-features.md): `readString()`, `writeString()`.
- **Lambda Enhancements**: `var` usage in lambda parameters.
- **Removed Features**: Removed `java.se.ee`, `JavaFX`, and others.
- [**New `Nest-Based Access Control`**](java11-new-nest-based-access-control.md): Simplified access between nested 
  classes.

---

## 🧩 **Java 12 (March 2019) - Minor Enhancements**

### 🔑 **Main Features:**

- [**Switch Expressions (Preview)**](java14-switch-expressions.md): Simplified switch syntax.
- **Garbage Collector Improvements**: G1 GC and Shenandoah GC.
- [**Compact Number Formatting**](java12-compact-number-formatting.md): New APIs in `java.text`.

---

## 🧩 **Java 13 (September 2019) - Text Blocks (Preview)**

### 🔑 **Main Features:**

- [**Text Blocks (Preview)**](java15-text-blocks.md): Multiline string literals using 
  `"""`.
- [**Switch Expressions (Preview)**](java14-switch-expressions.md): Continued improvements.

---

## 🧩 **Java 14 (March 2020) - Pattern Matching (Preview)**

### 🔑 **Main Features:**

- [**Pattern Matching for `instanceof` (Preview)**](java16-pattern-matching-for-instanceof.md): Simplified type checks.
- [**Records (Preview)**](java16-records.md): Data classes with minimal boilerplate.
- [**Switch Expressions**](java14-switch-expressions.md): Finalized feature.

---

## 🧩 **Java 15 (September 2020) - Sealed Classes (Preview)**

### 🔑 **Main Features:**

- [**Text Blocks**](java15-text-blocks.md): Finalized feature.
- [**Sealed Classes (Preview)**](java17-sealed-classes.md): Control which classes can extend a class.
- [**Hidden Classes**](java15-hidden-classes.md): Used by frameworks to create classes at runtime.

---

## 🧩 **Java 16 (March 2021) - Records and Pattern Matching**

### 🔑 **Main Features:**

- [**Records**](java16-records.md): Finalized feature.
- [**Pattern Matching for `instanceof`**](java16-pattern-matching-for-instanceof.md): 
  Finalized feature.
- [**Packaging Tool**](java16-packaging-tool.md): New `jpackage` tool for packaging applications.
- [**Stream.toList()**](java16-stream-features.md): Simplified list collection from streams.

---

## 🧩 **Java 17 (September 2021) - Long-Term Support (LTS)**

### 🔑 **Main Features:**

- [**Sealed Classes**](java17-sealed-classes.md): Finalized feature.
- [**Pattern Matching for Switch (Preview)**](java-pattern-matching-for-switch.md): Enhanced switch statement.
- [**New Garbage Collector (ZGC)**](java-new-garbage-collector-ZGC.md): Improved performance.
- **Remove Deprecated Features**: Removed **RMI Activation**, **Applets**, and more.

---

## 🧩 **Java 18 (March 2022) - Simple Improvements**

### 🔑 **Main Features:**

- [**UTF-8 by Default**](java-utf8-by-default-feature.md): Standardized text encoding.
- [**Simple Web Server**](java-simple-web-server.md): Lightweight HTTP server for prototyping.
- [**Pattern Matching for Switch (Preview)**](java-pattern-matching-for-switch.md): 
  Continued improvements.

---

## 🧩 **Java 19 (September 2022) - Project Loom (Preview)**

### 🔑 **Main Features:**

- [**Virtual Threads (Preview)**](java21-virtual-threads.md): Lightweight threads for scalability.
- [**Structured Concurrency (Preview)**](java-structured-concurrency-feature.md): Simplifies handling multiple 
  concurrent tasks.
- [**Pattern Matching for Switch**](java-pattern-matching-for-switch.md): Further refined.

---

## 🧩 **Java 20 (March 2023) - Project Loom Enhancements**

### 🔑 **Main Features:**

- [**Virtual Threads (Second Preview)**](java21-virtual-threads.md): Continued improvements.
- [**Record Patterns (Preview)**](java-record-patterns-feature.md): Destructuring patterns for data classes.
- [**Pattern Matching for Switch**](java-pattern-matching-for-switch.md): Finalized 
  feature.

---

## 🧩 **Java 21 (September 2023) - Latest LTS Version**

### 🔑 **Main Features:**

- [**Virtual Threads**](java21-virtual-threads.md): Finalized feature from Project Loom.
- [**Sequenced Collections**](java-sequenced-collections.md): New collections that maintain order.
- [**Record Patterns**](java-record-patterns-feature.md): Finalized feature.
- [**Pattern Matching for `switch`**](java-pattern-matching-for-switch.md): Finalized feature.
- [**New `String Templates` (Preview)**](java-string-templates.md): Simplifies string interpolation.

---

## ✅ **Summary of Key Java Features by Version:**

| **Java Version** | **Key Features**                                                                                                                                   | **Type** |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| Java 8           | Lambdas, Stream API, Date-Time API                                                                                                                 | Major    |
| Java 9           | Modules, JShell, Factory Methods for Collections                                                                                                   | Major    |
| Java 10          | [`var` Keyword](java10-var-keyword.md)                                                                                                             | Minor    |
| Java 11          | [HTTP Client API](java11-http-client-api.md), [String Methods](java11-string-methods.md)                                                           | LTS      |
| Java 12          | [Switch Expressions (Preview)](java14-switch-expressions.md)                                                                                       | Minor    |
| Java 13          | [Text Blocks](java15-text-blocks.md) (Preview)                                                                                                     | Minor    |
| Java 14          | [Pattern Matching for `instanceof`](java16-pattern-matching-for-instanceof.md), [Records (Preview)](java16-records.md)                             | Minor    |
| Java 15          | [Text Blocks](java15-text-blocks.md), [Sealed Classes (Preview)](java17-sealed-classes.md)                                                         | Minor    |
| Java 16          | [Records](java16-records.md), [Stream.toList()](java16-stream-features.md)                                                                         | Minor    |
| Java 17          | [Sealed Classes](java17-sealed-classes.md), [Pattern Matching for Switch](java-pattern-matching-for-switch.md)                                     | LTS      |
| Java 18          | [UTF-8 by Default](java-utf8-by-default-feature.md), [Simple Web Server](java-simple-web-server.md)                                                | Minor    |
| Java 19          | [Virtual Threads (Preview)](java21-virtual-threads.md), [Structured Concurrency](java-structured-concurrency-feature.md)                           | Major    |
| Java 20          | [Virtual Threads (Second Preview)](java21-virtual-threads.md), [Record Patterns](java-record-patterns-feature.md)                                  | Minor    |
| Java 21          | [Virtual Threads](java21-virtual-threads.md), [Sequenced Collections](java-sequenced-collections.md), [String Templates](java-string-templates.md) | LTS      |

---

### 💡 **Conclusion:**

- **Java 8, Java 11, Java 17, and Java 21** are the **LTS versions**, widely adopted by
  enterprises.
- **Key modern features** include [**Virtual Threads**](java21-virtual-threads.md), 
  [**Records**](java16-records.md), **Pattern Matching**,
  and **String Templates** to simplify code and improve performance.

By understanding these features, you can stay current with Java’s latest enhancements and
adopt **best practices** in your projects.
