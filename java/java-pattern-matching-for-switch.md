# 🎨 **Java Pattern Matching for Switch (Java 17 and beyond)**

Java introduced **Pattern Matching for `switch` statements** as a **preview feature** in **Java 17** through **JEP 406**. It became **stable** in **Java 21**, providing a **more concise and expressive way** to handle complex conditional logic using patterns.

Pattern Matching for `switch` allows you to:

✅ **Simplify type checks** and **casts** in switch statements.  
✅ Use **patterns** to match **types, constants, null values**, etc.  
✅ Write **concise, readable, and maintainable code**.

---

## 📌 **Why Use Pattern Matching for `switch`?**

Before pattern matching, developers had to write **verbose `instanceof` checks** followed by **manual type casting**. Pattern Matching for `switch` eliminates this verbosity by:

1. **Automatically checking the type**.
2. **Performing the cast** for you.
3. **Reducing boilerplate code**.

---

## 🛠️ **Basic Syntax of Pattern Matching for `switch`**

```java
switch (object) {
    case String s -> System.out.println("It's a string: " + s);
    case Integer i -> System.out.println("It's an integer: " + i);
    case null -> System.out.println("It's null");
    default -> System.out.println("Unknown type");
}
```

---

## 🖥️ **Example: Pattern Matching for `switch` in Action**

### ✅ **Before Pattern Matching:**

```java
public void printObjectType(Object obj) {
    if (obj instanceof String) {
        String s = (String) obj;
        System.out.println("It's a string: " + s);
    } else if (obj instanceof Integer) {
        Integer i = (Integer) obj;
        System.out.println("It's an integer: " + i);
    } else {
        System.out.println("Unknown type");
    }
}
```

---

### ✅ **Using Pattern Matching for `switch`:**

```java
public void printObjectType(Object obj) {
    switch (obj) {
        case String s -> System.out.println("It's a string: " + s);
        case Integer i -> System.out.println("It's an integer: " + i);
        default -> System.out.println("Unknown type");
    }
}
```

---

## 🧩 **Example with Multiple Patterns**

You can handle **multiple patterns** in a single `case` using the **OR (`|`) operator**.

```java
public void handleNumber(Number number) {
    switch (number) {
        case Integer i, Long l, Short s -> System.out.println("It's an integer-like number");
        case Float f, Double d -> System.out.println("It's a floating-point number");
        default -> System.out.println("Unknown number type");
    }
}
```

---

## 📚 **Example: Handling Null Values in Switch**

Pattern Matching for `switch` also allows handling **`null` values** directly.

```java
public void handleObject(Object obj) {
    switch (obj) {
        case null -> System.out.println("It's null");
        case String s -> System.out.println("It's a string: " + s);
        default -> System.out.println("Unknown type");
    }
}
```

---

## 🧪 **Example: Guarded Patterns (Using `if` in `case`)**

You can add **conditions** to a pattern using **guarded patterns** with an **`if` clause**.

```java
public void handleShape(Object shape) {
    switch (shape) {
        case String s when s.length() > 5 -> System.out.println("It's a long string: " + s);
        case String s -> System.out.println("It's a short string: " + s);
        case null -> System.out.println("It's null");
        default -> System.out.println("Unknown type");
    }
}
```

---

## 🔍 **Example: Exhaustiveness in Switch**

Java enforces **exhaustiveness checks** in pattern-matching `switch` statements to ensure all possible cases are covered.

### ✅ **Example with Sealed Classes:**

```java
sealed interface Shape permits Circle, Rectangle {}

final class Circle implements Shape {}
final class Rectangle implements Shape {}

public void handleShape(Shape shape) {
    switch (shape) {
        case Circle c -> System.out.println("It's a circle");
        case Rectangle r -> System.out.println("It's a rectangle");
    }
}
```

Since the **`Shape` interface is sealed**, the compiler knows all possible subclasses and ensures **no case is missed**.

---

## 🧑‍💻 **Practical Example: Handling Various Data Types**

```java
public void processInput(Object input) {
    switch (input) {
        case String s -> System.out.println("Processing a string: " + s);
        case Integer i when i > 0 -> System.out.println("Processing a positive integer: " + i);
        case Integer i -> System.out.println("Processing a non-positive integer: " + i);
        case Double d -> System.out.println("Processing a double: " + d);
        case null -> System.out.println("Input is null");
        default -> System.out.println("Unknown input type");
    }
}
```

---

## 📊 **Benefits of Pattern Matching for `switch`**

| **Benefit**                      | **Description**                                                       |
|----------------------------------|-----------------------------------------------------------------------|
| 🔧 **Reduces Boilerplate**       | No need for manual type checks and casts.                             |
| 🎯 **Improves Readability**      | More concise and expressive code.                                     |
| ✅ **Supports Null Handling**     | Handles `null` values directly in switch cases.                       |
| 🚀 **Supports Guarded Patterns** | Adds conditional logic using `when`.                                  |
| 🛡️ **Exhaustiveness Checks**    | Ensures all possible cases are handled, especially with sealed types. |

---

## 🚦 **Rules and Restrictions**

1. **Switch must be exhaustive.**  
   If you’re using sealed types, all possible subtypes must be handled.

2. **`case` labels must be unique.**  
   You cannot have overlapping patterns in different cases.

3. **Guarded patterns (`when`) are supported.**  
   You can use `case Type t when condition ->`.

---

## ⚡ **Summary of New Switch Features**

| **Feature**             | **Description**                                                        |
|-------------------------|------------------------------------------------------------------------|
| `case Type t`           | Matches an object against a type and casts it automatically.           |
| `case null`             | Handles null values directly in a switch statement.                    |
| `case t when condition` | Adds additional conditions to cases using `when`.                      |
| Exhaustiveness Checks   | Ensures all possible cases are covered, especially for sealed classes. |

---

## 🧩 **Conclusion**

Java's **Pattern Matching for `switch`** is a **powerful feature** that simplifies **type-checking and casting**. It helps reduce **boilerplate code**, improves **readability**, and ensures **more robust error handling**. With support for **guarded patterns** and **null handling**, it is a significant step forward in making **Java more expressive and concise**.

Let me know if you'd like more examples or explanations! 😊
