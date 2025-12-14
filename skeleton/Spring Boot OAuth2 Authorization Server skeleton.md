Here’s a **minimal Spring Boot 4.0.0 Authorization Server skeleton** you can use as a starting point. Spring Boot 4 introduces a new starter for OAuth2 Authorization Server support (`spring-boot-starter-security-oauth2-authorization-server`) and integrates Spring Authorization Server functionality built on Spring Security. ([mvnrepository.com][1])

---

## 🔧 1. Project Setup

### **Maven `pom.xml`**

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>auth-server</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        <java.version>17</java.version>
        <spring.boot.version>4.0.0</spring.boot.version>
    </properties>

    <dependencies>
        <!-- Spring Boot OAuth2 Authorization Server Starter -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security-oauth2-authorization-server</artifactId>
            <version>${spring.boot.version}</version>
        </dependency>

        <!-- Web (Tomcat + MVC) -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-webmvc</artifactId>
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

## 🧠 2. Main Application Class

**`AuthServerApplication.java`**

```java
package com.example.authserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class AuthServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(AuthServerApplication.class, args);
    }
}
```

---

## 🔐 3. Authorization Server Configuration

### **`AuthorizationServerConfig.java`**

This class configures core beans such as registered clients, key source, and server settings.

```java
package com.example.authserver.config;

import java.security.KeyPair;
import java.security.KeyPairGenerator;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.crypto.keys.*;
import org.springframework.security.oauth2.core.*;
import org.springframework.security.oauth2.server.authorization.*;
import org.springframework.security.oauth2.server.authorization.client.*;
import org.springframework.security.oauth2.server.authorization.config.annotation.web.configuration.*;
import org.springframework.security.web.*;

@Configuration
public class AuthorizationServerConfig {

    @Bean
    public RegisteredClientRepository registeredClientRepository() {
        RegisteredClient client = RegisteredClient.withId("demo")
            .clientId("demo-client")
            .clientSecret("{noop}secret")
            .authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE)
            .authorizationGrantType(AuthorizationGrantType.REFRESH_TOKEN)
            .redirectUri("http://127.0.0.1:8080/login/oauth2/code/demo")
            .scope("openid")
            .scope("profile")
            .build();

        return new InMemoryRegisteredClientRepository(client);
    }

    @Bean
    public JWKSource<SecurityContext> jwkSource() throws Exception {
        KeyPairGenerator keyPairGenerator = KeyPairGenerator.getInstance("RSA");
        keyPairGenerator.initialize(2048);
        KeyPair keyPair = keyPairGenerator.generateKeyPair();

        RSAKey rsaKey = new RSAKey.Builder((RSAPublicKey) keyPair.getPublic())
            .privateKey(keyPair.getPrivate())
            .keyID("demo-key")
            .build();

        JWKSet jwkSet = new JWKSet(rsaKey);
        return (jwkSelector, context) -> jwkSelector.select(jwkSet);
    }

    @Bean
    public AuthorizationServerSettings authorizationServerSettings() {
        return AuthorizationServerSettings.builder().build();
    }

    @Bean
    public SecurityFilterChain authorizationServerSecurityFilterChain(HttpSecurity http) throws Exception {
        OAuth2AuthorizationServerConfiguration.applyDefaultSecurity(http);
        return http.formLogin().and().build();
    }
}
```

This configuration provides:

* An in‑memory registered client (client ID = `demo-client`)
* Generated RSA keys for token signing
* Default authorization server endpoints such as `/oauth2/authorize`, `/oauth2/token`, `/oauth2/jwks`
* A basic form login for user authentication

---

## 📌 4. Basic Application Properties

**`src/main/resources/application.yml`**

```yaml
server:
  port: 9000

spring:
  security:
    user:
      name: user
      password: password
```

This sets up a default user for form login; in production you’d replace this with a proper `UserDetailsService`. ([Home][2])

---

## 🏁 5. Running & Testing

1. **Run the server**

   ```sh
   mvn spring-boot:run
   ```

2. **Authorization Endpoint**
   Visit in a browser:

   ```text
   http://localhost:9000/oauth2/authorize?response_type=code&client_id=demo-client&redirect_uri=http://127.0.0.1:8080/login/oauth2/code/demo&scope=openid
   ```

