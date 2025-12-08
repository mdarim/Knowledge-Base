Good — here’s a **suite of 8 advanced Java Stream + `Collectors.groupingBy(...)` patterns**, with full code-snippets and explanations. You can adapt them to your own domain types. I mostly use Java 8+ Streams + the standard `Collectors`. Where helpful I also show use of `mapping()`, `reducing()`, `maxBy()`, `collectingAndThen()`, nested grouping, and custom `Map` types.

---

## 🔧 Patterns & Examples

Assume a domain class and sample data to use across examples:

```java
record Product(String name, String category, double price, String brand) {}

// sample list
List<Product> products = List.of(
    new Product("iPhone", "Electronics", 999.99, "Apple"),
    new Product("MacBook Pro", "Electronics", 1999.99, "Apple"),
    new Product("Galaxy S", "Electronics", 799.99, "Samsung"),
    new Product("T-Shirt", "Clothing", 19.99, "H&M"),
    new Product("Jeans", "Clothing", 49.99, "Levis"),
    new Product("Blender", "Home", 89.99, "Philips"),
    new Product("Sofa", "Home", 499.99, "IKEA")
);
```

---

### 1. Basic grouping: group into Lists by a property

```java
Map<String, List<Product>> byCategory =
    products.stream()
            .collect(Collectors.groupingBy(Product::category));
```

**Result**: `Map` where key = category (e.g. `"Electronics"`, `"Clothing"`), and value = `List<Product>` in that category.
This is the simplest form of `groupingBy`. ([docs.oracle.com][1])

---

### 2. Group + count — how many per category

```java
Map<String, Long> countByCategory =
    products.stream()
            .collect(Collectors.groupingBy(
                Product::category,
                Collectors.counting()
            ));
```

**Result**: `Map<String, Long>` giving count of products per category.
This uses a downstream collector instead of default list collection. ([GeeksforGeeks][2])

---

### 3. Group + transform values — e.g. get set of product names per category

```java
Map<String, Set<String>> namesByCategory =
    products.stream()
            .collect(Collectors.groupingBy(
                Product::category,
                Collectors.mapping(Product::name, Collectors.toSet())
            ));
```

**Result**: `Map<String, Set<String>>`, where each category maps to a **set of product names** (deduplicated, unordered).
This uses `mapping()` + downstream `toSet()`. ([HowToDoInJava][3])

---

### 4. Group + numeric aggregation — e.g. sum or average price per category

```java
Map<String, Double> totalPriceByCategory =
    products.stream()
            .collect(Collectors.groupingBy(
                Product::category,
                Collectors.summingDouble(Product::price)
            ));

Map<String, Double> avgPriceByCategory =
    products.stream()
            .collect(Collectors.groupingBy(
                Product::category,
                Collectors.averagingDouble(Product::price)
            ));
```

**Result**: numeric summary per group (sum or average).
Very useful when you want aggregated metrics per group. ([docs.oracle.com][4])

---

### 5. Group + find max/min per group — e.g. most expensive product per category

```java
Map<String, Optional<Product>> mostExpensiveByCategory =
    products.stream()
            .collect(Collectors.groupingBy(
                Product::category,
                Collectors.maxBy(Comparator.comparingDouble(Product::price))
            ));
```

Because `maxBy(...)` yields an `Optional<T>`, the map value is `Optional<Product>` (empty if no elements). ([HowToDoInJava][3])

If you prefer to get a raw `Product` instead of `Optional`, you can combine with `collectingAndThen(...)`:

```java
Map<String, Product> mostExpensiveByCategoryNonEmpty =
    products.stream()
            .collect(Collectors.groupingBy(
                Product::category,
                Collectors.collectingAndThen(
                    Collectors.maxBy(Comparator.comparingDouble(Product::price)),
                    Optional::get  // beware: throws if Optional is empty
                )
            ));
```

This pattern is described in advanced Collector usage docs. ([java8tips.readthedocs.io][5])

---

### 6. Nested / Multi-level grouping — e.g. category → brand → list

```java
Map<String, Map<String, List<Product>>> byCategoryThenBrand =
    products.stream()
            .collect(Collectors.groupingBy(
                Product::category,
                Collectors.groupingBy(Product::brand)
            ));
```

**Result**: a nested `Map`, where outer key is category, inner key is brand, and inner value is list of products matching both criteria — effectively grouping by multiple dimensions. ([javaplanet.io][6])

You can combine nested grouping with downstream transforms, sums, etc. For example, category → brand → sum of prices:

```java
Map<String, Map<String, Double>> sumPriceByCategoryAndBrand =
    products.stream()
            .collect(Collectors.groupingBy(
                Product::category,
                Collectors.groupingBy(
                    Product::brand,
                    Collectors.summingDouble(Product::price)
                )
            ));
```

