Spring Boot leverages several well-known **design patterns** to promote clean, maintainable, and scalable code. These patterns are seamlessly integrated into its architecture, enabling developers to build robust applications efficiently. Below is an overview of the key design patterns utilized by Spring Boot:

---

### **1. Dependency Injection (DI) - Inversion of Control (IoC)**
- **Pattern**: Dependency Injection is a subtype of the Inversion of Control pattern.
- **How It's Used**:
    - Spring Boot uses DI to manage object creation and dependencies, allowing loose coupling between components.
    - The `@Autowired`, `@Bean`, and `@Component` annotations allow Spring to manage object lifecycles.
- **Example**:
  ```java
  @Service
  public class UserService {
      private final UserRepository userRepository;

      @Autowired
      public UserService(UserRepository userRepository) {
          this.userRepository = userRepository;
      }
  }
  ```
  The `UserService` does not create the `UserRepository` instance itself; Spring provides it.

---

### **2. Singleton Pattern**
- **Pattern**: Ensures that a class has only one instance throughout the application.
- **How It's Used**:
    - Spring's default scope for beans is `singleton`. This means one instance of a bean is created and shared across the application context.
- **Example**:
  ```java
  @Component
  public class MySingletonBean {
      // Single instance managed by Spring
  }
  ```

---

### **3. Factory Method Pattern**
- **Pattern**: Defines an interface for creating objects, but lets subclasses alter the type of objects that will be created.
- **How It's Used**:
    - Spring Boot uses factory methods to create beans.
    - The `@Bean` annotation is a common example where factory methods define how beans are created.
- **Example**:
  ```java
  @Configuration
  public class AppConfig {
      @Bean
      public MyService myService() {
          return new MyServiceImpl();
      }
  }
  ```

---

### **4. Proxy Pattern**
- **Pattern**: Provides a placeholder or proxy to control access to another object.
- **How It's Used**:
    - Spring uses proxies for implementing AOP (Aspect-Oriented Programming) features such as method interception (e.g., for transaction management, logging, etc.).
    - Examples include `@Transactional` and `@Caching`.
- **Example**:
  ```java
  @Transactional
  public void performTransaction() {
      // Transaction logic managed by Spring's proxy
  }
  ```

---

### **5. Template Method Pattern**
- **Pattern**: Defines the skeleton of an algorithm in a method, deferring some steps to subclasses.
- **How It's Used**:
    - Spring provides templates like `JdbcTemplate`, `RestTemplate`, and `JpaTemplate` to handle boilerplate code for database operations, REST API calls, etc.
- **Example**:
  ```java
  @Autowired
  private JdbcTemplate jdbcTemplate;

  public void saveUser(User user) {
      jdbcTemplate.update("INSERT INTO users (name, age) VALUES (?, ?)", user.getName(), user.getAge());
  }
  ```

---

### **6. Observer Pattern**
- **Pattern**: Defines a one-to-many dependency between objects, so when one object changes state, all its dependents are notified.
- **How It's Used**:
    - Event handling in Spring Boot leverages this pattern with `ApplicationEvent` and `ApplicationListener`.
- **Example**:
  ```java
  @Component
  public class MyEventListener implements ApplicationListener<MyCustomEvent> {
      @Override
      public void onApplicationEvent(MyCustomEvent event) {
          System.out.println("Event received: " + event.getMessage());
      }
  }
  ```

---

### **7. MVC (Model-View-Controller) Pattern**
- **Pattern**: Separates an application into three interconnected components: Model, View, and Controller.
- **How It's Used**:
    - Spring Boot's web layer implements MVC using `@Controller` or `@RestController` for controllers, and `Model` to pass data to the view layer (e.g., Thymeleaf or JSP).
- **Example**:
  ```java
  @RestController
  @RequestMapping("/users")
  public class UserController {
      @GetMapping("/{id}")
      public ResponseEntity<User> getUserById(@PathVariable Long id) {
          return ResponseEntity.ok(userService.findById(id));
      }
  }
  ```

