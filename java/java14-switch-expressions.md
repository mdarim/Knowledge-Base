# ✅ **Java 14: Switch Expressions**

In **Java 14**, **switch expressions** were introduced as a **permanent feature** after being previewed in **Java 12** and **Java 13**. This enhancement makes the `switch` statement **more concise**, **expressive**, and **less error-prone**.

Switch expressions allow the `switch` to **return a value**, use **arrow syntax** for cases, and eliminate the need for `break` statements.

---

## 🧩 **Key Features of Switch Expressions:**

| **Feature**            | **Description**                                                 |
|------------------------|-----------------------------------------------------------------|
| **Expression and Statement** | `switch` can now be used as both an **expression** and a **statement**. |
| **Arrow Syntax (`->`)** | Cleaner syntax using `->` instead of `case` and `break`.        |
| **Yield Statement**     | The `yield` keyword allows returning values from switch expressions. |
| **Multiple Case Labels** | Multiple labels can be combined using commas (e.g., `case A, B ->`). |

---

## 🔧 **Old `switch` Statement (Before Java 14)**

Here's how the traditional `switch` statement looked before **Java 14**:

```java
public int getDayNumber(String day) {
    int dayNumber;
    switch (day) {
        case "Monday":
            dayNumber = 1;
            break;
        case "Tuesday":
            dayNumber = 2;
            break;
        case "Wednesday":
            dayNumber = 3;
            break;
        default:
            dayNumber = -1;
            break;
    }
    return dayNumber;
}
```

### 🔴 **Problems with the Old Syntax:**
1. **Verbose**: Repeating `break` statements makes the code longer.
2. **Error-Prone**: Forgetting a `break` can cause **fall-through issues**.
3. **No Return Values**: The `switch` cannot **return values directly**.

---

## ✅ **New Switch Expression in Java 14**

Java 14 introduced **switch expressions** that simplify the code:

### 🔧 **Example with Arrow Syntax:**

```java
public int getDayNumber(String day) {
    return switch (day) {
        case "Monday" -> 1;
        case "Tuesday" -> 2;
        case "Wednesday" -> 3;
        default -> -1;
    };
}
```

### 📝 **Key Changes:**
- No need for **`break`**.
- The `switch` now **returns a value**.
- Cleaner **arrow syntax** (`->`).

---

## 🔧 **Example with `yield` Keyword:**

The `yield` keyword can be used to **return values** inside a `switch` expression when more complex logic is needed.

```java
public int getDayNumber(String day) {
    return switch (day) {
        case "Monday" -> 1;
        case "Tuesday" -> 2;
        case "Wednesday" -> 3;
        default -> {
            System.out.println("Invalid day!");
            yield -1;
        }
    };
}
```

### 📝 **Explanation:**
- The `yield` statement is used inside a **block** to return a value.
- This allows for **more complex logic** in the `default` or any other case.

---

## 🔧 **Example with Multiple Case Labels:**

You can combine **multiple case labels** with commas:

```java
public String getTypeOfDay(String day) {
    return switch (day) {
        case "Saturday", "Sunday" -> "Weekend";
        case "Monday", "Tuesday", "Wednesday", "Thursday", "Friday" -> "Weekday";
        default -> "Invalid day";
    };
}
```

### 📝 **Explanation:**
- **Multiple case labels** (e.g., `"Saturday", "Sunday"`) can be handled in a single line.
- This reduces **redundancy** and makes the code more readable.

---

## 🎯 **When to Use Switch Expressions?**

Switch expressions are ideal for:

| **Use Case**            | **Benefits**                                                |
|-------------------------|------------------------------------------------------------|
| **Returning values**     | Switch expressions can **directly return values**.         |
| **Multiple case labels** | Combine cases into a single **arrow case** for simplicity. |
| **Avoiding fall-through** | No need for **`break` statements** to prevent fall-through. |

---

## 📚 **Examples of Using Switch Expressions**

### ✅ **Example 1: Return String Value**

```java
public String getGreeting(String language) {
    return switch (language) {
        case "English" -> "Hello";
        case "French" -> "Bonjour";
        case "Spanish" -> "Hola";
        default -> "Unknown language";
    };
}
```

---

### ✅ **Example 2: Using Yield for Complex Logic**

```java
public int calculateScore(String grade) {
    return switch (grade) {
        case "A" -> 10;
        case "B" -> 8;
        case "C" -> 6;
        default -> {
            System.out.println("Invalid grade");
            yield 0;
        }
    };
}
```

---

### ✅ **Example 3: Traditional vs Modern Comparison**

| **Traditional Switch (Before Java 14)** | **Switch Expression (Java 14)**                     |
|-----------------------------------------|-----------------------------------------------------|
| Uses `break` to prevent fall-through     | No `break` needed                                  |
| Cannot return a value directly           | Can **return values** directly                     |
| More verbose                             | Cleaner and more concise with **arrow syntax**      |

---

## 🔧 **Example with Enum:**

Switch expressions also work with **enums**:

```java
public int getSeasonMonthCount(Season season) {
    return switch (season) {
        case WINTER -> 3;
        case SPRING -> 3;
        case SUMMER -> 3;
        case FALL -> 3;
    };
}
```

---

## 🛠️ **How to Run Switch Expressions:**

Make sure you're using **Java 14** or later.

```bash
javac Main.java
java Main
```

---

## 🎯 **Summary of Switch Expression Syntax:**

| **Old Syntax**                         | **New Syntax (Java 14)**             |
|----------------------------------------|--------------------------------------|
| `case value:`                          | `case value ->`                      |
| Requires `break` to avoid fall-through | No `break` needed                    |
| Cannot return values                   | Returns values directly with `yield` |
| Repetitive and verbose                 | Concise and expressive               |

---

## 🎯 **Conclusion:**

Switch expressions in **Java 14** provide a **more concise** and **expressive** way to use the `switch` construct. They eliminate **fall-through issues**, simplify **syntax** with **arrow cases**, and enable **returning values** directly from the `switch`.

These improvements make the code cleaner, less error-prone, and more maintainable — especially useful in **modern Java applications** and **functional programming** styles.

