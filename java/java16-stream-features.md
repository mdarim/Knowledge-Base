# 📚 **Java 16 Stream API Features and Enhancements**

Java 16 introduced **new methods** in the **Stream API** to enhance its functionality and make **stream processing** more efficient and expressive. The key enhancements focus on **easier manipulation of streams**, such as dropping or taking elements based on conditions and supporting **new collectors**.

---

## 🔧 **New Stream Methods in Java 16**

### **1. `toList()` Method** (Added to `java.util.stream.Stream`)

The `toList()` method is a **convenient collector** that converts a stream into an **immutable list** in a single step. Unlike `Collectors.toList()`, this method ensures that the returned list is **unmodifiable**.

#### ✅ **Before Java 16:**

```java
List<String> list = stream.collect(Collectors.toList());
```

#### ✅ **In Java 16:**

```java
List<String> list = stream.toList();
```

- The `toList()` method returns an **immutable list**.
- **Simplifies code** by removing the need to use `Collectors.toList()`.
- **Performance optimized** for the new `List.of()` internally.

---

### **2. `dropWhile()` Method** (Introduced in Java 9, Improved in Java 16)

The `dropWhile()` method **removes elements from the stream** as long as they match a given **predicate**. Once the predicate returns **false**, the rest of the elements are included.

#### ✅ **Example:**

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);
List<Integer> result = numbers.stream()
                              .dropWhile(n -> n < 4)
                              .toList();
System.out.println(result); // Output: [4, 5, 6]
```

---

### **3. `takeWhile()` Method** (Introduced in Java 9, Improved in Java 16)

The `takeWhile()` method **takes elements from the stream** as long as the predicate returns **true**. When the predicate returns **false**, the stream processing stops.

#### ✅ **Example:**

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);
List<Integer> result = numbers.stream()
                              .takeWhile(n -> n < 4)
                              .toList();
System.out.println(result); // Output: [1, 2, 3]
```

---

### **4. `mapMulti()` Method** (New in Java 16)

The `mapMulti()` method is an **advanced version of `flatMap()`**. It allows more **control over mapping logic** and can produce **zero or more elements** for each input element.

#### ✅ **Before Java 16: Using `flatMap()`**

```java
List<Integer> numbers = List.of(1, 2, 3);
List<Integer> result = numbers.stream()
                              .flatMap(n -> Stream.of(n, n * 2))
                              .toList();
System.out.println(result); // Output: [1, 2, 2, 4, 3, 6]
```

#### ✅ **In Java 16: Using `mapMulti()`**

```java
List<Integer> numbers = List.of(1, 2, 3);
List<Integer> result = numbers.stream()
                              .mapMulti((n, consumer) -> {
                                  consumer.accept(n);
                                  consumer.accept(n * 2);
                              })
                              .toList();
System.out.println(result); // Output: [1, 2, 2, 4, 3, 6]
```

- **More efficient** than `flatMap()` for some use cases.
- **More flexible** in handling **custom mapping logic**.

---

### **5. `Stream.ofNullable()` Method** (Introduced in Java 9, Still Relevant)

The `Stream.ofNullable()` method creates a **stream with a single element** or an **empty stream** if the element is `null`.

#### ✅ **Example:**

```java
String value = null;
Stream<String> stream = Stream.ofNullable(value);
System.out.println(stream.count()); // Output: 0
```

---

## 🧪 **Example: Using Java 16 Stream Methods**

```java
import java.util.List;

public class Java16StreamExample {
    public static void main(String[] args) {
        List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);

        // Using dropWhile()
        List<Integer> dropped = numbers.stream()
                                       .dropWhile(n -> n < 4)
                                       .toList();
        System.out.println("Dropped: " + dropped); // [4, 5, 6]

        // Using takeWhile()
        List<Integer> taken = numbers.stream()
                                     .takeWhile(n -> n < 4)
                                     .toList();
        System.out.println("Taken: " + taken); // [1, 2, 3]

        // Using mapMulti()
        List<Integer> mapped = numbers.stream()
                                      .mapMulti((n, consumer) -> {
                                          consumer.accept(n);
                                          consumer.accept(n * 2);
                                      })
                                      .toList();
        System.out.println("Mapped: " + mapped); // [1, 2, 2, 4, 3, 6]
    }
}
```

---

## 🚀 **Summary of New Stream API Methods in Java 16**

| **Method**    | **Description**                                                            |
|---------------|----------------------------------------------------------------------------|
| `toList()`    | Returns an **immutable list** from a stream.                               |
| `dropWhile()` | Drops elements **while the condition is true** and returns the rest.       |
| `takeWhile()` | Takes elements **while the condition is true** and ignores the rest.       |
| `mapMulti()`  | A more **flexible version of `flatMap()`**, allowing custom mapping logic. |

---

## 🎯 **Use Cases for New Stream Features in Java 16**

| **Feature**   | **Use Case**                                                                 |
|---------------|------------------------------------------------------------------------------|
| `toList()`    | Converting streams to **immutable lists** more easily.                       |
| `dropWhile()` | Dropping unwanted **leading elements** from a sorted stream.                 |
| `takeWhile()` | Taking **leading elements** from a sorted stream until a condition is false. |
| `mapMulti()`  | Efficient **custom mapping** with **zero or more outputs** per input.        |

---

### 💡 **When to Use `mapMulti()` vs `flatMap()`?**

| **Use `mapMulti()`**                                     | **Use `flatMap()`**                         |
|----------------------------------------------------------|---------------------------------------------|
| When **zero or more outputs** are needed per input.      | When a **one-to-many mapping** is required. |
| For **custom mapping logic** that requires more control. | For **simpler, one-step transformations**.  |

---

## ✅ **Conclusion**

Java 16 brings powerful enhancements to the **Stream API**, making it more **concise, expressive, and flexible**. The **`toList()`** method simplifies list conversion, while **`mapMulti()`** allows **more advanced mapping scenarios**. The **`dropWhile()`** and **`takeWhile()`** methods help process streams based on **conditions** more efficiently.

Let me know if you'd like more examples or further details! 😊
