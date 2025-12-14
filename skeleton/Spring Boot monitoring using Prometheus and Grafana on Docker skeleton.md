Here’s a **ready-to-use skeleton project** for monitoring a **Spring Boot** application using **Prometheus & Grafana** on **Docker** — including key files you need and how everything fits together.

---

## 🧱 Project Structure

```
spring-monitoring/
├── app/                          # Your Spring Boot app
│   ├── src/
│   ├── pom.xml
│   ├── Dockerfile
├── prometheus/
│   └── prometheus.yml
├── grafana/
│   └── provisioning/
│       ├── datasources/
│       │   └── datasource.yml
│       └── dashboards/
│           └── dashboard.yml
├── docker-compose.yml
```

---

## 1) Spring Boot App Setup

### 🧩 Add Dependencies

In `pom.xml` add the **Actuator** and **Prometheus Micrometer registry**:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-prometheus</artifactId>
    </dependency>
</dependencies>
```

Enable the endpoint in `application.yml`:

```yaml
management:
  endpoints:
    web:
      exposure:
        include: prometheus,health,info
  metrics:
    export:
      prometheus:
        enabled: true
```

This exposes metrics at:
➡️ `http://localhost:8080/actuator/prometheus` ([كاليكودر][1])

---

## 2) Dockerfile for Spring Boot

`app/Dockerfile`:

```dockerfile
FROM openjdk:17-jdk-slim
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

---

## 3) `prometheus.yml`

Put this under `prometheus/prometheus.yml`:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'spring-boot-app'
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets: ['host.docker.internal:8080']
```

This tells Prometheus to scrape the Spring Boot metrics endpoint. Using `host.docker.internal` lets the Prometheus container talk to your app running on the host network. ([Masterspringboot][2])

---

## 4) Grafana Data Source Provisioning (optional but helpful)

`grafana/provisioning/datasources/datasource.yml`:

```yaml
apiVersion: 1
datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true
```

---

## 5) Optional Dashboard Provisioning

Example minimal dashboard stub:
`grafana/provisioning/dashboards/dashboard.yml`

```yaml
apiVersion: 1
providers:
  - name: 'springboot'
    orgId: 1
    folder: ''
    type: file
    options:
      path: /var/lib/grafana/dashboards
```

You can export a real dashboard JSON from Grafana UI later. ([باريس][3])

---

## 6) Docker Compose

`docker-compose.yml`:

```yaml
version: '3.8'

services:
  spring-app:
    build: ./app
    ports:
      - "8080:8080"

  prometheus:
    image: prom/prometheus:latest
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    depends_on:
      - spring-app

  grafana:
    image: grafana/grafana:latest
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    volumes:
      - ./grafana/provisioning:/etc/grafana/provisioning
      - grafana-data:/var/lib/grafana
    ports:
      - "3000:3000"
    depends_on:
      - prometheus

volumes:
  grafana-data:
```

This stack does:

* **spring-app** — your app that exports metrics
* **prometheus** — scrapes `/actuator/prometheus` metrics
* **grafana** — visualizes metrics ✨

Start it with:

```bash
docker compose up --build
```

---

## 📊 How to Use

### ✅ Check Prometheus Target

Open:

```
http://localhost:9090/targets
```

If it’s up and shows your Spring Boot app as “UP”, scraping is working. ([Devapo Software House][4])

### ✅ Add Grafana Dashboard

1. Visit: `http://localhost:3000`
2. Login: `admin / admin` (or your set password)
3. Add **Prometheus** as data source (if not auto-provisioned)
4. Import dashboards:

    * e.g., JVM/Micrometer dashboards from Grafana Marketplace

---

## 🧠 Notes / Tips

✅ Docker networking:
In compose, services are on a default network. Use service names (`prometheus`, etc.) for internal access. ([Masterspringboot][2])

✅ Customize metrics:
You can add custom meters via `MeterRegistry`.

[1]: https://www.callicoder.com/spring-boot-actuator-metrics-monitoring-dashboard-prometheus-grafana/?utm_source=chatgpt.com "Spring Boot Actuator metrics monitoring with Prometheus and Grafana | CalliCoder"
[2]: https://www.masterspringboot.com/spring-boot-actuator/spring-boot-monitoring-with-grafana-and-prometheus/?utm_source=chatgpt.com "Spring Boot Monitoring with Grafana and Prometheus - Masterspringboot"
[3]: https://baris.io/blog/micrometer-spring-boot?utm_source=chatgpt.com "Grafana Dashboard for Spring Boot via Micrometer and Prometheus"
[4]: https://devapo.io/blog/technology/monitor-spring-boot-app-performance-with-apm-tools-prometheus-and-grafana/?utm_source=chatgpt.com "Monitor App performance with APM tools: Prometheus and Grafana"

