Cool — here is a full example of how you can set up a “base audit class + sample entity + configuration” for JPA using Spring Data JPA (with a Spring Boot–style setup). This uses annotation-based auditing (i.e. `@CreatedDate`, `@LastModifiedDate`, etc.), and shows how to plug in an `AuditorAware` to capture “who” made the change.

---

## 📁 1. Maven/Gradle deps (or just make sure you have)

You need Spring Data JPA plus `spring-aspects`. For example (Maven):

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-aspects</artifactId>
</dependency>
```

Having `spring-aspects` is required to allow auditing functionality. ([KnpCode][1])

---

## 🛠️ 2. Create a Base Auditable Class

Define a base class that holds audit fields. Use `@MappedSuperclass` so it doesn’t create its own table, and `@EntityListeners(AuditingEntityListener.class)` so changes are tracked automatically. ([KnpCode][1])

```java
package com.example.audit;

import javax.persistence.*;
import java.util.Date;

import org.springframework.data.annotation.CreatedBy;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedBy;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class Auditable<U> {

    @CreatedBy
    @Column(name = "created_by", updatable = false)
    private U createdBy;

    @CreatedDate
    @Column(name = "created_date", updatable = false)
    @Temporal(TemporalType.TIMESTAMP)
    private Date createdDate;

    @LastModifiedBy
    @Column(name = "last_modified_by")
    private U lastModifiedBy;

    @LastModifiedDate
    @Column(name = "last_modified_date")
    @Temporal(TemporalType.TIMESTAMP)
    private Date lastModifiedDate;

    // getters and setters
    public U getCreatedBy() { return createdBy; }
    public void setCreatedBy(U createdBy) { this.createdBy = createdBy; }

    public Date getCreatedDate() { return createdDate; }
    public void setCreatedDate(Date createdDate) { this.createdDate = createdDate; }

    public U getLastModifiedBy() { return lastModifiedBy; }
    public void setLastModifiedBy(U lastModifiedBy) { this.lastModifiedBy = lastModifiedBy; }

    public Date getLastModifiedDate() { return lastModifiedDate; }
    public void setLastModifiedDate(Date lastModifiedDate) { this.lastModifiedDate = lastModifiedDate; }
}
```

You could also use `java.time.LocalDateTime` or other types instead of `java.util.Date` depending on your preferences and configuration. ([Code With Sibin][2])

---

## 📦 3. Sample Entity Extending Auditable

Now you define a real entity that extends the base class. For example, let’s say you have a `Post` entity:

```java
package com.example.model;

import javax.persistence.*;

@Entity
@Table(name = "post")
public class Post extends Auditable<String> {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name="post_id")
    private Long id;

    @Column(name="post_title", nullable = false)
    private String title;

    @Column(name="post_content")
    private String content;

    // Constructors
    public Post() { }

    public Post(String title, String content) {
        this.title = title;
        this.content = content;
    }

    // getters & setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }

    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }

    public String getContent() { return content; }
    public void setContent(String content) { this.content = content; }

    @Override
    public String toString() {
        return "Post{" +
               "id=" + id +
               ", title='" + title + '\'' +
               ", content='" + content + '\'' +
               ", createdBy=" + getCreatedBy() +
               ", createdDate=" + getCreatedDate() +
               ", lastModifiedBy=" + getLastModifiedBy() +
               ", lastModifiedDate=" + getLastModifiedDate() +
               '}';
    }
}
```

With this setup, whenever you persist or update a `Post`, the audit fields (`createdBy`, `createdDate`, `lastModifiedBy`, `lastModifiedDate`) will be managed automatically by Spring Data JPA. ([KnpCode][1])

---

## 🧑‍⚙️ 4. Provide AuditorAware Implementation

To enable the `@CreatedBy` / `@LastModifiedBy` functionality you need to supply a bean that implements `AuditorAware<U>` so the framework knows who the “current user” is. For example:

```java
package com.example.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.domain.AuditorAware;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;

import java.util.Optional;

@Configuration
@EnableJpaAuditing
public class AuditConfig {

    @Bean
    public AuditorAware<String> auditorProvider() {
        return new AuditorAwareImpl();
    }

    public static class AuditorAwareImpl implements AuditorAware<String> {
        @Override
        public Optional<String> getCurrentAuditor() {
            // TODO: return currently logged-in user, e.g. from Spring Security
            return Optional.of("SYSTEM");  
            // or Optional.ofNullable(securityContext.getAuthentication().getName())
        }
    }
}
```

Here, `@EnableJpaAuditing` activates auditing support. ([Home][3])

When you later integrate with security (e.g. Spring Security), you can modify `getCurrentAuditor()` to return the authenticated user’s username or ID instead of a constant. ([Home][4])

---

## ✅ 5. Putting It All Together — What Happens

* On `persist` (first save of an entity), `createdBy` and `createdDate` are set.
* On `update`, `lastModifiedBy` and `lastModifiedDate` are updated.
* You don’t need to manually set audit fields from business code.
* All entities that extend `Auditable` automatically inherit audit behavior.

This gives you a consistent, reusable audit trail mechanism across your domain.

---

## 📝 6. Possible Variations / Enhancements

* Use `java.time.LocalDateTime` (or other date/time types) instead of `java.util.Date`.
* Use a custom “User” entity instead of `String` for `createdBy/lastModifiedBy` (i.e. `Auditable<User>`).
* Combine with soft-delete (e.g. `deletedAt`, `deletedBy`) if you want “logical deletion.”
* For full historical change tracking (all previous versions), consider using an audit/versioning tool like Hibernate Envers, rather than just audit-columns. ([Compile N Run][5])

[1]: https://www.knpcode.com/2020/03/spring-data-jpa-auditing-example.html?utm_source=chatgpt.com "Spring Data JPA Auditing Example | KnpCode"
[2]: https://codewithsibin.com/spring-data-jpa-auditing-a-complete-guide-with-setup-implementation-testing/?utm_source=chatgpt.com "Spring Data JPA Auditing: A Complete Guide with Setup, Implementation & Testing - Code With Sibin"
[3]: https://docs.spring.io/spring-data/data-jpa/docs/1.7.0.DATAJPA-580-SNAPSHOT/reference/html/auditing.html?utm_source=chatgpt.com "3. Auditing"
[4]: https://docs.spring.io/spring-data/relational/reference/data-commons/auditing.html?utm_source=chatgpt.com "Auditing :: Spring Data Relational"
[5]: https://www.compilenrun.com/docs/framework/spring/spring-data-access/spring-jpa-auditing/?utm_source=chatgpt.com "Spring JPA Auditing | Compile N Run"
