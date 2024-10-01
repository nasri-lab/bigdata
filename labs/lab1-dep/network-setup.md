
# Lab 1 - Annexe A: Setting Up VirtualBox VMs to Use Bridged Adapter Network Mode

This guide explains how to configure each virtual machine (VM) in VirtualBox to use the **Bridged Adapter** network mode, allowing VMs to communicate over the local network.

---

## Steps to Configure Bridged Adapter in VirtualBox

### Step 1: Power Off the VM (if running)
- If the VM is running, shut it down. You can do this by:
  1. Clicking on the VM in the VirtualBox main window.
  2. Choosing **Close** → **Power Off**.

### Step 2: Open VM Settings
1. In the VirtualBox main window, select the VM you want to configure.
2. Click on the **Settings** button (the gear icon).

### Step 3: Navigate to the Network Settings
1. In the **Settings** window, click on the **Network** tab.
2. You will see a drop-down menu labeled **Attached to**.

### Step 4: Select "Bridged Adapter"
1. From the **Attached to** drop-down, select **Bridged Adapter**.
2. In the **Name** field, select the network interface on your host machine that is connected to your local network.
   
   - For Ethernet (wired connection): Choose the **wired interface** (often `eth0` or similar).
   - For Wi-Fi: Choose the **wireless interface** (often `wlan0` or similar).

### Step 5: Enable Promiscuous Mode (Optional)
- For testing environments where network discovery might be required, you can set **Promiscuous Mode** to **Allow All**. However, this step is optional, so let it to **Deny All**.

### Step 6: Save the Network Configuration
1. After making the above changes, click **OK** to save the settings.

### Step 7: Start the VM
- Once you have configured the Bridged Adapter on the VM, start it.

---

## What Bridged Adapter Does

- **Bridged Adapter** connects the VM directly to the host machine’s network interface. This allows the VM to behave as if it is part of the same physical network as the host, obtaining an IP address from the network's DHCP server and being visible to other devices on the network.

---

By following these steps, your VMs will successfully communicate over the local network using the Bridged Adapter mode.
