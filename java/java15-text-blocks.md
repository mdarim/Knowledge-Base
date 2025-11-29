## 📌 **Java 15: Text Blocks**

Introduced as a **preview feature** in **Java 13**, text blocks were **standardized** in **Java 15**. They make it easier to work with **multi-line strings** by allowing developers to write them without using cumbersome **escape sequences** or **string concatenation**.

---

### 🔎 **What is a Text Block?**
A **text block** is a **multi-line string literal** that uses **triple double quotes (`"""`)** as delimiters. It helps you write cleaner, more readable code, especially for multi-line content such as:

- HTML
- SQL queries
- JSON/XML
- Markdown

---

### ✅ **Key Features of Text Blocks:**

1. **No need for escape characters** (`\n`, `\t`, etc.).
2. **Preserves multi-line structure** and **formatting**.
3. **More readable and maintainable code**.
4. **Supports indentation**.
5. **Automatic removal of leading whitespace**.
6. **Concatenation and formatting supported**.

---

### 📚 **Syntax:**

```java
String textBlock = """
    This is a multi-line
    text block in Java.
    """;
```

---

### 🛠 **Example Code:**

#### **1. Simple Multi-line Text Block**

```java
public class TextBlockExample {
    public static void main(String[] args) {
        String textBlock = """
            Hello, World!
            This is a multi-line text block.
            Welcome to Java 15.
            """;
        System.out.println(textBlock);
    }
}
```

#### **Output:**

```
Hello, World!
This is a multi-line text block.
Welcome to Java 15.
```

---

#### **2. Text Block for SQL Query**

```java
public class SQLTextBlockExample {
    public static void main(String[] args) {
        String sqlQuery = """
            SELECT id, name, email
            FROM users
            WHERE status = 'active'
            ORDER BY name;
            """;
        System.out.println(sqlQuery);
    }
}
```

#### **Output:**

```
SELECT id, name, email
FROM users
WHERE status = 'active'
ORDER BY name;
```

---

#### **3. HTML Text Block Example**

```java
public class HTMLTextBlockExample {
    public static void main(String[] args) {
        String html = """
            <html>
                <body>
                    <h1>Welcome to Java 15</h1>
                </body>
            </html>
            """;
        System.out.println(html);
    }
}
```

---

### 🔄 **Comparison Between Text Blocks and Traditional Strings**

| **Aspect**        | **Traditional Strings**                 | **Text Blocks**                        |
|-------------------|-----------------------------------------|----------------------------------------|
| Multi-line String | Requires `\n` and concatenation         | Uses `"""` to define multi-line text   |
| Readability       | Hard to read and maintain               | More readable and maintainable         |
| Escape Characters | Needs escape sequences (`\n`, `\t`, etc.) | No need for escape sequences           |
| Indentation       | Manual handling required                | Automatic handling of indentation      |

---

### 📋 **Using Escape Sequences in Text Blocks**

Text blocks support **escape sequences** just like regular strings. For example:

```java
public class EscapeSequenceExample {
    public static void main(String[] args) {
        String textBlock = """
            Hello\tWorld!\nJava 15 is here!
            """;
        System.out.println(textBlock);
    }
}
```

#### **Output:**

```
Hello   World!
Java 15 is here!
```

---

### 🎯 **Working with Indentation in Text Blocks**

Text blocks automatically handle **leading whitespace**. You can control **indentation** using the following rules:

1. **Leading whitespace is automatically removed**.
2. The indentation of the least indented line is used as a reference.

#### **Example with Indentation:**

```java
public class IndentationExample {
    public static void main(String[] args) {
        String json = """
            {
                "name": "John Doe",
                "age": 30,
                "active": true
            }
            """;
        System.out.println(json);
    }
}
```

#### **Output:**

```
{
    "name": "John Doe",
    "age": 30,
    "active": true
}
```

---

### 🔧 **Concatenating Text Blocks**

You can concatenate **text blocks** with other strings or variables:

```java
public class TextBlockConcatExample {
    public static void main(String[] args) {
        String name = "Java 15";
        String message = """
            Welcome to %s!
            """.formatted(name);

        System.out.println(message);
    }
}
```

#### **Output:**

```
Welcome to Java 15!
```

---

### 🧪 **Handling Special Characters**

Text blocks handle **special characters** like **quotes** easily without the need for escaping:

```java
public class SpecialCharactersExample {
    public static void main(String[] args) {
        String textBlock = """
            He said, "Hello, World!"
            'Single quotes' are also fine.
            """;
        System.out.println(textBlock);
    }
}
```

#### **Output:**

```
He said, "Hello, World!"
'Single quotes' are also fine.
```

---

### 🔄 **Using Text Blocks with Formatting**

You can use **`String.format()`** or **`formatted()`** to format text blocks:

```java
public class TextBlockFormattingExample {
    public static void main(String[] args) {
        String name = "John";
        int age = 30;

        String textBlock = """
            Name: %s
            Age: %d
            """.formatted(name, age);

        System.out.println(textBlock);
    }
}
```

#### **Output:**

```
Name: John
Age: 30
```

---

### ⚡ **Benefits of Text Blocks**

| **Benefit**               | **Description**                                              |
|---------------------------|--------------------------------------------------------------|
| **Improves readability**   | Simplifies multi-line strings without escaping or concatenation. |
| **Reduces errors**         | No need to manually handle escape sequences.                 |
| **Supports localization**  | Easily supports multiple languages with text blocks.         |
| **Improves maintainability** | Easier to modify and update multi-line text.               |

---

### ⚠️ **Things to Keep in Mind**

1. **Text blocks must be enclosed within triple double quotes (`"""`)**.
2. They **preserve line breaks**.
3. **Indentation is automatically adjusted** based on the least-indented line.

---

### 📋 **Summary:**

| **Feature**         | **Description**                                           |
|---------------------|-----------------------------------------------------------|
| Introduced in       | **Java 15**                                               |
| Purpose             | Simplify handling of **multi-line strings**               |
| Syntax              | Uses **triple double quotes (`"""`)**                     |
| Benefits            | **Improved readability**, **no escape sequences**, **indentation handling** |
| Usage Scenarios     | SQL queries, JSON/XML, HTML, Markdown, etc.               |

---
