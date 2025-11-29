Setting up a Kubernetes cluster on three machines without internet access requires
meticulous preparation to ensure all necessary components are available locally. Here's a
step-by-step guide to assist you through the process:

**1. Prepare the Environment:**

- **Operating System:** Ensure all three machines are running a compatible Linux
  distribution, such as CentOS or Ubuntu.

- **Network Configuration:** Verify that the machines can communicate with each other over
  the network, as this is crucial for cluster operations.

**2. Download Necessary Packages and Binaries:**

- **Kubernetes Binaries:** On a machine with internet access, download the required
  Kubernetes binaries (`kubeadm`, `kubelet`, and `kubectl`) for your specific OS and
  architecture.

- **Container Runtime:** Download the installation packages for a container runtime like
  Docker or containerd.

- **Additional Dependencies:** Identify and download any additional dependencies required
  by Kubernetes and your container runtime.

**3. Transfer Files to Offline Machines:**

- Use external storage devices or a local network share to transfer the downloaded
  packages and binaries to the offline machines.

**4. Install the Container Runtime:**

- On each machine, install the container runtime using the transferred packages.

- Ensure the service is enabled and started.

**5. Install Kubernetes Components:**

- Install `kubeadm`, `kubelet`, and `kubectl` on each machine using the downloaded
  binaries.

- Configure the package manager to recognize the local packages if necessary.

**6. Set Up a Local Repository (Optional):**

- To manage packages more efficiently, consider setting up a local repository on one of
  the machines.

- Configure the other machines to use this local repository for package installations and
  updates.

**7. Initialize the Kubernetes Cluster:**

- On the designated master node, initialize the cluster using `kubeadm`:

  ```bash
  kubeadm init --pod-network-cidr=10.244.0.0/16
  ```

- Set up the local kubeconfig for administrative access:

  ```bash
  mkdir -p $HOME/.kube
  cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  ```

**8. Install a Pod Network Add-on:**

- Download the manifest files for a pod network add-on (e.g., Flannel or Calico) on a
  machine with internet access.

- Transfer the files to the master node and apply them:

  ```bash
  kubectl apply -f <pod-network-manifest>.yaml
  ```

**9. Join Worker Nodes to the Cluster:**

- On the master node, generate the join command:

  ```bash
  kubeadm token create --print-join-command
  ```

- Run the provided join command on each worker node to integrate them into the cluster.

**10. Verify the Cluster:**

- On the master node, verify that all nodes are in the `Ready` state:

  ```bash
  kubectl get nodes
  ```

**Additional Considerations:**

- **Local Container Registry:** Set up a local container registry to host necessary
  container images, allowing the cluster to pull images without internet access.

- **Ongoing Maintenance:** Establish a process for updating packages and images by
  periodically downloading updates on an internet-connected machine and transferring them
  to the offline environment.

By following these steps, you can successfully set up a Kubernetes cluster on three
machines without internet access, ensuring a functional and self-sufficient environment. 
