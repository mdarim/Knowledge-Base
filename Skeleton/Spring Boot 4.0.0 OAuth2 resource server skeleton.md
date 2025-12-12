Below is a **Spring Boot 4.0.0 OAuth2 Resource Server skeleton** with **Gradle build** and **Docker setup** to get you started quickly.

---

## 🚀 1. Project Structure

```
spring-boot-oauth2-resource-server/
├── src/main/java/com/example/resourceserver/
│   ├── ResourceServerApplication.java
│   ├── config/SecurityConfig.java
├── src/main/resources/
│   └── application.yml
├── build.gradle
├── settings.gradle
├── Dockerfile
└── docker-compose.yml
```

---

## 🧱 2. Gradle Setup

**build.gradle** (Groovy DSL)

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '4.0.0'
    id 'io.spring.dependency-management' version '1.1.2'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '17'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-oauth2-resource-server'
    implementation 'org.springframework.boot:spring-boot-starter-security'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.withType(Test) {
    useJUnitPlatform()
}
```

* **spring-boot-starter-oauth2-resource-server** includes Spring Security resource server support. ([Home][1])

---

## ⚙️ 3. Main Application

**ResourceServerApplication.java**

```java
package com.example.resourceserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class ResourceServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ResourceServerApplication.class, args);
    }
}
```

---

## 🔐 4. Security Configuration

Spring Boot 4 uses the new `SecurityFilterChain` approach instead of `WebSecurityConfigurerAdapter`.

**SecurityConfig.java**

```java
package com.example.resourceserver.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.Customizer;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .anyRequest().authenticated()
            )
            .oauth2ResourceServer(oauth2 -> oauth2
                .jwt(Customizer.withDefaults())
            );
        return http.build();
    }
}
```

This config ensures all endpoints require a valid OAuth2 JWT token. ([Home][2])

---

## 📄 5. application.yml Example

**application.yml**

```yaml
server:
  port: 8080

spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: https://example-auth-server.com/realms/demo
```

Replace `issuer-uri` with your authorization server’s URL (e.g., Keycloak, Okta). ([Home][1])

---

## 🐳 6. Docker Setup

### Dockerfile

A simple multi-stage build:

```dockerfile
# Build stage
FROM gradle:8-jdk17 AS build
WORKDIR /workspace
COPY . .
RUN gradle clean bootJar --no-daemon

# Runtime stage
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY --from=build /workspace/build/libs/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/app/app.jar"]
```

This builds your Spring Boot app with Gradle and produces a runnable container image. ([jre-training.com][3])

---

### docker-compose.yml

Add your Resource Server and an authorization server like Keycloak (optional):

```yaml
version: '3.8'

services:
  resource-server:
    build: .
    ports:
      - "8080:8080"
    environment:
      - SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_ISSUER_URI=http://auth-server:8081/realms/demo
    depends_on:
      - auth-server

  auth-server:
    image: quay.io/keycloak/keycloak:26.1.3
    command: ["start-dev", "--http-port=8081"]
    environment:
      KC_DB: h2
      KC_REALM: demo
      KC_ADMIN: admin
      KC_ADMIN_PASSWORD: admin
    ports:
      - "8081:8081"
```

* The `auth-server` service is an example Keycloak OAuth2 provider for JWT token issuance. ([NashTech Blog][4])

---

## 🔎 7. Test Endpoint

Add a simple REST controller to test protection:

```java
package com.example.resourceserver;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/api/hello")
    public String hello() {
        return "Hello, secured world!";
    }
}
```

Requests to `/api/hello` now require a valid Bearer JWT.

---

## 📌 Summary

| Component              | Purpose                      |
|------------------------|------------------------------|
| Gradle                 | Build tool                   |
| Spring Boot 4          | App framework                |
| OAuth2 Resource Server | Secures APIs with JWT tokens |
| Dockerfile             | Containerize the app         |
| docker-compose         | Spin up app and auth server  |

---

## 🧠 Notes

* Spring Boot auto-configures OAuth2 resource server support when `spring-security-oauth2-resource-server` is on the classpath. ([Home][1])
* You must point `issuer-uri` (or `jwk-set-uri`) to a valid OAuth2 authorization server for JWT validation. ([Home][1])

---

If you want an **Authorization Server skeleton too (Keycloak or Spring Authorization Server)** let me know and I can extend this with token issuance flows and sample tokens!

[1]: https://docs.spring.io/spring-boot/reference/web/spring-security.html?utm_source=chatgpt.com "Spring Security :: Spring Boot"
[2]: https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/index.html?utm_source=chatgpt.com "OAuth 2.0 Resource Server :: Spring Security"
[3]: https://www.jre-training.com/Microservices/materials/spring_boot_tutorial.pdf?utm_source=chatgpt.com "Spring Boot"
[4]: https://blog.nashtechglobal.com/multi-tenant-spring-boot-oauth2-resource-server-with-github-and-keycloak/?utm_source=chatgpt.com "Multi-tenant Spring Boot OAuth2 Resource Server with ..."
