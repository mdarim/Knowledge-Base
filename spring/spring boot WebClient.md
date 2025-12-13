Below are **Spring Boot WebClient code examples** with **simple HTTP calls** and **OAuth2-protected API calls**. These are ready to use in a typical Spring Boot application.

---

## 🧪 1) Simple WebClient (No Auth)

### ✅ **Configuration (Bean)**

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.reactive.function.client.WebClient;

@Configuration
public class WebClientConfig {

    @Bean
    public WebClient webClient(WebClient.Builder builder) {
        return builder
                .baseUrl("https://api.example.com") // optional base URL
                .defaultHeader("Accept", "application/json")
                .build();
    }
}
```

### ✅ **Usage Example**

```java
import org.springframework.stereotype.Service;
import org.springframework.web.reactive.function.client.WebClient;
import reactor.core.publisher.Mono;

@Service
public class ApiService {

    private final WebClient webClient;

    public ApiService(WebClient webClient) {
        this.webClient = webClient;
    }

    public Mono<String> getSimpleData() {
        return webClient.get()
                .uri("/data")
                .retrieve()
                .bodyToMono(String.class);
    }
}
```

> You can also `.bodyToFlux(...)`, `.exchangeToMono(...)`, etc., based on needs.

---

## 🔐 2) WebClient with OAuth2 (Bearer Token via Spring Security)

To call APIs protected by OAuth2 (e.g., **client credentials**, **authorization code**), Spring Security integrates with `WebClient` via an **exchange filter function** that adds tokens automatically. ([Home][1])

---

### 📌 2a) Spring Security & WebClient Setup

#### 🧠 **Step 1: Add dependencies**

```xml
<!-- Enable OAuth2 Client support -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-client</artifactId>
</dependency>

<!-- Needed for WebClient reactive support -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```

---

### 📌 2b) OAuth2 Client Configuration (application.yml)

This config tells Spring Boot how to obtain tokens for your client:

```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          myclient:
            client-id: your-client-id
            client-secret: your-client-secret
            authorization-grant-type: client_credentials
            scope: read write

        provider:
          yourprovider:
            token-uri: https://auth-server.com/oauth/token
```

---

### 📌 2c) WebClient Bean Config with OAuth2

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.oauth2.client.OAuth2AuthorizedClientManager;
import org.springframework.security.oauth2.client.web.reactive.function.client.ServletOAuth2AuthorizedClientExchangeFilterFunction;
import org.springframework.web.reactive.function.client.WebClient;

@Configuration
public class WebClientOAuth2Config {

    @Bean
    public WebClient webClient(OAuth2AuthorizedClientManager authorizedClientManager) {
        ServletOAuth2AuthorizedClientExchangeFilterFunction oauth2 =
            new ServletOAuth2AuthorizedClientExchangeFilterFunction(authorizedClientManager);

        return WebClient.builder()
                .apply(oauth2.oauth2Configuration())  // attach OAuth2 to client
                .build();
    }
}
```

> Spring uses `OAuth2AuthorizedClientManager` and the filter function to fetch access tokens and send them as `Authorization: Bearer …` headers. ([Home][1])

---

### 📌 2d) Using OAuth2 WebClient in Your Code

```java
import org.springframework.security.oauth2.client.annotation.RegisteredOAuth2AuthorizedClient;
import org.springframework.security.oauth2.client.OAuth2AuthorizedClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.reactive.function.client.WebClient;

@RestController
public class OAuth2Controller {

    private final WebClient webClient;

    public OAuth2Controller(WebClient webClient) {
        this.webClient = webClient;
    }

    @GetMapping("/secured-data")
    public String getProtectedData() {
        // Note: "myclient" must match `registrationId` in properties
        return webClient.get()
                .uri("https://api.protected.com/resource")
                .attributes(ServletOAuth2AuthorizedClientExchangeFilterFunction.clientRegistrationId("myclient"))
                .retrieve()
                .bodyToMono(String.class)
                .block();  // block() only in non-reactive controller
    }
}
```

🔹 The `clientRegistrationId("myclient")` tells the filter which OAuth2 registration to use for the request. ([Home][1])

---

## 🛠 Optional: Set Default OAuth2 Client Globally

If you want every request to include the same OAuth2 token without specifying it each time:

```java
oauth2.setDefaultClientRegistrationId("myclient");
```

➡ This config applies the token by default to all requests — use carefully. ([Home][2])

---

## 🧠 Notes & Best Practices

✅ You **don’t need manual header management** — the filter adds the bearer token automatically. ([Home][1])
✅ If token expires, Spring Security **can refresh it** (when supported). ([Home][1])
✅ Use `.block()` only in synchronous code; prefer reactive chains in WebFlux.

---

## 💡 Quick Summary

| Use Case                                    | Configuration                                                                   |
|---------------------------------------------|---------------------------------------------------------------------------------|
| Simple HTTP calls                           | Basic WebClient with base URL and headers                                       |
| **OAuth2 client credentials / token flows** | Spring Security + `OAuth2AuthorizedClientManager` + exchange filter ([Home][1]) |
| Default bearer for all outgoing requests    | Set default `clientRegistrationId` ([Home][2])                                  |

---

If you want, I can also show how to use **WebClient with reactive controllers** returning `Mono` or `Flux` instead of `.block()`.

[1]: https://docs.spring.io/spring-security/reference/servlet/oauth2/?utm_source=chatgpt.com "OAuth2 :: Spring Security"
[2]: https://docs.spring.io/spring-security/reference/reactive/oauth2/client/authorized-clients.html?utm_source=chatgpt.com "Authorized Client Features :: Spring Security"
