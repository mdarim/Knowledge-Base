Here’s a **clean skeleton example** showing how to run a **Spring Boot service with Zipkin tracing**, and how to **deploy everything (app + Zipkin) via Docker**.

---

## 🧩 1. Project Structure

```
spring-boot-zipkin/
├── src/
│   └── main/
│       ├── java/com/example/demo/
│       │   └── DemoApplication.java
│       └── resources/
│           └── application.yml
├── Dockerfile
├── docker-compose.yml
├── pom.xml
```

---

## 🏷 2. Spring Boot Setup (Maven)

**`pom.xml`**

Add Spring Boot Starter & Zipkin / Sleuth:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Sleuth for tracing + Zipkin exporter -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-sleuth</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-sleuth-zipkin</artifactId>
    </dependency>
</dependencies>

<properties>
    <java.version>17</java.version>
    <spring-cloud.version>2023.0.4</spring-cloud.version>
</properties>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

---

## 🧠 3. Main Application

**`DemoApplication.java`**

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

    @RestController
    public class HelloController {
        @GetMapping("/hello")
        public String hello() {
            return "Hello from Spring Boot!";
        }
    }
}
```

---

## ⚙️ 4. Config (Zipkin settings)

**`application.yml`**

```yaml
spring:
  application:
    name: demo-service

  sleuth:
    sampler:
      probability: 1.0       # Always sample traces

  zipkin:
    base-url: http://zipkin:9411
    sender:
      type: web
```

> `zipkin:9411` refers to Docker Compose service name (later).

---

## 🐳 5. Dockerize the App

**`Dockerfile`**

```dockerfile
FROM eclipse-temurin:17-jdk-alpine
WORKDIR /app
COPY target/demo-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/app/app.jar"]
```

**Build command:**

```bash
mvn clean package -DskipTests
docker build -t demo-service .
```

---

## 🛠 6. Docker Compose (App + Zipkin)

**`docker-compose.yml`**

```yaml
version: "3.9"

services:

  zipkin:
    image: openzipkin/zipkin
    ports:
      - "9411:9411"

  demo:
    depends_on:
      - zipkin
    build: .
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=default
```

---

## ▶️ 7. Running Everything

```bash
docker compose up --build
```

Then visit:

* **Application:** [http://localhost:8080/hello](http://localhost:8080/hello)
* **Zipkin UI:** [http://localhost:9411](http://localhost:9411)

Zipkin will show traces from every request, including `/hello`.

---

## 📌 Notes

### 🟢 Sampling

* `spring.sleuth.sampler.probability=1.0`: record everything (dev).
* In prod, use a lower value like `0.1`.

---

## 🚀 Optional: Call the Service to Generate Traces

You can simulate requests to populate Zipkin:

```bash
curl http://localhost:8080/hello
```

---

If you want, I can extend this to include **multiple Spring Boot microservices** with correlated tracing.

Great — here’s an **extended skeleton** showing how to build and **deploy multiple Spring Boot microservices** with **Zipkin-based distributed tracing** using **Spring Cloud Sleuth + Zipkin in Docker Compose**.

This guide walks through **two services** (Service A calls Service B) plus a **Zipkin server** collecting traces.

---

## 🧠 Overview of the Architecture

* **Service A (frontend)** calls **Service B (backend)** over HTTP
* Both include **Spring Cloud Sleuth** for trace/span propagation
* A **Zipkin server** collects and visualizes traces from both services
* All services are containerized and run in Docker Compose

Sleuth automatically instruments HTTP calls and sends trace data to Zipkin. ([cloud.spring.io][1])

---

## 🧩 Project Structure

```
microservices-zipkin/
├── service-a/
│   ├── src/main/java/...
│   ├── application.yml
│   ├── Dockerfile
│   └── pom.xml
├── service-b/
│   ├── src/main/java/...
│   ├── application.yml
│   ├── Dockerfile
│   └── pom.xml
├── docker-compose.yml
└── README.md
```

---

## 🔧 1. Common Maven Dependencies

Each service just needs **Spring Boot Web** + **Sleuth + Zipkin**:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Add Sleuth + Zipkin support -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-sleuth</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zipkin</artifactId>
    </dependency>
</dependencies>

<properties>
    <java.version>17</java.version>
    <spring-cloud.version>2023.0.4</spring-cloud.version>
</properties>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

> This config ensures each service can trace and propagate spans to Zipkin. ([cloud.spring.io][1])

---

## 🚀 2. Service A (Caller)

**`ServiceAApplication.java`**

```java
@SpringBootApplication
public class ServiceAApplication {
    public static void main(String[] args) {
        SpringApplication.run(ServiceAApplication.class, args);
    }
}

