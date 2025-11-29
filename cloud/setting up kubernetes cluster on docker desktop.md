Here’s how you can set up a Kubernetes cluster on Docker Desktop and deploy a Spring Boot Gradle service:

---

### **1. Enable Kubernetes on Docker Desktop**
1. Install **Docker Desktop** on your machine from [Docker’s official site](https://www.docker.com/products/docker-desktop/).
2. Open Docker Desktop settings:
    - **Windows/Mac**: Go to **Settings > Kubernetes**.
    - Enable **Kubernetes** and click **Apply & Restart**.
3. Verify Kubernetes is running:
   ```bash
   kubectl cluster-info
   ```

---

### **2. Build and Containerize the Spring Boot Application**
1. **Create a Spring Boot Project with Gradle**:
    - Use [Spring Initializr](https://start.spring.io/) to generate a Spring Boot project with Gradle as the build tool.
    - Add necessary dependencies (e.g., Web, JPA, MySQL if needed).

2. **Write the Dockerfile**:
   Add a `Dockerfile` to the project root to containerize the application:
   ```dockerfile
   FROM openjdk:17-jdk-slim
   WORKDIR /app
   COPY build/libs/*.jar app.jar
   ENTRYPOINT ["java", "-jar", "app.jar"]
   ```

3. **Build the Spring Boot Application**:
   ```bash
   ./gradlew clean build
   ```

4. **Build the Docker Image**:
   ```bash
   docker build -t spring-boot-service:1.0 .
   ```

5. **Verify the Image**:
   ```bash
   docker images
   ```

---

### **3. Write Kubernetes Manifests**
1. **Deployment YAML**:
   Create `deployment.yaml` to define your application deployment:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: spring-boot-service
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: spring-boot-service
     template:
       metadata:
         labels:
           app: spring-boot-service
       spec:
         containers:
         - name: spring-boot-service
           image: spring-boot-service:1.0
           ports:
           - containerPort: 8080
   ```

2. **Service YAML**:
   Create `service.yaml` to expose the application:
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: spring-boot-service
   spec:
     type: LoadBalancer
     selector:
       app: spring-boot-service
     ports:
     - protocol: TCP
       port: 80
       targetPort: 8080
   ```

---

### **4. Push Docker Image to Docker Desktop**
1. Since Docker Desktop includes a local Kubernetes environment, tag the image for the local Docker registry:
   ```bash
   docker tag spring-boot-service:1.0 localhost:5000/spring-boot-service:1.0
   ```

2. Push the image to the local registry:
   ```bash
   docker push localhost:5000/spring-boot-service:1.0
   ```

---

### **5. Deploy to Kubernetes**
1. Apply the Kubernetes manifests:
   ```bash
   kubectl apply -f deployment.yaml
   kubectl apply -f service.yaml
   ```

2. Verify the deployment:
   ```bash
   kubectl get pods
   kubectl get services
   ```

3. Access the application:
    - For **Windows/Mac**, Docker Desktop will automatically assign a localhost IP.
    - Use the `EXTERNAL-IP` of the service if available:
      ```bash
      kubectl get service spring-boot-service
      ```

---

### **6. Verify Logs and Troubleshoot**
1. Check pod logs:
   ```bash
   kubectl logs <pod-name>
   ```

2. Describe resources for more details:
   ```bash
   kubectl describe pod <pod-name>
   kubectl describe service spring-boot-service
   ```

---

### **Optional Enhancements**
- **Add Persistent Storage**: Use Persistent Volumes (PVs) and Persistent Volume Claims (PVCs) for databases.
- **Use Helm**: Simplify Kubernetes deployments with Helm charts.
- **Configure Auto-Scaling**:
   ```bash
   kubectl autoscale deployment spring-boot-service --cpu-percent=50 --min=1 --max=5
   ```