Great — I can **generate a complete skeleton project** you can *clone and run immediately* for **Spring Boot + Prometheus + Grafana with Docker**.

Below is a *ready-to-run GitHub-style project template* (sourced and inspired by real examples like `cutePanda123/spring-boot-prometheus-demo` and others on GitHub) you can copy into your own directory. ([GitHub][1])

---

# 📦 Project Skeleton: **spring-boot-monitoring**

```
spring-boot-monitoring/
│
├── app/
│   ├── src/main/java/com/example/demo/
│   │   ├── DemoApplication.java
│   │   └── HelloController.java
│   ├── pom.xml
│   └── Dockerfile
│
├── prometheus/
│   └── prometheus.yml
│
├── grafana/
│   └── provisioning/
│       ├── datasources/
│       │   └── datasource.yml
│       └── dashboards/
│           └── dashboard.yml
│
└── docker-compose.yml
```

---

## 🧠 1) **Spring Boot Application – app**

### 🏷 `pom.xml`

```xml
<project ...>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>demo</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <packaging>jar</packaging>

  <properties>
    <java.version>17</java.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
      <groupId>io.micrometer</groupId>
      <artifactId>micrometer-registry-prometheus</artifactId>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

---

### 📌 `application.yml`

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
```

---

### 🧑‍💻 `DemoApplication.java`

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

---

### 👋 `HelloController.java`

```java
package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
    @GetMapping("/hello")
    public String hello() {
        return "Hello World!";
    }
}
```

---

### 📦 **Dockerfile**

```dockerfile
FROM openjdk:17-jdk-slim
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

---

## 📊 2) **Prometheus config – `prometheus/prometheus.yml`**

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: spring-boot
    metrics_path: /actuator/prometheus
    static_configs:
      - targets: ['spring-app:8080']
```

---

## 📈 3) **Grafana provisioning**

### 📓 `grafana/provisioning/datasources/datasource.yml`

```yaml
apiVersion: 1
datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true
```

---

### 📘 `grafana/provisioning/dashboards/dashboard.yml`

```yaml
apiVersion: 1
providers:
  - name: 'springboot'
    orgId: 1
    folder: ''
    type: file
    options:
      path: /var/lib/grafana/dashboards
```

> You can import a ready dashboard (JVM / Spring metrics) later into Grafana UI.

---

## 🚀 4) **Docker Compose – `docker-compose.yml`**

```yaml
version: '3.8'

services:
  spring-app:
    build: ./app
    ports:
      - "8080:8080"

  prometheus:
    image: prom/prometheus:latest
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    depends_on:
      - spring-app

  grafana:
    image: grafana/grafana:latest
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    volumes:
      - ./grafana/provisioning:/etc/grafana/provisioning
      - grafana-data:/var/lib/grafana
    ports:
      - "3000:3000"
    depends_on:
      - prometheus

volumes:
  grafana-data:
```

---

## 🟢 5) **Run It**

```sh
mvn clean package
docker compose up --build
```

* Spring: [http://localhost:8080/hello](http://localhost:8080/hello)
* Prometheus: [http://localhost:9090](http://localhost:9090)
* Grafana: [http://localhost:3000](http://localhost:3000) (admin/admin) ([GitHub][1])

---

## 🧾 Next Steps

✅ *Import dashboards*:
Use dashboards from Grafana Marketplace such as **Spring Boot Statistics** or **Micrometer JVM**. ([blog.boottechsolutions.com][2])

✅ *Explore metrics*:
Check Prometheus targets at:

```
http://localhost:9090/targets
```

[1]: https://github.com/cutePanda123/spring-boot-prometheus-demo?utm_source=chatgpt.com "GitHub - cutePanda123/spring-boot-prometheus-demo: This simple demo project can be used as an example for Prometheus and Grafana setups to monitor a Spring-Boot application."
[2]: https://blog.boottechsolutions.com/2024/07/29/monitoring-a-spring-boot-app-using-prometheus-and-grafana/?utm_source=chatgpt.com "Monitoring a Spring Boot app using Prometheus and Grafana - BootLabs TechBlog"
