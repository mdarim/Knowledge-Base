Cool — here is a **minimal, working template** (skeleton) for a Spring Boot 3 + Spring Security 6 application using JSON Web Token (JWT) for authentication. You can copy/paste and adapt to your needs (DB, user model, endpoints, etc). I also include comments explaining each part.

---

## 📁 Project Structure (typical)

```
src/main/java/com/example/jwt/
    JwtUtil.java
    JwtAuthFilter.java
    SecurityConfig.java
    AuthController.java
    AuthRequest.java
    AuthResponse.java
    MyUserDetailsService.java   // implements UserDetailsService
    (optional) User entity, UserRepository, etc
```

---

## 🔐 Configuration + JWT Utility

### JwtUtil.java — for token generation & validation

```java
package com.example.jwt;

import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.SignatureAlgorithm;

import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.stereotype.Service;

import java.util.Date;
import java.util.HashMap;
import java.util.Map;
import java.util.function.Function;

@Service
public class JwtUtil {

    private final String SECRET_KEY = "your-very-secret-key-here"; // ideally read from application properties/env

    public String extractUsername(String token) {
        return extractClaim(token, Claims::getSubject);
    }

    public Date extractExpiration(String token) {
        return extractClaim(token, Claims::getExpiration);
    }

    public <T> T extractClaim(String token, Function<Claims, T> claimsResolver) {
        final Claims claims = extractAllClaims(token);
        return claimsResolver.apply(claims);
    }

    private Claims extractAllClaims(String token) {
        return Jwts.parser()
                .setSigningKey(SECRET_KEY)
                .parseClaimsJws(token)
                .getBody();
    }

    private Boolean isTokenExpired(String token) {
        return extractExpiration(token).before(new Date());
    }

    public String generateToken(UserDetails userDetails) {
        Map<String,Object> claims = new HashMap<>();
        // you can add custom claims here like roles, email, etc
        return createToken(claims, userDetails.getUsername());
    }

    private String createToken(Map<String,Object> claims, String subject) {
        long validityInMs = 1000 * 60 * 60 * 10; // 10 hours

        return Jwts.builder()
                .setClaims(claims)
                .setSubject(subject)
                .setIssuedAt(new Date(System.currentTimeMillis()))
                .setExpiration(new Date(System.currentTimeMillis() + validityInMs))
                .signWith(SignatureAlgorithm.HS256, SECRET_KEY)
                .compact();
    }

    public Boolean validateToken(String token, UserDetails userDetails) {
        final String username = extractUsername(token);
        return (username.equals(userDetails.getUsername()) && !isTokenExpired(token));
    }
}
```

---

## 🔄 Filter — Intercept requests and validate JWT

### JwtAuthFilter.java

```java
package com.example.jwt;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.web.authentication.WebAuthenticationDetailsSource;
import org.springframework.stereotype.Component;
import org.springframework.web.filter.OncePerRequestFilter;

import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

@Component
public class JwtAuthFilter extends OncePerRequestFilter {

    @Autowired
    private JwtUtil jwtUtil;

    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain)
            throws ServletException, IOException {

        final String authHeader = request.getHeader("Authorization");
        String jwt = null;
        String username = null;

        if (authHeader != null && authHeader.startsWith("Bearer ")) {
            jwt = authHeader.substring(7);
            try {
                username = jwtUtil.extractUsername(jwt);
            } catch (Exception e) {
                // invalid token
            }
        }

        if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
            UserDetails userDetails = this.userDetailsService.loadUserByUsername(username);
            if (jwtUtil.validateToken(jwt, userDetails)) {
                UsernamePasswordAuthenticationToken authToken =
                        new UsernamePasswordAuthenticationToken(
                                userDetails, null, userDetails.getAuthorities());
                authToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));

                SecurityContextHolder.getContext().setAuthentication(authToken);
            }
        }

        filterChain.doFilter(request, response);
    }
}
```

---

## 🛡 Spring Security Configuration

### SecurityConfig.java

