Below is a **minimal skeleton** for a **Spring Boot 4.0.0** project acting as a **standalone OAuth2 client** that calls a protected **resource server**. It assumes you already have an **Authorization Server** that issues tokens (e.g., Keycloak, Okta, Spring Authorization Server, etc.).

Spring Boot 4 uses **Spring Security 6.x**, and OAuth2 support is provided via the `spring-boot-starter-oauth2-client` and `spring-boot-starter-oauth2-resource-server` starters. Spring Security’s OAuth2 support covers both **client** and **resource server** roles. ([Home][1])

---

## 📌 1) Project Setup

### **1.1 `pom.xml` (Maven)**

```xml
<project>
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.example</groupId>
  <artifactId>oauth2-client-skeleton</artifactId>
  <version>0.0.1-SNAPSHOT</version>

  <properties>
    <java.version>17</java.version>
    <spring.boot.version>4.0.0</spring.boot.version>
  </properties>

  <dependencies>
    <!-- Spring Boot Web -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- OAuth2 Client -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-oauth2-client</artifactId>
    </dependency>

    <!-- OAuth2 Resource Server (for validating incoming tokens if needed) -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
    </dependency>

    <!-- Optional: Spring Security -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-security</artifactId>
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

## 📌 2) Application Configuration

### **2.1 `application.yml`**

Replace placeholders (`YOUR_*`) with values from your Authorization Server:

```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          my-client:
            client-id: YOUR_CLIENT_ID
            client-secret: YOUR_CLIENT_SECRET
            authorization-grant-type: client_credentials
            scope: YOUR_SCOPE
        provider:
          my-auth-server:
            token-uri: https://auth-server.example.com/oauth/token
```

> This configures the client using **client_credentials** to request tokens from your Authorization Server. ([Home][1])

---

## 📌 3) Client Code

### **3.1 SecurityConfig.java**

```java
package com.example.oauth2clientskeleton;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth.anyRequest().authenticated())
            .oauth2Client(); // Enable OAuth2 Client support
        return http.build();
    }
}
```

---

### **3.2 Calling a Protected Resource with `WebClient`**

```java
package com.example.oauth2clientskeleton;

import org.springframework.security.oauth2.client.OAuth2AuthorizedClient;
import org.springframework.security.oauth2.client.OAuth2AuthorizedClientManager;
import org.springframework.security.oauth2.client.annotation.RegisteredOAuth2AuthorizedClient;
import org.springframework.security.oauth2.client.web.reactive.function.client.ServletOAuth2AuthorizedClientExchangeFilterFunction;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.reactive.function.client.WebClient;

@RestController
public class ProtectedResourceController {

    private final WebClient webClient;

    public ProtectedResourceController(OAuth2AuthorizedClientManager authorizedClientManager) {
        ServletOAuth2AuthorizedClientExchangeFilterFunction oauth2 =
            new ServletOAuth2AuthorizedClientExchangeFilterFunction(authorizedClientManager);

        this.webClient = WebClient.builder()
            .apply(oauth2.oauth2Configuration())
            .build();
    }

    @GetMapping("/call-resource")
    public String callProtectedResource(@RegisteredOAuth2AuthorizedClient("my-client") OAuth2AuthorizedClient client) {

        // Perform call to resource server
        String result = this.webClient
            .get()
            .uri("https://resource-server.example.com/protected")
            .attributes(ServletOAuth2AuthorizedClientExchangeFilterFunction.clientRegistrationId("my-client"))
            .retrieve()
            .bodyToMono(String.class)
            .block();

        return "Resource server returned: " + result;
    }
}
```

This uses WebClient and Spring’s OAuth2 support to inject a **Bearer token** into outgoing requests. See Spring Security guide. ([Home][1])

---

## 📌 4) (Optional) Resource Server Skeleton

If this same app also **protects APIs**, you can add:

```java
@Bean
public SecurityFilterChain resourceSecurity(HttpSecurity http) throws Exception {
    http
        .authorizeHttpRequests(auth -> auth.anyRequest().authenticated())
        .oauth2ResourceServer().jwt();
    return http.build();
}
```

and configure:

```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: https://auth-server.example.com
```

This makes the app a **resource server** validating JWTs. ([Home][2])

---

## 📌 5) Summary

* Use **`spring-boot-starter-oauth2-client`** to enable client functionality. ([Home][1])
* Provide `ClientRegistration` properties in `application.yml`.
* Use `SecurityFilterChain` with `.oauth2Client()` to enable OAuth2.
* Use **WebClient** with Spring’s OAuth2 support to call protected services.
* Optionally add **resource server** support with `.oauth2ResourceServer().jwt()` if this same app exposes protected APIs. ([Home][2])

---

If you want, I can also provide a **complete runnable GitHub-style project structure** (including tests and docker-compose with an auth server).

[1]: https://docs.spring.io/spring-security/reference/servlet/oauth2/?utm_source=chatgpt.com "OAuth2 :: Spring Security"
[2]: https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/index.html?utm_source=chatgpt.com "OAuth 2.0 Resource Server :: Spring Security"
