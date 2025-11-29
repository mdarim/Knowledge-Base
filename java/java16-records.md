## 📌 **Java 16: Records**

Java 16 introduced **Records**, a new feature that allows developers to create **immutable data classes** with **minimal boilerplate code**. Records simplify the creation of **value-based classes** that only hold data and provide **built-in implementations** for common methods like `equals()`, `hashCode()`, and `toString()`.

---

### 🔎 **What is a Record?**

A **Record** is a **special kind of class** in Java designed to hold **immutable data**. It automatically generates the following for you:

1. **Constructor**
2. **Getters** for all fields
3. **`equals()` method**
4. **`hashCode()` method**
5. **`toString()` method**

---

### ✅ **Syntax for a Record:**

```java
public record Person(String name, int age) {
}
```

The above code defines a `Person` record with two fields: `name` and `age`. Java automatically provides:

- A constructor `Person(String name, int age)`
- Getters `name()` and `age()`
- `equals()`, `hashCode()`, and `toString()` implementations

---

### 🛠 **Example Code:**

```java
public class RecordExample {
    public static void main(String[] args) {
        Person person = new Person("John Doe", 30);

        System.out.println(person.name());   // John Doe
        System.out.println(person.age());    // 30
        System.out.println(person);          // Person[name=John Doe, age=30]
    }
}

public record Person(String name, int age) {
}
```

#### **Output:**

```
John Doe
30
Person[name=John Doe, age=30]
```

---

### 🎯 **Key Features of Records:**

| **Feature**           | **Description**                                           |
|-----------------------|-----------------------------------------------------------|
| **Immutable Fields**   | Fields in a record are **final** and cannot be modified. |
| **Built-in Methods**   | Automatically provides `equals()`, `hashCode()`, and `toString()`. |
| **Compact Syntax**     | Reduces boilerplate code for data classes.               |
| **Readable Code**      | Makes the code more concise and readable.                |

---

### 📚 **Using Records in Detail:**

#### **1. Creating a Record**

```java
public record Car(String make, String model, int year) {
}
```

#### **2. Using the Record**

```java
public class Main {
    public static void main(String[] args) {
        Car car = new Car("Toyota", "Corolla", 2022);

        // Accessing fields
        System.out.println(car.make());   // Toyota
        System.out.println(car.model());  // Corolla
        System.out.println(car.year());   // 2022

        // toString() method
        System.out.println(car);          // Car[make=Toyota, model=Corolla, year=2022]
    }
}
```

---

### 🛠 **Customizing Records:**

You can add **custom methods** to a record, but **you cannot modify the fields** since they are **final**.

```java
public record Employee(String name, double salary) {
    // Custom method
    public double annualSalary() {
        return salary * 12;
    }
}
```

#### **Usage:**

```java
public class Main {
    public static void main(String[] args) {
        Employee employee = new Employee("Alice", 5000);
        System.out.println("Annual Salary: " + employee.annualSalary());
    }
}
```

---

### 🧪 **Example: Custom Constructor**

You can create a **custom constructor** in a record for additional validation.

```java
public record Person(String name, int age) {
    public Person {
        if (age < 0) {
            throw new IllegalArgumentException("Age must be positive");
        }
    }
}
```

---

### 🔄 **Limitations of Records:**

1. **Immutable**: Fields in records are **final** and cannot be changed after initialization.
2. **No Inheritance**: Records cannot **extend other classes** but can **implement interfaces**.
3. **No Additional Fields**: You cannot add **non-final fields** or **instance variables**.

---

### 🧩 **Example: Implementing an Interface**

```java
public record Product(String name, double price) implements Comparable<Product> {
    @Override
    public int compareTo(Product other) {
        return Double.compare(this.price, other.price);
    }
}
```

---

### ⚙️ **Equality and Hashing with Records:**

Records automatically provide **value-based equality** and **hash codes**.

```java
public class EqualityExample {
    public static void main(String[] args) {
        Person person1 = new Person("John", 25);
        Person person2 = new Person("John", 25);

        System.out.println(person1.equals(person2));  // true
        System.out.println(person1.hashCode() == person2.hashCode());  // true
    }
}
```

---

### 📋 **Summary of Record Features:**

| **Feature**               | **Description**                                               |
|---------------------------|---------------------------------------------------------------|
| **Immutable Fields**       | Fields in a record are `final` and cannot be modified.        |
| **Automatic Getters**      | Getters are automatically provided for all fields.            |
| **Built-in Methods**       | Includes `equals()`, `hashCode()`, and `toString()`.          |
| **Compact Syntax**         | Reduces boilerplate code for data classes.                    |
| **Custom Methods Allowed** | You can add custom methods for business logic.                |
| **Constructor Validation** | You can write custom constructors to validate input.          |

---

### 🔧 **Comparison: Traditional Class vs Record**

| **Aspect**             | **Traditional Class**                             | **Record**                                 |
|------------------------|---------------------------------------------------|--------------------------------------------|
| Syntax Length          | Longer due to boilerplate code                    | Short and concise                          |
| Mutability             | Fields can be mutable                             | Fields are always immutable (final)        |
| `equals()` Method      | Must be written manually                          | Auto-generated                             |
| `hashCode()` Method    | Must be written manually                          | Auto-generated                             |
| `toString()` Method    | Must be written manually                          | Auto-generated                             |

---

### 🛠 **When to Use Records?**

Use records when:

- You need to represent **immutable data**.
- You want to reduce **boilerplate code** for **data-holding classes**.
- You don’t need **inheritance**.
- You need **value-based equality** (comparing objects based on field values).

---

### 🚀 **Real-World Use Case:**

#### **1. Record for Storing User Data**

```java
public record User(String username, String email, boolean active) {
}
```

#### **2. Record for a Response Object**

```java
public record ApiResponse(int status, String message, Object data) {
}
```

---

### 📋 **Summary of Key Points:**

| **Aspect**                 | **Details**                                                   |
|----------------------------|---------------------------------------------------------------|
| **Introduced in**           | **Java 16**                                                   |
| **Purpose**                 | To create **immutable data classes** with less boilerplate.   |
| **Key Benefits**            | Automatic methods (`equals()`, `hashCode()`, `toString()`).   |
| **Limitations**             | Cannot extend classes, fields are immutable.                  |
| **Common Use Cases**        | DTOs (Data Transfer Objects), API responses, value objects.   |

---