---

### **8. Strategy Pattern**
- **Pattern**: Defines a family of algorithms, encapsulates each one, and makes them interchangeable.
- **How It's Used**:
    - Spring Security uses the Strategy Pattern for authentication and authorization mechanisms (e.g., `AuthenticationProvider` and `AccessDecisionManager`).
- **Example**:
  ```java
  @Configuration
  public class SecurityConfig extends WebSecurityConfigurerAdapter {
      @Override
      protected void configure(AuthenticationManagerBuilder auth) throws Exception {
          auth.authenticationProvider(customAuthenticationProvider());
      }
  }
  ```

---

### **9. Builder Pattern**
- **Pattern**: Constructs complex objects step by step.
- **How It's Used**:
    - Used in creating immutable objects, such as `ResponseEntity` in Spring Boot or configuring objects like `HttpClient`.
- **Example**:
  ```java
  ResponseEntity<String> response = ResponseEntity.status(HttpStatus.OK)
                                                  .body("Success");
  ```

---

### **10. Chain of Responsibility Pattern**
- **Pattern**: Passes requests along a chain of handlers.
- **How It's Used**:
    - Spring's filter chain for web requests (e.g., `OncePerRequestFilter` or `FilterChain` in Spring Security) implements this pattern.
- **Example**:
  ```java
  public class CustomFilter extends OncePerRequestFilter {
      @Override
      protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
          // Custom pre-processing
          filterChain.doFilter(request, response);
          // Custom post-processing
      }
  }
  ```

---

### Summary of design patterns
Spring Boot is built upon many well-established design patterns, which contribute to its flexibility, scalability, and ease of use. Understanding these patterns helps developers write cleaner, maintainable code and make better use of Spring Boot's features.

Here's a concise summary of key design patterns and how they are utilized, particularly in frameworks like Spring Boot:

---

#### **Creational Patterns**
1. **Singleton**:
  - Ensures a class has only one instance throughout the application.
  - Used in Spring's default bean scope (`@Component`, `@Service`).

2. **Factory Method**:
  - Creates objects without specifying the exact class.
  - Used with `@Bean` to define how beans are instantiated.

---

#### **Structural Patterns**
3. **Proxy**:
  - Provides a substitute to control access to an object.
  - Used in Spring AOP for method interception (e.g., `@Transactional`, `@Caching`).

4. **Adapter**:
  - Converts one interface into another.
  - Seen in integrating external APIs or libraries.

---

#### **Behavioral Patterns**
5. **Dependency Injection (IoC)**:
  - Injects dependencies rather than instantiating them manually.
  - Core of Spring Framework (`@Autowired`, constructor injection).

6. **Observer**:
  - Notifies multiple objects about state changes.
  - Used in Spring Events (`ApplicationEvent` and `ApplicationListener`).

7. **Template Method**:
  - Defines the skeleton of an operation, allowing subclasses to customize steps.
  - Implemented in `JdbcTemplate`, `RestTemplate`.

8. **Strategy**:
  - Encapsulates interchangeable algorithms.
  - Used in Spring Security for authentication and authorization (`AuthenticationProvider`).

9. **Chain of Responsibility**:
  - Passes a request along a chain of handlers.
  - Used in Spring's filter chains (`OncePerRequestFilter`).

---

#### **Architectural Patterns**
10. **Model-View-Controller (MVC)**:
  - Separates concerns for better modularity.
  - Used in Spring Web (`@Controller`, `@RestController`, `Model`).

11. **Event-Driven Architecture**:
  - Reacts to events asynchronously.
  - Powered by Spring Events and messaging systems like Kafka.

---

#### Why Design Patterns Matter
- Promote **code reuse** and **scalability**.
- Enable **cleaner, maintainable code**.
- Facilitate the use of well-tested practices for solving common problems.

Mastering these patterns allows developers to better leverage Spring Boot's capabilities and design robust, enterprise-level applications.