3. **Token Endpoint (example)**

   ```sh
   curl -X POST \
     -u demo-client:secret \
     -d grant_type=authorization_code \
     -d code=YOUR_AUTH_CODE \
     -d redirect_uri=http://127.0.0.1:8080/login/oauth2/code/demo \
     http://localhost:9000/oauth2/token
   ```

---

## 🔍 Notes

* The new **Spring Boot OAuth2 Authorization Server starter** provides integrated support for Spring Authorization Server features. ([mvnrepository.com][1])
* For more advanced setups (JPA persistence, dynamic client registration, custom token claims), you can extend the basic beans above. ([Home][3])
* Default form login is included for simplicity; in real apps replace with your own authentication mechanism.

---

If you want, I can also provide a **Gradle version**, a **Docker setup**, or an example with **OpenID Connect ID Tokens and refresh tokens**.

[1]: https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-oauth2-authorization-server/4.0.0?utm_source=chatgpt.com "Maven Repository: org.springframework.boot » spring-boot-starter-oauth2-authorization-server » 4.0.0"
[2]: https://docs.spring.io/spring-authorization-server/reference/getting-started.html?utm_source=chatgpt.com "Getting Started :: Spring Authorization Server"
[3]: https://docs.spring.io/spring-authorization-server/reference/configuration-model.html?utm_source=chatgpt.com "Configuration Model :: Spring Authorization Server"

Absolutely — here’s a **Spring Boot 4.0.0 Authorization Server skeleton with Gradle + Docker support** + an **example including OpenID Connect ID Tokens**.

This builds on the **Spring Authorization Server** support that’s now available through the Spring Boot starter `spring-boot-starter-oauth2-authorization-server` in **Spring Boot 4**. ([mvnrepository.com][1])

---

## 📦 1) Gradle Project Setup

**`build.gradle`**

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '4.0.0'
    id 'io.spring.dependency-management' version '1.1.0'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '17'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-oauth2-authorization-server'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-security'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

> Note: This starter includes Spring Authorization Server support out-of-the-box for Spring Boot 4. ([mvnrepository.com][1])

---

## 🧠 2) Main Application

**`src/main/java/com/example/authserver/AuthServerApplication.java`**

```java
package com.example.authserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class AuthServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(AuthServerApplication.class, args);
    }
}
```

---

## 🔐 3) Authorization Server Configuration

**`src/main/java/com/example/authserver/config/AuthorizationServerConfig.java`**

```java
package com.example.authserver.config;

import java.security.KeyPair;
import java.security.KeyPairGenerator;
import java.security.interfaces.RSAPrivateKey;
import java.security.interfaces.RSAPublicKey;

import com.nimbusds.jose.jwk.RSAKey;
import com.nimbusds.jose.jwk.JWKSet;
import com.nimbusds.jose.jwk.source.JWKSource;
import com.nimbusds.jose.proc.SecurityContext;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.crypto.factory.PasswordEncoderFactories;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.oauth2.core.AuthorizationGrantType;
import org.springframework.security.oauth2.jose.jws.MacAlgorithm;
import org.springframework.security.oauth2.server.authorization.InMemoryRegisteredClientRepository;
import org.springframework.security.oauth2.server.authorization.client.RegisteredClient;
import org.springframework.security.oauth2.server.authorization.client.RegisteredClientRepository;
import org.springframework.security.oauth2.server.authorization.config.ProviderSettings;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class AuthorizationServerConfig {

    @Bean
    public RegisteredClientRepository registeredClientRepository(PasswordEncoder passwordEncoder) {
        RegisteredClient client = RegisteredClient.withId("demo")
            .clientId("demo-client")
            .clientSecret(passwordEncoder.encode("secret"))
            .authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE)
            .authorizationGrantType(AuthorizationGrantType.REFRESH_TOKEN)
            .redirectUri("http://localhost:8080/login/oauth2/code/demo-client")
            .scope("openid")
            .scope("profile")
            .build();

        return new InMemoryRegisteredClientRepository(client);
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return PasswordEncoderFactories.createDelegatingPasswordEncoder();
    }

    @Bean
    public JWKSource<SecurityContext> jwkSource() throws Exception {
        KeyPairGenerator gen = KeyPairGenerator.getInstance("RSA");
        gen.initialize(2048);
        KeyPair pair = gen.generateKeyPair();

        RSAKey rsaKey = new RSAKey.Builder((RSAPublicKey) pair.getPublic())
            .privateKey((RSAPrivateKey) pair.getPrivate())
            .keyID("rsa-key")
            .build();

        return (jwkSelector, context) -> jwkSelector.select(new JWKSet(rsaKey));
    }

    @Bean
    public ProviderSettings providerSettings() {
        return ProviderSettings.builder().build();
    }

    @Bean
    public SecurityFilterChain authServerSecurityFilterChain(HttpSecurity http) throws Exception {
        org.springframework.security.oauth2.server.authorization.config.annotation.web.configuration.OAuth2AuthorizationServerConfiguration.applyDefaultSecurity(http);
        return http.formLogin().and().build();
    }
}
```

