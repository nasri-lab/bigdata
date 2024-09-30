
# Setting Up VirtualBox VMs to Use Bridged Adapter Network Mode

This guide explains how to configure each virtual machine (VM) in VirtualBox to use the **Bridged Adapter** network mode, allowing VMs to communicate over the local network.

---

## Steps to Configure Bridged Adapter in VirtualBox

### Step 1: Power Off the VM (if running)
- If the VM is running, shut it down. You can do this by:
  1. Clicking on the VM in the VirtualBox main window.
  2. Choosing **Close** → **Power Off**.

### Step 2: Open VM Settings
1. In the VirtualBox main window, select the VM you want to configure (e.g., `namenode`, `datanode1`, or `datanode2`).
2. Click on the **Settings** button (the gear icon).

### Step 3: Navigate to the Network Settings
1. In the **Settings** window, click on the **Network** tab.
2. You will see a drop-down menu labeled **Attached to**.

### Step 4: Select "Bridged Adapter"
1. From the **Attached to** drop-down, select **Bridged Adapter**.
2. In the **Name** field, select the network interface on your host machine that is connected to your local network. This could be `enp0s3` (for wired Ethernet) or `wlp2s0` (for Wi-Fi), depending on your host's configuration.
   
   - For Ethernet (wired connection): Choose the **wired interface** (often `eth0` or similar).
   - For Wi-Fi: Choose the **wireless interface** (often `wlan0` or similar).

### Step 5: Enable Promiscuous Mode (Optional)
- For testing environments where network discovery might be required, you can set **Promiscuous Mode** to **Allow All**. However, this step is optional.

### Step 6: Save the Network Configuration
1. After making the above changes, click **OK** to save the settings.

### Step 7: Repeat for All VMs
- Repeat steps 2 to 6 for each VM you are configuring (`namenode`, `datanode1`, and `datanode2`).

### Step 8: Start the VMs
- Once you have configured the Bridged Adapter on all VMs, start each VM one by one from the VirtualBox main window by selecting the VM and clicking **Start**.

---

## Verify Network Communication Between VMs

1. **Log in to each VM** and check the IP addresses assigned using the command:
   ```bash
   ifconfig
   ```
   You should see a valid IP address in the range of your local network (e.g., `192.168.1.x`).

2. **Ping Test**: Try pinging one VM from another to check if they can communicate.
   - On `namenode`, ping `datanode1`:
     ```bash
     ping 192.168.1.101
     ```
   - On `datanode1`, ping `datanode2`:
     ```bash
     ping 192.168.1.102
     ```

---

## What Bridged Adapter Does

- **Bridged Adapter** connects the VM directly to the host machine’s network interface. This allows the VM to behave as if it is part of the same physical network as the host, obtaining an IP address from the network's DHCP server and being visible to other devices on the network.

---

By following these steps, your VMs will successfully communicate over the local network using the Bridged Adapter mode.
