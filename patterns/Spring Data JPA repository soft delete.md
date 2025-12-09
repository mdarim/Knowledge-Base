Cool — here’s an example of a Spring Data JPA repository for your “enterprise-ready” base-entity setup (with soft-delete + audit + versioning), plus a custom “soft delete” method so your repository works nicely with soft-deletion semantics.

---

## 📄 Example Repository Interface

Assume your base entity class is named `BaseEntity`, and you have a concrete entity `Order extends BaseEntity`. A typical repository would look like this:

```java
package com.example.repository;

import com.example.domain.Order;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;
import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Optional;

@Repository
public interface OrderRepository extends JpaRepository<Order, Long> {

    // Soft-delete by id: set deleted = true (and optionally deletedBy / deletedAt via entity logic)
    @Modifying
    @Transactional
    @Query("UPDATE Order o SET o.deleted = true, o.deletedAt = CURRENT_TIMESTAMP, o.deletedBy = :deletedBy WHERE o.id = :id")
    void softDeleteById(Long id, String deletedBy);

    // Find all non-deleted
    @Query("SELECT o FROM Order o WHERE o.deleted = false")
    List<Order> findAllActive();

    // Find by id but only if not deleted
    @Query("SELECT o FROM Order o WHERE o.id = :id AND o.deleted = false")
    Optional<Order> findByIdAndNotDeleted(Long id);

    // (Optional) find including deleted, if needed
    @Query("SELECT o FROM Order o WHERE o.deleted = true")
    List<Order> findDeleted();
}
```

### Key Points

* We extend `JpaRepository<Order, Long>` — this gives us all the regular CRUD & paging methods. ([Home][1])
* We add a custom `softDeleteById(...)` method annotated with `@Modifying` + `@Transactional` — instead of hard delete, we mark the entity as deleted. This approach is common for soft-delete. ([Baeldung on Kotlin][2])
* We provide “active-only” finder methods like `findAllActive()` or `findByIdAndNotDeleted(...)` to return only non-deleted entities.

---

## ⚙️ Using a Custom Base Repository (Optional, for All Entities)

If you want to avoid repeating soft-delete methods in every repository, you can define a custom base repository interface + implementation, and have all your repositories extend it. Rough outline:

```java
// Base interface
@NoRepositoryBean
public interface SoftDeleteRepository<T, ID>
        extends JpaRepository<T, ID> {

    void softDeleteById(ID id, String deletedBy);
    List<T> findAllActive();
    Optional<T> findByIdAndNotDeleted(ID id);
}

// Implementation
public class SoftDeleteRepositoryImpl<T, ID>
        extends SimpleJpaRepository<T, ID>
        implements SoftDeleteRepository<T, ID> {

    private final EntityManager em;

    public SoftDeleteRepositoryImpl(
        JpaEntityInformation<T, ?> entityInformation,
        EntityManager em) {
        super(entityInformation, em);
        this.em = em;
    }

    @Override
    @Transactional
    public void softDeleteById(ID id, String deletedBy) {
        T entity = findById(id).orElseThrow(() -> new EntityNotFoundException());
        if (entity instanceof BaseEntity) {
            ((BaseEntity) entity).softDelete(deletedBy);
            save(entity);
        }
    }

    @Override
    public List<T> findAllActive() {
        String jpql = "SELECT e FROM " + getDomainClass().getSimpleName() +
                      " e WHERE e.deleted = false";
        return em.createQuery(jpql, getDomainClass()).getResultList();
    }

    @Override
    public Optional<T> findByIdAndNotDeleted(ID id) {
        String jpql = "SELECT e FROM " + getDomainClass().getSimpleName() +
                      " e WHERE e.id = :id AND e.deleted = false";
        T result = em.createQuery(jpql, getDomainClass())
                     .setParameter("id", id)
                     .getSingleResult();
        return Optional.ofNullable(result);
    }
}
```

Then in your configuration:

```java
@Configuration
@EnableJpaRepositories(
    basePackages = "com.example.repository",
    repositoryBaseClass = SoftDeleteRepositoryImpl.class
)
public class JpaConfig {
    // your config
}
```

This way all your repositories will inherit soft-delete + “active-only” query behavior by default. ([springframework.org][3])

[1]: https://docs.spring.io/spring-data/jpa/docs/3.0.2/reference/html?utm_source=chatgpt.com "Spring Data JPA - Reference Documentation"
[2]: https://www.baeldung.com/spring-jpa-soft-delete?utm_source=chatgpt.com "How to Implement a Soft Delete with Spring JPA | Baeldung"
[3]: https://www.springframework.org/spring-data/jpa/docs/2.2.9.RELEASE/reference/html/?utm_source=chatgpt.com "Spring Data JPA - Reference Documentation"
