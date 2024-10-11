# Ansible : Installation and Configuration on Rocky Linux Using Python

This document will guide you through the process of installing and configuring Ansible on Rocky Linux using Python. Ansible is an open-source automation tool that allows you to automate IT tasks, configuration management, application deployment, and more.

## **Prerequisites**

Before we begin, make sure you have the following prerequisites in place:

1. A running instance of Rocky Linux.
2. Python installed on your Rocky Linux server. Python 3 is preferred.

> **Note**: Using CentOS 7 for this is possible but requires the installation of Python 3.9 or above. The upgrade from Python 2.7.5 (preinstalled version) will also require updating paths, SSL certificates, etc. Therefore, Rocky Linux, which has the latest updates, is recommended.

## **Step 1: Update System Packages**

Let's start by ensuring that the system packages are up to date:

```bash
sudo yum update
```

## **Step 2: Check and Install Python and Pip**

Ansible requires Python on the host machine. If you are using Rocky Linux 8 or later, Python should already be installed. However, if it's not, you can install Python with the following command:

```bash
# To check if python/pip is installed
python --version
pip --version

# To install python 3.9.6
# Install required packages and dependencies
sudo yum groupinstall "Development Tools" -y
sudo yum install gcc open-ssl-devel bzip2-devel libffi-devel zlib-devel openssl-devel -y

# Download Python 3.9.6
wget https://www.python.org/ftp/python/3.9.6/Python-3.9.6.tgz

# Extract the package
sudo tar xzf Python-3.9.6.tgz

# Move to the directory
cd Python-3.9.6

# Compile the source code into an installation package
./configure --enable-optimizations 
make altinstall

# Verify if python is installed
python --version
```

We also need to install **`pip`**, the Python package manager, to easily install Ansible:

```bash
# Install pip for Python 3.9
wget https://bootstrap.pypa.io/get-pip.py
python3.9 get-pip.py

# Verify the installation
python3.9 --version # Or try using python --version
pip3.9 --version # Or try using pip --version
```

## **Step 3: Install Ansible**

Now that Python and pip are installed, let's proceed with installing Ansible:

```bash
# To install ansible
python -m pip install --user ansible
```

This will download and install the latest stable version of Ansible on your Rocky Linux server.

## **Step 4: Verify Ansible Installation**

After the installation is complete, you can verify that Ansible is installed correctly by checking its version:

```bash
# To check if ansible is installed right
ansible --version
python -m pip show ansible
```

This command will display the installed Ansible version and some other information about the installation.

## **Step 5: Configure Ansible**

Configuration of Ansible in the environment hosted on the machine with the hostname "control-node". In this environment, Ansible is set up to manage both Linux and Windows systems. Below are the configuration details for Ansible in this environment:

Creating the key files that are required to operate Ansible:

```bash
# Create Ansible directory for configuration management and navigate to the directory
mkdir /etc/ansible
cd /etc/ansible

# Create Ansible configuration file for project-wide settings
touch ansible.cfg 
# Create the 'hosts' file to define target hosts for Ansible operations
touch hosts
# Create the 'roles' directory to organize custom Ansible roles
touch roles
```

By default, Ansible does not require any additional configuration to work. However, if you want to customize some settings, you can create an **`ansible.cfg`** file.

The **`ansible.cfg`** file is the central configuration file for Ansible. It is located in the **`/etc/ansible/`** directory. This file contains various settings that influence how Ansible operates. Here are the key settings defined in this configuration file:

```bash
[defaults]

# Define the path for Ansible logs.
log_path = /etc/ansible/ansible.log

# Specify the path to the variables file.
vars_file = /etc/ansible/secret.yml
```

Explanation:

- **`log_path`**: This setting specifies the path to the Ansible log file. It collects logs for all tasks executed using Ansible, which can be useful for troubleshooting and auditing purposes.
- **`vars_file`**: This setting specifies the path to a variables file. In this environment, sensitive data and secrets are stored in a file called **`secret.yml`**, which is encrypted using ansible-vault.

## **Step 6: Inventory File (hosts)**

The **`hosts`** file serves as the inventory file for Ansible. It defines the target hosts and groups of hosts that Ansible will manage. In this environment, we have defined two groups: **`[linux]`** and **`[windows]`**. Here is the configuration of the **`hosts`** file:

```bash
# Inventory File (hosts)

# Linux Hosts Group
[linux]
#DEFINE LINUX HOSTS HERE#

[linux:vars]
# Set the SSH user for Linux hosts
ansible_user=ansibleuserlinux
# Define the SSH password for Linux hosts, encrypted and stored in 'secret.yml'
ansible_password="{{ linux_ansible_password }}"

# Windows Hosts Group
[windows]
#DEFINE WINDOWS HOSTS HERE#

[windows:vars]
# Set the WinRM user for Windows hosts
ansible_user=ansibleuserwindows
# Define the WinRM password for Windows hosts, encrypted and stored in 'secret.yml'
ansible_password="{{ windows_ansible_password }}"
# Configure the connection method for Windows hosts
ansible_connection=winrm
ansible_port=5985
# Ignore server certificate validation for WinRM
ansible_winrm_server_cert_validation=ignore
# Specify the WinRM transport method as NTLM
ansible_winrm_transport=ntlm
```

## **Roles Directory**

The **`roles`** directory is empty in this configuration, but it is intended to store custom Ansible roles. Ansible roles are collections of playbooks, templates, and other resources that make it easier to manage configuration tasks. You can create and organize roles in this directory as your automation needs evolve.

## **Step 7: Ansible Vault**

**Ansible Vault** is a critical feature of Ansible that provides data encryption and security for sensitive information, such as passwords, API keys, and other secret variables used in playbooks and roles. Here are its key importance and how to configure it:

### **Importance of Ansible Vault:**
1. **Data Security**: Ansible Vault encrypts variables and files, making it difficult for unauthorized users or processes to access confidential information.
2. **Compliance**: Helps meet compliance requirements for secure handling of sensitive data.
3. **Source Code Management**: Allows you to store secrets securely in version control systems like Git.
4. **Collaboration**: Teams can collaborate without exposing sensitive data to unauthorized users.
5. **Automation**: Seamlessly integrates into automation workflows.

### **How to Configure Ansible Vault:**

1. **Create an Encrypted File**:
    
    ```bash
    ansible-vault create secret.yml
    ```
    
2. **Edit an Encrypted File**:
    
    ```bash
    ansible-vault edit secret.yml
    ```
    
3. **Encrypt Existing Files**:
    
    ```bash
    ansible-vault encrypt existing_secret.yml
    ```
    
4. **Decrypt Files**:
    
    ```bash
    ansible-vault decrypt secret.yml
    ```
    
5. **Run Playbooks with Vaulted Variables**:
    
    When running playbooks that use vaulted variables, Ansible will prompt you for the passphrase to decrypt these variables. You can provide it interactively or use the **`--ask-vault-pass`** option to specify it in the command line.
```

## **References**

1. [Ansible Documentation](https://docs.ansible.com/ansible/latest/installation_guide/intro.html): Comprehensive guide on installing and configuring Ansible, including details for various operating systems.
   
2. [AnsiblePilot Guide](https://www.ansiblepilot.com/ansible-installation-guide-rocky-linux): Step-by-step instructions for installing Ansible on Rocky Linux, covering necessary dependencies and configurations.
   
3. [Rocky Linux Documentation](https://docs.rockylinux.org/guides/ansible/): Official Rocky Linux documentation providing insights into using Ansible effectively on the platform.