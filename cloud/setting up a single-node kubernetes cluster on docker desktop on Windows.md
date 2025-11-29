Setting up a Kubernetes cluster on **Docker Desktop** on a **Windows** machine is straightforward, as Docker Desktop includes a single-node Kubernetes cluster suitable for local development. Here's a step-by-step guide:

---

### **1. Install Docker Desktop**
1. Download Docker Desktop for Windows from [Docker's official site](https://www.docker.com/products/docker-desktop/).
2. Run the installer and follow the installation steps.
3. Ensure the following prerequisites are met:
    - Windows 10 or 11 Professional, Enterprise, or Education.
    - WSL 2 installed and set as the default backend for Docker Desktop.
    - Virtualization enabled in your BIOS.

---

### **2. Enable Kubernetes in Docker Desktop**
1. Open Docker Desktop.
2. Go to **Settings > Kubernetes**.
3. Check the box for **Enable Kubernetes**.
4. Click **Apply & Restart**.
5. Verify Kubernetes is running by running the following command in a terminal:
   ```bash
   kubectl cluster-info
   ```

---

### **3. Verify Kubernetes Installation**
Check that the Kubernetes cluster is up and running:
1. Open a terminal (e.g., Command Prompt, PowerShell, or Windows Terminal).
2. Run:
   ```bash
   kubectl get nodes
   ```
   You should see a single node with the `Ready` status.

---

### **4. Configure kubectl (Optional)**
Docker Desktop configures `kubectl` automatically. If you have multiple Kubernetes contexts, switch to the Docker Desktop context:
```bash
kubectl config use-context docker-desktop
```

---

### **5. Deploy a Sample Application**
1. Create a deployment:
   ```bash
   kubectl create deployment nginx --image=nginx
   ```
2. Expose it as a service:
   ```bash
   kubectl expose deployment nginx --type=NodePort --port=80
   ```
3. Find the service's NodePort:
   ```bash
   kubectl get service nginx
   ```
4. Access the application in your browser:
    - Use `http://localhost:<NodePort>`.

---

### **6. Deploy a Spring Boot Application**
To deploy your Spring Boot application on this Kubernetes setup:

#### Step 1: Build a Docker Image
1. Create a Dockerfile for your Spring Boot application:
   ```dockerfile
   FROM openjdk:17-jdk-slim
   WORKDIR /app
   COPY build/libs/*.jar app.jar
   ENTRYPOINT ["java", "-jar", "app.jar"]
   ```
2. Build the application JAR:
   ```bash
   ./gradlew clean build
   ```
3. Build the Docker image:
   ```bash
   docker build -t spring-boot-service:1.0 .
   ```

#### Step 2: Push the Image to Docker Desktop
Since Docker Desktop runs locally, no external registry is needed:
```bash
docker tag spring-boot-service:1.0 localhost:5000/spring-boot-service:1.0
docker push localhost:5000/spring-boot-service:1.0
```

#### Step 3: Create Kubernetes Manifests
1. **Deployment YAML**:
   Save this as `deployment.yaml`:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: spring-boot-service
   spec:
     replicas: 1
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
           image: localhost:5000/spring-boot-service:1.0
           ports:
           - containerPort: 8080
   ```

2. **Service YAML**:
   Save this as `service.yaml`:
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: spring-boot-service
   spec:
     type: NodePort
     selector:
       app: spring-boot-service
     ports:
     - protocol: TCP
       port: 80
       targetPort: 8080
   ```

#### Step 4: Deploy to Kubernetes
1. Apply the deployment and service:
   ```bash
   kubectl apply -f deployment.yaml
   kubectl apply -f service.yaml
   ```
2. Verify the pods are running:
   ```bash
   kubectl get pods
   ```
3. Access the service:
   ```bash
   kubectl get service spring-boot-service
   ```
   Use `http://localhost:<NodePort>` to access your Spring Boot service.

---

### **7. Optional Steps**
- **Helm**: Use Helm to manage deployments if your application has many components.
- **Persistent Volumes**: Use persistent storage for databases or logs.
- **Metrics**: Install Kubernetes metrics for monitoring:
  ```bash
  kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
  ```

---
