# ✅ **Java 11 String Methods: New Methods and Examples**

Java 11 introduced several new methods in the **`String`** class to enhance string handling. These methods make common string operations more efficient and concise, improving code readability and maintainability.

---

## 🆕 **New String Methods in Java 11:**

| **Method**          | **Description**                                           |
|---------------------|-----------------------------------------------------------|
| `isBlank()`         | Checks if a string is empty or contains only whitespace.   |
| `lines()`           | Splits a string into a stream of lines.                   |
| `strip()`           | Removes leading and trailing whitespaces.                 |
| `stripLeading()`    | Removes leading whitespaces.                              |
| `stripTrailing()`   | Removes trailing whitespaces.                             |
| `repeat(int count)` | Repeats the string a specified number of times.           |

---

### 🔧 **1. `isBlank()`**

This method returns **`true`** if the string is either **empty** or contains only **whitespace characters**.

#### ✅ **Example:**

```java
public class Main {
    public static void main(String[] args) {
        String str1 = "   ";
        String str2 = "Hello";

        System.out.println(str1.isBlank());  // true
        System.out.println(str2.isBlank());  // false
    }
}
```

---

### 🔧 **2. `lines()`**

This method splits a **multi-line string** into a **`Stream<String>`** where each line is a separate element in the stream.

#### ✅ **Example:**

```java
import java.util.stream.Stream;

public class Main {
    public static void main(String[] args) {
        String str = "Hello\nWorld\nJava 11";

        Stream<String> lines = str.lines();
        lines.forEach(System.out::println);
    }
}
```

### **Output:**
```text
Hello
World
Java 11
```

---

### 🔧 **3. `strip()`**

This method removes **leading** and **trailing whitespace** characters. It is similar to **`trim()`**, but more Unicode-aware.

#### ✅ **Example:**

```java
public class Main {
    public static void main(String[] args) {
        String str = "   Hello Java 11   ";

        System.out.println("Original: [" + str + "]");
        System.out.println("Stripped: [" + str.strip() + "]");
    }
}
```

### **Output:**
```text
Original: [   Hello Java 11   ]
Stripped: [Hello Java 11]
```

---

### 🔧 **4. `stripLeading()`**

This method removes **only the leading whitespace** from the string.

#### ✅ **Example:**

```java
public class Main {
    public static void main(String[] args) {
        String str = "   Leading Spaces Removed";

        System.out.println("Original: [" + str + "]");
        System.out.println("Stripped Leading: [" + str.stripLeading() + "]");
    }
}
```

### **Output:**
```text
Original: [   Leading Spaces Removed]
Stripped Leading: [Leading Spaces Removed]
```

---

### 🔧 **5. `stripTrailing()`**

This method removes **only the trailing whitespace** from the string.

#### ✅ **Example:**

```java
public class Main {
    public static void main(String[] args) {
        String str = "Trailing Spaces Removed   ";

        System.out.println("Original: [" + str + "]");
        System.out.println("Stripped Trailing: [" + str.stripTrailing() + "]");
    }
}
```

### **Output:**
```text
Original: [Trailing Spaces Removed   ]
Stripped Trailing: [Trailing Spaces Removed]
```

---

### 🔧 **6. `repeat(int count)`**

This method returns a **new string** that is the result of repeating the original string **`count`** times.

#### ✅ **Example:**

```java
public class Main {
    public static void main(String[] args) {
        String str = "Java ";

        System.out.println(str.repeat(3));  // Java Java Java 
    }
}
```

---

### 🔧 **7. Using All Methods Together**

#### ✅ **Example:**

```java
import java.util.stream.Stream;

public class Main {
    public static void main(String[] args) {
        String multiLineStr = "  Hello Java 11 \nWelcome to the new String methods  ";

        // Using strip()
        System.out.println("Stripped: [" + multiLineStr.strip() + "]");

        // Using lines()
        Stream<String> lines = multiLineStr.lines();
        lines.forEach(line -> System.out.println("Line: " + line.strip()));

        // Using isBlank()
        System.out.println("Is blank: " + "   ".isBlank());

        // Using repeat()
        System.out.println("Repeated: " + "Java 11 ".repeat(2));
    }
}
```

### **Output:**
```text
Stripped: [Hello Java 11 
Welcome to the new String methods]
Line: Hello Java 11
Line: Welcome to the new String methods
Is blank: true
Repeated: Java 11 Java 11 
```

---

## 🎯 **Comparison Between `strip()`, `trim()`, and `stripLeading()`/`stripTrailing()`:**

| **Method**        | **Removes**                          | **Unicode Aware** |
|-------------------|--------------------------------------|-------------------|
| `strip()`         | Leading and trailing whitespace       | ✅ Yes            |
| `stripLeading()`  | Leading whitespace only               | ✅ Yes            |
| `stripTrailing()` | Trailing whitespace only              | ✅ Yes            |
| `trim()`          | Leading and trailing whitespace       | ❌ No             |

---

## 🎯 **When to Use These Methods?**

| **Method**          | **Use Case**                                           |
|---------------------|-------------------------------------------------------|
| `isBlank()`         | To check if a string is empty or contains only whitespace. |
| `lines()`           | To process multi-line strings efficiently.             |
| `strip()`           | To remove whitespace in a Unicode-aware way.           |
| `stripLeading()`    | To remove leading spaces only.                         |
| `stripTrailing()`   | To remove trailing spaces only.                        |
| `repeat(int count)` | To repeat strings multiple times.                      |

---

These new **Java 11 String methods** improve how developers work with strings, making code more concise and easier to maintain. 😊
