# KASM - Multi Server Installation (DB, WebApp and Agent/Agents)

Instructions for each server role in a Kasm Workspaces deployment and the order in which you should install them.

### **1. Database Server**

**Role:** This server hosts the database and Redis services. It needs to be set up before the Web App server because the Web App server will connect to this database.

### **Installation Steps:**

1. **Prepare the Server:**
    - **Update and configure the server:**
        
        ```bash
        sudo dnf update -y
        sudo hostnamectl set-hostname kasm-db
        sudo setenforce 0
        sudo sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
        sudo systemctl stop firewalld
        sudo systemctl disable firewalld
        sudo dnf install -y epel-release wget git unzip
        
        ```
        
    - **Install Docker and Docker Compose:**
        
        ```bash
        sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
        sudo dnf install -y docker-ce docker-ce-cli containerd.io
        sudo systemctl start docker
        sudo systemctl enable docker
        
        sudo curl -L "https://github.com/docker/compose/releases/download/$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep -oP '"tag_name": "\K(.*)(?=")')/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
        sudo chmod +x /usr/local/bin/docker-compose
        
        docker --version
        docker-compose --version
        
        ```
        
2. **Install Kasm Workspaces Database Role:**
    - **Download and extract Kasm Workspaces:**
        
        ```bash
        cd /tmp
        curl -O https://kasm-static-content.s3.amazonaws.com/kasm_release_1.15.0.06fdc8.tar.gz
        tar -xf kasm_release_1.15.0.06fdc8.tar.gz
        
        ```
        
    - **Run the installation script:**
        
        ```bash
        sudo bash kasm_release/install.sh --role db --database-user [REDACTED] --database-name [REDACTED]
        
        ```
        
    - **Replace `[DATABASE_USERNAME]` and `[DATABASE_NAME]` with the desired values. If not provided, defaults will be used.**

### **2. Web App Server**

**Role:** This server hosts the Web Application and Manager services. It must be installed after the Database server because it needs to connect to the database.

### **Installation Steps:**

1. **Prepare the Server:**
    - **Update and configure the server:**
        
        ```bash
        sudo dnf update -y
        sudo hostnamectl set-hostname kasm-web
        sudo setenforce 0
        sudo sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
        sudo systemctl stop firewalld
        sudo systemctl disable firewalld
        sudo dnf install -y epel-release wget git unzip
        
        ```
        
    - **Install Docker and Docker Compose:**
        
        ```bash
        sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
        sudo dnf install -y docker-ce docker-ce-cli containerd.io
        sudo systemctl start docker
        sudo systemctl enable docker
        
        sudo curl -L "https://github.com/docker/compose/releases/download/$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep -oP '"tag_name": "\K(.*)(?=")')/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
        sudo chmod +x /usr/local/bin/docker-compose
        
        docker --version
        docker-compose --version
        
        ```
        
2. **Install Kasm Workspaces Web App Role:**
    - **Download and extract Kasm Workspaces:**
        
        ```bash
        cd /tmp
        curl -O https://kasm-static-content.s3.amazonaws.com/kasm_release_1.15.0.06fdc8.tar.gz
        tar -xf kasm_release_1.15.0.06fdc8.tar.gz
        
        ```
        
    - **Run the installation script:**
        
        ```bash
        sudo bash kasm_release/install.sh --role app --db-hostname [REDACTED] --db-password [REDACTED] --redis-password [REDACTED] --database-user [REDACTED] --database-name [REDACTED]
        
        ```
        
    - **Replace placeholders with the values from your database server setup.**

### **3. Agent Server**

**Role:** This server hosts the Agent service that creates end-user sessions. It should be installed after the Web App server since it needs to connect to it.

### **Installation Steps:**

1. **Prepare the Server:**
    - **Update and configure the server:**
        
        ```bash
        sudo dnf update -y
        sudo hostnamectl set-hostname kasm-agent
        sudo setenforce 0
        sudo sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
        sudo systemctl stop firewalld
        sudo systemctl disable firewalld
        sudo dnf install -y epel-release wget git unzip
        
        ```
        
    - **Install Docker and Docker Compose:**
        
        ```bash
        sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
        sudo dnf install -y docker-ce docker-ce-cli containerd.io
        sudo systemctl start docker
        sudo systemctl enable docker
        
        sudo curl -L "https://github.com/docker/compose/releases/download/$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep -oP '"tag_name": "\K(.*)(?=")')/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
        sudo chmod +x /usr/local/bin/docker-compose
        
        docker --version
        docker-compose --version
        
        ```
        
2. **Install Kasm Workspaces Agent Role:**
    - **Download and extract Kasm Workspaces:**
        
        ```bash
        cd /tmp
        curl -O https://kasm-static-content.s3.amazonaws.com/kasm_release_1.15.0.06fdc8.tar.gz
        tar -xf kasm_release_1.15.0.06fdc8.tar.gz
        
        ```
        
    - **Run the installation script:**
        
        ```bash
        sudo bash kasm_release/install.sh --role agent --public-hostname [REDACTED] --manager-hostname [REDACTED] --manager-token [REDACTED]
        
        ```
        
    - **Replace placeholders with values from your Web App server setup.**

### **Order of Installation**

1. **Database Server** - Install this first because it needs to be available for the Web App server.
2. **Web App Server** - Install this next as it depends on the Database server.
3. **Agent Server** - Install this last since it needs to connect to the Web App server.

### **Additional Steps**

After installation, ensure that:

- The Web App server can access the Database server on ports 5432 and 6379.
- The Agent server can communicate with the Web App server on port 443.

This guide should help you set up Kasm Workspaces on Rocky Linux effectively.