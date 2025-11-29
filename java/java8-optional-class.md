# 🚀 **Java 8 Optional Class**

The **`Optional`** class in **Java 8** is a **container object** that may or may not contain a **non-null value**. It helps to avoid **`NullPointerException`** by providing a way to **handle null values gracefully**.

The `Optional` class provides methods to:
1. Check if a value is present.
2. Return a default value if a value is missing.
3. Execute actions based on the presence or absence of a value.

---

## ✅ **Why Use `Optional`?**

- Avoid **`NullPointerException`**.
- Make your code **more readable and robust**.
- Eliminate the need for **null checks**.
- Encourage functional-style programming.

---

## ✅ **Creating an `Optional`**

There are **three ways** to create an `Optional`:

| **Method**                  | **Description**                                     | **Example**                    |
|-----------------------------|-----------------------------------------------------|--------------------------------|
| `Optional.of(value)`         | Creates an `Optional` with a non-null value         | `Optional<String> opt = Optional.of("Java");` |
| `Optional.ofNullable(value)` | Creates an `Optional` that can hold a null value    | `Optional<String> opt = Optional.ofNullable(null);` |
| `Optional.empty()`           | Creates an empty `Optional`                        | `Optional<String> opt = Optional.empty();` |

---

## ✅ **Example Code:**

```java
import java.util.Optional;

public class OptionalExample {
    public static void main(String[] args) {
        // Creating an Optional with a non-null value
        Optional<String> optionalValue = Optional.of("Java 8");

        // Check if the value is present
        if (optionalValue.isPresent()) {
            System.out.println("Value is present: " + optionalValue.get());
        }

        // Creating an Optional with a null value
        Optional<String> optionalNull = Optional.ofNullable(null);

        // Using orElse() to provide a default value
        String result = optionalNull.orElse("Default Value");
        System.out.println("Result: " + result);

        // Using ifPresent() to execute an action if the value is present
        optionalValue.ifPresent(value -> System.out.println("Processing: " + value));
    }
}
```

---

## ✅ **Important Methods in `Optional`**

| **Method**              | **Description**                                                     |
|-------------------------|---------------------------------------------------------------------|
| `isPresent()`            | Returns `true` if a value is present.                              |
| `get()`                  | Returns the value if present, otherwise throws `NoSuchElementException`. |
| `orElse(T other)`        | Returns the value if present, otherwise returns the default value.  |
| `orElseGet(Supplier)`    | Returns the value if present, otherwise invokes the supplier.       |
| `orElseThrow()`          | Returns the value if present, otherwise throws an exception.        |
| `ifPresent(Consumer)`    | Executes the given action if the value is present.                 |
| `map(Function)`          | Transforms the value if present and returns a new `Optional`.       |
| `flatMap(Function)`      | Similar to `map()`, but the function must return an `Optional`.     |
| `filter(Predicate)`      | Returns the `Optional` if the value matches the predicate.          |

---

## ✅ **Common Use Cases**

### 🔷 **Using `orElse()` to Provide a Default Value:**
```java
Optional<String> optional = Optional.ofNullable(null);
String result = optional.orElse("Default Value");
System.out.println(result);  // Output: Default Value
```

---

### 🔷 **Using `ifPresent()` to Perform an Action:**
```java
Optional<String> optional = Optional.of("Java 8");
optional.ifPresent(value -> System.out.println("Processing: " + value));
```

---

### 🔷 **Using `map()` to Transform a Value:**
```java
Optional<String> optional = Optional.of("Java 8");
Optional<Integer> length = optional.map(String::length);
System.out.println(length.orElse(0));  // Output: 6
```

---

### 🔷 **Using `flatMap()` to Handle Nested Optionals:**
```java
Optional<Optional<String>> nestedOptional = Optional.of(Optional.of("Java 8"));
Optional<String> flatOptional = nestedOptional.flatMap(opt -> opt);
System.out.println(flatOptional.orElse("No Value"));  // Output: Java 8
```

---

### 🔷 **Using `filter()` to Apply a Condition:**
```java
Optional<String> optional = Optional.of("Java 8");
optional.filter(value -> value.startsWith("Java"))
        .ifPresent(System.out::println);  // Output: Java 8
```

---

## ✅ **Best Practices for Using `Optional`**

1. **Avoid using `Optional.get()`** to prevent `NoSuchElementException`.  
   Use **`orElse()`**, **`orElseGet()`**, or **`ifPresent()`** instead.

2. **Use `Optional` for return types**, not for method parameters or instance variables.

3. **Combine with Stream API** for more concise code.

---

## ✅ **Summary**

| **Method**            | **Usage**                        |
|-----------------------|----------------------------------|
| `Optional.of(value)`   | Wraps a non-null value.          |
| `Optional.ofNullable(value)` | Wraps a value that might be null. |
| `Optional.empty()`     | Creates an empty `Optional`.     |
| `orElse()`             | Returns a default value if empty.|
| `ifPresent()`          | Executes an action if a value is present. |

---
