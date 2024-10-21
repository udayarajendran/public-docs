# Setting up Jellyfin in a Proxmox LXC Container

This guide walks you through creating and configuring a Proxmox LXC container to install Jellyfin, an open-source media server.

### Prerequisites
- **Proxmox VE** installed.
- Access to Proxmox Web Interface.
- Ubuntu LXC template downloaded.

## Step 1: Create the LXC Container
1. **Login to Proxmox** and go to the Datacenter node.
2. **Create a new container** by clicking on `Create CT`.
3. Fill out the **Container details**:
   - Hostname: `jellyfin-ct`
   - Password: Set a root password.
4. **Select the template**:
   - Choose the Ubuntu template (recommended version: 22.04 Jammy).
5. **Allocate resources**:
   - Assign 6 GB disk space.
   - Set memory to `2048 MB` and CPU to `2 cores` (adjust as needed).
6. **Network settings**:
   - Set a static IP or configure DHCP, depending on your setup.
7. **Create the container**.

Once the container is created, start it and access the shell through the Proxmox console.

## Step 2: Update the Container
1. Update the package list to ensure everything is up-to-date:

    ```bash
    apt update
    ```

2. Install dependencies for handling repositories and HTTPS communication:

    ```bash
    apt install apt-transport-https ca-certificates curl gnupg2 software-properties-common
    ```

## Step 3: Add the Jellyfin Repository and GPG Key
1. **Remove any previous Jellyfin repository** (if present):

    ```bash
    rm /etc/apt/sources.list.d/jellyfin.list
    ```

2. **Add the Jellyfin repository for Ubuntu Jammy (22.04)**:
   
   First, ensure there are no old GPG keys, then add the new repository:

    ```bash
    curl -fsSL https://repo.jellyfin.org/ubuntu/jellyfin_team.gpg.key | gpg --dearmor -o /usr/share/keyrings/jellyfin.gpg
    echo "deb [signed-by=/usr/share/keyrings/jellyfin.gpg] https://repo.jellyfin.org/ubuntu jammy main" | tee /etc/apt/sources.list.d/jellyfin.list
    ```

## Step 4: Install Jellyfin
1. **Update the package list** again to recognize the new repository:

    ```bash
    apt update
    ```

2. **Install Jellyfin**:

    ```bash
    apt install jellyfin
    ```

## Step 5: Start and Enable Jellyfin
1. **Start the Jellyfin service**:

    ```bash
    systemctl start jellyfin
    ```

2. **Enable Jellyfin to start on boot**:

    ```bash
    systemctl enable jellyfin
    ```

## Step 6: Access the Jellyfin Web Interface
Once Jellyfin is installed and running, you can access it by navigating to the following URL in your web browser:

```
http://<container-ip>:8096
```

You can now configure Jellyfin through the web interface and begin organizing and streaming your media library.