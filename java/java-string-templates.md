# 📚 **Java String Templates (Preview Feature)**

Java **String Templates** is a **new feature introduced in Java 21** as a **preview feature**. It provides a more **concise, readable, and secure way** to embed expressions within strings. This feature improves upon the traditional **string concatenation** and **`String.format()`** approaches, making it easier to build **dynamic strings** in Java code.

---

## 🧩 **What are String Templates?**

String Templates allow you to embed **expressions inside curly braces (`{}`)** within a string, which will be evaluated and replaced with their corresponding values. This makes string interpolation more **readable** and **less error-prone** compared to manual concatenation.

**Syntax:**

```java
String formattedString = STR."Hello, {name}! You are {age} years old.";
```

- `STR` is the **template processor**.
- Expressions inside `{}` are **automatically evaluated**.
- The resulting string is **type-safe** and **secure**.

---

## 🎯 **Why Use String Templates?**

| **Traditional Approach**                                       | **String Templates Approach**                   |
|----------------------------------------------------------------|-------------------------------------------------|
| `"Hello, " + name + "! You are " + age + " years old."`        | `STR."Hello, {name}! You are {age} years old."` |
| `String.format("Hello, %s! You are %d years old.", name, age)` | `STR."Hello, {name}! You are {age} years old."` |

String Templates provide a **cleaner**, **safer**, and more **maintainable** way to format strings.

---

## 🧪 **Basic Example of String Templates**

```java
public class StringTemplatesExample {
    public static void main(String[] args) {
        String name = "Alice";
        int age = 25;

        String message = STR."Hello, {name}! You are {age} years old.";
        System.out.println(message); // Output: Hello, Alice! You are 25 years old.
    }
}
```

---

## 🔍 **Supported Expressions in String Templates**

String Templates support a variety of expressions inside `{}` including:

| **Expression**         | **Example**                                           |
|------------------------|-------------------------------------------------------|
| Variable               | `STR."Hello, {name}!"`                                |
| Method Call            | `STR."The length is {text.length()}."`                |
| Mathematical Operation | `STR."2 + 3 = {2 + 3}"`                               |
| Conditional Expression | `STR."You are {age >= 18 ? "an adult" : "a minor"}."` |

---

## 🛠 **Advanced Example with Method Calls**

```java
public class StringTemplatesExample {
    public static void main(String[] args) {
        String text = "Java";
        int count = 3;

        String result = STR."The text {text.toUpperCase()} repeats {count} times.";
        System.out.println(result); // Output: The text JAVA repeats 3 times.
    }
}
```

---

## 🚀 **Benefits of String Templates**

| **Benefit**                 | **Description**                                           |
|-----------------------------|-----------------------------------------------------------|
| 🚀 **Improved Readability** | Reduces the need for complex concatenation or formatting. |
| 🔒 **Type-Safe**            | Prevents runtime errors by ensuring correct types.        |
| 🛡️ **Secure**              | Avoids common string injection vulnerabilities.           |
| 📚 **Maintainable**         | Easier to update and maintain dynamic strings.            |

---

## 🧩 **Using `TemplateProcessor` for Custom Formatting**

The `STR` is a **built-in `TemplateProcessor`** that handles the replacement of placeholders. You can create your **custom `TemplateProcessor`** to handle templates differently.

Example of a custom processor:

```java
import java.lang.template.*;

public class CustomTemplateProcessor {
    public static void main(String[] args) {
        TemplateProcessor processor = TemplateProcessor.of(s -> s.toUpperCase());
        String message = processor.process("Hello, {name}!", "name", "Alice");
        System.out.println(message); // Output: HELLO, ALICE!
    }
}
```

---

## 🌐 **Combining String Templates with HTTP Requests**

String Templates can be very useful when **building HTTP requests** dynamically.

```java
public class HttpRequestExample {
    public static void main(String[] args) {
        String user = "john_doe";
        String endpoint = "https://api.example.com/users/{user}";

        System.out.println(STR."Fetching data from {endpoint}.");
    }
}
```

---

## ⚠️ **Limitations and Requirements**

| **Aspect**           | **Details**                          |
|----------------------|--------------------------------------|
| **Introduced In**    | Java 21 (Preview)                    |
| **Preview Feature**  | Requires **`--enable-preview`** flag |
| **Requires JDK 21+** | Not available in earlier versions    |

---

## 📋 **Enabling String Templates in Your Project**

### **Using Command Line:**

```bash
javac --enable-preview --release 21 Main.java
java --enable-preview Main
```

### **Using Gradle:**

```gradle
tasks.withType(JavaCompile).configureEach {
    options.compilerArgs += ['--enable-preview']
}
```

### **Using Maven:**

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.10.1</version>
    <configuration>
        <release>21</release>
        <compilerArgs>
            <arg>--enable-preview</arg>
        </compilerArgs>
    </configuration>
</plugin>
```

---

## 📌 **Summary of Java String Templates**

| **Feature**         | **Description**                                |
|---------------------|------------------------------------------------|
| **Introduced In**   | Java 21 (Preview Feature)                      |
| **Purpose**         | Embedding expressions directly in strings.     |
| **Key Syntax**      | `STR."Hello, {name}!"`                         |
| **Key Benefits**    | Readable, secure, maintainable, type-safe.     |
| **Usage Scenarios** | Logging, HTTP requests, dynamic messages, etc. |

---

Would you like more examples of **real-world use cases** for String Templates? 😊
