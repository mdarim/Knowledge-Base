To install Docker on Fedora 41, you have two primary options: using Fedora's official
repositories or Docker's own repositories. Here's a step-by-step guide for both methods:

**Option 1: Installing Docker from Fedora's Official Repositories**

1. **Update Your System:**
   ```bash
   sudo dnf update -y
   ```

2. **Install Docker Packages:**
   Fedora provides Docker-related packages with specific naming conventions. Install the
   necessary packages using:
   ```bash
   sudo dnf install -y moby-engine docker-cli docker-compose
   ```
    - `moby-engine`: The Docker Engine package.
    - `docker-cli`: The Docker command-line interface.
    - `docker-compose`: Tool for defining and running multi-container Docker applications.

3. **Start and Enable Docker Service:**
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

4. **Verify Installation:**
   Run a test container to ensure Docker is installed correctly:
   ```bash
   sudo docker run hello-world
   ```

**Option 2: Installing Docker from Docker's Official Repository**

1. **Uninstall Conflicting Packages:**
   Remove any existing Docker packages that might conflict:
   ```bash
   sudo dnf remove docker \
                 docker-client \
                 docker-client-latest \
                 docker-common \
                 docker-latest \
                 docker-latest-logrotate \
                 docker-logrotate \
                 docker-selinux \
                 docker-engine-selinux \
                 docker-engine
   ```

2. **Install Required Packages:**
   ```bash
   sudo dnf install -y dnf-plugins-core
   ```

3. **Add Docker's Official Repository:**
   ```bash
   sudo dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo
   ```

4. **Install Docker Engine:**
   ```bash
   sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```

5. **Start and Enable Docker Service:**
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

6. **Verify Installation:**
   Run a test container to ensure Docker is installed correctly:
   ```bash
   sudo docker run hello-world
   ```

**Post-Installation Steps:**

- **Running Docker as a Non-Root User:**
  By default, Docker commands need to be run with `sudo`. To run Docker as a non-root
  user:
    1. Create the `docker` group if it doesn't exist:
       ```bash
       sudo groupadd docker
       ```
    2. Add your user to the `docker` group:
       ```bash
       sudo usermod -aG docker $USER
       ```
    3. Log out and log back in to apply the changes.

**Alternative: Using Podman**

Fedora also offers `podman`, a daemonless container engine that is compatible with Docker
commands. For many use cases, `podman` can serve as a drop-in replacement for Docker. To
install `podman`:

```bash
sudo dnf install -y podman
```

You can then use `podman` similarly to Docker:

```bash
podman run hello-world
```

For more detailed information, refer to the official Docker installation guide for
Fedora. ([Docker Documentation](https://docs.docker.com/engine/install/fedora/?utm_source=chatgpt.com))

Additionally, you might find this video tutorial helpful:

[How to Install Docker Desktop in Fedora 41 Gnome 47](https://www.youtube.com/watch?v=GWPGETcAbuU&utm_source=chatgpt.com)
 
