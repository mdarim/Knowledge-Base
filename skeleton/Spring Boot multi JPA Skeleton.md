Here’s a **Skeleton / Starter Guide** to configuring **multiple JPA datasources** in **Spring Boot 4.0.0** with **Gradle**, based on the official Spring Boot recommendations (multiple `EntityManagerFactory` + `PlatformTransactionManager`) and common patterns from Spring Boot 3.x (valid for 4.0 too). ([Home][1])

---

## 🧱 1) **Project Setup (Gradle)**

**build.gradle.kts**

```kotlin
plugins {
    id("org.springframework.boot") version "4.0.0"
    id("io.spring.dependency-management") version "1.1.2"
    kotlin("jvm") version "1.9.0"
    kotlin("plugin.spring") version "1.9.0"
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-data-jpa")
    implementation("org.springframework.boot:spring-boot-starter-web")
    runtimeOnly("com.mysql:mysql-connector-j") // or your DB drivers
}

java.sourceCompatibility = JavaVersion.VERSION_17
```

*Use the correct JDBC driver(s) for your database(s).*

---

## 📑 2) **application.yml (or .properties)**

```yaml
spring:
  datasource:
    primary:
      url: jdbc:mysql://localhost:3306/db_primary
      username: user
      password: secret
      driver-class-name: com.mysql.cj.jdbc.Driver

    secondary:
      url: jdbc:postgresql://localhost:5432/db_secondary
      username: other
      password: otherSecret
      driver-class-name: org.postgresql.Driver
```

You can also scope additional JPA settings under each prefix if needed.

---

## 📦 3) **Package Structure (Important)**

Separate entity/repository packages for each datasource:

```
src/main/kotlin/com/example/app/
├── primary/
│   ├── entity/
│   └── repository/
└── secondary/
    ├── entity/
    └── repository/
```

Each `EnableJpaRepositories` config explicitly scans only its own packages. ([Baeldung on Kotlin][2])

---

## 🔧 4) **Java/Kotlin Configuration Classes**

### 👉 4.1 Primary DB Config

```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
    basePackages = "com.example.app.primary.repository",
    entityManagerFactoryRef = "primaryEntityManagerFactory",
    transactionManagerRef = "primaryTransactionManager"
)
public class PrimaryDataSourceConfig {

    @Bean
    @Primary
    @ConfigurationProperties("spring.datasource.primary")
    public DataSourceProperties primaryDataSourceProps() {
        return new DataSourceProperties();
    }

    @Bean
    @Primary
    public DataSource primaryDataSource() {
        return primaryDataSourceProps()
                .initializeDataSourceBuilder().build();
    }

    @Bean
    @Primary
    public LocalContainerEntityManagerFactoryBean primaryEntityManagerFactory(
            EntityManagerFactoryBuilder builder) {
        return builder
                .dataSource(primaryDataSource())
                .packages("com.example.app.primary.entity")
                .persistenceUnit("primaryPU")
                .build();
    }

    @Bean
    @Primary
    public PlatformTransactionManager primaryTransactionManager(
            final LocalContainerEntityManagerFactoryBean primaryEntityManagerFactory) {
        return new JpaTransactionManager(
                primaryEntityManagerFactory.getObject());
    }
}
```

### 👉 4.2 Secondary DB Config

```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
    basePackages = "com.example.app.secondary.repository",
    entityManagerFactoryRef = "secondaryEntityManagerFactory",
    transactionManagerRef = "secondaryTransactionManager"
)
public class SecondaryDataSourceConfig {

    @Bean
    @ConfigurationProperties("spring.datasource.secondary")
    public DataSourceProperties secondaryDataSourceProps() {
        return new DataSourceProperties();
    }

    @Bean
    public DataSource secondaryDataSource() {
        return secondaryDataSourceProps()
                .initializeDataSourceBuilder().build();
    }

    @Bean
    public LocalContainerEntityManagerFactoryBean secondaryEntityManagerFactory(
            EntityManagerFactoryBuilder builder) {
        return builder
                .dataSource(secondaryDataSource())
                .packages("com.example.app.secondary.entity")
                .persistenceUnit("secondaryPU")
                .build();
    }

    @Bean
    public PlatformTransactionManager secondaryTransactionManager(
            final LocalContainerEntityManagerFactoryBean secondaryEntityManagerFactory) {
        return new JpaTransactionManager(
                secondaryEntityManagerFactory.getObject());
    }
}
```

**Key points**

* Each datasource gets its own `EntityManagerFactory` and `PlatformTransactionManager`. ([Home][1])
* Mark the **primary** datasource beans with `@Primary` so Spring Boot auto-config knows which to pick when only one is expected. ([Home][3])
* `@EnableJpaRepositories` must point to the correct packages and refer to the correct EMF and TM. ([Baeldung on Kotlin][2])

---

## 🧠 5) **Common Gotchas**

### 📌 Beans must not overlap

Separate your entity and repository packages so Spring Data doesn’t auto-detect the wrong entities/repositories. ([Baeldung on Kotlin][2])

### 📌 JPA properties

If you set **JPA properties** (like `hibernate.ddl-auto`, dialect, etc.), you might need to apply them separately or via custom `JpaProperties` beans. ([Home][1])

### 📌 EntityManagerFactoryBuilder injection

Spring Boot auto-config supplies an `EntityManagerFactoryBuilder` for you. Use it; don’t construct manually unless necessary. ([Home][1])

---

## 🧪 6) **Using the Repositories**

With this setup, you can inject repositories like this:

```java
@Autowired
private PrimaryEntityRepository primaryRepo;

@Autowired
private SecondaryEntityRepository secondaryRepo;
```

Each repository will operate against its own datasource.

---

## 🎯 Summary

| Layer                    | Primary | Secondary |
|--------------------------|---------|-----------|
| DataSource               | ✔️      | ✔️        |
| EntityManagerFactory     | ✔️      | ✔️        |
| TransactionManager       | ✔️      | ✔️        |
| Separate Repository Base | ✔️      | ✔️        |

This skeleton lets you build on a **multi-JPA setup** using Spring Boot 4.0.0 with **Gradle** and aligns with the official Spring Boot guidance for multiple datasources. ([Home][1])

---

If you want, I can also generate a **complete working sample GitHub project** or an **application.yml** version of this config!

[1]: https://docs.spring.io/spring-boot/how-to/data-access.html?utm_source=chatgpt.com "Data Access :: Spring Boot"
[2]: https://www.baeldung.com/spring-boot-configure-multiple-datasources?utm_source=chatgpt.com "Configure and Use Multiple DataSources in Spring Boot"
[3]: https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/?utm_source=chatgpt.com "Spring Boot Reference Guide"
