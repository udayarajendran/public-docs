
# Proxmox - Installation, Cluster Setup, Ceph Setup and HA Setup

## Overview

This document outlines the steps required to install Proxmox, set up a cluster, configure Ceph, and enable High Availability (HA) across three nodes.

### Requirements

- **3 Nodes** with Proxmox installed.
- **Secondary Drives** in each of the nodes.

### Test Environment Nodes

- **Node 1:** `192.168.1.1` - `0`
- **Node 2:** `192.168.1.2` - `1`
- **Node 3:** `192.168.1.3` - `2`

> **Note:** Replace the IP addresses with your actual node IPs as needed.

### Network Configuration

During installation, assign static IP addresses to each node. Below is an example configuration for `/etc/network/interfaces`:

```bash
auto lo
iface lo inet loopback

iface enp1s0 inet manual

auto vmbr0
iface vmbr0 inet static
    address 192.168.1.1/24
    gateway 192.168.1.254
    bridge-ports enp1s0
    bridge-stp off
    bridge-fd 0

source /etc/network/interfaces.d/*
```

### Detecting and Formatting Disks

To detect all disks, use:

```bash
lsblk
```

Your primary drive where Proxmox is installed will likely be `/dev/sda`, and additional drives such as `/dev/sdb`, `/dev/sdc`, etc., will serve as secondary drives. In this setup, `/dev/sdb` is used as the secondary drive.

### Formatting the Secondary Drive

1. Start by entering the `fdisk` utility:

    ```bash
    fdisk /dev/sdb
    ```

2. At the prompt, create a new GPT partition table:

    ```bash
    g
    ```

3. Create a new partition:

    ```bash
    n
    ```

4. Follow the prompts to use the entire disk, then write the changes:

    ```bash
    w
    ```

Repeat the above steps for all nodes.

### Cluster Setup

The cluster will be set up via the Proxmox GUI.

1. **Create Cluster on Node 1:**
    - Navigate to `Datacenter` → `Create Cluster`.
    - Name your cluster and leave the rest as default.
2. **Join Cluster on Nodes 2 and 3:**
    - On Node 1, go to `Datacenter` → `Cluster` → `Join Information` and copy the provided information.
    - On Nodes 2 and 3, go to `Datacenter` → `Cluster` → `Join Cluster`, paste the copied information, and complete the setup by providing the required password and selecting the appropriate network link.

Once complete, you will be able to manage all three nodes from a single interface.

### Ceph Setup

Ceph provides scalable storage solutions for your cluster. Follow these steps to install and configure Ceph across all nodes.

### Enabling Proxy for Installation

If you're behind a proxy, configure it to install Ceph:

1. Edit `/etc/environment` to add proxy settings:

    ```bash
    http_proxy="http://proxyserver_ip:port"
    https_proxy="http://proxyserver_ip:port"
    ftp_proxy="http://proxyserver_ip:port"
    no_proxy="localhost,127.0.0.1"
    ```

2. Source the environment:

    ```bash
    source /etc/environment
    ```

3. Configure APT proxy settings in `/etc/apt/apt.conf.d/95proxies`:

    ```bash
    Acquire::http::Proxy "http://proxyserver_ip:port/";
    Acquire::https::Proxy "http://proxyserver_ip:port/";
    ```

### Installing Ceph

- Navigate to each node → Ceph installation and follow the prompts.
- Select "No-Subscription" and the latest version "Reef."
- Configure both the public network and the cluster network - ideally, we would want different network configurations for this (can be the same for test environments).
- Remove the proxy settings after the installation is complete.

### Creating OSD Disks for Ceph (Ceph OSD)

Before creating OSD disks, ensure they are formatted correctly:

1. Remove any active device mapper entries:

    ```bash
    dmsetup ls
    dmsetup remove /dev/mapper/your_device_mapper_entry
    ```

2. Wipe the disk:

    ```bash
    wipefs -a /dev/sdb
    sgdisk --zap-all /dev/sdb
    dd if=/dev/zero of=/dev/sdb bs=1M count=10
    ```

3. Recheck the disk status:

    ```bash
    lsblk /dev/sdb
    ```

4. Add the disk as a new OSD:

    ```bash
    ceph-volume lvm create --data /dev/sdb
    ```

Go to Node → Ceph → OSD → Create OSD, and select your secondary disk from the drop-down menu.

### Ceph Monitor

Ceph monitors ensure the health and status of the cluster. Set them up as follows:

1. Navigate to Node → Ceph → Monitor.
2. The first node will already have a monitor created. Create monitors on the other nodes and follow the prompts to complete the setup.

### Ceph Pool

A Ceph pool shares the OSD disk across all nodes. To create a pool:

1. Navigate to Node → Ceph → Pool → Create Pool.
2. Follow the prompts to complete the setup.

### High Availability (HA) Setup

Setting up HA ensures that services remain available even if a node fails.

1. Go to `Datacenter` → `HA` → `Groups` → `Create`.
2. Follow the prompts to create an HA group and add all nodes to this group.

This concludes the setup process for Proxmox, Ceph, and High Availability in a clustered environment. Following these steps will provide a robust and resilient infrastructure.