**What this does:**

* Registers an OAuth client in memory
* Generates RSA keys for signing tokens
* Exposes endpoints `/oauth2/authorize`, `/oauth2/token`, `/oauth2/jwks`, etc.
* Enables basic username/password login for user authentication

> This is similar to the official “Getting Started” steps from Spring Authorization Server docs. ([Home][2])

---

## ⚙️ 4) Application Properties

**`src/main/resources/application.yml`**

```yaml
server:
  port: 9000

spring:
  security:
    user:
      name: user
      password: password
```

This sets up a simple in-memory user for login (replace for production).

---

## 🧪 5) OpenID Connect Support (ID Tokens)

To issue ID tokens (OIDC):

1. Add `scope("openid")` to your registered client (already done above).
2. Include the following support bean:

```java
@Bean
public org.springframework.security.oauth2.server.authorization.config.ClientSettings clientSettings() {
    return org.springframework.security.oauth2.server.authorization.config.ClientSettings.builder()
        .requireAuthorizationConsent(true)
        .build();
}
```

This prompts the user to **consent to scopes** and allows OIDC flows.

---

## 🐳 6) Optional: Dockerfile

**`Dockerfile`**

```dockerfile
FROM eclipse-temurin:17-jdk-alpine
WORKDIR /app
COPY build/libs/auth-server-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 9000
ENTRYPOINT ["java","-jar","/app/app.jar"]
```

**Build & Run:**

```sh
./gradlew build
docker build -t auth-server .
docker run -p 9000:9000 auth-server
```

---

## 📍 7) Testing the Server

### Authorization Code Flow:

```
http://localhost:9000/oauth2/authorize?
  response_type=code&
  client_id=demo-client&
  redirect_uri=http://localhost:8080/login/oauth2/code/demo-client&
  scope=openid%20profile
```

### Token Request (example with `curl`):

```sh
curl -u demo-client:secret \
  -d grant_type=authorization_code \
  -d code=AUTH_CODE \
  -d redirect_uri=http://localhost:8080/login/oauth2/code/demo-client \
  http://localhost:9000/oauth2/token
```

You should get access + ID tokens.

---

## 📌 Notes

* The `spring-boot-starter-oauth2-authorization-server` starter simplifies getting a Spring Authorization Server up and running with minimal config. ([mvnrepository.com][1])
* You can extend this skeleton with:

    * **Persistent client & token stores (JPA)**
    * **Custom user details with database**
    * **CORS / security hardening**
    * **PKCE support for public clients**
* For full reference and advanced config, see the *Spring Authorization Server Getting Started* guide. ([Home][2])

---

If you want a **complete sample GitHub repo** for this configuration (with resource server and client app), I can generate one for you! 👍

[1]: https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-oauth2-authorization-server/4.0.0?utm_source=chatgpt.com "Maven Repository: org.springframework.boot » spring-boot-starter-oauth2-authorization-server » 4.0.0"
[2]: https://docs.spring.io/spring-authorization-server/reference/getting-started.html?utm_source=chatgpt.com "Getting Started :: Spring Authorization Server"
