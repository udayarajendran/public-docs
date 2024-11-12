# SaltStack Installation and Configuration

This guide details the steps to install SaltStack using `pip` on a system without a native Salt package manager and the configuration of the Salt master and minion.

### Step 1: Update System Packages
```bash
dnf update -y
```
- **Explanation**: Updates the system packages to ensure you have the latest software and security updates.

### Step 2: Verify Python Installation
```bash
python --version
```
- **Explanation**: Verifies the version of Python installed on the system, as Salt requires Python.

### Step 3: Install `pip` for Python
```bash
wget https://bootstrap.pypa.io/get-pip.py
python get-pip.py
```
- **Explanation**: Downloads the `get-pip.py` script and runs it to install `pip`. `pip` is the Python package manager needed to install Salt.

### Step 4: Install Development Tools
```bash
dnf install gcc python3-devel make libffi-devel openssl-devel
```
- **Explanation**: Installs necessary development tools and libraries required to build and install Salt from `pip`. These libraries help handle encryption and other dependencies for Salt.

### Step 5: Install Salt Using `pip`
```bash
pip install salt
```
- **Explanation**: Installs Salt using `pip`. This method is used as an alternative to package managers (e.g., `dnf` or `apt`), especially when Salt is not available as a package in the repository.

### Step 6: Create Directories for Configuration Files
```bash
mkdir -p /etc/salt/master.d
mkdir -p /etc/salt/minion.d
```
- **Explanation**: Creates directories for storing configuration files for both the Salt master and minion. The `.d` directories allow modular configuration.

### Step 7: Configure Salt Master
```bash
nano /etc/salt/master.d/roots.conf
```
Add the following content:
```yaml
file_roots:
  base:
    - /srv/salt/base
```
- **Explanation**: Opens the `roots.conf` file in a text editor to configure the file roots for the Salt master. The `file_roots` configuration tells the master where to find Salt state files. Here, `/srv/salt/base` is set as the base directory for Salt states.

#### Verifying the Master Configuration
```bash
cat /etc/salt/master.d/roots.conf
```
- **Explanation**: Displays the content of the master configuration file to verify the file roots are correctly set.

### Step 8: Configure Salt Minion
```bash
nano /etc/salt/minion.d/master.conf
```
Add the following content:
```yaml
master: localhost
```
- **Explanation**: Opens the `master.conf` file to specify the address of the Salt master. Here, `localhost` is specified, which is common for testing setups where the Salt master and minion run on the same system.

#### Verifying the Minion Configuration
```bash
cat /etc/salt/minion.d/master.conf
```
- **Explanation**: Displays the content of the minion configuration file to verify the master is set to `localhost`.

### Step 9: Start both Salt Master and Minion
```bash
salt-master -d
salt-minion -d
```

- **Explanation**: This should start salt master and minion deamon services.


