Here’s a detailed look at **advanced usage** of Java Streams with `collect()` + `Collectors.groupingBy(...)` — including nested grouping, downstream reductions, custom map types, etc. 👇

---

## 🔹 Recap: Basic `groupingBy`

At its simplest, `groupingBy` lets you group a stream of elements into a `Map<K, List<T>>`, where the key `K` is derived via a classifier function. For example:

```java
Map<String, List<Person>> peopleByCity = 
    people.stream()
          .collect(Collectors.groupingBy(Person::getCity));
```

Here, all `Person` objects are grouped into lists keyed by their `city`. ([php.cn][1])

But real-world needs are often more complex. That’s where “advanced” uses of `groupingBy` shine.

---

## ⚙️ Advanced Techniques: Downstream Collectors & More

`groupingBy` has overloaded versions that let you fine-tune what happens *within* each group. Key options:

### - Use a downstream collector to aggregate rather than list

By default each group yields a `List<T>`, but you can replace that with other collectors — e.g. to get counts, sums, sets, averages — using second parameter: `groupingBy(classifier, downstreamCollector)`. ([docs.oracle.com][2])

Examples:

```java
// Count by city
Map<String, Long> countByCity = people.stream()
    .collect(Collectors.groupingBy(
        Person::getCity,
        Collectors.counting()
    ));

// Sum of salaries by department
Map<Department, Double> avgSalaryByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Person::getDepartment,
        Collectors.averagingDouble(Person::getSalary)
    ));

// Transform values before collecting: e.g. collect only names (distinct) per city
Map<String, Set<String>> namesByCity = people.stream()
    .collect(Collectors.groupingBy(
        Person::getCity,
        Collectors.mapping(Person::getName, Collectors.toSet())
    ));
```

Using these downstream operations gives you great flexibility — not just grouping but summarising or transforming data per group. ([m.php.cn][3])

### - Nested / Multi-level grouping

You can “group by multiple levels” by using a `groupingBy` inside another:

```java
Map<String /* city */, Map<Integer /* age */, List<Person>>> nested = people.stream()
    .collect(Collectors.groupingBy(
        Person::getCity,
        Collectors.groupingBy(Person::getAge)
    ));
```

This builds a nested `Map`: first-level key is city, value is another map keyed by age. Useful when you need hierarchy in grouping. ([php.cn][1])

You can mix downstream collectors too — e.g. city → age → count, or city → age → set of names. ([m.php.cn][4])

### - Choosing custom Map implementations (ordering, concurrency, etc.)

Sometimes you care about what kind of `Map` you get back: sorted keys, insertion order, thread-safe, etc. There’s an overload of `groupingBy` where you pass a `Supplier<M>` to specify the `Map` type:

```java
Map<String, Set<Locale>> countryToLocales = 
    locales.stream().collect(
        groupingBy(Locale::getCountry,
                   TreeMap::new,          // custom map type
                   Collectors.toSet())
    );
```

This gives you control over ordering (e.g. `TreeMap`), concurrency if you use concurrent collectors, etc. ([docs.oracle.com][2])

---

## 🧑‍💻 Real-World Example: Combining Techniques

Suppose you have a list of products:

```java
record Product(String name, String category, double price) {}
List<Product> products = List.of(
    new Product("iPhone", "Electronics", 900),
    new Product("TV", "Electronics", 400),
    new Product("Shirt", "Clothing", 50),
    new Product("Jeans", "Clothing", 80),
    new Product("Blender", "Home", 120)
);
```

With `groupingBy` you can:

* Group products by category: `Map<String, List<Product>>`
* Get count per category: `Map<String, Long>`
* Get total sum of prices per category: `Map<String, Double>`
* Get a set of product names per category: `Map<String, Set<String>>`

Example:

```java
Map<String, Double> totalByCategory = products.stream()
    .collect(Collectors.groupingBy(
        Product::category,
        Collectors.summingDouble(Product::price)
    ));

Map<String, Set<String>> namesByCategory = products.stream()
    .collect(Collectors.groupingBy(
        Product::category,
        Collectors.mapping(Product::name, Collectors.toSet())
    ));
```

If you wanted a sorted map by category, replace `groupingBy(...)` with the 3-arg version passing a `TreeMap::new` as the map supplier.

If products had sub-categories, you could even nest another `groupingBy` inside (e.g. category → subcategory → list/sum/counted results).

---

## ✅ When to Use Which Version

| Use case                                                             | Use this pattern                                                              |
|----------------------------------------------------------------------|-------------------------------------------------------------------------------|
| Simply group by a field, get lists                                   | `groupingBy(classifier)`                                                      |
| Group + count / sum / average / min / max / collect transformed data | `groupingBy(classifier, downstreamCollector)`                                 |
| Group by multiple levels / nested categories                         | nested `groupingBy` — e.g. `groupingBy(a, groupingBy(b, …))`                  |
| Need sorted keys / specific Map type / ordering / concurrency        | `groupingBy(classifier, mapFactory, downstreamCollector)`                     |
| For parallel streams (thread-safe grouping)                          | `groupingByConcurrent(...)` variants (similar overloads) ([HowToDoInJava][5]) |

[1]: https://www.php.cn/en/faq/1796833814.html?utm_source=chatgpt.com "How to use Java Stream collect() with groupingBy?-javaTutorial-php.cn"
[2]: https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html?utm_source=chatgpt.com "Collectors (Java Platform SE 8 )"
[3]: https://m.php.cn/en/faq/1796895320.html?utm_source=chatgpt.com "How to use the groupingBy collector in Java Streams?-javaTutorial-php.cn"
[4]: https://m.php.cn/en/faq/1796920340.html?utm_source=chatgpt.com "How to use Collectors.groupingBy in java streams-javaTutorial-php.cn"
[5]: https://howtodoinjava.com/java/stream/collectors-groupingby/?utm_source=chatgpt.com "Java 8 Stream - Collectors GroupingBy with Examples"
