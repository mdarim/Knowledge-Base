# ✅ **Java 9 Factory Methods for Collections**

Java 9 introduced **factory methods** for creating **immutable collections** easily using the new static methods in the **`List`**, **`Set`**, and **`Map`** interfaces. These factory methods provide a concise way to create **unmodifiable collections** without the need to use cumbersome boilerplate code.

---

## 🎯 **Why Use Factory Methods for Collections?**

1. **Simplified syntax** for creating immutable collections.
2. **Better performance** compared to older methods.
3. **Prevents accidental modification** by returning unmodifiable collections.
4. **Thread-safe** because the collections are immutable.

---

## 🛠️ **New Factory Methods:**

| **Interface** | **Factory Method**     | **Example**              |
|---------------|------------------------|--------------------------|
| `List`        | `List.of()`            | `List.of("A", "B", "C")` |
| `Set`         | `Set.of()`             | `Set.of("A", "B", "C")`  |
| `Map`         | `Map.of()`             | `Map.of("key1", "value1", "key2", "value2")` |
| `Map`         | `Map.ofEntries()`      | `Map.ofEntries(Map.entry("key1", "value1"))` |

---

## 🔧 **1. Creating Immutable Lists Using `List.of()`**

The **`List.of()`** method creates an **unmodifiable list** with the provided elements.

### ✅ **Example:**

```java
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<String> fruits = List.of("Apple", "Banana", "Mango");

        System.out.println(fruits);  // Output: [Apple, Banana, Mango]

        // Attempting to modify the list will throw an UnsupportedOperationException
        // fruits.add("Orange");  // This will throw an exception
    }
}
```

---

## 🔧 **2. Creating Immutable Sets Using `Set.of()`**

The **`Set.of()`** method creates an **unmodifiable set** with the provided elements.

### ✅ **Example:**

```java
import java.util.Set;

public class Main {
    public static void main(String[] args) {
        Set<String> colors = Set.of("Red", "Green", "Blue");

        System.out.println(colors);  // Output: [Red, Green, Blue]

        // Attempting to modify the set will throw an UnsupportedOperationException
        // colors.add("Yellow");  // This will throw an exception
    }
}
```

### ⚠️ **Important:**
- The `Set.of()` method does **not allow duplicate elements**. If you pass duplicate values, it will throw an **`IllegalArgumentException`**.

---

## 🔧 **3. Creating Immutable Maps Using `Map.of()`**

The **`Map.of()`** method creates an **unmodifiable map** with **key-value pairs**.

### ✅ **Example:**

```java
import java.util.Map;

public class Main {
    public static void main(String[] args) {
        Map<String, String> countries = Map.of(
            "USA", "Washington D.C.",
            "France", "Paris",
            "Germany", "Berlin"
        );

        System.out.println(countries);  // Output: {USA=Washington D.C., France=Paris, Germany=Berlin}

        // Attempting to modify the map will throw an UnsupportedOperationException
        // countries.put("Japan", "Tokyo");  // This will throw an exception
    }
}
```

---

## 🔧 **4. Creating Immutable Maps Using `Map.ofEntries()`**

The **`Map.ofEntries()`** method provides a way to create **larger maps** more cleanly by using **`Map.entry()`**.

### ✅ **Example:**

```java
import java.util.Map;

public class Main {
    public static void main(String[] args) {
        Map<String, String> countries = Map.ofEntries(
            Map.entry("USA", "Washington D.C."),
            Map.entry("France", "Paris"),
            Map.entry("Germany", "Berlin")
        );

        System.out.println(countries);  // Output: {USA=Washington D.C., France=Paris, Germany=Berlin}
    }
}
```

---

## 🔧 **5. Key Characteristics of Immutable Collections:**

| **Property**                 | **Description**                                      |
|------------------------------|------------------------------------------------------|
| **Immutable**                 | Collections created using these methods cannot be modified. |
| **No Null Values Allowed**    | These factory methods do not allow `null` values.   |
| **Thread-Safe**               | Immutable collections are inherently thread-safe.   |
| **Compact and Efficient**     | Created with optimized performance in mind.         |

---

## ⚙️ **Comparison with Previous Approach:**

### **Before Java 9:**

```java
List<String> list = Collections.unmodifiableList(
    new ArrayList<>(Arrays.asList("A", "B", "C"))
);
```

### **In Java 9:**

```java
List<String> list = List.of("A", "B", "C");
```

---

## ⚠️ **Exceptions Thrown by Factory Methods:**

| **Method**   | **Exception**                | **Condition**                  |
|--------------|-----------------------------|--------------------------------|
| `List.of()`  | `UnsupportedOperationException` | If modification is attempted.  |
| `Set.of()`   | `IllegalArgumentException`     | If duplicates are present.     |
| `Map.of()`   | `NullPointerException`         | If any key or value is `null`. |

---

## 🎯 **Use Cases for Factory Methods:**

| **Scenario**                     | **Factory Method to Use** |
|----------------------------------|---------------------------|
| Creating a small immutable list   | `List.of()`               |
| Creating a small immutable set    | `Set.of()`                |
| Creating a small immutable map    | `Map.of()`                |
| Creating a large immutable map    | `Map.ofEntries()`         |

---

## 🎉 **Practical Example: Configuration Map**

Imagine you need to create a configuration map for your application.

```java
import java.util.Map;

public class Config {
    public static final Map<String, String> APP_CONFIG = Map.of(
        "url", "https://example.com",
        "timeout", "5000",
        "retries", "3"
    );

    public static void main(String[] args) {
        System.out.println(APP_CONFIG);
    }
}
```

---

## ✅ **Summary of New Factory Methods:**

| **Method**            | **Description**                                      |
|-----------------------|------------------------------------------------------|
| `List.of()`           | Creates an unmodifiable list.                       |
| `Set.of()`            | Creates an unmodifiable set.                        |
| `Map.of()`            | Creates an unmodifiable map with key-value pairs.   |
| `Map.ofEntries()`     | Creates an unmodifiable map using `Map.entry()`.    |

These factory methods make code more concise, safer, and more efficient. Let me know if you need further clarification or examples! 😊
