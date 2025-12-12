Below is a **skeleton Spring Boot 4.0.0 standalone (non-web) OAuth2 client** that calls a protected resource server using **client credentials grant** (suitable for service-to-service REST calls), built with **Gradle**.

This kind of app does **not use web dependencies** (no Spring MVC/WebFlux) and runs as a plain JAR. You’ll configure Spring Security’s OAuth2 client support programmatically (Spring Boot 4 uses Spring Security 6.x OAuth2 support).([docs.enterprise.spring.io][1])

---

## 1) Gradle Build (build.gradle.kts)

```kotlin
plugins {
    id("org.springframework.boot") version "4.0.0"
    id("io.spring.dependency-management") version "1.1.2"
    kotlin("jvm") version "1.9.0" // or Java
}

group = "org.example"
version = "0.0.1-SNAPSHOT"
java.sourceCompatibility = JavaVersion.VERSION_17

repositories {
    mavenCentral()
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-security")
    implementation("org.springframework.boot:spring-boot-starter-oauth2-client")
    implementation("org.springframework.boot:spring-boot-starter-restclient") // Spring’s RestClient (sync)
}
```

> **Note:** Adding `spring-boot-starter-oauth2-client` gives you Spring Security’s OAuth2 client support. Spring Boot will *not* auto configure the OAuth2AuthorizedClientManager for a non-web app — you define beans manually.([GitHub][2])

---

## 2) Application Properties (application.yml)

```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          my-client:
            client-id: your-client-id
            client-secret: your-client-secret
            authorization-grant-type: client_credentials
            scope: read write
        provider:
          my-provider:
            token-uri: https://auth-server.example.com/oauth2/token
```

This tells Spring where the Authorization Server token endpoint is and the credentials to use.([docs.enterprise.spring.io][1])

---

## 3) Core Client Configuration

Create configuration classes to declare the necessary beans for token acquisition and a REST client:

```java
package org.example;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.oauth2.client.*;
import org.springframework.security.oauth2.client.registration.*;
import org.springframework.security.oauth2.client.web.reactive.function.client.*;
import org.springframework.web.client.RestClient;
import org.springframework.web.client.RestClient.Builder;

@Configuration
public class OAuth2ClientConfig {

    @Bean
    public ClientRegistrationRepository clientRegistrationRepository(
            org.springframework.boot.context.properties.ConfigurationPropertiesBinder binder) {
        return new InMemoryClientRegistrationRepository(
            ClientRegistration.withRegistrationId("my-client")
                .tokenUri("https://auth-server.example.com/oauth2/token")
                .clientId("your-client-id")
                .clientSecret("your-client-secret")
                .authorizationGrantType(AuthorizationGrantType.CLIENT_CREDENTIALS)
                .scope("read", "write")
                .build()
        );
    }

    @Bean
    public OAuth2AuthorizedClientService authorizedClientService(
            ClientRegistrationRepository registrations) {
        return new InMemoryOAuth2AuthorizedClientService(registrations);
    }

    @Bean
    public OAuth2AuthorizedClientManager authorizedClientManager(
            ClientRegistrationRepository registrations,
            OAuth2AuthorizedClientService authorizedClientService) {

        AuthorizedClientServiceOAuth2AuthorizedClientManager manager =
            new AuthorizedClientServiceOAuth2AuthorizedClientManager(registrations, authorizedClientService);

        return manager;
    }

    @Bean
    public RestClient restClient(OAuth2AuthorizedClientManager authorizedClientManager) {
        // Configure a RestClient that automatically adds Authorization header
        OAuth2AuthorizedClientExchangeFilterFunction oauth2 =
            new OAuth2AuthorizedClientExchangeFilterFunction(authorizedClientManager);

        return RestClient.builder()
            .requestInterceptor(oauth2)   // inject bearer token
            .build();
    }
}
```

* **ClientRegistrationRepository** holds your OAuth2 client config.
* **OAuth2AuthorizedClientManager** obtains tokens (client_credentials grant).
* **RestClient** injects the token header on outgoing calls.([Home][3])

> *Spring Boot won’t auto-create `OAuth2AuthorizedClientManager` for non-web apps, so you define it yourself.*([GitHub][2])

---

## 4) Main Application

```java
package org.example;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.client.RestClient;

@SpringBootApplication
public class ClientApp implements CommandLineRunner {

    private final RestClient restClient;

    public ClientApp(RestClient restClient) {
        this.restClient = restClient;
    }

    public static void main(String[] args) {
        SpringApplication.run(ClientApp.class, args);
    }

    @Override
    public void run(String... args) {
        String resourceUrl = "https://api.example.com/data";
        String response = restClient.get()
                .uri(resourceUrl)
                .retrieve()
                .bodyToMono(String.class)
                .block();

        System.out.println("Resource response: " + response);
    }
}
```

This runs at startup, makes an OAuth2-protected call to the resource server, and prints the result.

---

## 5) What Happens Under-the-Hood

1. **Token acquisition:** `AuthorizedClientServiceOAuth2AuthorizedClientManager` requests a token from the provider’s token endpoint (client_credentials).
2. **RestClient interceptor:** Adds `Authorization: Bearer <token>` to each request.
3. **Resource call:** The remote protected API validates the token and returns the resource.([docs.enterprise.spring.io][1])

---

## 6) Testing and Running

Build and run:

```sh
./gradlew bootJar
java -jar build/libs/your-app.jar
```

You’ll see the app start, fetch an access token using client credentials, then call the resource server using that token.

---

## Notes & Caveats

* **No Web Dependencies:** Because there is **no Spring MVC/WebFlux starter**, Spring Boot won’t auto configure web-specific beans — you must define the OAuth2 client manager yourself.([GitHub][2])
* **Grant Type:** This example uses **client_credentials** (no user interaction). For other flows (e.g., authorization_code), you need interactive authorization or PKCE.
* **Token Refresh:** Spring Security handles refresh tokens when configured (but client_credentials typically doesn’t use refresh tokens).
* **Security:** Secure your client secret and follow best practices for production.

---

If you want this in **Kotlin DSL**, **WebClient**-based reactive, or for a **different grant type** (e.g., authorization_code with PKCE), let me know and I can tailor the example.

[1]: https://docs.enterprise.spring.io/spring-security/reference/servlet/oauth2/index.html?utm_source=chatgpt.com "OAuth2 :: Spring Security"
[2]: https://github.com/spring-projects/spring-security/issues/15877?utm_source=chatgpt.com "Add `OAuth2AuthorizedClientManager` autoconfiguration without `spring-boot-starter-web` dependency · Issue #15877 · spring-projects/spring-security · GitHub"
[3]: https://spring.io/blog/2024/10/28/restclient-support-for-oauth2-in-spring-security-6-4?utm_source=chatgpt.com "RestClient Support for OAuth2 in Spring Security 6.4"