---

### 7. Group with custom `Map` type (e.g. sorted keys using `TreeMap`)

By default `groupingBy(...)` returns a non-specified `Map` (often `HashMap`), but there is an overload where you can supply the `Map` factory:

```java
Map<String, List<Product>> sortedByCategory =
    products.stream()
            .collect(Collectors.groupingBy(
                Product::category,
                TreeMap::new,
                Collectors.toList()
            ));
```

This makes the resulting map use `TreeMap` — useful if you want the groups sorted by key. ([docs.oracle.com][1])

---

### 8. Complex reductions: mapping + reducing in group — e.g. group by brand and get total price, or perhaps some custom aggregated object

Suppose you want per-brand total price of products — but imagine your products list is large and you want a custom type. A common approach:

```java
Map<String, Double> totalPriceByBrand =
    products.stream()
            .collect(Collectors.groupingBy(
                Product::brand,
                Collectors.summingDouble(Product::price)
            ));
```

You can also do more complex transformations: mapping to a different value, then reducing. Example (brand → concatenated product names):

```java
Map<String, String> namesConcatenatedByBrand =
    products.stream()
            .collect(Collectors.groupingBy(
                Product::brand,
                Collectors.mapping(Product::name, Collectors.joining(", "))
            ));
```

If you need both sum and count (or average) in one pass, you might define a custom accumulator type or use `collectingAndThen(...)` on a helper class, though this becomes more manual (beyond built-in simple collectors). The docs note that `reducing()` collectors are most useful when used as downstream collectors of `groupingBy`/`partitioningBy`. ([docs.oracle.com][1])

---

## 📋 Summary Table of Patterns

| Use Case                                                                  | Collector Pattern                                                              |
|---------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| Simple grouping → list                                                    | `groupingBy(classifier)`                                                       |
| Count per group                                                           | `groupingBy(classifier, counting())`                                           |
| Transform to set/list of a different property                             | `groupingBy(classifier, mapping(..., toSet()/toList()))`                       |
| Sum / average / numeric aggregation per group                             | `groupingBy(classifier, summingDouble()/averagingDouble()/...)`                |
| Max or min element per group                                              | `groupingBy(classifier, maxBy(...))` (optionally + `collectingAndThen`)        |
| Nested / multi-level grouping                                             | `groupingBy(..., groupingBy(...))`                                             |
| Custom Map backing (sorting, special map type)                            | `groupingBy(classifier, mapFactory, downstream)`                               |
| Combined transformations & reductions (e.g. mapping + joining / reducing) | `groupingBy(..., mapping(..., downstream))` / `groupingBy(..., reducing(...))` |

---

## 🧠 When to Prefer Which Pattern (and What to Watch Out For)

* Use basic `groupingBy(classifier)` when you just need to bucket data into lists.
* If you don’t need entire list but only summary (count / sum / min / max) — prefer downstream collectors (`counting()`, `summingDouble()`, `maxBy()`, etc.) for efficiency.
* Use `mapping(...)` when you need only a particular attribute of the grouped objects (e.g. names, IDs) — avoids storing full objects.
* Use nested grouping when you need multi-dimensional hierarchy (e.g. category → brand → …).
* Use custom map factories if order or special `Map` behavior (e.g. sorted keys) matters.
* Beware: some collectors (like `maxBy`) produce `Optional<T>` — you may need to handle empty groups or unwrap optionally.
* For complex aggregations (multiple metrics per group, custom aggregate objects) — often better to define a helper class or use a custom collector / `reducing()`.

[1]: https://docs.oracle.com/en/java/javase/23/docs/api/java.base/java/util/stream/Collectors.html?utm_source=chatgpt.com "Collectors (Java SE 23 & JDK 23)"
[2]: https://www.geeksforgeeks.org/collectors-groupingby-method-in-java-with-examples/?utm_source=chatgpt.com "Collectors groupingBy() method in Java with Examples - GeeksforGeeks"
[3]: https://howtodoinjava.com/java/stream/collectors-groupingby/?utm_source=chatgpt.com "Java 8 Stream - Collectors GroupingBy with Examples"
[4]: https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html?utm_source=chatgpt.com "Collectors (Java Platform SE 8 )"
[5]: https://java8tips.readthedocs.io/en/stable/advcollectors.html?utm_source=chatgpt.com "9. Predefined Collectors — Java 8 tips 1.0 documentation"
[6]: https://javaplanet.io/java-core/java-8-streams/grouping-operations/?utm_source=chatgpt.com "Grouping Operations"
