Here’s a **starter skeleton** pattern you can use to build a **Spring Boot application with observability** and **deploy it on Docker**—including metrics, tracing, and logs. I’ll give you the basic files you need and explain how observability works in this context.

---

## 🧱 What “Observability Skeleton” Means

Observability typically consists of **three pillars**:

1. **Metrics** (e.g., Prometheus)
2. **Distributed Tracing** (e.g., Jaeger/Tempo with OpenTelemetry)
3. **Logs** (structured logging + aggregation)

Spring Boot (3+) supports **Actuator, Micrometer, and Micrometer Tracing** natively, and you can expose signals and send them to external backends with OpenTelemetry or Otto exporters. ([Home][1])

Also, there are ready-made example GitHub projects that show all signals together (metrics, logs, tracing) with Docker. ([GitHub][2])

---

## 📦 Project Structure

```
springboot-observability/
├─ src/main/java/com/example/app/
│   ├─ Application.java
│   └─ controller/
│       └─ HelloController.java
├─ Dockerfile
├─ application.yml
├─ docker-compose.yml
├─ pom.xml
└─ README.md
```

---

## 🧠 1. Spring Boot Application

### `Application.java`

```java
package com.example.app;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

---

### `HelloController.java`

```java
package com.example.app.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import io.micrometer.core.annotation.Timed;

@RestController
public class HelloController {

    @Timed("hello.requests") // metric example
    @GetMapping("/hello")
    public String hello() {
        return "Hello from Spring Boot!";
    }
}
```

---

## 🛠️ 2. Add Observability Dependencies (Maven)

In `pom.xml`:

```xml
<dependencies>
    <!-- Spring Boot Actuator -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>

    <!-- Micrometer Prometheus -->
    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-prometheus</artifactId>
    </dependency>

    <!-- Micrometer Tracing + OTEL Bridge -->
    <dependency>
      <groupId>io.micrometer</groupId>
      <artifactId>micrometer-tracing-bridge-otel</artifactId>
    </dependency>

    <!-- Optional: OpenTelemetry Exporter -->
    <dependency>
      <groupId>io.opentelemetry</groupId>
      <artifactId>opentelemetry-exporter-otlp</artifactId>
    </dependency>
</dependencies>
```

---

## ⚙️ 3. Application Configuration

### `application.yml`

```yaml
server:
  port: 8080

management:
  endpoints:
    web:
      exposure:
        include: health,info,prometheus
  metrics:
    export:
      prometheus:
        enabled: true

spring:
  main:
    allow-bean-definition-overriding: true
```

This config exposes:

* `/actuator/health`
* `/actuator/prometheus` (for metrics) ([GeeksforGeeks][3])

---

## 🐳 4. Dockerfile

```dockerfile
# Build stage
FROM maven:3.9.5-eclipse-temurin-17 as build
WORKDIR /workspace
COPY . .
RUN mvn clean package -DskipTests

# Runtime stage
FROM eclipse-temurin:17-jdk-slim
COPY --from=build /workspace/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/app.jar"]
```

Build with:

```bash
docker build -t springboot-observability .
```

---

## 🧩 5. docker-compose with Observability Backends

This `docker-compose.yml` skeleton runs Prometheus and Jaeger for metrics/tracing:

```yaml
version: "3.8"

services:
  app:
    image: springboot-observability
    ports:
      - "8080:8080"
    environment:
      OTEL_EXPORTER_OTLP_ENDPOINT: "http://otel-collector:4318/v1/traces"

  prometheus:
    image: prom/prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml

  jaeger:
    image: jaegertracing/all-in-one
    ports:
      - "16686:16686"

  otel-collector:
    image: otel/opentelemetry-collector-contrib
    ports:
      - "4318:4318"
      - "55681:55681"
    command: ["--config=/etc/otel-collector-config.yml"]
    volumes:
      - ./otel-collector-config.yml:/etc/otel-collector-config.yml
