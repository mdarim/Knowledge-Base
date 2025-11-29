# 🎯 **Java Record Patterns (Java 19+ and Java 21)**

**Record Patterns** is a powerful feature introduced as a **preview** in **Java 19** and **Java 20**, and **became stable in Java 21**. This feature allows **pattern matching** for **records**, enabling you to deconstruct record instances in a concise and readable way using **pattern matching for `switch` and `instanceof`**.

---

## ✅ **What Are Record Patterns?**

A **record pattern** allows you to **deconstruct a record object** by extracting its **components (fields)** directly in **`switch` statements, `instanceof`, and `if` conditions**.

Instead of manually accessing record components, you can match them **directly within the pattern**, simplifying **data extraction and validation**.

---

## 🖥️ **Example: Without Record Patterns (Before Java 19)**

Consider a **record** called `Person` with two components:

```java
public record Person(String name, int age) {}
```

Before record patterns, you would need to use **manual getters** to extract the fields:

```java
public void printPersonDetails(Object obj) {
    if (obj instanceof Person) {
        Person person = (Person) obj;
        String name = person.name();
        int age = person.age();
        System.out.println("Name: " + name + ", Age: " + age);
    }
}
```

---

## ✅ **With Record Patterns (Java 19+)**

With **record patterns**, you can deconstruct the `Person` record directly **within an `instanceof` or `switch` statement**:

```java
public void printPersonDetails(Object obj) {
    if (obj instanceof Person(String name, int age)) {
        System.out.println("Name: " + name + ", Age: " + age);
    }
}
```

- The **pattern automatically extracts** the `name` and `age` components.
- No need for **explicit casting or getters**.

---

## 📚 **Example: Record Patterns with `switch` Statement**

You can use **record patterns in `switch`** to handle different cases based on the record's components.

```java
public void handlePerson(Object obj) {
    switch (obj) {
        case Person(String name, int age) when age > 18 ->
            System.out.println(name + " is an adult.");
        case Person(String name, int age) ->
            System.out.println(name + " is a minor.");
        default ->
            System.out.println("Unknown object.");
    }
}
```

---

## 🧩 **Example: Nested Record Patterns**

Record patterns support **nested patterns**, allowing you to **deconstruct records within records**.

### ✅ **Nested Records:**

```java
public record Address(String city, String country) {}
public record Person(String name, int age, Address address) {}
```

### ✅ **Deconstructing Nested Records:**

```java
public void handlePersonWithAddress(Object obj) {
    if (obj instanceof Person(String name, int age, Address(String city, String country))) {
        System.out.println(name + " lives in " + city + ", " + country);
    }
}
```

---

## 🔧 **Example: Combining Record Patterns with `if` Conditions**

You can **combine record patterns with `if` conditions** for more complex matching logic.

```java
public void processPerson(Object obj) {
    if (obj instanceof Person(String name, int age, Address address) && age > 18) {
        System.out.println(name + " is an adult living at " + address.city());
    }
}
```

---

## 🚀 **Example: Using Record Patterns in Methods**

You can use **record patterns directly in method parameters** for **concise code**.

```java
public void greetPerson(Person(String name, int age)) {
    System.out.println("Hello, " + name + "! You are " + age + " years old.");
}
```

---

## 🛠️ **Example: Record Patterns in Loops**

Record patterns can be used in **enhanced `for` loops** to simplify iteration over collections of records.

```java
List<Person> people = List.of(
    new Person("Alice", 25, new Address("New York", "USA")),
    new Person("Bob", 17, new Address("London", "UK"))
);

for (Person(String name, int age, Address(String city, String country)) : people) {
    System.out.println(name + " lives in " + city + ", " + country);
}
```

---

## 🧪 **Example: Handling Multiple Patterns in `switch`**

```java
public void printInfo(Object obj) {
    switch (obj) {
        case Person(String name, int age, Address address) when age > 18 ->
            System.out.println(name + " is an adult living in " + address.city());
        case Person(String name, int age, Address address) ->
            System.out.println(name + " is a minor living in " + address.city());
        default ->
            System.out.println("Unknown object.");
    }
}
```

---

## 📊 **Benefits of Record Patterns**

| **Benefit**                    | **Description**                                             |
|--------------------------------|-------------------------------------------------------------|
| 🧹 **Reduces Boilerplate**     | No need for manual getters or type casts.                   |
| 🎯 **Simplifies Code**         | Deconstructs records directly in `switch` and `instanceof`. |
| 🚀 **Supports Nested Records** | Handles nested records seamlessly.                          |
| 💡 **Improves Readability**    | More concise and expressive code.                           |

---

## 🛡️ **Exhaustiveness Check for Sealed Records**

Record patterns work well with **sealed classes and records**, ensuring **all possible cases are covered**.

```java
sealed interface Shape permits Circle, Rectangle {}

record Circle(double radius) implements Shape {}
record Rectangle(double length, double width) implements Shape {}

public void handleShape(Shape shape) {
    switch (shape) {
        case Circle(double radius) -> System.out.println("Circle with radius: " + radius);
        case Rectangle(double length, double width) -> System.out.println("Rectangle with length: " + length + " and width: " + width);
    }
}
```

---

## ⚙️ **Restrictions and Rules**

1. **Exhaustiveness**:  
   For sealed classes or interfaces, all record patterns must cover every possible subtype.

2. **Final Fields Only**:  
   Record patterns can only deconstruct **final fields** in records.

3. **Nested Patterns**:  
   Nested patterns are allowed but must adhere to type safety rules.

---

## 🎯 **Summary of Record Patterns**

| **Feature**           | **Description**                                             |
|-----------------------|-------------------------------------------------------------|
| `instanceof` Pattern  | Deconstructs record components directly in `instanceof`.    |
| `switch` Pattern      | Simplifies `switch` statements with pattern matching.       |
| **Nested Patterns**   | Supports deconstructing nested records.                     |
| **Guarded Patterns**  | Adds conditions using `when`.                               |
| **Method Parameters** | Allows using record patterns directly in method signatures. |

---

## 🧩 **Conclusion**

**Record Patterns** in Java **simplify the extraction of data** from records using **pattern matching**. They make **code more readable, concise, and expressive**, reducing boilerplate and improving maintainability. This feature is especially useful in applications involving **data-rich objects**, making it easier to **handle complex patterns and nested records**.
