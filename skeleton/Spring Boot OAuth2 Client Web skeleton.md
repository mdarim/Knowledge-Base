Here’s a **minimal Spring Boot 4.0.0** OAuth2 Client skeleton using **Gradle** and a **Docker setup**, including key code snippets and configuration you can use as a starter project.

---

## 🧱 1. Project Setup (Gradle)

### **build.gradle.kts**

Use Kotlin DSL if you prefer; adjust to Groovy DSL accordingly.

```kotlin
plugins {
    id("org.springframework.boot") version "4.0.0"
    id("io.spring.dependency-management") version "1.1.2"
    kotlin("jvm") version "1.9.21"
    kotlin("plugin.spring") version "1.9.21"
}

group = "com.example"
version = "0.0.1-SNAPSHOT"
java.sourceCompatibility = JavaVersion.VERSION_17

repositories {
    mavenCentral()
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web")
    implementation("org.springframework.boot:spring-boot-starter-oauth2-client")
    implementation("org.springframework.boot:spring-boot-starter-security")
    implementation("org.springframework.boot:spring-boot-starter-oauth2-resource-server")
    testImplementation("org.springframework.boot:spring-boot-starter-test")
}
```

✔️ This adds **OAuth2 Client** support via `spring-boot-starter-oauth2-client` and core web & security starters.([Home][1])

---

## 🔐 2. Configure OAuth2 Client

### **application.yml**

Replace with your provider’s details (Google / Okta / Keycloak, etc.):

```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          my-client:
            client-id: YOUR_CLIENT_ID
            client-secret: YOUR_CLIENT_SECRET
            authorization-grant-type: authorization_code
            redirect-uri: "{baseUrl}/login/oauth2/code/{registrationId}"
            scope: openid,profile,email
        provider:
          my-provider:
            issuer-uri: https://your-authorization-server.com
```

✔️ Spring will auto-configure the OAuth2 Client and wiring.([Home][2])

---

## 🔒 3. Security Configuration

Create a basic security config enabling OAuth2 login and client features:

```java
package com.example.demo;

import org.springframework.context.annotation.Bean;
import org.springframework.security.config.Customizer;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.web.SecurityFilterChain;

public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .anyRequest().authenticated()
            )
            .oauth2Login(Customizer.withDefaults())   // enable OAuth2 login
            .oauth2Client(Customizer.withDefaults()); // enable OAuth2 client
        return http.build();
    }
}
```

✔️ This secures all endpoints and enables Spring’s OAuth2 client & login support.([Home][2])

---

## 📦 4. Controller Example

Just to test authentication:

```java
package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import java.security.Principal;

@RestController
public class UserController {

    @GetMapping("/user")
    public Principal user(Principal principal) {
        return principal;
    }
}
```

---

## 🐳 5. Docker Setup

### **Dockerfile**

Simple container for the Spring Boot app:

```dockerfile
FROM eclipse-temurin:17-jdk-alpine

ARG JAR_FILE=build/libs/*.jar

COPY ${JAR_FILE} app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","/app.jar"]
```

📌 This builds on OpenJDK 17 Alpine and runs your Spring Boot jar.([Home][3])

---

## 🛠 6. Build & Run

### Build the Jar

```bash
./gradlew clean build
```

### Build the Docker image

```bash
docker build -t spring-oauth2-client .
```

### Run the container

```bash
docker run -p 8080:8080 spring-oauth2-client
```

---

## 🚀 Alternative: Cloud Native Buildpack (Optional)

Spring Boot supports `bootBuildImage` with Gradle for OCI images:

```bash
./gradlew bootBuildImage
```

✔️ This produces an optimized container image using Cloud Native Buildpacks.([Home][4])

---

## 🧠 Notes

* Spring Boot 4 uses Spring Security 6.x OAuth2 client features, and the configuration model shown above follows the modern OAuth2 Client support.([Home][5])
* For more UI-centric OAuth flows (login redirect, social login), see the **Spring guide on Spring Boot & OAuth2**.([Home][1])
* You’ll need a real OAuth2 provider (authorization server) to get client ID/secret — e.g., **Keycloak**, **Okta**, or **Azure AD**. Community repos like **Keycloak + Spring OAuth2 Docker examples** can help you set that up quickly.([github.com][6])

---

If you want, I can also provide a **docker-compose setup including an Authorization Server (like Keycloak)** to test this fully locally.

[1]: https://spring.io/guides/tutorials/spring-boot-oauth2?utm_source=chatgpt.com "Getting Started | Spring Boot and OAuth2"
[2]: https://docs.spring.io/spring-security/reference/servlet/oauth2/?utm_source=chatgpt.com "OAuth2 :: Spring Security"
[3]: https://spring.io/guides/gs/spring-boot-docker?utm_source=chatgpt.com "Getting Started | Spring Boot with Docker"
[4]: https://docs.spring.io/spring-boot/gradle-plugin/packaging-oci-image.html?utm_source=chatgpt.com "Packaging OCI Images :: Spring Boot"
[5]: https://docs.spring.io/spring-security/reference/servlet/oauth2/index.html?utm_source=chatgpt.com "OAuth2 :: Spring Security"
[6]: https://github.com/mateuszwlosek/oauth2-example?utm_source=chatgpt.com "GitHub - mateuszwlosek/oauth2-example: Oauth2 + Spring Boot + Keycloak + Client + Resource Server"
