To install Docker on a Linux system, the process varies slightly depending on your
specific distribution. Below is a general guide that covers the standard commands used
across various Linux distributions. For distribution-specific instructions, please refer
to the official Docker
documentation. ([Docker Documentation](https://docs.docker.com/engine/install/?utm_source=chatgpt.com))

**1. Uninstall Old Versions (if any):**

Before installing Docker, it's advisable to remove any older versions to prevent
conflicts.

```bash
sudo apt-get remove docker docker-engine docker.io containerd runc
```

**2. Update Package Index:**

Ensure your package index is up-to-date.

```bash
sudo apt-get update
```

**3. Install Required Packages:**

Install packages that allow `apt` to use repositories over HTTPS.

```bash
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

**4. Add Docker’s Official GPG Key:**

Add Docker's official GPG key to your system.

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

**5. Set Up the Docker Repository:**

Add Docker's official repository to your package source list.

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

**6. Install Docker Engine:**

Update the package index again and install Docker Engine along with related components.

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

**7. Start and Enable Docker Service:**

Start the Docker service and enable it to run on system startup.

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

**8. Verify Installation:**

Run a test Docker container to ensure everything is set up correctly.

```bash
sudo docker run hello-world
```

**Optional: Manage Docker as a Non-Root User**

To run Docker commands without `sudo`, add your user to the Docker group.

```bash
sudo usermod -aG docker $USER
```

After running this command, log out and log back in to apply the changes.

**Note:** The above commands are tailored for Debian-based distributions like Ubuntu. For
other distributions such as Fedora, CentOS, or Arch Linux, the package manager commands
and repository setup will differ. Please consult
the [official Docker installation guide](https://docs.docker.com/engine/install/) for
instructions specific to your distribution.

For a visual walkthrough, you might find this tutorial helpful:

[How To Install Docker on Ubuntu 24.04 LTS](https://www.youtube.com/watch?v=J4dZ2jcpiP0&utm_source=chatgpt.com)
