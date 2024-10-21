
# Mounting NFS Storage on Ubuntu Using Autofs

This guide explains how to mount an NFS (Network File System) storage on Ubuntu using `autofs`, allowing automatic mounting of NFS shares when accessed.

## Prerequisites
- Ensure you have `autofs` and `nfs-common` installed. If not, install them using:
  ```bash
  sudo apt update
  sudo apt install autofs nfs-common
  ```

## Step 1: Configure Autofs
1. **Edit the Autofs Master Configuration File**:
   Open `/etc/auto.master` in a text editor:
   ```bash
   sudo nano /etc/auto.master
   ```
   Add the following line at the end:
   ```plaintext
   /- /etc/auto.nfs --timeout=60
   ```

2. **Create the Autofs Map for NFS**:
   Create and edit the NFS configuration file:
   ```bash
   sudo nano /etc/auto.nfs
   ```
   Add the following line to define your NFS mount:
   ```plaintext
   /mnt/truenas -fstype=nfs,rw,nosuid,noexec truenas.domain.com:/mnt/share
   ```

## Step 2: Create the Mount Point
1. **Create the directory for the NFS share**:
   ```bash
   sudo mkdir -p /mnt/truenas
   ```

## Step 3: Restart Autofs
1. **Restart the autofs service** to apply the changes:
   ```bash
   sudo systemctl restart autofs
   ```

## Step 4: Test the Configuration
1. **Access the mount point** to trigger the NFS share to mount:
   ```bash
   ls /mnt/truenas
   ```

   If everything is configured correctly, the NFS share should mount automatically when you access it. You can check the mounted file systems with:
   ```bash
   df -h
   ```

## Example Configuration
Here’s a summary of your configuration:

1. **/etc/auto.master**:
   ```plaintext
   /- /etc/auto.nfs --timeout=60
   ```

2. **/etc/auto.nfs**:
   ```plaintext
   /mnt/truenas -fstype=nfs,rw,nosuid,noexec truenas.domain.com:/mnt/share
   ```

3. **Create the mount point**:
   ```bash
   sudo mkdir -p /mnt/truenas
   ```

## Notes
- Ensure that the NFS server (`truenas.domain.com`) is configured to allow access to the client IP where you are mounting.
- Check your firewall settings to allow NFS traffic if necessary.

This setup will allow you to automatically mount your NFS storage using `autofs` on Ubuntu.
