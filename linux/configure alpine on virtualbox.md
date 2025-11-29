

## virtualbox alpine mount a folder

To mount a shared folder in Alpine Linux running on VirtualBox, follow these steps:

1. **Enable the Community Repository**:
   Ensure the Community repository is enabled in Alpine's `/etc/apk/repositories` file. If
   it's not enabled, add it by running:
   ```sh
   echo "https://dl-cdn.alpinelinux.org/alpine/edge/community" | tee -a /etc/apk/repositories
   ```

2. **Install Required Packages**:
   Install the necessary packages for VirtualBox Guest Additions:
   ```sh
   apk update
   apk add virtualbox-guest-additions linux-virt
   ```

3. **Reboot the System**:
   Reboot Alpine to load the new modules:
   ```sh
   reboot
   ```

4. **Create a Mount Point**:
   Create a directory where the shared folder will be mounted:
   ```sh
   mkdir -p /mnt/shared
   ```

5. **Mount the Shared Folder**:
   Use the `mount` command to mount the shared folder. Replace `vbox_shared` with the name
   of your shared folder as configured in VirtualBox:
   ```sh
   mount -t vboxsf vbox_shared /mnt/shared
   ```

6. **Automate Mounting at Boot**:
   To automatically mount the shared folder at boot, add the following line to
   `/etc/fstab`:
   ```sh
   vbox_shared /mnt/shared vboxsf defaults 0 0
   ```
   Additionally, ensure the `vboxsf` module is loaded at boot by creating a file
   `/etc/modules-load.d/vboxsf.conf` with the following content:
   ```
   vboxsf
   ```
   This configuration ensures that the shared folder is mounted automatically each time
   the system starts.

For more detailed information, refer to the Alpine Linux Wiki on VirtualBox shared
folders. ([Alpine Linux](https://wiki.alpinelinux.org/wiki/VirtualBox_shared_folders?utm_source=chatgpt.com))

## how to configure Bridged Adapter on virtualbox alpine so The VM appears as a separate device on your network.

To configure your Alpine Linux virtual machine (VM) in VirtualBox to use a Bridged
Adapter, allowing it to appear as a separate device on your network, follow these steps:

1. **Shut Down the VM:**
    - Ensure your Alpine Linux VM is powered off before making network configuration
      changes.

2. **Access VM Settings:**
    - In VirtualBox Manager, select your Alpine VM.
    - Click on the **Settings** button.

3. **Configure Network Adapter:**
    - Navigate to the **Network** section.
    - Under the **Adapter 1** tab (or another adapter if you prefer), ensure **Enable
      Network Adapter** is checked.
    - Set **Attached to:** to **Bridged Adapter**.
    - In the **Name:** dropdown, select the network interface of your host machine that
      you want to bridge to (e.g., `eth0` for wired connections or `wlan0` for wireless).
    - Click on the **Advanced** dropdown to expand additional settings.
    - For **Adapter Type**, select **Paravirtualized Network (virtio-net)**. This adapter
      type is known to work well with Alpine Linux and can help in obtaining an IP address
      from your network's DHCP
      server. ([quan.hoabinh.vn](https://quan.hoabinh.vn/post/2018/12/let-virtualbox-bridge-network-adapter-work-with-alpine-linux-guest?utm_source=chatgpt.com))

4. **Save and Start the VM:**
    - Click **OK** to save your settings.
    - Start your Alpine Linux VM.

5. **Configure Network Settings in Alpine Linux:**
    - Once the VM is running, log in to your Alpine Linux system.
    - To automatically obtain an IP address via DHCP, run:
      ```sh
      setup-interfaces
      ```
    - Follow the prompts to configure your network interface (typically `eth0`) to use
      DHCP.
    - After configuration, restart the networking service:
      ```sh
      /etc/init.d/networking restart
      ```
    - To verify that your VM has obtained an IP address, run:
      ```sh
      ip addr
      ```
      Look for the `inet` entry under your network interface (e.g., `eth0`) to see the
      assigned IP address.

**Important Considerations:**

- **Wireless Connections:** If your host machine is connected to the network via Wi-Fi, be
  aware that some wireless adapters and network configurations may not support bridged
  networking. In such cases, the VM might not receive an IP address from the DHCP
  server. ([Reddit](https://www.reddit.com/r/AlpineLinux/comments/vmonw8/bridged_wifi_network_in_virtual_box_wont_getting/?utm_source=chatgpt.com))

- **DHCP Server Availability:** Ensure that your network has an active DHCP server to
  assign an IP address to your VM. If a DHCP server isn't available, you'll need to
  configure a static IP address within your Alpine Linux VM.

By following these steps, your Alpine Linux VM should function as a separate device on
your network, with its own IP address, facilitating SSH access and other network
interactions. 
