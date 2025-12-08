Cool — here are a number of “real-world” / non-toy Java code snippets using streams + `collect(...)` to build Sets (or Maps of Sets), including generics, grouping, flattening, filtering, etc. Feel free to copy/paste and adapt.

---

## 🧩 Example snippets: advanced `Stream` → `Set` / grouped/nested collections

```java
import java.util.*;
import java.util.stream.*;
import static java.util.stream.Collectors.*;

class Person {
    private String city;
    private String lastName;
    public Person(String city, String lastName) { this.city = city; this.lastName = lastName; }
    public String getCity() { return city; }
    public String getLastName() { return lastName; }
}

class Order {
    private String customerName;
    private List<Item> items;
    public Order(String customerName, List<Item> items) { this.customerName = customerName; this.items = items; }
    public String getCustomerName() { return customerName; }
    public List<Item> getItems() { return items; }
}

class Item {
    private String name;
    public Item(String name) { this.name = name; }
    public String getName() { return name; }
}

public class StreamSetExamples {
    public static void main(String[] args) {
        // 1) Simple: stream to Set (deduplicated, unspecified Set impl)
        Set<String> fruits = Stream.of("apple", "banana", "apple", "orange")
                                   .collect(toSet());
        System.out.println(fruits);
        
        // 2) Specific Set implementation: TreeSet (sorted)
        Set<String> sortedFruits = Stream.of("banana","apple","orange","apple")
                                         .collect(toCollection(TreeSet::new));
        System.out.println(sortedFruits);
        
        // 3) Grouping + mapping: Map<City, Set<lastNames>>
        List<Person> people = List.of(
            new Person("London", "Smith"),
            new Person("Paris", "Dupont"),
            new Person("London", "Johnson"),
            new Person("Paris", "Dupont") // duplicate lastName for Paris
        );
        Map<String, Set<String>> lastNamesByCity = people.stream()
            .collect(groupingBy(
                        Person::getCity,
                        mapping(Person::getLastName, toSet())
            ));
        System.out.println(lastNamesByCity);
        
        // 4) Grouping + flattening nested collections — orders to items
        List<Order> orders = List.of(
          new Order("Alice", List.of(new Item("Item1"), new Item("Item2"))),
          new Order("Bob",   List.of(new Item("Item3"))),
          new Order("Alice", List.of(new Item("Item2"), new Item("Item4")))
        );
        Map<String, Set<String>> itemsByCustomer = orders.stream()
            .collect(groupingBy(
                Order::getCustomerName,
                flatMapping(o -> o.getItems().stream().map(Item::getName),
                            toSet())
            ));
        System.out.println(itemsByCustomer);
        
        // 5) Example: filtering + grouping + set: include only persons matching predicate
        List<Person> people2 = List.of(
          new Person("London", "Smith"),
          new Person("London", "Adams"),
          new Person("Paris", "Durand"),
          new Person("Paris", "Martin")
        );
        Map<String, Set<Person>> filteredByCity = people2.stream()
            .collect(groupingBy(
               Person::getCity,
               filtering(p -> p.getLastName().startsWith("S"),
                         toSet())
            ));
        System.out.println(filteredByCity);
    }
}
```

---

## 📚 What each example shows & when to use

| # | Use-case / What it does                                                                                                                                       | When it’s useful / why common                                                                                                                                  |
|---|---------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | Basic deduplication: stream → `Set` with `toSet()`                                                                                                            | When you just want to remove duplicates and don’t care about order or specific implementation.                                                                 |
| 2 | Stream → specific `Set` implementation (e.g. sorted `TreeSet`) via `toCollection(...)`                                                                        | When you want ordering (sorted or insertion-order), or need a particular `Set` type. ([amitph][1])                                                             |
| 3 | Group elements by a key and collect mapped values into a `Set` per key: `Map<K, Set<V>>` via `groupingBy(..., mapping(..., toSet()))`                         | Useful for grouping + deduplication, e.g. “all last names per city (unique)”. ([Oracle Docs][2])                                                               |
| 4 | Handle nested collections in objects: flatten nested collections + group by a key, collecting flattened results into a `Set` per key — via `flatMapping(...)` | Very handy when each element has a collection (e.g. orders → items, users → roles, etc.), and you want a flat set per group. ([Oracle Docs][3])                |
| 5 | Combine grouping + filtering + set collection: e.g. filter elements per group before collecting into a `Set` — using `filtering(...)` downstream collector    | Useful to avoid pre-filtering and still maintain mapping for all groups (with possibly empty sets), or to combine complex downstream logic. ([Oracle Docs][3]) |

