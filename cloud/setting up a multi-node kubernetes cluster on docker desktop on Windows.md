Setting up a **multi-node Kubernetes cluster** on Docker Desktop is not directly supported since Docker Desktop Kubernetes operates as a single-node cluster primarily designed for local development. However, you can set up a multi-node Kubernetes cluster on Windows by using tools like **kubeadm**, **kind** (Kubernetes IN Docker), or **Minikube** with multiple virtual nodes.

Here’s how you can set up a multi-node Kubernetes cluster on Windows using **kind** (Kubernetes IN Docker):

---

### **1. Install Required Tools**
1. **Docker Desktop**:
    - Install Docker Desktop for Windows from [Docker's official site](https://www.docker.com/products/docker-desktop/).
    - Enable **WSL 2** backend in Docker Desktop settings.
    - Enable Kubernetes in Docker Desktop settings (optional, but not required for `kind`).

2. **Install kubectl**:
    - Download `kubectl` from the Kubernetes official release page:
      [Install kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
    - Add `kubectl` to your system’s PATH.

3. **Install kind**:
    - Download `kind` from the official GitHub repository:
      [Install kind](https://kind.sigs.k8s.io/docs/user/quick-start/).
    - Add `kind` to your system’s PATH.

   Verify the installations:
   ```bash
   docker --version
   kubectl version --client
   kind version
   ```

---

### **2. Create a Multi-Node Cluster Using Kind**
Kind uses Docker containers to simulate Kubernetes nodes.

#### Step 1: Create a Kind Cluster Configuration File
Create a file named `kind-config.yaml` with the following content:
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
networking:
  podSubnet: "10.244.0.0/16" # Optional, ensures compatibility with certain CNI plugins
```

#### Step 2: Create the Cluster
Run the following command to create a multi-node cluster:
```bash
kind create cluster --config kind-config.yaml
```

Verify the cluster:
```bash
kubectl cluster-info
kubectl get nodes
```

You should see a control-plane node and two worker nodes.

---

### **3. Install a Networking Plugin**
To enable communication between pods, install a Container Network Interface (CNI) plugin such as **Calico** or **Flannel**.

#### Install Calico:
```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
```

---

### **4. Deploy an Application**
1. **Create a Deployment**:
   ```bash
   kubectl create deployment nginx --image=nginx
   ```

2. **Expose the Deployment**:
   ```bash
   kubectl expose deployment nginx --type=NodePort --port=80
   ```

3. **Find the Service NodePort**:
   ```bash
   kubectl get service nginx
   ```

4. **Access the Application**:
    - Use `http://<control-plane-IP>:<NodePort>` to access the application.
    - For local Docker, you can access it via `http://localhost:<NodePort>`.

---

### **5. Managing the Cluster**
- List all nodes:
  ```bash
  kubectl get nodes
  ```
- Check pod status:
  ```bash
  kubectl get pods -A
  ```
- Delete the cluster:
  ```bash
  kind delete cluster
  ```

---

### **6. Optional Enhancements**
- **Persistent Storage**: Use Persistent Volumes and Persistent Volume Claims for stateful applications.
- **Load Balancer**: Use tools like **MetalLB** to simulate a load balancer for services:
  ```bash
  kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.7/manifests/metallb.yaml
  ```
- **Monitoring**: Install Kubernetes dashboard or Prometheus for monitoring.

---

### **Why Use Kind for Multi-Node on Windows?**
Kind is the most practical way to simulate a multi-node Kubernetes cluster on Docker Desktop because:
- It runs Kubernetes nodes as Docker containers.
- It supports multi-node configurations out of the box.
- It is lightweight and ideal for testing and development.

### Details

Let’s break it down into the following tasks. Let me know which one you want to focus on:

1. **Setting up Kind with a multi-node cluster**:
    - Creating the `kind` configuration.
    - Setting up a networking plugin like **Calico** or **Flannel**.

2. **Deploying a sample application**:
    - Deploying a basic service (like Nginx) or a custom Spring Boot app.
    - Exposing the application to access it locally.

3. **Enhancing the setup**:
    - Setting up **Persistent Volumes** for data persistence.
    - Adding a **Load Balancer** (using **MetalLB**) to manage service traffic.
    - Installing monitoring tools (e.g., Kubernetes Dashboard, Prometheus, Grafana).

4. **Troubleshooting**:
    - Debugging issues with the cluster setup.
    - Verifying pod-to-pod communication.

### Deploying a spring boot application

Deploying a **Spring Boot application** on a multi-node **Kind Kubernetes cluster** involves the following steps:

---

### **Step 1: Prepare Your Spring Boot Application**

#### Create a Spring Boot Project
- Use [Spring Initializr](https://start.spring.io/) to generate a new project with these dependencies:
    - **Spring Web** (for REST APIs).
    - **Spring Boot DevTools** (optional for local development).
    - **Actuator** (optional for health checks).

#### Build the Application
1. Add your application logic.
2. Build the JAR file using Gradle or Maven:
   ```bash
   ./gradlew clean build
   ```
   The JAR file will be located in `build/libs`.

---

### **Step 2: Create a Docker Image**

#### Write a Dockerfile
Create a `Dockerfile` in the project root:
```dockerfile
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY build/libs/*.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

#### Build and Tag the Docker Image
1. Build the Docker image:
   ```bash
   docker build -t spring-boot-app:1.0 .
   ```

2. Verify the image:
   ```bash
   docker images
   ```

---

### **Step 3: Load the Docker Image into the Kind Cluster**

Since Kind nodes don't have access to your local Docker images by default, you must load the image into the cluster:

```bash
kind load docker-image spring-boot-app:1.0
```

---

### **Step 4: Create Kubernetes Manifests**

#### Deployment YAML
Create a file `deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: spring-boot-app
spec:
  replicas: 2
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
Create a file `service.yaml`:
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

### **Step 5: Deploy the Application**

#### Apply the Manifests
1. Apply the deployment and service:
   ```bash
   kubectl apply -f deployment.yaml
   kubectl apply -f service.yaml
   ```

2. Verify that pods are running:
   ```bash
   kubectl get pods
   ```

3. Check the service:
   ```bash
   kubectl get service spring-boot-app
   ```

---

### **Step 6: Access the Application**

#### Find the Service's NodePort
Get the `NodePort` value:
```bash
kubectl get service spring-boot-app
```

- The output will show a port like `30001`.
- Access the application in your browser:
    - `http://<control-plane-IP>:<NodePort>`
    - For local Kind clusters, you can use `http://localhost:<NodePort>`.

---

### **Step 7: Verify Logs and Debugging**

1. **Check Logs**:
   ```bash
   kubectl logs -f <pod-name>
   ```

2. **Describe Resources**:
   ```bash
   kubectl describe pod <pod-name>
   kubectl describe service spring-boot-app
   ```

---

### **Optional Enhancements**

1. **Persistent Storage**: Use a Persistent Volume and Claim for saving data.
2. **Load Balancer**: Install **MetalLB** for a simulated load balancer in Kind:
   ```bash
   kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.7/manifests/metallb.yaml
   ```
3. **Health Probes**: Add readiness and liveness probes in the `deployment.yaml`:
   ```yaml
   readinessProbe:
     httpGet:
       path: /actuator/health
       port: 8080
     initialDelaySeconds: 5
     periodSeconds: 10
   livenessProbe:
     httpGet:
       path: /actuator/health
       port: 8080
     initialDelaySeconds: 15
     periodSeconds: 20
   ```

---
