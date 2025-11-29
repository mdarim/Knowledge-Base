# ✅ **Java 9: Private Methods in Interfaces**

One of the significant features introduced in **Java 9** is the ability to define *
*private methods** inside **interfaces**. This enhancement helps in **code reusability**
and **maintainability** by allowing interfaces to have reusable logic shared across
multiple **default** and **static** methods.

---

## 🎯 **Why Private Methods in Interfaces?**

Before Java 9, interfaces could only have:

- **Abstract methods** (without implementation)
- **Default methods** (introduced in Java 8)
- **Static methods** (introduced in Java 8)

However, **default** and **static** methods often had **repeated code**, which led to *
*code duplication**. With private methods, interfaces can now share reusable logic between
default and static methods, **avoiding duplication**.

---

## 🛠️ **Syntax of Private Methods in Interfaces:**

```java
// Private method
private void methodName() {
    // logic
}

// Private static method
private static void methodName() {
    // logic
}
```

---

## ✅ **Types of Private Methods in Interfaces:**

| **Method Type**  | **Description**                                    |
|------------------|----------------------------------------------------|
| `private`        | Used to **share logic** among **default methods**. |
| `private static` | Used to **share logic** among **static methods**.  |

---

## 🔧 **Example 1: Private Methods in Interfaces**

```java
interface MyInterface {
    // Default method
    default void defaultMethod1() {
        System.out.println("Default Method 1");
        commonLogic();  // Calling private method
    }

    // Default method
    default void defaultMethod2() {
        System.out.println("Default Method 2");
        commonLogic();  // Calling private method
    }

    // Private method
    private void commonLogic() {
        System.out.println("Common Logic executed");
    }
}

public class Main {
    public static void main(String[] args) {
        MyInterface myInterface = new MyInterface() {
        };  // Anonymous class
        myInterface.defaultMethod1();
        myInterface.defaultMethod2();
    }
}
```

### **Output:**

```text
Default Method 1
Common Logic executed
Default Method 2
Common Logic executed
```

---

## 🔧 **Example 2: Private Static Methods in Interfaces**

```java
interface UtilityInterface {
    // Static method
    static void staticMethod1() {
        System.out.println("Static Method 1");
        staticCommonLogic();  // Calling private static method
    }

    // Static method
    static void staticMethod2() {
        System.out.println("Static Method 2");
        staticCommonLogic();  // Calling private static method
    }

    // Private static method
    private static void staticCommonLogic() {
        System.out.println("Static Common Logic executed");
    }
}

public class Main {
    public static void main(String[] args) {
        UtilityInterface.staticMethod1();
        UtilityInterface.staticMethod2();
    }
}
```

### **Output:**

```text
Static Method 1
Static Common Logic executed
Static Method 2
Static Common Logic executed
```

---

## 🔧 **Example 3: Real-World Use Case**

Imagine an interface for validating user input in multiple ways.

```java
interface Validator {
    // Default method for email validation
    default boolean validateEmail(String email) {
        return isNotEmpty(email) && email.contains("@");
    }

    // Default method for password validation
    default boolean validatePassword(String password) {
        return isNotEmpty(password) && password.length() >= 8;
    }

    // Private method to check if a string is not empty
    private boolean isNotEmpty(String str) {
        return str != null && !str.trim().isEmpty();
    }
}

public class Main {
    public static void main(String[] args) {
        Validator validator = new Validator() {
        };

        System.out.println("Email valid: " + validator.validateEmail("test@example.com"));
        System.out.println("Password valid: " + validator.validatePassword("12345678"));
    }
}
```

### **Output:**

```text
Email valid: true
Password valid: true
```

---

## 🎯 **When to Use Private Methods in Interfaces:**

| **Scenario**                            | **Use Case**                                  |
|-----------------------------------------|-----------------------------------------------|
| Reusing common logic in default methods | Use **private methods** in interfaces.        |
| Reusing common logic in static methods  | Use **private static methods** in interfaces. |

---

## ❓ **FAQ:**

### 🔹 **Q: Can private methods in interfaces be inherited by implementing classes?**

**A:** No, private methods are not accessible outside the interface. They are only
accessible within the interface to support the implementation of default and static
methods.

### 🔹 **Q: Can private methods have a body?**

**A:** Yes, private methods in interfaces must have a body (implementation).

### 🔹 **Q: Can private methods be abstract?**

**A:** No, private methods in interfaces cannot be abstract.

---

## ✅ **Summary of Key Points:**

| **Feature**               | **Description**                                           |
|---------------------------|-----------------------------------------------------------|
| **Private Method**        | Helps avoid duplication in default methods.               |
| **Private Static Method** | Helps avoid duplication in static methods.                |
| **Access Modifier**       | Private methods are accessible only within the interface. |

---

This feature greatly improves code **readability**, **reusability**, and **maintainability**
in Java interfaces. 
