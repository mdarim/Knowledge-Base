Here’s an overview of how to use stored procedures in Spring Data JPA — including the main annotations, when/how to use them, and some example patterns.

---

## ✅ Supported ways to call stored procedures in Spring Data JPA

* Spring Data JPA supports stored procedures via JPA 2.1’s stored-procedure support. ([Home][1])
* You can declare stored-procedure metadata on your entities via @NamedStoredProcedureQuery (and optionally multiple via `@NamedStoredProcedureQueries`). ([Home][2])
* Alternatively (or in addition), you can call stored procedures directly from repository interfaces using @Procedure. ([Home][1])
* As a fallback (or for special needs), you may also use a native query via @Query (with `nativeQuery=true`) to call the stored procedure. ([Baeldung on Kotlin][3])

---

## 🛠️ Example Patterns

### Defining a stored procedure in the database

For example, in SQL you might have:

```sql
CREATE PROCEDURE plus1inout (IN arg INT, OUT res INT)
BEGIN
  SET res = arg + 1;
END;
```

(This is similar to examples in official docs.) ([Home][1])

---

### Option 1: Map via entity with `@NamedStoredProcedureQuery`

```java
@Entity
@NamedStoredProcedureQuery(
  name = "User.plus1",                // logical name for JPA
  procedureName = "plus1inout",       // actual DB procedure name
  parameters = {
    @StoredProcedureParameter(mode = ParameterMode.IN,  name = "arg", type = Integer.class),
    @StoredProcedureParameter(mode = ParameterMode.OUT, name = "res", type = Integer.class)
  }
)
public class User {
    @Id
    private Long id;
    // other fields…
}
```

Then in your repository interface:

```java
public interface UserRepository extends JpaRepository<User, Long> {
    @Procedure(name = "User.plus1")
    Integer plus1(@Param("arg") Integer arg);
}
```

Calling `userRepository.plus1(5)` would execute the stored procedure and return the `res` output parameter (i.e. 6). ([Home][2])

---

### Option 2: Use `@Procedure` directly on repository method (no entity-side annotation required)

```java
public interface CarRepository extends JpaRepository<Car, Integer> {
  
  // If the method name matches the stored procedure name:
  @Procedure
  Integer GET_TOTAL_CARS_BY_MODEL(@Param("model_in") String model);

  // Or explicitly specify procedure name:
  @Procedure(procedureName = "GET_TOTAL_CARS_BY_MODEL")
  Integer getTotalCarsByModel(@Param("model_in") String model);
}
```

Here the stored procedure might be something like:

```sql
CREATE PROCEDURE GET_TOTAL_CARS_BY_MODEL(IN model_in VARCHAR(50), OUT count_out INT) 
BEGIN
  SELECT COUNT(*) INTO count_out 
    FROM car 
    WHERE model = model_in;
END
```

So calling `getTotalCarsByModel("Tesla")` returns the count of cars with model “Tesla.” ([Baeldung on Kotlin][3])

---

### Option 3: Use native query + `@Query`

This is useful if you want full control — for example, returning a list of entities:

```java
public interface CarRepository extends JpaRepository<Car, Integer> {

  @Query(value = "CALL FIND_CARS_AFTER_YEAR(:year_in)", nativeQuery = true)
  List<Car> findCarsAfterYear(@Param("year_in") Integer year);
}
```

Assuming you have a stored procedure `FIND_CARS_AFTER_YEAR`, this will map the result set to `Car` entities. ([Baeldung on Kotlin][3])

---

## ⚠️ Things to watch out for / Tips

* If the stored procedure returns multiple OUT parameters (or a result set plus out params), simple `@Procedure` with a single return type may not suffice. In that case, define the stored proc via `@NamedStoredProcedureQuery` and let the repository method return a `Map<String,Object>` (parameter name → value). ([Home][1])
* If your stored procedure returns a result set (e.g. a cursor) instead of out params, then `@Procedure` cannot directly map it; use native query (or more advanced JPA / JDBC handling) instead. ([Home][2])
* Using `@Procedure` keeps code concise and integrated with JPA — usually cleaner than using plain JDBC or `JdbcTemplate`. ([Java Code Geeks][4])

[1]: https://docs.spring.io/spring-data/jpa/docs/3.0.0-M4/reference/html/?utm_source=chatgpt.com "Spring Data JPA - Reference Documentation"
[2]: https://docs.spring.io/spring-data/jpa/reference/jpa/stored-procedures.html?utm_source=chatgpt.com "Stored Procedures :: Spring Data JPA"
[3]: https://www.baeldung.com/spring-data-jpa-stored-procedures?utm_source=chatgpt.com "Calling Stored Procedures from Spring Data JPA Repositories | Baeldung"
[4]: https://www.javacodegeeks.com/spring-jpa-procedure-annotation-example.html?utm_source=chatgpt.com "Spring JPA @Procedure Annotation Example - Java Code Geeks"

Great — here are **complete, real-world example setups** for using stored procedures with Spring Data JPA + a relational database — with variants for **MySQL**, **PostgreSQL**, and **Oracle Database**. Each example includes the SQL for the stored procedure, the JPA entity (if needed), the repository interface, and a sample service call.

