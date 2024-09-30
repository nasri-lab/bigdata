
# Lab 1: Setting Up Unique Static IP Addresses for VirtualBox VMs

This lab explains how to assign unique static IP addresses to each VirtualBox VM to ensure they can communicate over the network.

---

## Prerequisites
- **VirtualBox** with Ubuntu Server installed on each VM.
- **Network Mode** set to **Bridged Adapter** (see [Bridged Adapter Setup](https://github.com/your-repo-name/hadoop-distributed-lab/blob/main/network-setup.md)).

---

## Step 1: Identify the Network Interface on Each VM
1. **Log in** to each VM.
2. Run the following command to list the network interfaces and find the active one (usually `enp0s3`):
   ```bash
   ip a
   ```
3. You should see output like this, showing the available interfaces:
   ```
   2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
       link/ether 08:00:27:53:8b:dc brd ff:ff:ff:ff:ff:ff
       inet 192.168.1.105/24 brd 192.168.1.255 scope global dynamic enp0s3
   ```
   - **Take note of the interface name** (in this example, `enp0s3`).

---

## Step 2: Configure Static IP Address
You’ll now configure the VM to have a unique static IP.

1. **Edit the network configuration file**:
   - Use the following command to open the network configuration file:
     ```bash
     sudo nano /etc/netplan/00-installer-config.yaml
     ```

2. **Modify the file to assign a static IP**:
   - Replace the content of the file with the following configuration, making sure to:
     - Replace the IP address with a **unique IP** for each VM (e.g., `192.168.1.100` for `namenode`, `192.168.1.101` for `datanode1`, and `192.168.1.102` for `datanode2`).
     - Keep the `gateway4` and `nameservers` consistent for all VMs (this is the gateway to your local network, typically your router’s IP).
     ```yaml
     network:
       version: 2
       renderer: networkd
       ethernets:
         enp0s3:
           dhcp4: no
           addresses:
             - 192.168.1.100/24   # Unique static IP for this VM
           gateway4: 192.168.1.1
           nameservers:
             addresses:
               - 8.8.8.8
     ```

3. **Repeat this process** for each VM, but ensure that:
   - The IP addresses are unique:
     - **VM1 (NameNode)**: `192.168.1.100`
     - **VM2 (DataNode1)**: `192.168.1.101`
     - **VM3 (DataNode2)**: `192.168.1.102`

4. **Save and exit**: Press `Ctrl + O` to save the file, and `Ctrl + X` to exit the editor.

---

## Step 3: Apply the Changes
1. After editing the configuration file, apply the changes to set the static IP:
   ```bash
   sudo netplan apply
   ```

2. To verify that the static IP was set, run:
   ```bash
   ip a
   ```
   You should now see the static IP address you assigned under the network interface (e.g., `192.168.1.100` for the NameNode).

---

## Step 4: Verify Network Connectivity
1. **Ping Test**: Try pinging the other VMs to ensure that they can communicate with each other.
   - On `namenode`, ping `datanode1`:
     ```bash
     ping 192.168.1.101
     ```
   - On `datanode1`, ping `datanode2`:
     ```bash
     ping 192.168.1.102
     ```
   If the pings are successful, the VMs are correctly configured with unique static IPs and are able to communicate over the network.

---

## Step 5: Update the `/etc/hosts` File
To make it easier to communicate between the VMs by hostname (instead of IP address), you can update the `/etc/hosts` file on each VM.

1. Open the `/etc/hosts` file:
   ```bash
   sudo nano /etc/hosts
   ```

2. Add the following entries, replacing with the correct IP addresses of your VMs:
   ```
   192.168.1.100 namenode
   192.168.1.101 datanode1
   192.168.1.102 datanode2
   ```

3. Save and exit the file. Now, instead of using IP addresses, you can use the hostnames (`namenode`, `datanode1`, `datanode2`) to communicate between VMs.

---

## Summary
- You’ve successfully configured unique static IP addresses for each VM, ensuring that they can communicate over the local network.
- You also verified the configuration using ping tests and updated the `/etc/hosts` file to allow communication by hostname.

This completes the lab on setting up unique static IP addresses for your VirtualBox VMs.
