Cool — here are **5 real-world scenarios** with full code (or code-sketch) that use advanced Collectors + Stream API / groupingBy (and other downstream collectors) to solve typical problems. Each shows a practical data-processing case.

---

## 🧩 Scenario 1: Employees by Department + Salary Stats (avg / sum / max)

Suppose you have this domain:

```java
class Employee {
    private String name;
    private String department;
    private double salary;
    // plus constructors / getters …
}
List<Employee> employees = ...;
```

You may want:

* **List of employees per department**
* **Average salary per department**
* **Total salary per department**
* **Highest-paid employee per department**

### Code:

```java
import static java.util.stream.Collectors.*;

Map<String, List<Employee>> empsByDept = employees.stream()
    .collect(groupingBy(Employee::getDepartment));

Map<String, Double> avgSalaryByDept = employees.stream()
    .collect(groupingBy(Employee::getDepartment,
                       averagingDouble(Employee::getSalary)));

Map<String, Double> totalSalaryByDept = employees.stream()
    .collect(groupingBy(Employee::getDepartment,
                       summingDouble(Employee::getSalary)));

Map<String, Optional<Employee>> maxSalaryEmpByDept = employees.stream()
    .collect(groupingBy(Employee::getDepartment,
               maxBy(Comparator.comparingDouble(Employee::getSalary))));
```

You can also unwrap the `Optional<Employee>` if you know department always has ≥1 employee (or handle empty appropriately). This pattern is widespread — grouping + aggregation per group. ([AI Code Snippet][1])

---

## 🛒 Scenario 2: Sales / Orders — Total Sales per Region (or per Customer)

Imagine you have an `Order` class:

```java
class Order {
    String region;
    String customerId;
    double amount;
    // getters …
}
List<Order> orders = ...;
```

You want to compute — e.g. — **total sales amount per region**, or **total spend per customer**.

### Code:

```java
Map<String, Double> totalSalesByRegion = orders.stream()
    .collect(groupingBy(Order::getRegion,
              summingDouble(Order::getAmount)));

Map<String, Double> totalSpendByCustomer = orders.stream()
    .collect(groupingBy(Order::getCustomerId,
              summingDouble(Order::getAmount)));
```

This leverages grouping + summing downstream — classic for financial / sales reports. (This kind of usage is described in streams grouping tutorials.) ([Codippa][2])

---

## 🗂️ Scenario 3: Multi-level Grouping — e.g. Employees → Dept → Age-Group or Team

Suppose your `Employee` has more fields, e.g.:

```java
class Employee {
    String name;
    String department;
    int age;
    String team;
    // getters …
}
```

You might want to group first by department, then within each department group by team — or by age-group (young / senior / etc).

### Code:

```java
// Dept → Team → List<Employee>
Map<String, Map<String, List<Employee>>> byDeptThenTeam =
    employees.stream()
        .collect(groupingBy(Employee::getDepartment,
                 groupingBy(Employee::getTeam)));

// Dept → AgeGroup → List<Employee>
Map<String, Map<String, List<Employee>>> byDeptThenAgeGroup =
    employees.stream()
        .collect(groupingBy(Employee::getDepartment,
             groupingBy(emp -> emp.getAge() < 30 ? "Young" : "Senior")));
```

This hierarchical grouping is helpful when you need nested organization — e.g. generate a report like: department → team → members. ([Codippa][2])

---

## 📦 Scenario 4: Transform + Collect Only Specific Fields — e.g. Names per Department / Unique IDs

Sometimes you don’t need full objects as values. Maybe you just want a list (or set) of names, IDs, or other attribute per group.

Given `Employee` as before:

```java
// Department → List of employee names
Map<String, List<String>> namesByDept = employees.stream()
    .collect(groupingBy(Employee::getDepartment,
             mapping(Employee::getName, toList())));

// Or Department → Set of employee names (unique)
Map<String, Set<String>> uniqueNamesByDept = employees.stream()
    .collect(groupingBy(Employee::getDepartment,
             mapping(Employee::getName, toSet())));
```

This is handy when you want a lightweight “view” — e.g. for UI display or reporting, without carrying full object overhead. This is supported via `mapping(...)`. ([HowToDoInJava][3])

---

## 🥇 Scenario 5: Top-N per Group — e.g. Top 3 Highest Paid Employees per Department

Sometimes you want to not just group, but pick top-N items per group. For example: “give me the 3 highest-paid employees for each department”.

With `Employee(name, dept, salary)`:

```java
Map<String, List<Employee>> top3ByDept = employees.stream()
    .collect(groupingBy(Employee::getDepartment,
        collectingAndThen(
            toList(),
            list -> list.stream()
                        .sorted(Comparator.comparingDouble(Employee::getSalary).reversed())
                        .limit(3)
                        .collect(toList())
        )));
```

This uses `groupingBy` + `collectingAndThen(...)`: first collects all employees per department, then transforms each list (sort + pick top 3). This pattern is common in reporting requirements / “leaderboard per group” type tasks. ([LinkedIn][4])

---

## ⚠️ Notes / When to Use What

* Use `mapping(...)` when you only care about certain attributes (name, ID, etc.).
* Use numeric aggregations (`summingDouble`, `averagingDouble`, etc.) when you need summary metrics.
* For nested grouping (multi-level), structure collectors carefully; readability matters.
* When needing top-N / sorted subsets per group, using `collectingAndThen(...)` with a custom transformation gives great flexibility.
* Always consider if the downstream result should be `List`, `Set`, aggregated value, or even a custom object — `groupingBy` + downstream gives that flexibility.

[1]: https://www.aicodesnippet.com/java/java-8-features/calculating-average-salary-by-department-using-collectorsaveragingint-and-groupingby.html?utm_source=chatgpt.com "Java : Calculating Average Salary by Department using Collectors.averagingInt and groupingBy"
[2]: https://codippa.com/java-collectors-groupingby/?utm_source=chatgpt.com "Master Java Stream Collectors.groupingBy() With Examples"
[3]: https://howtodoinjava.com/java/stream/collectors-groupingby/?utm_source=chatgpt.com "Java 8 Stream - Collectors GroupingBy with Examples"
[4]: https://www.linkedin.com/posts/sajikuttan-se_java-interviewquestion-interviewprep-activity-7378280195029553152-7_9e?utm_source=chatgpt.com "How to find top 3 highest-paid employees by department using Java Streams | Saji Kuttan posted on the topic | LinkedIn"
