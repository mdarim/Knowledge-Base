Good — below is a **full minimal example** of how you can implement **CQRS** in a Spring Boot application using **ApplicationEventPublisher** to notify the query side when the write side changes — i.e. a write model ⇒ event ⇒ read model flow. This uses only Spring (no heavy CQRS framework). I draw from patterns described in a few sources. ([Java && More][1])

---

## 📁 Project structure (simplified)

```
src/main/java/
  command/
    ProductCommandService.java
    commands/  (optional, command DTOs)
  domain/
    Product.java    (JPA entity)
  events/
    ProductCreatedEvent.java
    ProductUpdatedEvent.java
  query/
    ProductView.java    (the read model / projection entity)
    ProductViewRepository.java  (Spring Data repository)
    ProductEventListener.java
    ProductQueryService.java
  web/
    ProductController.java  (REST endpoints for commands & queries)
```

---

## 🧠 Step-by-step code example

### 1. Define domain/write-model entity (for writes)

```java
// domain/Product.java
@Entity
@Table(name = "product")
public class Product {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private BigDecimal price;
    private Integer stock;

    // constructors, getters/setters
}
```

### 2. Define event classes (simple POJOs — no need to extend `ApplicationEvent`)

```java
// events/ProductCreatedEvent.java
public class ProductCreatedEvent {
    private final Long productId;
    private final String name;
    private final BigDecimal price;
    private final Integer stock;

    public ProductCreatedEvent(Long productId, String name, BigDecimal price, Integer stock) {
        this.productId = productId;
        this.name = name;
        this.price = price;
        this.stock = stock;
    }

    // getters ...
}

// events/ProductUpdatedEvent.java
public class ProductUpdatedEvent {
    private final Long productId;
    private final String name;
    private final BigDecimal price;
    private final Integer stock;

    public ProductUpdatedEvent(Long productId, String name, BigDecimal price, Integer stock) {
        this.productId = productId;
        this.name = name;
        this.price = price;
        this.stock = stock;
    }

    // getters ...
}
```

Note: Since Spring 4.2, you don’t need to extend `ApplicationEvent` — Spring will wrap POJOs in `PayloadApplicationEvent`. ([reflectoring.io][2])

### 3. Command service: handle writes and publish events via ApplicationEventPublisher

```java
// command/ProductCommandService.java

@Service
public class ProductCommandService {
    private final ProductRepository productRepository;
    private final ApplicationEventPublisher eventPublisher;

    public ProductCommandService(ProductRepository productRepository,
                                 ApplicationEventPublisher eventPublisher) {
        this.productRepository = productRepository;
        this.eventPublisher = eventPublisher;
    }

    @Transactional
    public Long createProduct(String name, BigDecimal price, Integer stock) {
        Product product = new Product();
        product.setName(name);
        product.setPrice(price);
        product.setStock(stock);
        Product saved = productRepository.save(product);

        // publish event
        ProductCreatedEvent event = new ProductCreatedEvent(
            saved.getId(), saved.getName(), saved.getPrice(), saved.getStock());
        eventPublisher.publishEvent(event);

        return saved.getId();
    }

    @Transactional
    public void updateProduct(Long id, String name, BigDecimal price, Integer stock) {
        Product product = productRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("Not found"));
        product.setName(name);
        product.setPrice(price);
        product.setStock(stock);
        Product saved = productRepository.save(product);

        // publish event
        ProductUpdatedEvent event = new ProductUpdatedEvent(
            saved.getId(), saved.getName(), saved.getPrice(), saved.getStock());
        eventPublisher.publishEvent(event);
    }
}
```

This is the “write side” (commands). Once you persist changes via repository, you publish an event. ([Java && More][1])

### 4. Read-side / Query-model (projection): handle events and maintain a read-optimized view

```java
// query/ProductView.java
@Entity
@Table(name = "product_view")
public class ProductView {
    @Id
    private Long id;

    private String name;
    private BigDecimal price;
    private Integer stock;

    // constructors, getters/setters
}

// query/ProductViewRepository.java
public interface ProductViewRepository extends JpaRepository<ProductView, Long> { }

// query/ProductEventListener.java
@Service
public class ProductEventListener {

    private final ProductViewRepository viewRepository;

    public ProductEventListener(ProductViewRepository viewRepository) {
        this.viewRepository = viewRepository;
    }

    @EventListener
    @TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
    public void on(ProductCreatedEvent event) {
        ProductView view = new ProductView();
        view.setId(event.getProductId());
        view.setName(event.getName());
        view.setPrice(event.getPrice());
        view.setStock(event.getStock());
        viewRepository.save(view);
    }

    @EventListener
    @TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
    public void on(ProductUpdatedEvent event) {
        ProductView view = viewRepository.findById(event.getProductId())
            .orElse(new ProductView());   // or throw
        view.setName(event.getName());
        view.setPrice(event.getPrice());
        view.setStock(event.getStock());
        viewRepository.save(view);
    }
}
```

