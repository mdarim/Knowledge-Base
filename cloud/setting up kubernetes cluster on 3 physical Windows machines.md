Setting up a Kubernetes cluster across three physical Windows machines involves several
key steps to ensure a functional and efficient environment. Here's a comprehensive guide
to assist you through the process:

**1. Prepare the Windows Machines:**

- **Operating System:** Ensure all three machines are running a compatible version of
  Windows, such as Windows 10 Pro, Enterprise, or Windows Server editions, which support
  Hyper-V.

- **Hardware Requirements:** Each machine should have sufficient CPU, memory, and storage
  resources to handle the anticipated workloads.

**2. Enable Hyper-V:**

Hyper-V is a virtualization platform that allows you to create and manage virtual machines
on Windows.

- **Steps to Enable Hyper-V:**

    - Open **Control Panel** → **Programs** → **Turn Windows features on or off**.

    - Check the box for **Hyper-V** and click **OK**.

    - Restart the machine to apply the changes.

**3. Install Docker Desktop:**

Docker Desktop provides the necessary container runtime for Kubernetes.

- **Installation Steps:**

    - Download Docker Desktop from the official website.

    - Run the installer and follow the on-screen instructions.

    - During installation, ensure that the option to enable Kubernetes is selected.

    - After installation, start Docker Desktop and allow it to complete the Kubernetes
      setup.

**4. Configure Kubernetes Cluster:**

With Docker Desktop, you can set up a single-node Kubernetes cluster on each machine. To
create a multi-node cluster across the three machines, consider the following approach:

- **Using `kubeadm`:**

    - `kubeadm` is a tool that simplifies the process of setting up a Kubernetes cluster.

    - **Installation:**

        - Install `kubeadm`, `kubelet`, and `kubectl` on each machine.

        - Ensure that each machine has a compatible container runtime installed.

    - **Cluster Initialization:**

        - On the designated master node, initialize the cluster:

          ```bash
          kubeadm init --pod-network-cidr=10.244.0.0/16
          ```

        - Set up the local kubeconfig for administrative access:

          ```bash
          mkdir -p $HOME/.kube
          cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
          ```

    - **Join Worker Nodes:**

        - On the master node, generate the join command:

          ```bash
          kubeadm token create --print-join-command
          ```

        - Run the provided join command on each worker node to integrate them into the
          cluster.

**5. Set Up Networking:**

Kubernetes requires a networking solution to facilitate communication between pods.

- **Install a Pod Network Add-on:**

    - Apply a network add-on, such as Flannel or Calico, to enable pod networking:

      ```bash
      kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
      ```

**6. Verify Cluster Functionality:**

- **Check Node Status:**

    - On the master node, verify that all nodes are in the `Ready` state:

      ```bash
      kubectl get nodes
      ```

- **Deploy a Test Application:**

    - Deploy a simple application to ensure that the cluster is functioning correctly:

      ```bash
      kubectl create deployment hello-world --image=k8s.gcr.io/echoserver:1.4
      kubectl expose deployment hello-world --type=NodePort --port=8080
      ```

    - Access the application using the appropriate NodePort to confirm successful
      deployment.

**Additional Considerations:**

- **Resource Allocation:** Ensure that each machine has adequate resources allocated to
  handle the Kubernetes components and the workloads you plan to deploy.

- **Networking Configuration:** Verify that all machines can communicate with each other
  over the network, and configure any necessary firewall rules to allow Kubernetes
  traffic.

- **High Availability:** For production environments, consider setting up multiple master
  nodes and implementing load balancing to achieve high availability.

By following these steps, you can establish a functional Kubernetes cluster across three
physical Windows machines, enabling you to manage and deploy containerized applications
effectively. 
