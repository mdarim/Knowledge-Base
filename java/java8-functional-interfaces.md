# 🚀 **Java 8 Functional Interfaces**

Java 8 introduced **functional interfaces** to support **lambda expressions** and **functional programming**. A **functional interface** is an interface that contains exactly **one abstract method** and can have **multiple default or static methods**.

---

## ✅ **Key Features of Functional Interfaces**

1. **Contains only one abstract method.**
2. Can have **default** and **static** methods.
3. Can be used with **lambda expressions**, **method references**, and **constructor references**.
4. Annotated with **`@FunctionalInterface`** to ensure compliance.

---

## ✅ **Built-in Functional Interfaces in `java.util.function` Package**

| **Interface**      | **Abstract Method**          | **Description**                                   |
|--------------------|------------------------------|---------------------------------------------------|
| `Function<T, R>`   | `R apply(T t)`               | Takes one argument and returns a result.          |
| `Predicate<T>`     | `boolean test(T t)`          | Takes one argument and returns a boolean result.  |
| `Consumer<T>`      | `void accept(T t)`           | Takes one argument and returns no result.         |
| `Supplier<T>`      | `T get()`                    | Takes no arguments and returns a result.          |
| `BiFunction<T, U, R>` | `R apply(T t, U u)`       | Takes two arguments and returns a result.         |
| `BiConsumer<T, U>` | `void accept(T t, U u)`      | Takes two arguments and returns no result.        |

---

## ✅ **Custom Functional Interface Example**

### 🔷 **1. Defining a Functional Interface**
```java
@FunctionalInterface
interface MyFunctionalInterface {
    void displayMessage(String message);
}
```

---

### 🔷 **2. Using Lambda Expression with Functional Interface**
```java
public class FunctionalInterfaceExample {
    public static void main(String[] args) {
        // Using lambda expression to implement the abstract method
        MyFunctionalInterface myFunc = message -> System.out.println("Message: " + message);

        // Calling the method
        myFunc.displayMessage("Hello, Java 8!");
    }
}
```

**Output:**
```
Message: Hello, Java 8!
```

---

### ✅ **Common Built-in Functional Interfaces**

---

### 🔷 **1. `Function<T, R>` Example**
Represents a function that takes **one argument** and **returns a result**.

```java
import java.util.function.Function;

public class FunctionExample {
    public static void main(String[] args) {
        Function<String, Integer> lengthFunction = str -> str.length();
        System.out.println("Length of 'Java': " + lengthFunction.apply("Java"));
    }
}
```

**Output:**
```
Length of 'Java': 4
```

---

### 🔷 **2. `Predicate<T>` Example**
Represents a **condition** that takes **one argument** and returns a **boolean**.

```java
import java.util.function.Predicate;

public class PredicateExample {
    public static void main(String[] args) {
        Predicate<Integer> isEven = num -> num % 2 == 0;
        System.out.println("Is 4 even? " + isEven.test(4));
        System.out.println("Is 5 even? " + isEven.test(5));
    }
}
```

**Output:**
```
Is 4 even? true
Is 5 even? false
```

---

### 🔷 **3. `Consumer<T>` Example**
Represents an operation that takes **one argument** and returns **no result**.

```java
import java.util.function.Consumer;

public class ConsumerExample {
    public static void main(String[] args) {
        Consumer<String> printConsumer = message -> System.out.println("Message: " + message);
        printConsumer.accept("Hello, Consumer!");
    }
}
```

**Output:**
```
Message: Hello, Consumer!
```

---

### 🔷 **4. `Supplier<T>` Example**
Represents a **supplier** of results, takes **no arguments**, and **returns a result**.

```java
import java.util.function.Supplier;

public class SupplierExample {
    public static void main(String[] args) {
        Supplier<String> stringSupplier = () -> "Hello from Supplier!";
        System.out.println(stringSupplier.get());
    }
}
```

**Output:**
```
Hello from Supplier!
```

---

### 🔷 **5. `BiFunction<T, U, R>` Example**
Represents a function that takes **two arguments** and **returns a result**.

```java
import java.util.function.BiFunction;

public class BiFunctionExample {
    public static void main(String[] args) {
        BiFunction<Integer, Integer, Integer> addFunction = (a, b) -> a + b;
        System.out.println("Sum: " + addFunction.apply(5, 10));
    }
}
```

**Output:**
```
Sum: 15
```

---

### 🔷 **6. `BiConsumer<T, U>` Example**
Represents an operation that takes **two arguments** and returns **no result**.

```java
import java.util.function.BiConsumer;

public class BiConsumerExample {
    public static void main(String[] args) {
        BiConsumer<String, Integer> printBiConsumer = (name, age) -> {
            System.out.println("Name: " + name);
            System.out.println("Age: " + age);
        };

        printBiConsumer.accept("Alice", 30);
    }
}
```

**Output:**
```
Name: Alice
Age: 30
```

---

## ✅ **Using Functional Interfaces with Stream API**

The Stream API in Java 8 makes extensive use of **functional interfaces**.

#### 🔷 **Example: Using `Predicate` in a Stream**
```java
import java.util.Arrays;
import java.util.List;
import java.util.function.Predicate;

public class StreamPredicateExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

        // Using Predicate to filter even numbers
        numbers.stream()
               .filter(num -> num % 2 == 0)
               .forEach(System.out::println);
    }
}
```

**Output:**
```
2
4
6
```

---

## ✅ **Custom Functional Interface with Default and Static Methods**

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);

    // Default method
    default void printResult(int result) {
        System.out.println("Result: " + result);
    }

    // Static method
    static void showUsage() {
        System.out.println("Usage: Implement the calculate() method.");
    }
}

public class CalculatorExample {
    public static void main(String[] args) {
        Calculator add = (a, b) -> a + b;
        int result = add.calculate(10, 20);
        add.printResult(result);

        Calculator.showUsage();
    }
}
```

**Output:**
```
Result: 30
Usage: Implement the calculate() method.
```

---

## ✅ **@FunctionalInterface Annotation**

The `@FunctionalInterface` annotation is used to indicate that an interface is a functional interface.  
It helps **prevent accidental addition** of more abstract methods, which would break the functional interface contract.

#### 🔷 **Example:**
```java
@FunctionalInterface
interface MyInterface {
    void abstractMethod();
    // Adding another abstract method will cause a compile-time error.
}
```

---

## ✅ **Summary of Built-in Functional Interfaces**

| **Interface**         | **Description**                                  |
|-----------------------|--------------------------------------------------|
| `Function<T, R>`      | Takes one argument and returns a result.         |
| `Predicate<T>`        | Takes one argument and returns a boolean.        |
| `Consumer<T>`         | Takes one argument and returns nothing.          |
| `Supplier<T>`         | Takes no arguments and returns a result.         |
| `BiFunction<T, U, R>` | Takes two arguments and returns a result.        |
| `BiConsumer<T, U>`    | Takes two arguments and returns nothing.         |

---
