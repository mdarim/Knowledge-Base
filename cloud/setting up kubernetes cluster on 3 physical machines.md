Setting up a Kubernetes cluster across three physical machines allows for a robust and scalable environment to deploy and manage your applications. Below is a step-by-step guide to achieve this setup:

**Prerequisites:**

1. **Three Physical Machines:**
    - **Master Node:** This machine will function as the control plane, managing the cluster.
    - **Worker Nodes:** Two machines will serve as worker nodes, running the workloads assigned by the master.

2. **Operating System:**
    - Install a compatible Linux distribution on all machines, such as Ubuntu 20.04 LTS or CentOS 7.9.

3. **Network Configuration:**
    - Ensure all machines can communicate with each other over the network.
    - Assign static IP addresses to each machine for consistent communication.

4. **Hardware Requirements:**
    - **Master Node:** At least 2 CPUs and 2GB of RAM.
    - **Worker Nodes:** At least 1 CPU and 1GB of RAM each.

**Installation Steps:**

1. **Update System Packages:**
    - On all machines, update the package lists:
      ```bash
      sudo apt-get update && sudo apt-get upgrade -y
      ```

2. **Install Docker:**
    - Kubernetes requires a container runtime; Docker is a popular choice.
    - Install Docker on all machines:
      ```bash
      sudo apt-get install -y docker.io
      ```
    - Start and enable Docker:
      ```bash
      sudo systemctl start docker
      sudo systemctl enable docker
      ```

3. **Disable Swap:**
    - Kubernetes requires swap to be disabled.
    - Turn off swap:
      ```bash
      sudo swapoff -a
      ```
    - To disable swap permanently, comment out the swap line in `/etc/fstab`.

4. **Install Kubernetes Components:**
    - On all machines, install `kubeadm`, `kubelet`, and `kubectl`:
      ```bash
      sudo apt-get install -y apt-transport-https curl
      curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
      cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
      deb https://apt.kubernetes.io/ kubernetes-xenial main
      EOF
      sudo apt-get update
      sudo apt-get install -y kubelet kubeadm kubectl
      sudo apt-mark hold kubelet kubeadm kubectl
      ```

5. **Initialize the Master Node:**
    - On the master node, initialize the cluster:
      ```bash
      sudo kubeadm init --pod-network-cidr=10.244.0.0/16
      ```
    - Set up the local kubeconfig for the `ubuntu` user:
      ```bash
      mkdir -p $HOME/.kube
      sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
      sudo chown $(id -u):$(id -g) $HOME/.kube/config
      ```

6. **Install a Pod Network Add-on:**
    - To allow communication between pods, install a network add-on like Flannel:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
      ```

7. **Join Worker Nodes to the Cluster:**
    - On the master node, retrieve the join command:
      ```bash
      kubeadm token create --print-join-command
      ```
    - Run the outputted join command on each worker node to join them to the cluster.

8. **Verify the Cluster:**
    - On the master node, check the status of the nodes:
      ```bash
      kubectl get nodes
      ```
    - All nodes should be in the `Ready` state.

By following these steps, you will have a functional Kubernetes cluster spanning three physical machines, ready to deploy and manage your applications.

For a visual walkthrough of setting up a 3-node Kubernetes cluster, you may find this video helpful:

[How to Setup a 3 Node Kubernetes Cluster for CKA Step by Step](https://www.youtube.com/watch?v=XJufs3ZZBVY) 
