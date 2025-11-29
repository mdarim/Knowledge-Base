## 📌 **Java 16: Pattern Matching for `instanceof`**

Java 16 introduced **Pattern Matching for `instanceof`** as a feature that simplifies **type checks and type casts** when using the `instanceof` operator. This enhancement makes code **shorter**, **cleaner**, and **less error-prone** by eliminating the need for **explicit casting** after an `instanceof` check.

---

### 🔎 **What is Pattern Matching for `instanceof`?**

Traditionally, when using `instanceof`, you had to perform **two steps**:

1. **Check the type** using `instanceof`.
2. **Cast** the object to that type.

With **Pattern Matching for `instanceof`**, these two steps are combined into **one concise statement**.

---

### ✅ **Before Java 16 (Old Way):**

```java
if (obj instanceof String) {
    String str = (String) obj;  // Explicit cast
    System.out.println(str.length());
}
```

### ✅ **Java 16 (New Way):**

```java
if (obj instanceof String str) {
    System.out.println(str.length());  // No explicit cast needed
}
```

---

### 🛠 **Example Code:**

#### **1. Basic Example**

```java
public class PatternMatchingExample {
    public static void main(String[] args) {
        Object obj = "Hello, Java 16!";

        if (obj instanceof String str) {
            System.out.println("String length: " + str.length());
        }
    }
}
```

#### **Output:**

```
String length: 14
```

---

### 📚 **How It Works:**

- `instanceof` now performs both the **type check** and **type casting**.
- The variable declared after `instanceof` (e.g., `String str`) is **automatically cast** if the check passes.
- You can directly use the new variable within the `if` block.

---

### 🛠 **2. Example with `else` Block**

```java
public class PatternMatchingExample {
    public static void main(String[] args) {
        Object obj = 123;

        if (obj instanceof String str) {
            System.out.println("String length: " + str.length());
        } else {
            System.out.println("Not a string.");
        }
    }
}
```

#### **Output:**

```
Not a string.
```

---

### 🛠 **3. Example with `null` Check**

Pattern Matching for `instanceof` automatically handles **`null` values**.

```java
public class PatternMatchingExample {
    public static void main(String[] args) {
        Object obj = null;

        if (obj instanceof String str) {
            System.out.println("String length: " + str.length());
        } else {
            System.out.println("Object is null or not a string.");
        }
    }
}
```

#### **Output:**

```
Object is null or not a string.
```

---

### 🔄 **Combining with Logical Operators**

You can also combine **logical operators** like `&&` and `||` with the pattern-matching `instanceof`.

#### **Example:**

```java
public class LogicalOperatorExample {
    public static void main(String[] args) {
        Object obj = "Hello, Java!";

        if (obj instanceof String str && str.length() > 5) {
            System.out.println("String is long enough: " + str);
        }
    }
}
```

#### **Output:**

```
String is long enough: Hello, Java!
```

---

### 🧪 **Example with Methods**

```java
public class PatternMatchingExample {
    public static void main(String[] args) {
        Object obj = "Pattern Matching";

        printObjectInfo(obj);
    }

    public static void printObjectInfo(Object obj) {
        if (obj instanceof String str) {
            System.out.println("String value: " + str);
        } else {
            System.out.println("Not a string.");
        }
    }
}
```

#### **Output:**

```
String value: Pattern Matching
```

---

### ⚙️ **Benefits of Pattern Matching for `instanceof`:**

| **Benefit**           | **Description**                                             |
|-----------------------|-------------------------------------------------------------|
| **Cleaner Code**       | Eliminates redundant type casts.                           |
| **Less Error-Prone**   | Reduces chances of casting errors.                         |
| **More Readable**      | Combines type checking and casting into a single step.      |
| **Automatic `null` Check** | No need for manual `null` checks in `instanceof`.         |

---

### 📋 **Rules to Remember:**

1. The variable declared in the pattern (`String str`) is only **accessible inside the `if` block**.
2. Pattern matching **handles `null` values** safely.
3. You can use **logical operators** like `&&` and `||` in the `if` condition.

---

### 🛠 **Example: Using Pattern Matching in a Switch Statement (Java 17+ Feature)**

In Java 17, **switch statements** were enhanced to support **pattern matching**:

```java
public class SwitchPatternMatchingExample {
    public static void main(String[] args) {
        Object obj = "Hello, Java 17!";

        switch (obj) {
            case String str -> System.out.println("String length: " + str.length());
            default -> System.out.println("Not a string.");
        }
    }
}
```

---

### 🛠 **Real-World Use Case: Handling Different Types in a Method**

```java
public class RealWorldExample {
    public static void main(String[] args) {
        printValue("Java");
        printValue(100);
    }

    public static void printValue(Object obj) {
        if (obj instanceof String str) {
            System.out.println("It's a string: " + str);
        } else if (obj instanceof Integer num) {
            System.out.println("It's an integer: " + num);
        } else {
            System.out.println("Unknown type.");
        }
    }
}
```

#### **Output:**

```
It's a string: Java
It's an integer: 100
```

---

### 🚀 **Summary of Pattern Matching for `instanceof`:**

| **Aspect**                 | **Before Java 16**                                       | **Java 16+ (Pattern Matching)**                             |
|----------------------------|----------------------------------------------------------|-------------------------------------------------------------|
| **Type Check & Casting**    | Manual type check + explicit cast                        | Automatic type check and cast in one step                   |
| **Syntax**                  | `if (obj instanceof String) { String str = (String) obj; }` | `if (obj instanceof String str) { ... }`                    |
| **`null` Handling**         | Requires additional checks                               | Automatically handles `null` values                         |
| **Readability**             | Less readable due to redundant code                      | More concise and readable                                   |

---