@RestController
class AController {

    private final RestTemplate restTemplate;

    public AController(RestTemplate rest) {
        this.restTemplate = rest;
    }

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }

    @GetMapping("/call-b")
    public String callB() {
        return restTemplate.getForObject("http://service-b:8081/hello", String.class);
    }
}
```

**`application.yml`**

```yaml
spring:
  application:
    name: service-a

  sleuth:
    sampler:
      probability: 1.0

  zipkin:
    base-url: http://zipkin:9411
```

**Dockerfile**

```dockerfile
FROM eclipse-temurin:17-jdk-alpine
WORKDIR /app
COPY target/service-a.jar app.jar
ENTRYPOINT ["java","-jar","/app/app.jar"]
```

---

## 🚀 3. Service B (Responder)

**`ServiceBApplication.java`**

```java
@SpringBootApplication
public class ServiceBApplication {
    public static void main(String[] args) {
        SpringApplication.run(ServiceBApplication.class, args);
    }
}

@RestController
class BController {
    @GetMapping("/hello")
    public String hello() {
        return "Hello from Service B!";
    }
}
```

**`application.yml`**

```yaml
spring:
  application:
    name: service-b

  sleuth:
    sampler:
      probability: 1.0

  zipkin:
    base-url: http://zipkin:9411
```

**Dockerfile**

```dockerfile
FROM eclipse-temurin:17-jdk-alpine
WORKDIR /app
COPY target/service-b.jar app.jar
ENTRYPOINT ["java","-jar","/app/app.jar"]
```

---

## 🐳 4. Docker Compose (Service Network)

**`docker-compose.yml`**

```yaml
version: "3.9"

services:
  zipkin:
    image: openzipkin/zipkin
    container_name: zipkin
    ports:
      - "9411:9411"

  service-a:
    build:
      context: ./service-a
    container_name: service-a
    ports:
      - "8080:8080"
    depends_on:
      - zipkin
      - service-b

  service-b:
    build:
      context: ./service-b
    container_name: service-b
    ports:
      - "8081:8081"
    depends_on:
      - zipkin
```

---

## ▶️ 5. Build and Run

From project root:

```bash
# Build jars first
cd service-a && mvn clean package -DskipTests
cd ../service-b && mvn clean package -DskipTests

# Then bring up everything
docker compose up --build
```

You should now be able to try:

🔹 [http://localhost:8080/call-b](http://localhost:8080/call-b) → calls Service B
🔹 [http://localhost:9411](http://localhost:9411) → Zipkin UI

In Zipkin, “Find Traces” will show a trace that spans **Service A → Service B**. ([javainuse.com][2])

---

## 🧪 What Happens Under the Hood

* Sleuth assigns each incoming request a **traceId + spanId**
* When Service A calls Service B via RestTemplate, Sleuth propagates that context
* Zipkin receives spans and compiles a **trace timeline**
* You can **visualize where time was spent** in each service call

This is how distributed tracing helps debug latency / errors across services. ([HowToDoInJava][3])

---

## 🛠 Tips & Enhancements

📌 **Sampling**
Keep `probability: 1.0` for dev only — in production, lower it. ([cloud.spring.io][1])

📌 **Use Feign / WebClient**
RestTemplate works but Feign (OpenFeign) simplifies inter-service calls and still gets traced.

📌 **Security / Gateways**
If using API Gateway (e.g., Spring Cloud Gateway), Sleuth can propagate trace IDs through it.

📌 **Service Discovery**
If you add Eureka, you can configure Zipkin to resolve service addresses via registry. ([HowToDoInJava][3])

---

If you want, I can show how to **add OpenFeign** or integrate an **API Gateway** with tracing as well!

[1]: https://cloud.spring.io/spring-cloud-sleuth/reference/html/?utm_source=chatgpt.com "Spring Cloud Sleuth"
[2]: https://www.javainuse.com/spring/cloud-sleuth?utm_source=chatgpt.com "Spring Cloud Tutorial - Distributed Log Tracing using Sleuth and Zipkin Example | JavaInUse"
[3]: https://howtodoinjava.com/spring-cloud/spring-cloud-zipkin-sleuth-tutorial/?utm_source=chatgpt.com "Spring Cloud - Zipkin and Sleuth"
