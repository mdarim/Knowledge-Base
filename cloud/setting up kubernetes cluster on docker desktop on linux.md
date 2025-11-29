Setting up a Kubernetes cluster on **Docker Desktop** across multiple machines is not directly supported since Docker Desktop is primarily designed for local, single-node Kubernetes development. However, you can achieve a multi-node Kubernetes cluster using **kubeadm** or a tool like **K3s**. If Docker Desktop must be used, you can simulate multi-machine setups with manual networking and a single machine cluster.

Here’s how to proceed with **multi-machine Kubernetes clusters** based on Docker containers and standard tools.

---

### **Option 1: Multi-Machine Kubernetes Using Kubeadm**
To create a Kubernetes cluster spanning multiple physical machines:

---

#### **Step 1: Prerequisites**
1. Install Docker Engine on each machine:
   ```bash
   sudo apt-get update
   sudo apt-get install -y docker.io
   sudo systemctl enable docker
   ```

2. Install Kubernetes tools (`kubeadm`, `kubectl`, `kubelet`) on each machine:
   ```bash
   sudo apt-get update
   sudo apt-get install -y apt-transport-https ca-certificates curl
   curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
   echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
   sudo apt-get update
   sudo apt-get install -y kubelet kubeadm kubectl
   sudo apt-mark hold kubelet kubeadm kubectl
   ```

3. Disable swap on all machines:
   ```bash
   sudo swapoff -a
   sudo sed -i '/ swap / s/^/#/' /etc/fstab
   ```

---

#### **Step 2: Initialize the Control Plane (Master Node)**
1. Run the following on one machine (the master node):
   ```bash
   sudo kubeadm init --pod-network-cidr=10.244.0.0/16
   ```

2. Save the `kubeadm join` command displayed after initialization. This will look like:
   ```bash
   kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
   ```

3. Configure `kubectl` for the master node:
   ```bash
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
   ```

---

#### **Step 3: Join Worker Nodes**
1. Run the saved `kubeadm join` command on each worker node to join the cluster:
   ```bash
   kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
   ```

2. Verify that all nodes are connected to the cluster:
   ```bash
   kubectl get nodes
   ```

---

#### **Step 4: Install a Pod Network Add-On**
Install a networking plugin like Flannel or Calico to enable pod communication:

- **Flannel**:
  ```bash
  kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
  ```

- **Calico**:
  ```bash
  kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
  ```

---

#### **Step 5: Deploy an Application**
1. Create a simple deployment to verify:
   ```bash
   kubectl create deployment nginx --image=nginx
   kubectl expose deployment nginx --type=NodePort --port=80
   ```

2. Access the application:
    - Use the `NodePort` to access the service:
      ```bash
      kubectl get service nginx
      ```
    - Visit `http://<node-ip>:<node-port>` in your browser.

---

### **Option 2: Lightweight Kubernetes with K3s**
If you prefer a simpler setup, use **K3s**, a lightweight Kubernetes distribution suitable for multi-machine clusters.

1. Install K3s on the master node:
   ```bash
   curl -sfL https://get.k3s.io | sh -
   ```

2. Get the join token:
   ```bash
   sudo cat /var/lib/rancher/k3s/server/node-token
   ```

3. Install K3s on each worker node, pointing to the master:
   ```bash
   curl -sfL https://get.k3s.io | K3S_URL=https://<master-ip>:6443 K3S_TOKEN=<token> sh -
   ```

4. Verify the cluster:
   ```bash
   kubectl get nodes
   ```

---

### **Option 3: Docker Desktop with Manual Networking**
If Docker Desktop is mandatory and multi-machine is needed, simulate a cluster:

1. Enable Docker Desktop’s Kubernetes on each machine.
2. Use Docker Swarm for inter-machine container communication.
3. Deploy services manually with multi-node orchestration.

---