---

## ✅ Why these patterns help & what you gain

* **Clean, declarative code**: avoids manual loops, conditionals, and mutable container management; streams + collectors make code more functional and concise.
* **Deduplication + grouping + flattening — in one pass**: for nested data (lists of lists, objects with collections), you can flatten, group, dedupe, and map with a single `collect(...)` — efficient and less error-prone.
* **Flexibility over result type**: you control whether the result is a plain `HashSet`, a sorted `TreeSet`, insertion-ordered `LinkedHashSet`, or even immutable via newer collectors (e.g. `toUnmodifiableSet()`). ([amitph][1])
* **Composability for complex data transformations**: mixing `groupingBy`, `mapping`, `flatMapping`, `filtering`, `collectingAndThen`, etc., gives powerful data pipelines — helpful in real-world data processing.

[1]: https://www.amitph.com/java-8-stream-collectors/?utm_source=chatgpt.com "Java 8 Streams API - Stream Collectors - amitph"
[2]: https://docs.oracle.com/en/java/javase/13/docs/api/java.base/java/util/stream/Collectors.html?utm_source=chatgpt.com "Collectors (Java SE 13 & JDK 13 )"
[3]: https://docs.oracle.com/javase/9/docs/api/java/util/stream/Collectors.html?utm_source=chatgpt.com "Collectors (Java SE 9 & JDK 9 )"

---

## Example 1 — Flattening nested lists + collecting unique values (null-safe)

Let’s say you have a data model where each `User` has a list of email addresses (some may be `null`, lists may be empty or `null`), and you want to collect **all unique email domains** across a list of users.

```java
import java.util.*;
import java.util.stream.*;
import static java.util.stream.Collectors.*;

class User {
    private List<String> emails;
    public User(List<String> emails) { this.emails = emails; }
    public List<String> getEmails() { return emails; }
}

public class EmailDomainCollector {
    public static Set<String> extractUniqueDomains(List<User> users) {
        if (users == null) {
            return Collections.emptySet();
        }
        return users.stream()
                    // skip null user entries
                    .filter(Objects::nonNull)
                    // get email list safely — avoid null list
                    .flatMap(user -> {
                        List<String> emails = user.getEmails();
                        return (emails == null ? Stream.<String>empty() : emails.stream());
                    })
                    // skip null email strings
                    .filter(Objects::nonNull)
                    // extract domain part (after '@'), filter valid ones
                    .map(email -> {
                        int at = email.indexOf("@");
                        return (at >= 0 && at < email.length() - 1) ? email.substring(at + 1).toLowerCase() : null;
                    })
                    .filter(Objects::nonNull)
                    .collect(toSet());
    }
}
```

**What this does / why it’s useful:**

* Handles potential `null` in users list, or `null` email-lists, or null email strings.
* Uses `flatMap(...)` + `filter(...)` + `map(...)` to flatten nested collections and extract just what you want.
* `toSet()` ensures uniqueness — you end up with a set of distinct domains.

---

## Example 2 — Grouping + mapping + filtering: e.g. grouping employees by department and collecting high-earners’ names

Suppose you have an `Employee` class and want a `Map<Department, Set<String>>` of names of employees in that department earning above a threshold.

