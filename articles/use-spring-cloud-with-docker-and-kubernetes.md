# 🌐 **Spring Cloud with Docker and Kubernetes**

**Spring Cloud**, **Docker**, and **Kubernetes** form a powerful stack to build, deploy, and manage **cloud-native microservices**. This guide explains how Spring Cloud is used with **Docker** for **containerization** and **Kubernetes** for **orchestration and scaling**.

---

## 🚀 **1. Spring Cloud + Docker**

Docker is a **containerization tool** that helps package Spring Cloud microservices into **lightweight, portable containers**.

### ✅ **Why Use Docker with Spring Cloud?**
- **Consistency** across environments (dev, test, production).
- **Portability** of applications.
- **Simplified deployment** process.
- **Scalable** microservices.

### 📋 **Steps to Use Spring Cloud with Docker:**

#### 🛠 **Step 1: Create a Spring Cloud Microservice**

Here's a simple **Eureka Server** as an example of a Spring Cloud service.

**`pom.xml` (Dependencies):**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

**Main Class:**

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
}
```

---

#### 🛠 **Step 2: Create a Dockerfile**

The **Dockerfile** specifies how to build a Docker image for your Spring Cloud service.

```Dockerfile
# Use a base image with JDK
FROM openjdk:17-jdk-slim

# Set the working directory
WORKDIR /app

# Copy the built JAR file
COPY target/eureka-server.jar eureka-server.jar

# Expose port 8761 for Eureka Server
EXPOSE 8761

# Run the application
ENTRYPOINT ["java", "-jar", "eureka-server.jar"]
```

---

#### 🛠 **Step 3: Build and Run the Docker Image**

1️⃣ **Build the Docker image:**

```bash
docker build -t eureka-server:1.0 .
```

2️⃣ **Run the Docker container:**

```bash
docker run -p 8761:8761 eureka-server:1.0
```

---

### ✅ **Key Spring Cloud Services to Containerize with Docker:**
| **Service**             | **Description**                                     |
|-------------------------|-----------------------------------------------------|
| **Eureka Server**        | Service registry for microservices.                |
| **Config Server**        | Externalized configuration management.             |
| **API Gateway**          | Gateway for routing requests to microservices.      |
| **Circuit Breaker**      | Resilience and fault tolerance.                    |

---

## 🖥️ **2. Spring Cloud + Kubernetes**

**Kubernetes (K8s)** is a **container orchestration platform** that automates the **deployment, scaling, and management** of Docker containers.

### ✅ **Why Use Kubernetes with Spring Cloud?**
- **Service Discovery**: Kubernetes handles **service registration and discovery** natively.
- **Configuration Management**: Kubernetes **ConfigMaps** can manage externalized configurations.
- **Load Balancing**: Kubernetes provides **built-in load balancing** for microservices.
- **Resilience and Scaling**: Automatically handles **failures and scaling** of microservices.

---

### 📋 **Steps to Deploy Spring Cloud Microservices on Kubernetes:**

#### 🛠 **Step 1: Create a Kubernetes Deployment File**

**`eureka-server-deployment.yaml`:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: eureka-server
spec:
  replicas: 3
  selector:
    matchLabels:
      app: eureka-server
  template:
    metadata:
      labels:
        app: eureka-server
    spec:
      containers:
      - name: eureka-server
        image: eureka-server:1.0
        ports:
        - containerPort: 8761
---
apiVersion: v1
kind: Service
metadata:
  name: eureka-server
spec:
  selector:
    app: eureka-server
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8761
  type: LoadBalancer
```

---

#### 🛠 **Step 2: Apply the Kubernetes Configuration**

1️⃣ **Deploy the service to Kubernetes:**

```bash
kubectl apply -f eureka-server-deployment.yaml
```

2️⃣ **Check the status of the deployment:**

```bash
kubectl get pods
```

---

### ✅ **Key Spring Cloud Components in Kubernetes:**

| **Component**             | **Purpose in Kubernetes**                                     |
|---------------------------|---------------------------------------------------------------|
| **Spring Cloud Config**    | Manages externalized configuration using **ConfigMaps**.       |
| **Eureka Server**          | Can be replaced with **Kubernetes native service discovery**.  |
| **Spring Cloud Gateway**   | Acts as an **API Gateway** for Kubernetes services.            |
| **Circuit Breaker**        | Can integrate with **Resilience4j** or use Kubernetes scaling. |

---

### 🧩 **Spring Cloud Kubernetes**

**Spring Cloud Kubernetes** is a Spring Cloud project that integrates with **Kubernetes** to simplify:

| **Feature**               | **Description**                                         |
|---------------------------|---------------------------------------------------------|
| **ConfigMaps and Secrets** | Access **Kubernetes ConfigMaps** and **Secrets**.       |
| **Service Discovery**      | Uses **Kubernetes services** for discovery.             |
| **Load Balancing**         | Integrates with **Kubernetes load balancing**.          |
| **Leader Election**        | Elect a leader instance in distributed applications.     |

---

## 🔄 **How Spring Cloud Works with Kubernetes:**

1. **Service Discovery**:  
   Kubernetes **replaces Eureka** with its built-in service discovery mechanism.

2. **Externalized Configuration**:  
   Use **Kubernetes ConfigMaps** instead of Spring Cloud Config Server.

3. **Resilience and Fault Tolerance**:  
   Use **Kubernetes readiness and liveness probes** to ensure service health.

4. **Scaling**:  
   Kubernetes can **auto-scale** Spring Cloud microservices using **Horizontal Pod Autoscaler (HPA)**.

---

## 📝 **Example: Kubernetes ConfigMap for Spring Boot App**

Create a **ConfigMap** to externalize configuration.

**`application-config.yaml`:**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  application.yml: |
    spring:
      application:
        name: cloud-native-app
```

---

## 🔐 **Securing Spring Cloud Apps on Kubernetes**

Use **Spring Security** with **Kubernetes Secrets** to manage credentials securely.

**Example: Kubernetes Secret for Database Credentials**

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-credentials
type: Opaque
data:
  username: bXlfdXNlcg==
  password: c2VjcmV0
```

---

## 📊 **Monitoring and Observability**

Integrate **Spring Boot Actuator** with **Prometheus** and **Grafana** for monitoring in Kubernetes.

| **Tool**          | **Purpose**                    |
|-------------------|--------------------------------|
| **Prometheus**    | Collects metrics from services. |
| **Grafana**       | Visualizes metrics data.        |
| **Jaeger/Zipkin** | Distributed tracing.            |

---

## 📋 **Summary:**

| **Tool/Technology**   | **Purpose**                                           |
|-----------------------|-------------------------------------------------------|
| **Spring Cloud**       | Build microservices and cloud-native apps.            |
| **Docker**             | Containerize Spring Cloud microservices.              |
| **Kubernetes**         | Orchestrate and scale Docker containers.              |
| **Spring Cloud Kubernetes** | Native integration with Kubernetes services.     |
| **Prometheus & Grafana**| Monitoring and visualization tools for observability.|

---
