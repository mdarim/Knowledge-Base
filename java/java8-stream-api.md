# 🚀 **Java Stream API (Introduced in Java 8)**

The **Stream API** in Java is a powerful tool for **processing collections of data** in a **declarative and functional style**. It provides methods to filter, map, and reduce data using a **stream pipeline**.

---

## ✅ **What is a Stream?**

A **Stream** is a **sequence of elements** from a **source** (such as a collection) that supports **data processing operations**.

- **Stream is not a data structure.**
- **It doesn’t store data.**
- **It processes data on demand.**

---

## ✅ **How to Create a Stream?**

There are several ways to create a stream:

### 🔷 **1. From a Collection**
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
Stream<String> stream = names.stream();
```

### 🔷 **2. From an Array**
```java
String[] namesArray = {"Alice", "Bob", "Charlie"};
Stream<String> stream = Arrays.stream(namesArray);
```

### 🔷 **3. Using `Stream.of()`**
```java
Stream<String> stream = Stream.of("Alice", "Bob", "Charlie");
```

### 🔷 **4. From a File (Using `Files.lines()`)**
```java
Stream<String> lines = Files.lines(Paths.get("file.txt"));
```

---

## ✅ **Stream Operations**

There are two types of **Stream operations**:

| **Type**        | **Description**                            | **Example**                  |
|-----------------|--------------------------------------------|------------------------------|
| Intermediate    | Returns another stream                     | `filter()`, `map()`, `sorted()` |
| Terminal        | Produces a result or side-effect           | `forEach()`, `collect()`, `count()` |

### 🧪 **Common Intermediate Operations:**

| **Method**     | **Description**                          |
|----------------|------------------------------------------|
| `filter()`     | Filters elements based on a condition.    |
| `map()`        | Transforms elements.                     |
| `sorted()`     | Sorts the elements.                      |
| `distinct()`   | Removes duplicate elements.               |
| `limit()`      | Limits the number of elements.            |
| `skip()`       | Skips the first few elements.             |

### 🧪 **Common Terminal Operations:**

| **Method**     | **Description**                          |
|----------------|------------------------------------------|
| `forEach()`    | Performs an action for each element.      |
| `collect()`    | Collects elements into a collection.      |
| `count()`      | Returns the number of elements.           |
| `reduce()`     | Reduces elements to a single value.       |
| `findFirst()`  | Returns the first element.                |
| `anyMatch()`   | Checks if any element matches a condition.|

---

## ✅ **Stream Example:**

### 🔷 **Example 1: Filtering and Printing Names**
```java
import java.util.Arrays;
import java.util.List;

public class StreamExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");

        // Using Stream API
        names.stream()
             .filter(name -> name.startsWith("A"))
             .forEach(System.out::println);
    }
}
```

**Output:**
```
Alice
```

---

### 🔷 **Example 2: Mapping and Collecting Results**
```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class StreamExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

        // Using Stream API to convert names to uppercase
        List<String> upperCaseNames = names.stream()
                                           .map(String::toUpperCase)
                                           .collect(Collectors.toList());

        System.out.println(upperCaseNames);
    }
}
```

**Output:**
```
[ALICE, BOB, CHARLIE]
```

---

### 🔷 **Example 3: Using `reduce()` to Sum Numbers**
```java
import java.util.Arrays;
import java.util.List;

public class StreamExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

        // Using reduce to calculate the sum
        int sum = numbers.stream()
                         .reduce(0, Integer::sum);

        System.out.println("Sum: " + sum);
    }
}
```

**Output:**
```
Sum: 15
```

---

### 🔷 **Example 4: Using `sorted()` and `distinct()`**
```java
import java.util.Arrays;
import java.util.List;

public class StreamExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(5, 3, 1, 2, 5, 3);

        // Sorting and removing duplicates
        numbers.stream()
               .distinct()
               .sorted()
               .forEach(System.out::println);
    }
}
```

**Output:**
```
1
2
3
5
```

---

### ✅ **Collecting Results using `Collectors`**

The **`collect()`** method is a terminal operation that **transforms the elements of a stream into a collection** or another data structure.

#### 🔷 **Example: Collecting into a List**
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

List<String> filteredNames = names.stream()
                                  .filter(name -> name.startsWith("A"))
                                  .collect(Collectors.toList());

System.out.println(filteredNames);
```

**Output:**
```
[Alice]
```

---

### ✅ **Parallel Streams**

You can create a **parallel stream** to process elements in **parallel** for better performance on large datasets.

#### 🔷 **Example:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

numbers.parallelStream()
       .forEach(System.out::println);
```

---

### ✅ **Common Use Cases of Stream API:**

1. **Filtering data** (e.g., filtering a list of users by age).
2. **Transforming data** (e.g., converting names to uppercase).
3. **Sorting and removing duplicates**.
4. **Reducing data** to a single result (e.g., sum of numbers).
5. **Collecting data** into different types (e.g., `List`, `Set`, `Map`).

---

### ✅ **Summary of Key Methods:**

| **Method**        | **Description**                                   |
|-------------------|---------------------------------------------------|
| `filter()`        | Filters elements based on a condition.            |
| `map()`           | Transforms each element.                          |
| `sorted()`        | Sorts elements.                                   |
| `distinct()`      | Removes duplicates.                                |
| `forEach()`       | Performs an action on each element.               |
| `collect()`       | Collects elements into a collection.              |
| `reduce()`        | Reduces elements to a single value.               |
| `count()`         | Returns the number of elements.                   |
| `findFirst()`     | Returns the first element in the stream.          |
| `anyMatch()`      | Checks if any element matches a condition.         |

---

### ✅ **Advantages of Stream API:**

1. **Concise and Readable Code**: Reduces boilerplate code.
2. **Functional Programming**: Encourages a more declarative style.
3. **Parallel Processing**: Supports parallel streams for better performance.
4. **Lazy Evaluation**: Processes elements on demand.

---
