
# Hadoop HDFS Distributed Setup on Ubuntu (1 VM to clone into 3 VMs)

This lab demonstrates how to install and configure Hadoop HDFS in distributed mode on 1 Ubuntu virtual machine and then clone it to create the other VMs using VirtualBox.

## Prerequisites:
- **VirtualBox** installed on your machine.
- **Ubuntu Server ISO** (You can download from [Ubuntu](https://ubuntu.com/download/server)).
- **At least 8 GB of RAM** (allocated across the 3 VMs).

---

## Step 1: Create and Configure 1 Master Virtual Machine

### 1.1 Create the First VM (Master VM)
1. Open VirtualBox and create a new VM called `master`.
2. Assign **2 GB of RAM** and **2 CPU cores**.
3. Attach the **Ubuntu Server ISO** to the VM and start the installation.
4. Follow the Ubuntu installation process and install the minimal system (without GUI).
5. Set up a username and password for SSH access.

### 1.2 Install Java on the Master VM
Hadoop requires Java, so install OpenJDK on the Master VM:
   ```bash
   sudo apt update
   sudo apt install openjdk-8-jdk
   ```

### 1.3 Download and Install Hadoop on the Master VM
1. Download Hadoop on the Master VM:
   ```bash
   wget https://downloads.apache.org/hadoop/common/hadoop-3.3.0/hadoop-3.3.0.tar.gz
   ```
2. Extract Hadoop and move it to `/usr/local/hadoop`:
   ```bash
   tar -xvzf hadoop-3.3.0.tar.gz
   sudo mv hadoop-3.3.0 /usr/local/hadoop
   ```

### 1.4 Configure Environment Variables on the Master VM
1. On the Master VM, edit the `~/.bashrc` file to add the Hadoop environment variables:
   ```bash
   sudo nano ~/.bashrc
   ```
   Add the following lines at the end of the file:
   ```bash
   export HADOOP_HOME=/usr/local/hadoop
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
   export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
   ```
2. Apply the changes:
   ```bash
   source ~/.bashrc
   ```

### 1.5 Configure Network Settings (Static IP and Bridged Adapter)
1. **Set up a static IP** for this VM (see the [Static IP Setup Guide](https://github.com/your-repo-name/hadoop-distributed-lab/blob/main/static-ip-setup.md)).
2. Ensure that the network is set to **Bridged Adapter** mode (see [Bridged Adapter Setup](https://github.com/your-repo-name/hadoop-distributed-lab/blob/main/network-setup.md)).

---

## Step 2: Clone the Master VM to Create 2 DataNodes

### 2.1 Clone the Master VM
1. Once the Master VM is fully configured, **shut it down**.
2. In the VirtualBox main window, right-click on the `master` VM and select **Clone**.
3. Create two clones:
   - Name one clone **datanode1**.
   - Name the second clone **datanode2**.
4. When cloning, choose the option **Reinitialize the MAC address of all network cards** to avoid network conflicts.

### 2.2 Configure Network Settings for DataNodes
1. After cloning, power on each new VM (**datanode1** and **datanode2**).
2. Assign a **unique static IP** to each cloned VM (refer to the [Static IP Setup Guide](https://github.com/your-repo-name/hadoop-distributed-lab/blob/main/static-ip-setup.md)).
3. Update the `/etc/hosts` file on all VMs (Master, DataNode1, DataNode2) to include the IP addresses and hostnames of each VM.

```bash
# NameNode (Master VM)
192.168.1.100   namenode

# DataNode1
192.168.1.101   datanode1

# DataNode2
192.168.1.102   datanode2
```

---

## Step 3: Configure Hadoop for Distributed Mode on All VMs

### 3.1 Configure `core-site.xml` on All VMs
- On each VM (master, datanode1, datanode2), configure the `core-site.xml` file to set up HDFS.
   ```bash
   sudo nano /usr/local/hadoop/etc/hadoop/core-site.xml
   ```
   Add the following configuration:
   ```xml
   <configuration>
       <property>
           <name>fs.defaultFS</name>
           <value>hdfs://master:9000</value>
       </property>
   </configuration>
   ```

### 3.2 Configure `hdfs-site.xml` on All VMs
- This file configures the replication of blocks and storage settings.
   ```bash
   sudo nano /usr/local/hadoop/etc/hadoop/hdfs-site.xml
   ```
   Add the following configuration:
   ```xml
   <configuration>
       <property>
           <name>dfs.replication</name>
           <value>2</value>   <!-- Set replication to 2 for fault tolerance -->
       </property>
       <property>
           <name>dfs.namenode.name.dir</name>
           <value>file:///usr/local/hadoop/data/namenode</value>
       </property>
       <property>
           <name>dfs.datanode.data.dir</name>
           <value>file:///usr/local/hadoop/data/datanode</value>
       </property>
   </configuration>
   ```

### 3.3 Configure `yarn-site.xml` on All VMs
1. Open `yarn-site.xml` and configure it:
   ```bash
   sudo nano /usr/local/hadoop/etc/hadoop/yarn-site.xml
   ```
2. Add this configuration to ensure YARN is set up for resource management:
   ```xml
   <configuration>
       <property>
           <name>yarn.nodemanager.aux-services</name>
           <value>mapreduce_shuffle</value>
       </property>
   </configuration>
   ```

### 3.4 Format the NameNode
On the **master** VM, format the NameNode:
   ```bash
   hdfs namenode -format
   ```

### 3.5 Start HDFS and YARN Services
1. **Start HDFS** on all VMs:
   ```bash
   start-dfs.sh
   ```
2. **Start YARN**:
   ```bash
   start-yarn.sh
   ```

---

## Step 4: Verify the Setup
1. On the **master VM**, open the HDFS Web UI to verify that all nodes are running:
   ```
   http://<master-ip>:9870
   ```

2. On the **master VM**, verify the DataNodes by checking the web interface for live DataNodes.

---

## Summary
In this lab, you:
- Configured a single master VM (NameNode).
- Cloned the master VM to create two DataNodes.
- Configured HDFS for a distributed mode on all VMs.
- Started HDFS and YARN services and verified the setup.