```java
import java.util.*;
import java.util.stream.*;
import static java.util.stream.Collectors.*;

class Employee {
    private String department;
    private String name;
    private double salary;
    public Employee(String department, String name, double salary) {
        this.department = department;
        this.name = name;
        this.salary = salary;
    }
    public String getDepartment() { return department; }
    public String getName() { return name; }
    public double getSalary() { return salary; }
}

public class HighEarnerByDept {
    public static Map<String, Set<String>> highEarnersByDept(List<Employee> employees, double threshold) {
        if (employees == null) {
            return Collections.emptyMap();
        }
        return employees.stream()
                        .filter(Objects::nonNull)
                        .collect(groupingBy(
                            Employee::getDepartment,
                            filtering(e -> e.getSalary() >= threshold,
                                      mapping(Employee::getName,
                                              toSet()))
                        ));
    }
}
```

**Key points:**

* Uses `groupingBy(...)` to partition by department. ([Oracle Docs][1])
* Uses downstream `filtering(...)` so that for each department you only collect employees meeting the salary cutoff. ([Oracle Docs][2])
* Uses `mapping(...)` to convert from `Employee` to `String name`. ([Oracle Docs][3])
* Final result: for every department (even those with no high earners), you get a mapping to either a (possibly empty) `Set<String>`. That differs from pre-filtering + grouping, which might omit departments entirely.

This kind of downstream filtering + mapping is a common “real-world” use for stream + set collection.

---

## Example 3 — Nested objects + flatMapping + collecting nested unique properties: e.g. orders → customers → unique purchased product IDs

Suppose you have a domain with customers placing multiple orders; each `Order` has a list of `LineItem`s. You want to build `Map<CustomerId, Set<ProductId>>` — i.e. for each customer, a deduplicated set of all product IDs they ever ordered.

```java
import java.util.*;
import java.util.stream.*;
import static java.util.stream.Collectors.*;

class LineItem {
    private String productId;
    public LineItem(String productId) { this.productId = productId; }
    public String getProductId() { return productId; }
}

class Order {
    private String customerId;
    private List<LineItem> items;
    public Order(String customerId, List<LineItem> items) {
        this.customerId = customerId;
        this.items = items;
    }
    public String getCustomerId() { return customerId; }
    public List<LineItem> getItems() { return items; }
}

public class OrderAggregator {
    public static Map<String, Set<String>> productsByCustomer(List<Order> orders) {
        if (orders == null) {
            return Collections.emptyMap();
        }
        return orders.stream()
            .filter(Objects::nonNull)
            .collect(groupingBy(
                Order::getCustomerId,
                flatMapping(order -> {
                    List<LineItem> items = order.getItems();
                    return (items == null ? Stream.<String>empty()
                            : items.stream()
                                   .filter(Objects::nonNull)
                                   .map(LineItem::getProductId)
                                   .filter(Objects::nonNull));
                }, toSet())
            ));
    }
}
```

**What’s going on / why this matters:**

* We `groupingBy` customer ID. ([Oracle Docs][2])
* We use `flatMapping(...)` as downstream to flatten all `LineItem` lists across possibly many orders per customer, then map to product IDs, filter out possible nulls — and finally collect to a `Set`. ([Oracle Docs][1])
* This ensures: for each customer, you get a **deduplicated** set of product IDs, across all their orders — even if they ordered the same product multiple times.

---

## ✅ Why these templates are “enterprise-grade / robust”

* They handle **null safety** (null lists, null items, null parent lists).
* They flatten nested structures cleanly using `flatMap`/`flatMapping`.
* They group by keys (department, customer id, etc.), yet produce **sets** per group (deduplicated, unordered but unique).
* They separate mapping/filtering logic from collection logic — making transformations easier to read and maintain.
* They leverage downstream collectors (`mapping`, `filtering`, `flatMapping`) that are part of the standard API (since Java 9+) — so no custom collectors needed. ([Oracle Docs][1])

---

If you like — I can also build a **fourth example** that results in an **immutable** `Map<K, Set<V>>` (i.e. where both the map and the sets inside are unmodifiable) — useful in multithreaded / caching / functional-style code.
Do you want me to build that?

