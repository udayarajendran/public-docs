
# Setting Up a Local Repository on Rocky Linux 8.7

## Step 1: Prepare Your Server

Ensure your server has enough storage to hold the repository. You'll also need to have access to the ISO file, either downloaded or available on your network.

1. **Download the ISO:**
    - You can download it from the official site.
2. **Install necessary packages:**
   You need the `createrepo` package to create the repository.
    
    ```bash
    sudo dnf install createrepo
    ```
    
## Step 2: Create Directory for the Repository

1. **Create a directory where you will store the repository files:**
    
    ```bash
    sudo mkdir -p /var/localrepo/rockylinux_8.7
    ```
    
2. **Mount the ISO**
   If you have the Rocky Linux 8.7 ISO file, mount it to a temporary directory. (Assuming /root/transfer/ is where the ISO is located)
    
    ```bash
    sudo mount -o loop /root/transfer/rockylinux-8.7-x86_64-dvd.iso /mnt
    ```
    
3. **Copy files from the ISO to the repository directory:**
    
    ```bash
    sudo cp -r /mnt/* /var/localrepo/rockylinux_8.7
    ```
    
4. **Unmount the ISO after copying:**
    
    ```bash
    sudo umount /mnt
    ```
    
## Step 3: Create the Repository Metadata

1. **Go to the directory where you copied the files:**
    
    ```bash
    cd /var/localrepo/rockylinux_8.7
    ```
    
2. **Initialize the repository metadata:**
    
    ```bash
    sudo createrepo .
    ```
    
   This command will create the necessary repository metadata files in the directory.

## Step 4: Configure Nginx to Serve the Repository

We would use Nginx to serve the repository to other workstations over HTTP.

1. **Install Nginx:**
    
    ```bash
    sudo dnf install nginx
    ```
    
2. **Create a configuration for your local repo:**
   Open the Nginx configuration file or create a new one under `/etc/nginx/conf.d/`.
    
    ```bash
    sudo nano /etc/nginx/conf.d/localrepo.conf
    ```
    
3. **Add the following content to serve the repository:**
    
    ```
    server {
        listen 80;
        server_name <YOUR_IP_ADDRESS>; # Use the IP address or the hostname
    
        location /rockylinux_8.7/ {
            alias /var/localrepo/rockylinux_8.7/;
            autoindex on;
        }
    }
    ```
    
4. **Start and enable Nginx:**
    
    ```bash
    sudo systemctl start nginx
    sudo systemctl enable nginx
    ```
    
5. **Check Nginx status:**
    
    ```bash
    sudo systemctl status nginx
    ```
    
6. **Open firewall ports if needed:**
   If the firewall isn’t disabled, open HTTP protocol to serve the repository over port number 80:
    
    ```bash
    sudo firewall-cmd --permanent --add-service=http
    sudo firewall-cmd --reload
    ```
    
## Step 5: Configure Clients to Use the Local Repository

On each client machine, you need to point the `dnf` package manager to use the local repository.

1. **Create a repo file:**
    
    ```bash
    sudo nano /etc/yum.repos.d/localrepo.repo
    ```
    
2. **Add the following content:**
    
    ```
    [localrepo]
    name=Local Rocky Linux 8.7 Repo
    baseurl=http://<YOUR_IP_ADDRESS>/rockylinux_8.7/
    enabled=1
    gpgcheck=0
    priority=1 # Use this if you still prefer to use external repository but prioritize local repo
    ```
    
3. **Clean `dnf` cache and test the repository:**
    
    ```bash
    sudo dnf clean all
    sudo dnf repolist
    ```
    
This will show the local repository in the list. You can now install packages from the local repository.
