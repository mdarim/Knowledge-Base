# Java 8: method reference operator

The method reference operator (::) is a feature introduced in Java 8 that provides a
shorthand for lambda expressions when the method being called is already defined. It
allows you to refer to a method without invoking it, improving the readability and
conciseness of your code.

## **Lambda Expression vs. Method Reference**

| **Lambda Expression**         | **Equivalent Method Reference** |
|--------------------------------|---------------------------------|
| `(a, b) -> Math.max(a, b)`     | `Math::max`                     |
| `str -> System.out.println(str)` | `System.out::println`          |
| `() -> new ArrayList<>()`      | `ArrayList::new`                |

---

## **Use Cases of Method References**

- **Improves code readability** by reducing boilerplate.
- **Useful in functional programming** with Java's **Stream API** and **Functional Interfaces**.
- **Simplifies lambda expressions** when the method already exists.

---

## **When to Use Method References:**

- When a **lambda expression** calls an **existing method** without modifying it.
- When using **Stream API operations** such as `forEach()`, `map()`, `filter()`, etc.

---
