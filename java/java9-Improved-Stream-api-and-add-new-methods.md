### ✅ **Java 9: Improved Stream API and New Methods**

Java 9 introduced significant improvements to the **Stream API** by adding **new methods** and **functionalities** to make working with streams more powerful and concise.

---

## 🎯 **New Methods in Stream API (Java 9)**

| **Method**            | **Description**                                         |
|-----------------------|---------------------------------------------------------|
| `takeWhile()`         | Takes elements from the stream while the condition is true. |
| `dropWhile()`         | Drops elements from the stream while the condition is true and then takes the rest. |
| `ofNullable()`        | Creates a stream from a single nullable element.         |
| `iterate()` (enhanced)| Introduced an overloaded version to provide a termination condition. |
| `flatMap()` (Optional)| Returns a stream from `Optional`.                        |

---

## 🔧 **1. `takeWhile()` Method**

The **`takeWhile()`** method returns a stream of elements as long as the condition holds true. Once the condition becomes false, it stops processing further elements.

### ✅ **Example:**

```java
import java.util.stream.Stream;

public class Main {
    public static void main(String[] args) {
        Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9)
              .takeWhile(n -> n < 5)
              .forEach(System.out::println);
    }
}
```

**Output:**
```text
1
2
3
4
```

---

## 🔧 **2. `dropWhile()` Method**

The **`dropWhile()`** method drops elements as long as the condition is true and then processes the remaining elements.

### ✅ **Example:**

```java
import java.util.stream.Stream;

public class Main {
    public static void main(String[] args) {
        Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9)
              .dropWhile(n -> n < 5)
              .forEach(System.out::println);
    }
}
```

**Output:**
```text
5
6
7
8
9
```

---

## 🔧 **3. `ofNullable()` Method**

The **`ofNullable()`** method creates a stream with a single element, which can be **null**. If the element is `null`, it returns an empty stream.

### ✅ **Example:**

```java
import java.util.stream.Stream;

public class Main {
    public static void main(String[] args) {
        Stream.ofNullable(null)
              .forEach(System.out::println);  // No output

        Stream.ofNullable("Hello")
              .forEach(System.out::println);  // Output: Hello
    }
}
```

---

## 🔧 **4. Enhanced `iterate()` Method**

The **`iterate()`** method now has an overloaded version that allows specifying a termination condition.

### ✅ **Example:**

```java
import java.util.stream.Stream;

public class Main {
    public static void main(String[] args) {
        Stream.iterate(1, n -> n <= 10, n -> n + 1)
              .forEach(System.out::println);
    }
}
```

**Output:**
```text
1
2
3
4
5
6
7
8
9
10
```

---

## 🔧 **5. `flatMap()` with `Optional`**

In Java 9, you can directly use `flatMap()` with **`Optional`** to avoid handling nested `Optional` objects.

### ✅ **Example:**

```java
import java.util.Optional;
import java.util.stream.Stream;

public class Main {
    public static void main(String[] args) {
        Optional<String> optional = Optional.of("Hello");

        Stream<String> stream = optional.stream();
        stream.forEach(System.out::println);
    }
}
```

**Output:**
```text
Hello
```

---

## 🎯 **Summary of New Methods**

| **Method**     | **Use Case**                              |
|----------------|-------------------------------------------|
| `takeWhile()`  | Stop processing when the condition is false. |
| `dropWhile()`  | Skip elements until the condition becomes false. |
| `ofNullable()` | Handle nullable elements in streams.       |
| `iterate()`    | Terminate infinite streams with a condition. |
| `flatMap()`    | Flatten `Optional` to a stream.            |

---

## ✅ **Practical Use Case Example**

Here’s a more practical example to demonstrate these new methods in a single program.

### 📄 **Example: Process a List of Numbers**

```java
import java.util.List;
import java.util.stream.Stream;

public class Main {
    public static void main(String[] args) {
        List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

        // Take numbers less than 5
        Stream<Integer> stream1 = numbers.stream().takeWhile(n -> n < 5);
        System.out.println("takeWhile:");
        stream1.forEach(System.out::println);

        // Drop numbers less than 5
        Stream<Integer> stream2 = numbers.stream().dropWhile(n -> n < 5);
        System.out.println("dropWhile:");
        stream2.forEach(System.out::println);
    }
}
```

---

## 🎯 **When to Use the New Methods:**

| **Scenario**               | **Method to Use** |
|----------------------------|-------------------|
| Processing elements until a condition is false | `takeWhile()`      |
| Skipping initial elements based on a condition | `dropWhile()`      |
| Handling potentially `null` values in streams  | `ofNullable()`     |
| Creating finite streams from infinite series   | `iterate()`        |
| Flattening nested `Optional` objects           | `flatMap()`        |

---

These new methods in Java 9 significantly enhance the **Stream API** by making it more versatile and efficient for functional programming. Let me know if you need more examples or further assistance! 😊