```java
package com.example.jwt;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
@EnableWebSecurity
public class SecurityConfig {

    private final JwtAuthFilter jwtAuthFilter;
    private final MyUserDetailsService userDetailsService; // you create this

    public SecurityConfig(JwtAuthFilter jwtAuthFilter, MyUserDetailsService userDetailsService) {
        this.jwtAuthFilter = jwtAuthFilter;
        this.userDetailsService = userDetailsService;
    }

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
          .csrf(csrf -> csrf.disable())
          .authorizeHttpRequests(auth -> auth
              .requestMatchers("/auth/login", "/auth/register").permitAll()
              .anyRequest().authenticated()
          )
          .sessionManagement(sess -> sess.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
          .and()
          .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    // If you need an AuthenticationManager bean (e.g. for login), can auto-wire via AuthenticationConfiguration
    @Bean
    public AuthenticationManager authenticationManager(org.springframework.security.config.annotation.authentication.configuration.AuthenticationConfiguration config) throws Exception {
        return config.getAuthenticationManager();
    }
}
```

---

## 🔐 Authentication Controller + Request/Response

### AuthRequest.java

```java
package com.example.jwt;

public class AuthRequest {
    private String username;
    private String password;

    // getters and setters
}
```

### AuthResponse.java

```java
package com.example.jwt;

public class AuthResponse {
    private String jwt;

    public AuthResponse(String jwt) {
        this.jwt = jwt;
    }

    // getter
}
```

### AuthController.java

```java
package com.example.jwt;

import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.AuthenticationException;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/auth")
public class AuthController {

    private final AuthenticationManager authManager;
    private final JwtUtil jwtUtil;
    private final MyUserDetailsService userDetailsService;

    public AuthController(AuthenticationManager authManager, JwtUtil jwtUtil, MyUserDetailsService userDetailsService) {
        this.authManager = authManager;
        this.jwtUtil = jwtUtil;
        this.userDetailsService = userDetailsService;
    }

    @PostMapping("/login")
    public AuthResponse login(@RequestBody AuthRequest request) throws Exception {
        try {
            authManager.authenticate(
                new UsernamePasswordAuthenticationToken(request.getUsername(), request.getPassword())
            );
        } catch (AuthenticationException e) {
            throw new Exception("Invalid username/password");
        }

        final var userDetails = userDetailsService.loadUserByUsername(request.getUsername());
        final String jwt = jwtUtil.generateToken(userDetails);
        return new AuthResponse(jwt);
    }
}
```

---

## 👤 UserDetailsService + User Model (simplified)

You need to implement a class that implements `UserDetailsService`, fetches user data (from DB or in-memory), and returns a `UserDetails` instance. E.g.:

```java
@Service
public class MyUserDetailsService implements UserDetailsService {
    // e.g. @Autowired UserRepository userRepo;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        // load user entity from DB...
        // return new org.springframework.security.core.userdetails.User(
        //        user.getUsername(),
        //        user.getPassword(),  // hashed password
        //        List.of(new SimpleGrantedAuthority("ROLE_USER"))
        // );
    }
}
```

For testing you may just hard-code a user.

---

## ✅ What this template gives you

* `/auth/login` endpoint where you send username + password → you get a JWT token.
* The JWT is signed and expires after a defined time.
* For every subsequent request you send `Authorization: Bearer <token>` header → the filter intercepts, validates token, sets authentication → secured endpoints will work.
* Stateless: no HTTP session — good for REST APIs / scalable microservices.

---

## 💡 Notes & Where to Extend / Improve

* Use a secure secret (via properties / env variable), not hard-coded.
* Use hashed passwords (e.g. `BCryptPasswordEncoder`).
* Use a real persistent datastore (e.g. JPA + MySQL/PostgreSQL) for users.
* Add roles/authorities to the `UserDetails`, include them in JWT claims if needed.
* Handle token expiration, refresh tokens or logout if needed.
* Consider error handling (invalid token, expired token), custom entry point, exception filters, etc.
* Secure configuration: e.g. CORS, CSRF (often disabled for stateless APIs), HTTPS, secret management.
