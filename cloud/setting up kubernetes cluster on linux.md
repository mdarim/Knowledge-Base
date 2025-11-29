Setting up Kubernetes on multiple physical machines involves creating a Kubernetes cluster with one or more master nodes (control plane) and several worker nodes. Here's a step-by-step guide:

---

### **Prerequisites**
1. **Hardware Requirements**:
    - Each machine should meet the minimum Kubernetes hardware requirements:
        - CPU: 2+ cores (recommended)
        - RAM: 2 GB+ (4 GB recommended for control plane)
        - Disk Space: 20 GB+ per node
    - Network connectivity between all nodes.

2. **Operating System**:
    - Use a Linux distribution (e.g., Ubuntu, CentOS, or Debian). Ensure all machines run the same OS version.

3. **Networking**:
    - Ensure all machines have static IPs or DHCP with reserved IP addresses.
    - Disable swap on all machines:
      ```bash
      sudo swapoff -a
      ```
      Add this line to `/etc/fstab` to make it permanent:
      ```plaintext
      # Comment out or remove any swap entry
      ```

4. **Install Required Tools**:
    - **Docker** or another container runtime (e.g., containerd or CRI-O).
    - **kubeadm**: For initializing the cluster.
    - **kubectl**: For interacting with the cluster.
    - **kubelet**: Kubernetes node agent.

---

### **Step 1: Install Kubernetes Dependencies on All Machines**
Run the following commands on each machine:

#### Update System
```bash
sudo apt-get update && sudo apt-get upgrade -y
```

#### Install Docker
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
sudo systemctl enable docker
```

#### Install kubeadm, kubelet, and kubectl
```bash
sudo apt-get update && sudo apt-get install -y apt-transport-https ca-certificates curl
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

---

### **Step 2: Initialize the Kubernetes Master Node**
Run this command only on the master node:

```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

#### Save the Join Command
- After initialization, `kubeadm` will provide a `kubeadm join` command.
- Copy this command—it will be used to add worker nodes to the cluster.

#### Configure kubectl for the Master Node
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

---

### **Step 3: Install a Pod Network Add-On**
To enable communication between pods, install a network plugin (e.g., **Flannel**):

```bash
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```

---

### **Step 4: Join Worker Nodes to the Cluster**
Run the `kubeadm join` command from Step 2 on each worker node. For example:

```bash
sudo kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

---

### **Step 5: Verify the Cluster**
On the master node, check the status of the nodes:

```bash
kubectl get nodes
```

You should see all nodes in the `Ready` state.

---

### **Step 6: Additional Setup**
1. **Enable kubectl Autocomplete** (Optional):
   ```bash
   echo "source <(kubectl completion bash)" >> ~/.bashrc
   source ~/.bashrc
   ```

2. **Install Metrics Server**:
   ```bash
   kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
   ```

3. **Set Up Storage** (Optional):
    - Install a dynamic storage provisioner like **NFS** or **Rook/Ceph** if persistent storage is required.

---

### **Step 7: Deploy an Application**
Deploy a simple application to verify the setup. For example:

```bash
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --type=NodePort --port=80
```

Access the application by finding the node's IP and the NodePort:

```bash
kubectl get services
```

---

### **Troubleshooting**
- Check node status:
  ```bash
  kubectl describe node <node-name>
  ```
- Check pod logs:
  ```bash
  kubectl logs <pod-name>
  ```
- Restart `kubelet` if a node is not joining:
  ```bash
  sudo systemctl restart kubelet
  ```

---

Would you like a more detailed guide on any specific step?