---

## 🧩 Example 1 — MySQL + Spring Data JPA (basic IN / OUT param, or result set)

### SQL: define stored procedures in MySQL

Suppose you have a table:

```sql
CREATE TABLE car (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  model VARCHAR(100),
  year INT
);
```

Then:

```sql
DELIMITER //
CREATE PROCEDURE GET_TOTAL_CARS_BY_MODEL(
    IN model_in VARCHAR(100),
    OUT count_out INT
)
BEGIN
    SELECT COUNT(*) INTO count_out
      FROM car
      WHERE model = model_in;
END;
//
DELIMITER ;
```

Or a procedure returning a result set:

```sql
DELIMITER //
CREATE PROCEDURE FIND_CARS_AFTER_YEAR(IN year_in INT)
BEGIN
    SELECT * FROM car WHERE year >= year_in ORDER BY year;
END;
//
DELIMITER ;
```

These align with the tutorials for MySQL + JPA. ([Baeldung on Kotlin][1])

### JPA Repository: call stored procedure via `@Procedure`

You may (option A) call the OUT-param procedure:

```java
public interface CarRepository extends JpaRepository<Car, Long> {

    @Procedure("GET_TOTAL_CARS_BY_MODEL")
    Integer getTotalCarsByModel(@Param("model_in") String model);
}
```

Or (option B) call the result-set returning procedure:

```java
public interface CarRepository extends JpaRepository<Car, Long> {

    @Query(value = "CALL FIND_CARS_AFTER_YEAR(:year_in)", nativeQuery = true)
    List<Car> findCarsAfterYear(@Param("year_in") Integer year);
}
```

This matches what the MySQL + Spring Boot guides show. ([Baeldung on Kotlin][1])

### Usage in a Service:

```java
@Service
public class CarService {

    @Autowired
    private CarRepository carRepository;

    public int countByModel(String model) {
        return carRepository.getTotalCarsByModel(model);
    }

    public List<Car> getCarsAfterYear(int year) {
        return carRepository.findCarsAfterYear(year);
    }
}
```

---

## 🐘 Example 2 — PostgreSQL + Spring Data JPA (function or procedure)

For PostgreSQL things are a little more nuanced (depending on whether you use a function or a procedure). According to the JDBC driver docs, procedures (since PG 11) can be invoked with `CallableStatement`. ([jdbc.postgresql.org][2])
But many JPA tutorials show calling database *functions* (returning a value or a table) — especially if you want a result set mapped to entities. ([Java Code Geeks][3])

### Example: database function returning a value

SQL:

```sql
CREATE OR REPLACE FUNCTION get_car_count_by_model(model_in VARCHAR)
RETURNS INTEGER AS $$
BEGIN
    RETURN (SELECT COUNT(*) FROM car WHERE model = model_in);
END;
$$ LANGUAGE plpgsql;
```

Alternatively, a function returning a set of rows:

```sql
CREATE OR REPLACE FUNCTION find_cars_after_year(year_in INT)
RETURNS SETOF car AS $$
BEGIN
    RETURN QUERY
      SELECT * FROM car WHERE year >= year_in ORDER BY year;
END;
$$ LANGUAGE plpgsql;
```

*(Note: for result sets, you need to map returned rows — JPA + Hibernate may need extra mapping or use native queries.)*

### JPA Repository: call function via `@NamedStoredProcedureQuery` + `@Procedure`

```java
@Entity
@Table(name = "car")
@NamedStoredProcedureQuery(
    name = "Car.getCountByModel",
    procedureName = "get_car_count_by_model",
    parameters = {
        @StoredProcedureParameter(mode = ParameterMode.IN, name = "model_in", type = String.class)
    }
)
public class Car {
    @Id
    private Long id;
    private String model;
    private Integer year;
    // getters/setters
}
```

Repository:

```java
public interface CarRepository extends JpaRepository<Car, Long> {

    @Procedure(name = "Car.getCountByModel")
    Integer getCountByModel(@Param("model_in") String model);
}
```

For returning a list, you might instead choose native query:

```java
public interface CarRepository extends JpaRepository<Car, Long> {
    @Query(value = "SELECT * FROM find_cars_after_year(:year_in)", nativeQuery = true)
    List<Car> findCarsAfterYear(@Param("year_in") Integer year);
}
```

This approach is commonly referenced. ([Java Code Geeks][3])

### Important caveat (PostgreSQL + JPA + functions vs procedures):

Because PostgreSQL functions and procedures behave differently, sometimes you get errors when trying to call a procedure that returns a table. For instance: using `CALL` where the driver expects a function. ([devpress.csdn.net][4])

Thus — many people define a function (rather than procedure) when they expect a return value or result set.

---

## 🏛️ Example 3 — Oracle Database + Spring Data JPA (IN, OUT, REF_CURSOR + entity mapping)

With Oracle, you can use stored procedures (or package procedures). If your procedure returns a REF_CURSOR (result set), you can map it with `@NamedStoredProcedureQuery`. Some JPA providers (e.g. EclipseLink) tend to support REF_CURSOR better than others. ([logicbig.com][5])

