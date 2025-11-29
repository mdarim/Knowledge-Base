Deploying a **Spring Boot application** on a **multi-node Minikube Kubernetes cluster** involves several steps. Minikube allows you to run Kubernetes clusters on your local machine and supports creating multi-node clusters. Here’s a step-by-step guide to achieve this:

---

### **Step 1: Install Required Tools**

1. **Install Minikube**:
   Follow the official guide to install Minikube:
    - [Minikube Installation](https://minikube.sigs.k8s.io/docs/)

   After installing, verify the installation:
   ```bash
   minikube version
   ```

2. **Install kubectl**:
   Kubernetes command-line tool, `kubectl`, is required to interact with the cluster.
    - [Install kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

   Verify the installation:
   ```bash
   kubectl version --client
   ```

3. **Install Docker** (if not already installed):
   Minikube uses Docker to build and push images. You’ll need Docker running locally.
    - [Install Docker](https://docs.docker.com/get-docker/)

---

### **Step 2: Set Up Minikube with Multi-Node Support**

1. **Start a Multi-Node Minikube Cluster**:
   You can create a multi-node Minikube cluster using the `--nodes` flag. This will create one master node and the specified number of worker nodes.

   Example: Create a cluster with 1 master and 2 worker nodes.
   ```bash
   minikube start --nodes 3
   ```

   Minikube uses a virtual machine to create these nodes. If you're using a virtual machine driver like VirtualBox, Docker, or HyperKit, ensure that the required driver is installed.

2. **Verify the Cluster**:
   Once the cluster is created, verify the nodes are set up properly.
   ```bash
   kubectl get nodes
   ```

   You should see all three nodes listed (`control-plane` and two `worker` nodes).

---

### **Step 3: Build Your Spring Boot Application**

#### 1. **Create a Spring Boot Project**:
Use [Spring Initializr](https://start.spring.io/) to generate a new Spring Boot application. Add dependencies such as:
- **Spring Web** (for REST APIs).
- **Spring Boot DevTools** (optional for development).
- **Actuator** (for health checks).

#### 2. **Build the JAR File**:
Run the following command to build the JAR file (if using Gradle):
```bash
./gradlew clean build
```
This will produce the JAR file in the `build/libs/` directory.

---

### **Step 4: Create Docker Image for the Spring Boot Application**

1. **Write a Dockerfile**:
   Create a `Dockerfile` in the root of the project:
   ```dockerfile
   FROM openjdk:17-jdk-slim
   WORKDIR /app
   COPY build/libs/*.jar app.jar
   ENTRYPOINT ["java", "-jar", "app.jar"]
   ```

2. **Build the Docker Image**:
   Build the Docker image with the following command:
   ```bash
   docker build -t spring-boot-app:1.0 .
   ```

3. **Verify the Image**:
   Check if the image is built successfully:
   ```bash
   docker images
   ```

---

### **Step 5: Load Docker Image into Minikube**

Minikube uses its own Docker daemon, so you need to load the Docker image into the Minikube cluster.

1. **Configure Docker to Use Minikube’s Docker Daemon**:
   Run the following command to point your local Docker client to Minikube’s Docker daemon:
   ```bash
   eval $(minikube -p minikube docker-env)
   ```

2. **Load the Image into Minikube**:
   After configuring Docker, reload the Docker image into Minikube:
   ```bash
   docker build -t spring-boot-app:1.0 .
   ```

---

### **Step 6: Create Kubernetes Manifests**

Create the Kubernetes configuration files for the Spring Boot deployment.

#### Deployment YAML
Create a file named `deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: spring-boot-app
spec:
  replicas: 2  # Deploy two replicas across nodes
  selector:
    matchLabels:
      app: spring-boot-app
  template:
    metadata:
      labels:
        app: spring-boot-app
    spec:
      containers:
      - name: spring-boot-app
        image: spring-boot-app:1.0
        ports:
        - containerPort: 8080
```

#### Service YAML
Create a file named `service.yaml` to expose the application:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: spring-boot-app
spec:
  type: NodePort
  selector:
    app: spring-boot-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

---

### **Step 7: Deploy the Application to Kubernetes**

1. **Apply the Manifests**:
   Apply both the deployment and the service:
   ```bash
   kubectl apply -f deployment.yaml
   kubectl apply -f service.yaml
   ```

2. **Verify the Pods**:
   Ensure the pods are running on the cluster:
   ```bash
   kubectl get pods
   ```

3. **Check the Services**:
   Get the services to find the NodePort:
   ```bash
   kubectl get service spring-boot-app
   ```

---

### **Step 8: Access the Application**

1. **Get the NodePort**:
   Get the NodePort for your service:
   ```bash
   kubectl get service spring-boot-app
   ```

2. **Access the Application**:
   Use the `NodePort` to access the application:
   ```bash
   minikube service spring-boot-app --url
   ```

   This command will provide a URL that you can visit in your browser to access the application.

---

### **Step 9: Verify Logs and Troubleshoot**

1. **Check Logs**:
   To check the logs for a specific pod:
   ```bash
   kubectl logs -f <pod-name>
   ```

2. **Describe Resources**:
   You can describe resources to check their status:
   ```bash
   kubectl describe pod <pod-name>
   kubectl describe service spring-boot-app
   ```

---

### **Step 10: Scaling and Management**

1. **Scale the Deployment**:
   If you want to scale the application across more nodes or replicas:
   ```bash
   kubectl scale deployment spring-boot-app --replicas=4
   ```

2. **Delete the Deployment**:
   To delete the deployment when you're done:
   ```bash
   kubectl delete -f deployment.yaml
   kubectl delete -f service.yaml
   ```

---

### **Optional Enhancements**

1. **MetalLB**: For external load balancing in Minikube, you can install MetalLB (useful if you want to expose services with external IPs).
   ```bash
   kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.7/manifests/metallb.yaml
   ```

2. **Persistent Volumes**: Use persistent volumes to store data (e.g., database storage for applications).

---