* The listener methods respond to events and update (or create) the read-model projection (`ProductView`).
* Using `@TransactionalEventListener(phase = AFTER_COMMIT)` ensures that the event is handled only after the write transaction commits — avoiding inconsistencies if the transaction rolls back. ([Baeldung on Kotlin][3])

### 5. Query service / Controller for read side

```java
// query/ProductQueryService.java
@Service
public class ProductQueryService {
    private final ProductViewRepository viewRepository;
    public ProductQueryService(ProductViewRepository viewRepository) {
        this.viewRepository = viewRepository;
    }

    public Optional<ProductView> getById(Long id) {
        return viewRepository.findById(id);
    }

    public List<ProductView> getAll() {
        return viewRepository.findAll();
    }
}
```

```java
// web/ProductController.java
@RestController
@RequestMapping("/products")
public class ProductController {
    private final ProductCommandService commandService;
    private final ProductQueryService queryService;

    public ProductController(ProductCommandService commandService,
                             ProductQueryService queryService) {
        this.commandService = commandService;
        this.queryService = queryService;
    }

    @PostMapping
    public ResponseEntity<Long> create(@RequestBody CreateProductDto dto) {
        Long id = commandService.createProduct(dto.getName(), dto.getPrice(), dto.getStock());
        return ResponseEntity.ok(id);
    }

    @PutMapping("/{id}")
    public ResponseEntity<Void> update(@PathVariable Long id,
                                       @RequestBody UpdateProductDto dto) {
        commandService.updateProduct(id, dto.getName(), dto.getPrice(), dto.getStock());
        return ResponseEntity.ok().build();
    }

    @GetMapping("/{id}")
    public ResponseEntity<ProductView> get(@PathVariable Long id) {
        return queryService.getById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }

    @GetMapping
    public List<ProductView> list() {
        return queryService.getAll();
    }
}
```

With this separation:

* Writes (commands) go through `ProductCommandService` and publish events.
* Reads (queries) are served from the read-optimized `ProductView` table.

Thus you have a basic CQRS separation in a single Spring Boot application without extra frameworks.

---

## 🔎 Why this works & what to watch out for

* Spring’s application event mechanism is flexible: you can publish any object as an event (not just subclasses of `ApplicationEvent`). ([reflectoring.io][2])
* Using `@EventListener` (or `@TransactionalEventListener`) decouples the command and read sides: the command service doesn’t need to know about the read model. ([backendhance.com][4])
* Because the update of the read model happens via events — and can be asynchronous (or delayed) — you accept **eventual consistency**: immediately after a command, the read model might not yet reflect the changes, until the event is processed. This is a typical trade-off in CQRS. ([Springfuse][5])

---

## 🧪 When this simple approach is enough — and when you might need more

### ✅ Good for:

* Monolithic or modular applications where you just want simple CQRS without heavy frameworks.
* Scenarios where eventual consistency is acceptable.
* When you want to decouple write and read concerns using Spring built-in mechanisms (no extra dependencies).

### ⚠️ Limitations / When to consider more advanced approach:

* If you need **durable events**, replication across multiple services, replayability, or event sourcing — the built-in Spring event bus is in-memory per application instance, not a durable event store.
* For distributed microservices — cross-service communication — you likely want a **message broker / event bus** (like Kafka, RabbitMQ), not just Spring’s in-memory events.
* For scalability, auditing, or rebuilding read models — you may need a full event store + projection pipeline, or dedicated CQRS framework (or combine CQRS with event sourcing). Many guides recommend such when system grows. ([Java Code Geeks][6])

[1]: https://gaetanopiazzolla.github.io/java/design-patterns/springboot/2025/03/17/cqrs.html?utm_source=chatgpt.com "Implementing CQRS with Spring Modulith | Java && More"
[2]: https://reflectoring.io/spring-boot-application-events-explained/?utm_source=chatgpt.com "Spring Boot Application Events Explained"
[3]: https://www.baeldung.com/spring-data-ddd?utm_source=chatgpt.com "DDD aggregates and @DomainEvents | Baeldung"
[4]: https://backendhance.com/en/blog/2023/spring-events/?utm_source=chatgpt.com "Spring Events: Decouple Your Application with @EventListener and ApplicationEvents | Backendhance"
[5]: https://www.springfuse.com/implementing-cqrs-spring-boot-applications/?utm_source=chatgpt.com "Implementing CQRS in Spring Boot Applications"
[6]: https://www.javacodegeeks.com/2025/06/implementing-cqrs-and-event-sourcing-with-axon-framework-in-spring.html?utm_source=chatgpt.com "Implementing CQRS and Event Sourcing with Axon Framework in Spring - Java Code Geeks"