### Example: Oracle stored procedure with REF_CURSOR

Suppose you have:

```sql
-- inside some package, or standalone
CREATE OR REPLACE PROCEDURE GET_CARS_AFTER_YEAR(
    p_year IN NUMBER,
    p_cursor OUT SYS_REFCURSOR
) AS
BEGIN
    OPEN p_cursor FOR
      SELECT id, model, year
      FROM car
      WHERE year >= p_year
      ORDER BY year;
END;
/
```

### JPA Entity + NamedStoredProcedureQuery

```java
@Entity
@Table(name = "car")
@NamedStoredProcedureQuery(
  name = "Car.findByYear",
  procedureName = "GET_CARS_AFTER_YEAR",
  resultClasses = { Car.class },
  parameters = {
    @StoredProcedureParameter(mode = ParameterMode.IN, name = "p_year", type = Integer.class),
    @StoredProcedureParameter(mode = ParameterMode.REF_CURSOR, name = "p_cursor", type = void.class)
  }
)
public class Car {
    @Id
    private Long id;
    private String model;
    private Integer year;
    // getters/setters
}
```

Repository interface:

```java
public interface CarRepository extends JpaRepository<Car, Long> {

    @Procedure(name = "Car.findByYear")
    List<Car> getCarsAfterYear(@Param("p_year") Integer year);
}
```

Some caveats: older versions of JPA/Hibernate with Oracle + REF_CURSOR had issues — sometimes need different JPA provider (e.g. EclipseLink) for full REF_CURSOR support. ([logicbig.com][5])

---

## ⚠️ Gotchas, Limitations & Tips

* With simple stored procedures that return a **single OUT parameter**, Spring Data JPA + `@Procedure` works smoothly — the method can return that single value. ([Home][6])
* For multiple OUT parameters: earlier versions of Spring Data JPA didn’t fully support multiple OUT parameters — e.g. a StackOverflow thread notes this limitation. ([Stack Overflow][7])
* If the stored procedure returns a *result set* (cursor / table) — you often need to use `@NamedStoredProcedureQuery` with `ResultClasses` (or map via native query) — not just a simple `@Procedure` with a primitive return type. ([Baeldung on Kotlin][8])
* JPA provider and database matter: some features (like REF_CURSOR) may work in one combination (Oracle + EclipseLink) but be problematic in another (Oracle + Hibernate). ([logicbig.com][5])
* For PostgreSQL: due to differences between functions and procedures, you may prefer defining a **function returning a value or setof rows** rather than a procedure — especially if you need a result set. ([jdbc.postgresql.org][2])

---

## ✅ Which approach when?

| Use case / DB type                                              | Recommended approach                                                                                                   |
|-----------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Simple OUT parameter, MySQL, Oracle, or DB function             | `@Procedure` in repository — easy and clean                                                                            |
| Return result set from stored procedure (e.g. list of entities) | Define `@NamedStoredProcedureQuery` on entity with resultClasses or use native `@Query(nativeQuery=true)`              |
| Complex result (multiple OUT params + cursor)                   | `@NamedStoredProcedureQuery` + `@Procedure`, possibly returning `Map<String, Object>` — but check JPA provider support |
| PostgreSQL (returning table)                                    | Use a function returning SETOF rather than a procedure; map with JPA via `@NamedStoredProcedureQuery` or native query  |

[1]: https://www.baeldung.com/spring-data-jpa-stored-procedures?utm_source=chatgpt.com "Calling Stored Procedures from Spring Data JPA Repositories | Baeldung"
[2]: https://jdbc.postgresql.org/documentation/callproc/?utm_source=chatgpt.com "Calling Stored Functions and Procedures | pgJDBC"
[3]: https://www.javacodegeeks.com/spring-jpa-procedure-annotation-example.html?utm_source=chatgpt.com "Spring JPA @Procedure Annotation Example - Java Code Geeks"
[4]: https://devpress.csdn.net/postgresql/630cb4bec67703293081a481.html?utm_source=chatgpt.com "Can't call PostgreSQL's 11 Stored Procedure with Hibernate_rxjava_Postgredaxiang-PostgreSQL"
[5]: https://www.logicbig.com/tutorials/spring-framework/spring-data/stored-procedure-ref-cursor.html?utm_source=chatgpt.com "Spring Data JPA - Using @Procedure with JPA named queries to call Oracle procedure involving Ref Cursor"
[6]: https://docs.spring.io/spring-data/jpa/reference/jpa/stored-procedures.html?utm_source=chatgpt.com "Stored Procedures :: Spring Data JPA"
[7]: https://stackoverflow.com/questions/42531970/correctly-call-a-stored-procedure-using-spring-data-jpa?utm_source=chatgpt.com "java - Correctly call a stored procedure using Spring Data JPA - Stack Overflow"
[8]: https://www.baeldung.com/jpa-stored-procedures?utm_source=chatgpt.com "A Guide to Stored Procedures with JPA | Baeldung"
