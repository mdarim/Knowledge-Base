# 🚀 **Java 8 Date and Time API (`java.time` Package)**

Java 8 introduced the **Date and Time API** in the `java.time` package to address the shortcomings of the old `java.util.Date` and `java.util.Calendar` classes. The new API is more **robust**, **easier to use**, and follows **ISO 8601 standards**.

---

## ✅ **Why the New Date and Time API?**

| **Old API Issues**           | **New API Solutions**                      |
|------------------------------|-------------------------------------------|
| `java.util.Date` is mutable   | New API provides **immutable** classes.   |
| Complex and error-prone       | New API is **intuitive and easy to use**. |
| Thread-unsafe                | New API is **thread-safe**.               |
| Poor timezone handling        | New API provides better **timezone support**. |

---

## ✅ **Main Classes in `java.time` Package**

| **Class**               | **Description**                                      |
|-------------------------|------------------------------------------------------|
| `LocalDate`             | Represents a **date** without time (e.g., 2024-12-24). |
| `LocalTime`             | Represents a **time** without a date (e.g., 10:15:30). |
| `LocalDateTime`         | Represents **both date and time** (e.g., 2024-12-24T10:15:30). |
| `ZonedDateTime`         | Represents date and time with **time zone**.         |
| `Instant`               | Represents a **timestamp** (e.g., seconds since epoch). |
| `Duration`              | Represents the **difference between two times**.     |
| `Period`                | Represents the **difference between two dates**.     |
| `ZoneId`                | Represents a **time zone** identifier.              |
| `DateTimeFormatter`     | Used to **format and parse** date-time objects.      |

---

## ✅ **Creating Date and Time Objects**

### 🔷 **1. `LocalDate` Example**
```java
import java.time.LocalDate;

public class DateExample {
    public static void main(String[] args) {
        LocalDate date = LocalDate.now();  // Current date
        System.out.println("Current Date: " + date);

        LocalDate specificDate = LocalDate.of(2024, 12, 24);  // Specific date
        System.out.println("Specific Date: " + specificDate);
    }
}
```

**Output:**
```
Current Date: 2024-12-24
Specific Date: 2024-12-24
```

---

### 🔷 **2. `LocalTime` Example**
```java
import java.time.LocalTime;

public class TimeExample {
    public static void main(String[] args) {
        LocalTime time = LocalTime.now();  // Current time
        System.out.println("Current Time: " + time);

        LocalTime specificTime = LocalTime.of(10, 15, 30);  // Specific time
        System.out.println("Specific Time: " + specificTime);
    }
}
```

**Output:**
```
Current Time: 10:45:32.123
Specific Time: 10:15:30
```

---

### 🔷 **3. `LocalDateTime` Example**
```java
import java.time.LocalDateTime;

public class DateTimeExample {
    public static void main(String[] args) {
        LocalDateTime dateTime = LocalDateTime.now();  // Current date and time
        System.out.println("Current DateTime: " + dateTime);

        LocalDateTime specificDateTime = LocalDateTime.of(2024, 12, 24, 10, 15, 30);
        System.out.println("Specific DateTime: " + specificDateTime);
    }
}
```

**Output:**
```
Current DateTime: 2024-12-24T10:45:32.123
Specific DateTime: 2024-12-24T10:15:30
```

---

### 🔷 **4. `ZonedDateTime` Example**
```java
import java.time.ZonedDateTime;
import java.time.ZoneId;

public class ZonedDateTimeExample {
    public static void main(String[] args) {
        ZonedDateTime zonedDateTime = ZonedDateTime.now(ZoneId.of("Asia/Dubai"));
        System.out.println("Dubai Time: " + zonedDateTime);

        ZonedDateTime utcTime = ZonedDateTime.now(ZoneId.of("UTC"));
        System.out.println("UTC Time: " + utcTime);
    }
}
```

**Output:**
```
Dubai Time: 2024-12-24T14:45:32.123+04:00[Asia/Dubai]
UTC Time: 2024-12-24T10:45:32.123Z[UTC]
```

---

## ✅ **Working with `Instant` (Timestamp)**
```java
import java.time.Instant;

public class InstantExample {
    public static void main(String[] args) {
        Instant timestamp = Instant.now();  // Current timestamp
        System.out.println("Timestamp: " + timestamp);
    }
}
```

**Output:**
```
Timestamp: 2024-12-24T10:45:32.123Z
```

---

## ✅ **Calculating Duration and Period**

### 🔷 **1. `Duration` (Time Difference)**
```java
import java.time.Duration;
import java.time.LocalTime;

public class DurationExample {
    public static void main(String[] args) {
        LocalTime start = LocalTime.of(10, 0);
        LocalTime end = LocalTime.of(12, 30);

        Duration duration = Duration.between(start, end);
        System.out.println("Duration: " + duration);
    }
}
```

**Output:**
```
Duration: PT2H30M
```

---

### 🔷 **2. `Period` (Date Difference)**
```java
import java.time.LocalDate;
import java.time.Period;

public class PeriodExample {
    public static void main(String[] args) {
        LocalDate start = LocalDate.of(2022, 1, 1);
        LocalDate end = LocalDate.of(2024, 12, 24);

        Period period = Period.between(start, end);
        System.out.println("Period: " + period.getYears() + " years, " + period.getMonths() + " months, " + period.getDays() + " days");
    }
}
```

**Output:**
```
Period: 2 years, 11 months, 23 days
```

---

## ✅ **Formatting Date and Time (`DateTimeFormatter`)**

### 🔷 **Example: Custom Formatting**
```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class DateTimeFormatterExample {
    public static void main(String[] args) {
        LocalDateTime dateTime = LocalDateTime.now();
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy HH:mm:ss");

        String formattedDateTime = dateTime.format(formatter);
        System.out.println("Formatted DateTime: " + formattedDateTime);
    }
}
```

**Output:**
```
Formatted DateTime: 24-12-2024 14:45:32
```

---

## ✅ **Parsing Date and Time**

### 🔷 **Example: Parsing a String into `LocalDate`**
```java
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

public class ParsingExample {
    public static void main(String[] args) {
        String dateString = "24-12-2024";
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy");

        LocalDate date = LocalDate.parse(dateString, formatter);
        System.out.println("Parsed Date: " + date);
    }
}
```

**Output:**
```
Parsed Date: 2024-12-24
```

---

## ✅ **Key Differences Between `LocalDateTime` and `ZonedDateTime`**

| **Aspect**           | **`LocalDateTime`**                   | **`ZonedDateTime`**               |
|----------------------|--------------------------------------|----------------------------------|
| Time Zone Support     | No                                    | Yes                               |
| Use Case             | Local date and time operations         | Date and time with timezone info |

---

## ✅ **Summary of the Java 8 Date and Time API**

| **Class**               | **Description**                                      |
|-------------------------|------------------------------------------------------|
| `LocalDate`             | Date without time (e.g., 2024-12-24).                |
| `LocalTime`             | Time without date (e.g., 10:15:30).                  |
| `LocalDateTime`         | Date and time without timezone.                      |
| `ZonedDateTime`         | Date and time with timezone.                         |
| `Instant`               | Timestamp (e.g., seconds since epoch).               |
| `Duration`              | Time difference.                                     |
| `Period`                | Date difference.                                     |
| `DateTimeFormatter`     | Formatting and parsing dates.                        |

---
