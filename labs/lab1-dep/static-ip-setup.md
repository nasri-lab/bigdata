
# Lab 1 - Annexe B: Setting Up Unique Static IP Addresses for VirtualBox VMs

This lab explains how to assign unique static IP addresses to each VirtualBox VM to ensure they can communicate over the network.

---

## Step 1: Identify the Network Interface on the VM
1. **Log in** to the VM.
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
     - Replace the IP address with a **unique IP** (e.g., `192.168.0.230` if this address is not assigned to another VM).
     - Keep the `gateway4` and `nameservers` consistent for this VMs (this is the gateway to your local network, typically your router’s IP).
     ```yaml
     network:
       version: 2
       renderer: networkd
       ethernets:
         enp0s3:
           dhcp4: no
           addresses:
             - 192.168.0.230/24   # Unique static IP for this VM
           gateway4: 192.168.0.1
           nameservers:
             addresses:
               - 8.8.8.8
     ```

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
   You should now see the static IP address you assigned under the network interface (e.g., `192.168.0.230` for the NameNode).

---

## Summary
- You’ve successfully configured unique static IP addresses for the VM, ensuring that they can communicate over the local network.

This completes the lab on setting up unique static IP addresses for your VirtualBox VMs.
