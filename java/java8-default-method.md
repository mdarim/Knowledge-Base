# 🚀 **Java 8 Default Methods**

Java 8 introduced **default methods** in interfaces to provide **concrete methods** with
a **default implementation**. This allows **interface evolution** without breaking
existing implementations, solving one of the limitations of traditional interfaces.

---

## ✅ **What is a Default Method?**

A **default method** is a method in an **interface** that has a **default implementation**
. It uses the `default` keyword in the method declaration.

---

### ✅ **Why Use Default Methods?**

1. **Interface evolution**: Add new methods to an interface without breaking existing
   implementations.
2. **Code reuse**: Provide a default behavior that implementing classes can use or
   override.
3. **Backward compatibility**: Allows interfaces to evolve without forcing all
   implementing classes to change.

---

### ✅ **Syntax of Default Method**

```java
public interface MyInterface {
    // Default method
    default void defaultMethod() {
        System.out.println("This is a default method");
    }
}
```

---

### ✅ **Example: Default Method in an Interface**

```java
interface Vehicle {
    // Abstract method
    void start();

    // Default method
    default void stop() {
        System.out.println("Vehicle is stopping...");
    }
}

class Car implements Vehicle {
    @Override
    public void start() {
        System.out.println("Car is starting...");
    }
}

public class DefaultMethodExample {
    public static void main(String[] args) {
        Car car = new Car();
        car.start();
        car.stop();  // Using the default method from Vehicle interface
    }
}
```

**Output:**

```
Car is starting...
Vehicle is stopping...
```

---

### ✅ **Overriding a Default Method**

A class that implements an interface with a default method can **override** that method.

#### 🔷 **Example:**

```java
interface Vehicle {
    default void stop() {
        System.out.println("Vehicle is stopping...");
    }
}

class Car implements Vehicle {
    @Override
    public void stop() {
        System.out.println("Car is stopping...");
    }
}

public class OverrideDefaultMethodExample {
    public static void main(String[] args) {
        Car car = new Car();
        car.stop();  // Uses the overridden method in Car class
    }
}
```

**Output:**

```
Car is stopping...
```

---

### ✅ **Multiple Interfaces with Default Methods**

If a class implements **multiple interfaces** with **conflicting default methods**, it
must **override the method** to resolve the conflict.

#### 🔷 **Example:**

```java
interface InterfaceA {
    default void display() {
        System.out.println("Display from InterfaceA");
    }
}

interface InterfaceB {
    default void display() {
        System.out.println("Display from InterfaceB");
    }
}

class MyClass implements InterfaceA, InterfaceB {
    @Override
    public void display() {
        System.out.println("Display from MyClass");
    }
}

public class MultipleInterfacesExample {
    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.display();
    }
}
```

**Output:**

```
Display from MyClass
```

---

### ✅ **Static Methods in Interfaces (Related to Default Methods)**

Java 8 also introduced **static methods** in interfaces, which belong to the interface
itself and cannot be overridden by implementing classes.

#### 🔷 **Example:**

```java
interface MathUtils {
    // Static method
    static int add(int a, int b) {
        return a + b;
    }
}

public class StaticMethodExample {
    public static void main(String[] args) {
        // Calling the static method directly from the interface
        int result = MathUtils.add(5, 10);
        System.out.println("Result: " + result);
    }
}
```

**Output:**

```
Result: 15
```

---

### ✅ **Real-World Use Case of Default Methods**

Consider the **`List` interface** in the Java Collections Framework. In Java 8, the `List`
interface got new default methods like **`forEach()`** and **`replaceAll()`** without
breaking the existing implementations.

#### 🔷 **Example:**

```java
import java.util.ArrayList;
import java.util.List;

public class DefaultMethodUseCase {
    public static void main(String[] args) {
        List<String> names = new ArrayList<>();
        names.add("Alice");
        names.add("Bob");

        // Using the forEach() default method in List interface
        names.forEach(name -> System.out.println("Name: " + name));
    }
}
```

**Output:**

```
Name: Alice
Name: Bob
```

---

### ✅ **Key Differences Between Abstract Methods and Default Methods**

| **Aspect**           | **Abstract Method**                           | **Default
Method**                                |
|----------------------|-----------------------------------------------|--------------------------------------------------|
| **
Definition**        | Method with no implementation in the interface. | Method with a default implementation in the interface. |
| **
Keyword**           | No special keyword.                           | Uses the `default` keyword.                     |
| **
Implementation**    | Must be implemented by the implementing class. | Can be used as-is or overridden by the class.    |

---

### ✅ **Benefits of Default Methods**

| **Benefit**            | **Description**                                |
|------------------------|------------------------------------------------|
| **Backward
Compatibility** | Allows adding new methods to interfaces without breaking existing code. |
| **Code Reuse**         | Provides a reusable default implementation.    |
| **Interface Evolution** | Makes it easier to evolve APIs over time.     |

---

### ✅ **Best Practices for Using Default Methods**

1. **Use default methods for backward compatibility.**
2. **Avoid using default methods to add significant logic.**
3. **Keep default methods simple and focused.**
4. **Always override conflicting default methods when implementing multiple interfaces.**

---

### ✅ **Summary of Default Methods**

| **Aspect**             | **Details**                                  |
|------------------------|----------------------------------------------|
| **Introduced in**       | Java 8                                       |
| **Keyword**             | `default`                                    |
| **Can be overridden?**  | Yes                                          |
| **Use case**            | Interface evolution and backward compatibility. |
| **Related feature**     | Static methods in interfaces.                |

---

### ✅ **Quick Code Recap**

#### 🔷 **Default Method Syntax:**

```java
public interface MyInterface {
    default void myMethod() {
        System.out.println("Default Method");
    }
}
```

#### 🔷 **Overriding a Default Method:**

```java
class MyClass implements MyInterface {
    @Override
    public void myMethod() {
        System.out.println("Overridden Method");
    }
}
```

---
