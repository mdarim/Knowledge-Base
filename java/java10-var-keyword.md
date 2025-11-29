# ✅ **Java 10 `var` Keyword: Local Variable Type Inference**

The **`var` keyword** was introduced in **Java 10** as part of the **local variable type inference** feature. It allows you to declare variables without explicitly specifying their type. Instead, the **Java compiler infers the type** from the variable's initializer.

---

## 🎯 **Key Points about `var` in Java:**

| **Feature**               | **Details**                                                         |
|---------------------------|----------------------------------------------------------------------|
| **Introduced in**          | Java 10                                                              |
| **Type Inference**         | The compiler infers the variable's type based on the assigned value. |
| **Local Scope Only**       | Can only be used for **local variables** (inside methods, loops, etc.). |
| **Cannot be Null**         | `var` variables **cannot be initialized with `null`**.               |
| **Cannot be Used for Fields** | Cannot be used for **class fields** or **method parameters**.       |

---

## 🛠️ **1. Basic Example of `var` Usage**

```java
public class Main {
    public static void main(String[] args) {
        var message = "Hello, Java 10!";
        var number = 100;
        var price = 19.99;

        System.out.println("Message: " + message);
        System.out.println("Number: " + number);
        System.out.println("Price: " + price);
    }
}
```

### **Output:**
```text
Message: Hello, Java 10!
Number: 100
Price: 19.99
```

---

## 🛠️ **2. Using `var` in Loops**

### ✅ **Example with `for` Loop:**

```java
public class Main {
    public static void main(String[] args) {
        var numbers = new int[]{1, 2, 3, 4, 5};

        for (var num : numbers) {
            System.out.println(num);
        }
    }
}
```

---

## 🛠️ **3. Using `var` with Collections**

### ✅ **Example with `List`:**

```java
import java.util.List;

public class Main {
    public static void main(String[] args) {
        var list = List.of("Java", "Python", "C++");

        for (var item : list) {
            System.out.println(item);
        }
    }
}
```

---

## 🛠️ **4. Using `var` with Streams**

```java
import java.util.stream.Stream;

public class Main {
    public static void main(String[] args) {
        var stream = Stream.of("A", "B", "C");

        stream.forEach(item -> {
            var message = "Processing: " + item;
            System.out.println(message);
        });
    }
}
```

---

## 🛠️ **5. Using `var` with Maps**

```java
import java.util.Map;

public class Main {
    public static void main(String[] args) {
        var map = Map.of("key1", "value1", "key2", "value2");

        for (var entry : map.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
    }
}
```

---

## 🛑 **Restrictions on Using `var`**

| **Restriction**                   | **Reason**                                                        |
|------------------------------------|-------------------------------------------------------------------|
| **Cannot use `var` for method parameters** | Method parameters must have explicit types.                      |
| **Cannot use `var` for class fields** | Only local variables are supported.                              |
| **Cannot initialize `var` with `null`** | The compiler cannot infer the type from `null`.                  |
| **Cannot use `var` without initialization** | The type must be inferred from the assigned value.               |

### ❌ **Invalid Code Examples:**

```java
var number;  // ❌ Error: variable needs to be initialized
var value = null;  // ❌ Error: cannot infer type from 'null'
```

---

## 🛠️ **6. Using `var` with Complex Types**

```java
import java.util.HashMap;

public class Main {
    public static void main(String[] args) {
        var map = new HashMap<String, Integer>();
        map.put("A", 1);
        map.put("B", 2);

        for (var entry : map.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
    }
}
```

---

## 🎯 **When to Use `var`?**

| ✅ **Recommended**                      | ❌ **Not Recommended**                       |
|-----------------------------------------|---------------------------------------------|
| When the type is obvious and improves readability. | When it makes the code harder to understand. |
| For complex types (e.g., collections, streams). | For method parameters or class fields.       |
| In loops and stream operations.          | When initialization is `null` or unclear.    |

---

## 🧪 **7. Type Inference Rules for `var`**

| **Initializer**            | **Inferred Type**               |
|----------------------------|---------------------------------|
| `var message = "Hello";`    | `String`                        |
| `var number = 42;`          | `int`                           |
| `var price = 19.99;`        | `double`                        |
| `var list = List.of(1, 2);` | `List<Integer>`                 |

---

## 🔧 **Comparison of `var` with Explicit Types**

| **Before Java 10**            | **With Java 10 `var`**       |
|--------------------------------|-----------------------------|
| `List<String> list = new ArrayList<>();` | `var list = new ArrayList<String>();` |
| `Map<Integer, String> map = new HashMap<>();` | `var map = new HashMap<Integer, String>();` |

---

## ✅ **Advantages of Using `var`**

1. **Improves Code Readability** (especially for complex types).
2. **Reduces Boilerplate Code.**
3. **Makes Code More Concise.**
4. **Enhances Maintainability** (if variable types change later).

---

## ❌ **Disadvantages of Using `var`**

1. **May Reduce Code Readability** (if overused or used improperly).
2. **Type Inference Might Be Confusing** for new developers.
3. **Cannot Be Used Everywhere** (e.g., method parameters, class fields).

---

## 🛠️ **Summary of New `String` Methods in Java 11:**

| **Method**              | **Description**                                       |
|-------------------------|-------------------------------------------------------|
| `isBlank()`             | Returns `true` if the string is empty or contains only whitespace. |
| `lines()`               | Splits a string into a stream of lines.               |
| `strip()`, `stripLeading()`, `stripTrailing()` | Removes leading/trailing spaces. |
| `repeat(int n)`         | Repeats the string `n` times.                         |

---

This makes **Java 10's `var` keyword** a powerful tool for simplifying your code.
