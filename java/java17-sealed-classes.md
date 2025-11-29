## 📌 **Java 17: Sealed Classes**

Java 17 introduced **Sealed Classes**, which provide **more control over inheritance** by **restricting which classes or interfaces can extend or implement a sealed class**. This feature allows developers to define a **closed hierarchy** of classes, improving maintainability and ensuring **controlled polymorphism**.

---

### 🔎 **What Are Sealed Classes?**

A **sealed class** is a class that **restricts** which other classes can **extend** it. Sealed classes allow you to:

1. **Control the hierarchy** of your class.
2. **Ensure security and integrity** by limiting which classes can inherit your base class.
3. Prevent **unwanted extensions** of your class.

---

### ✅ **Keywords:**

| **Keyword**   | **Description**                                                |
|---------------|----------------------------------------------------------------|
| `sealed`      | Declares a class or interface as sealed.                       |
| `permits`     | Specifies the allowed subclasses.                               |
| `non-sealed`  | Allows the subclass to be extended further.                    |
| `final`       | Prevents any further extension from the subclass.              |

---

### ✅ **Basic Syntax:**

```java
public sealed class Shape permits Circle, Rectangle {
}

public final class Circle extends Shape {
}

public final class Rectangle extends Shape {
}
```

In this example:

- `Shape` is a **sealed class**.
- Only `Circle` and `Rectangle` are **allowed** to extend `Shape`.
- `Circle` and `Rectangle` are declared as **final**, meaning they cannot be extended further.

---

### 🛠 **Example Code:**

#### **Sealed Class with Permitted Subclasses**

```java
// Base sealed class
public sealed class Shape permits Circle, Rectangle {
    public abstract double area();
}

// Permitted subclasses
public final class Circle extends Shape {
    private final double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public double area() {
        return Math.PI * radius * radius;
    }
}

public final class Rectangle extends Shape {
    private final double width;
    private final double height;

    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }

    @Override
    public double area() {
        return width * height;
    }
}
```

---

### 🎯 **Key Rules for Sealed Classes:**

1. **A sealed class must specify its permitted subclasses** using the `permits` clause.
2. **All permitted subclasses must be in the same module or package**.
3. Permitted subclasses must be declared as **`final`**, **`sealed`**, or **`non-sealed`**.

---

### 🔄 **Permitted Subclass Types:**

| **Subclass Type**   | **Description**                                          |
|---------------------|----------------------------------------------------------|
| `final`             | The subclass cannot be extended further.                 |
| `sealed`            | The subclass can restrict its own subclasses.            |
| `non-sealed`        | The subclass can be extended by any class.               |

---

### 🛠 **Example: Using `non-sealed` Keyword**

```java
public sealed class Vehicle permits Car, Truck {
}

public final class Car extends Vehicle {
}

public non-sealed class Truck extends Vehicle {
}
```

In this case:

- `Vehicle` is a **sealed class**.
- `Car` is **final** and cannot be extended further.
- `Truck` is **non-sealed**, meaning it can be extended by any class.

---

### 🛠 **Example: Sealed Interfaces**

You can also use **sealed classes** with **interfaces**.

```java
public sealed interface Shape permits Circle, Rectangle {
}

public final class Circle implements Shape {
}

public final class Rectangle implements Shape {
}
```

---

### 🔧 **Benefits of Sealed Classes:**

| **Benefit**                  | **Description**                                            |
|------------------------------|------------------------------------------------------------|
| **Controlled Inheritance**    | Limits which classes can extend the base class.            |
| **Improved Security**         | Prevents unwanted extensions that could compromise security. |
| **Better Maintainability**    | Makes the class hierarchy easier to understand and manage. |
| **Exhaustive Switch Statements** | Allows the compiler to check for all permitted subclasses in switch expressions. |

---

### 🔄 **Switch with Sealed Classes:**

Sealed classes work **perfectly with switch expressions**, allowing the compiler to ensure all **permitted types are covered**.

```java
public static double calculateArea(Shape shape) {
    return switch (shape) {
        case Circle c -> Math.PI * c.radius() * c.radius();
        case Rectangle r -> r.width() * r.height();
    };
}
```

---

### ⚠️ **When to Use Sealed Classes?**

Use sealed classes when:

1. You want **strict control** over the inheritance hierarchy.
2. You want to ensure **type safety** in your code.
3. You need **exhaustive checks** in **switch statements**.

---

### 📋 **Comparison: Sealed vs Non-Sealed vs Final Classes**

| **Type**           | **Description**                                          |
|--------------------|----------------------------------------------------------|
| `sealed`           | Limits which classes can extend it.                      |
| `final`            | Prevents any class from extending it.                    |
| `non-sealed`       | Allows unrestricted extension from the subclass.          |

---

### 📚 **Real-World Use Case:**

#### **1. Payment System Example**

```java
public sealed class Payment permits CreditCardPayment, PayPalPayment, BankTransfer {
}

public final class CreditCardPayment extends Payment {
}

public final class PayPalPayment extends Payment {
}

public final class BankTransfer extends Payment {
}
```

In this example:

- `Payment` is a sealed class.
- Only the permitted subclasses (`CreditCardPayment`, `PayPalPayment`, and `BankTransfer`) can extend `Payment`.
- This ensures no **unauthorized payment methods** can be added to the system.

---

### 🛠 **How Sealed Classes Improve Type Safety:**

```java
public static void processPayment(Payment payment) {
    switch (payment) {
        case CreditCardPayment c -> System.out.println("Processing Credit Card Payment");
        case PayPalPayment p -> System.out.println("Processing PayPal Payment");
        case BankTransfer b -> System.out.println("Processing Bank Transfer");
    }
}
```

In this case, the **compiler will ensure** that all **permitted subclasses** are handled in the switch statement.

---

### 🚀 **Summary of Key Points:**

| **Aspect**          | **Details**                                                 |
|---------------------|-------------------------------------------------------------|
| **Introduced in**   | **Java 17**                                                 |
| **Purpose**         | To **control inheritance** and **improve type safety**.     |
| **Key Keywords**    | `sealed`, `permits`, `non-sealed`, `final`.                 |
| **Use Cases**       | **Closed hierarchies** like Payment Systems, Shape Classes. |
| **Compatible with** | **Interfaces** and **Switch Statements**.                   |

---

Let me know if you need further clarification or more examples! 😊