[1]: https://docs.oracle.com/en/java/javase/16/docs/api/java.base/java/util/stream/Collectors.html?utm_source=chatgpt.com "Collectors (Java SE 16 & JDK 16)"
[2]: https://docs.oracle.com/javase/9/docs/api/java/util/stream/Collectors.html?utm_source=chatgpt.com "Collectors (Java SE 9 & JDK 9 )"
[3]: https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/stream/Collectors.html?utm_source=chatgpt.com "Collectors (Java SE 11 & JDK 11 )"

Cool — here’s a **4th** example: using modern Java (Java 10+) stream collectors to produce an **immutable** `Map<K, Set<V>>`. This means the *map* itself — and each *set* inside it — are unmodifiable.

---

## 🛡️ Example 4 — Immutable `Map<K, Set<V>>` via `toUnmodifiableMap` + unmodifiable sets

Suppose you have a list of `Order` objects; each `Order` has a `customerId` and a list of `productId`s. You want a `Map<String, Set<String>>` mapping each customer to the **unique** product IDs they ordered — and then make sure the resulting map and the sets inside are immutable (so nobody accidentally modifies them later).

```java
import java.util.*;
import java.util.stream.*;
import static java.util.stream.Collectors.*;

class Order {
    private final String customerId;
    private final List<String> productIds;
    public Order(String customerId, List<String> productIds) {
        this.customerId = customerId;
        this.productIds = productIds;
    }
    public String getCustomerId() { return customerId; }
    public List<String> getProductIds() { return productIds; }
}

public class ImmutableOrderSummary {
    public static Map<String, Set<String>> productsByCustomerImmutable(List<Order> orders) {
        if (orders == null) {
            return Map.of(); // empty unmodifiable map
        }
        // First, group and collect sets (mutable), then convert to immutable
        Map<String, Set<String>> temp = orders.stream()
            .filter(Objects::nonNull)
            .collect(groupingBy(
                Order::getCustomerId,
                flatMapping(order -> {
                    List<String> ids = order.getProductIds();
                    return (ids == null ? Stream.<String>empty()
                                       : ids.stream().filter(Objects::nonNull));
                }, toSet())
            ));

        // Now transform to an unmodifiable map whose values are unmodifiable sets
        return temp.entrySet().stream()
            .collect(toUnmodifiableMap(
                Map.Entry::getKey,
                e -> Set.copyOf(e.getValue())  // unmodifiable set copy
            ));
    }
}
```

### What’s going on:

* We first do a normal `groupingBy + flatMapping + toSet()` to get a mutable `Map<String, Set<String>>`.
* Then we transform that into an unmodifiable map — using `Collectors.toUnmodifiableMap(...)`, which produces a map that cannot be modified after creation. ([Oracle Docs][1])
* For the values (the sets), we use `Set.copyOf(...)`, which returns an unmodifiable copy of the input set. (Alternatively, if you were on Java 10+ and wanted, you could use streams + `toUnmodifiableSet()`, but `Set.copyOf(...)` works well here.)
* The result: **immutable map**, pointing to **immutable sets** — safe to share across threads or expose to callers without worrying about accidental modifications.

---

## ✅ Why this is useful (immutable collections)

* The returned `Map` and inner `Set` objects cannot be modified: no add/remove/put. This helps prevent accidental bugs (especially in shared or multi-threaded contexts). ([Oracle Docs][2])
* Because immutability is guaranteed, you can safely expose the result of this method to external code (clients, APIs) without fear they’ll mutate your internal data.
* Performance + memory: immutable collections from static factory methods (or via `toUnmodifiable...`) tend to have lower overhead (no modification housekeeping) and can be more space-efficient than mutable ones when you don’t need changes. ([Oracle Docs][2])

[1]: https://docs.oracle.com/en/java/javase/23/docs/api/java.base/java/util/stream/Collectors.html?utm_source=chatgpt.com "Collectors (Java SE 23 & JDK 23)"
[2]: https://docs.oracle.com/en/java/javase/24/core/creating-immutable-lists-sets-and-maps.html?utm_source=chatgpt.com "Creating Unmodifiable Lists, Sets, and Maps"