```

---

## 📊 6. Prometheus Config Example

`prometheus.yml`

```yaml
scrape_configs:
  - job_name: 'springboot'
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets: ['app:8080']
```

---

## 🚀 7. Run Everything

From the project root:

```bash
docker compose up
```

Access:

* App: [http://localhost:8080/hello](http://localhost:8080/hello)
* Prometheus UI: [http://localhost:9090](http://localhost:9090)
* Jaeger UI (traces): [http://localhost:16686](http://localhost:16686)

---

## 🔎 What This Setup Gives You

✅ Spring Boot Actuator exposes health & metrics
✅ Micrometer + Prometheus collects metrics
✅ OpenTelemetry (via micrometer tracing) exports traces
✅ Docker & Docker Compose run the full observability stack

This pattern matches similar skeleton/demo projects on GitHub. ([GitHub][2])

---

If you want, I can generate a **GitHub-ready template repo** (with working Docker Compose + Prometheus + Jaeger + OpenTelemetry) that you can clone and run directly—just let me know!

[1]: https://spring.io/blog/2022/10/12/observability-with-spring-boot-3/?utm_source=chatgpt.com "Observability with Spring Boot 3"
[2]: https://github.com/blueswen/spring-boot-observability?utm_source=chatgpt.com "GitHub - blueswen/spring-boot-observability: Observe Spring Boot app with three pillars of observability: Traces (Tempo), Metrics (Prometheus), Logs (Loki) on Grafana through OpenTelemetry and OpenMetrics."
[3]: https://www.geeksforgeeks.org/observability-with-spring-boot/?utm_source=chatgpt.com "Observability With Spring Boot - GeeksforGeeks"

Awesome — I found some **ready-made GitHub repositories** you can use as a **Spring Boot observability skeleton** with Docker, metrics, tracing, and dashboards. You can **clone and run these directly** or use them as starting points for your own project.

---

## 🚀 Ready-Made Observability Skeletons

### 📌 1) **blueswen/spring-boot-observability**

**Features:**
✔ Spring Boot app with all three observability pillars — **metrics (Prometheus)**, **traces (Tempo / OpenTelemetry)**, and **logs (Loki)**
✔ Full **Docker Compose** stack incl. Grafana dashboards
✔ Works with Grafana for unified tracing/metrics/log correlation ([GitHub][1])

🔗 GitHub: [https://github.com/blueswen/spring-boot-observability](https://github.com/blueswen/spring-boot-observability) ([GitHub][1])

---

### 📌 2) **EightAugusto/spring-boot-trace-metric-example**

**Features:**
✔ Spring Boot 3 observability demo
✔ Tracing with **Jaeger**
✔ Metrics with **Prometheus**
✔ Includes Docker support and scripts to start tools and the app ([GitHub][2])

🔗 GitHub: [https://github.com/EightAugusto/spring-boot-trace-metric-example](https://github.com/EightAugusto/spring-boot-trace-metric-example) ([GitHub][2])

---

### 📌 3) **ashenwgt/springboot-observability-grafana-prometheus-tempo-loki**

**Features:**
✔ Spring Boot observability setup with **Prometheus**, **Grafana**, **Tempo/Zipkin**, and **Loki**
✔ Includes observability Docker config files
✔ Good example for extending to microservices ([GitHub][3])

🔗 GitHub: [https://github.com/ashenwgt/springboot-observability-grafana-prometheus-tempo-loki](https://github.com/ashenwgt/springboot-observability-grafana-prometheus-tempo-loki) ([GitHub][3])

---

## 📌 Quick Start (Clone & Run)

Here’s a simple way to use one of these repos (e.g., the **blueswen** one):

```bash
# Clone the repo
git clone https://github.com/blueswen/spring-boot-observability.git
cd spring-boot-observability

# Build the app
./mvnw clean package

# Start observability stack + app
docker compose up --build
```

Then open:
📍 **Spring Boot**: [http://localhost:8080](http://localhost:8080)
📍 **Grafana**: [http://localhost:3000](http://localhost:3000)
📍 **Prometheus**: [http://localhost:9090](http://localhost:9090)
📍 **Loki / Tempo / Traces** via Grafana UI

---

## 📌 What’s Included in These Templates

| Feature                                    | Included?           |               |
|--------------------------------------------|---------------------|---------------|
| Spring Boot metrics (/actuator/prometheus) | ✅                   |               |
| Prometheus (metrics backend)               | ✅                   |               |
| Tracing (Jaeger / Tempo)                   | ✅                   |               |
| Logs (Loki)                                | (in some templates) |               |
| Grafana dashboards                         | ✅                   |               |
| Docker & Docker Compose                    | ✅                   | ([GitHub][1]) |

---

## 📌 Tips for Customization

✔ **Add OTLP exporter** to send traces to other backends (e.g., Honeycomb)
✔ **Configure log format** to include Trace/Span IDs for correlation
✔ **Add service-specific metrics** (custom counters, timers) via Micrometer
✔ **Add alerts** in Prometheus or Grafana

---

If you want, I can tailor **a repo with exactly the stack you want** (e.g., Jaeger vs Tempo, Loki vs Elastic, with GitHub Actions CI, Kubernetes manifests, etc.). Just tell me your preferences!

[1]: https://github.com/blueswen/spring-boot-observability?utm_source=chatgpt.com "GitHub - blueswen/spring-boot-observability: Observe Spring Boot app with three pillars of observability: Traces (Tempo), Metrics (Prometheus), Logs (Loki) on Grafana through OpenTelemetry and OpenMetrics."
[2]: https://github.com/EightAugusto/spring-boot-trace-metric-example?utm_source=chatgpt.com "GitHub - EightAugusto/spring-boot-trace-metric-example: Spring Boot 3 trace and metric example with Observability"
[3]: https://github.com/ashenwgt/springboot-observability-grafana-prometheus-tempo-loki?utm_source=chatgpt.com "GitHub - ashenwgt/springboot-observability-grafana-prometheus-tempo-loki: Observability Demo Project (using Springboot, Grafana, Prometheus, Tempo, Loki, Micrometer etc.)"
