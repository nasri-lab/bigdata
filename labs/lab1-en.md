
# Hadoop HDFS Distributed Setup on Ubuntu (3 VMs in VirtualBox)

This lab demonstrates how to install and configure Hadoop HDFS in distributed mode on 3 Ubuntu virtual machines using VirtualBox.

## Prerequisites:
- **VirtualBox** installed on your machine.
- **Ubuntu Server ISO** (You can download from [Ubuntu](https://ubuntu.com/download/server)).
- **At least 8 GB of RAM** (allocated across the 3 VMs).

---

## Step 1: Create and Configure 3 Virtual Machines

1. **Create the First Virtual Machine (NameNode)**
   - Open VirtualBox and create a new VM called `namenode`.
   - Assign 2 GB of RAM and 2 CPU cores.
   - Attach the Ubuntu Server ISO to the VM and start the installation.
   - Follow the Ubuntu installation process and install the minimal system (without GUI).
   - Set up a username and password for SSH access.
   - Repeat this process two more times to create VMs named `datanode1` and `datanode2`.

2. **Network Configuration**
   - Set up each VM to use the **Bridged Adapter** network mode so they can communicate over the local network.
   - Set up each VM to use the [Bridged Adapter network mode](https://github.com/nasri-lab/bigdata/blob/main/labs/network-setup.md) so they can communicate over the local network.
   - Ensure that each machine has a unique static IP.

3. **Install OpenSSH on each VM**
   - After the Ubuntu installation, log in to each VM and install OpenSSH:
     ```bash
     sudo apt update
     sudo apt install openssh-server
     ```

4. **Configure Static IP Addresses**
   - Configure static IP addresses for each VM by editing the network configuration:
     ```bash
     sudo nano /etc/netplan/00-installer-config.yaml
     ```
     For `namenode` (example):
     ```yaml
     network:
       version: 2
       renderer: networkd
       ethernets:
         enp0s3:
           dhcp4: no
           addresses:
             - 192.168.1.100/24
           gateway4: 192.168.1.1
           nameservers:
             addresses:
               - 8.8.8.8
     ```
   - Repeat the process for `datanode1` and `datanode2`, assigning `192.168.1.101` and `192.168.1.102` respectively.
   - Apply the changes:
     ```bash
     sudo netplan apply
     ```

5. **Configure `/etc/hosts` File for Each VM**
   - On each VM, edit the `/etc/hosts` file to add the IP addresses and hostnames of all three VMs.
     ```bash
     sudo nano /etc/hosts
     ```
     Add the following lines:
     ```
     192.168.1.100 namenode
     192.168.1.101 datanode1
     192.168.1.102 datanode2
     ```

---

## Step 2: Install and Configure Hadoop

1. **Install Java on Each VM**
   - Hadoop requires Java, so install OpenJDK on each VM:
     ```bash
     sudo apt update
     sudo apt install openjdk-8-jdk
     ```

2. **Download and Extract Hadoop on Each VM**
   - Download Hadoop on each VM:
     ```bash
     wget https://downloads.apache.org/hadoop/common/hadoop-3.3.0/hadoop-3.3.0.tar.gz
     ```
   - Extract Hadoop and move it to `/usr/local/hadoop`:
     ```bash
     tar -xvzf hadoop-3.3.0.tar.gz
     sudo mv hadoop-3.3.0 /usr/local/hadoop
     ```

3. **Configure Hadoop Environment Variables**
   - On each VM, edit the `~/.bashrc` file to add the Hadoop environment variables:
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
   - Apply the changes:
     ```bash
     source ~/.bashrc
     ```

---

## Step 3: Configure Hadoop for Distributed Mode

1. **Configure `core-site.xml` (on all VMs)**
   - Open `core-site.xml`:
     ```bash
     sudo nano /usr/local/hadoop/etc/hadoop/core-site.xml
     ```
   - Add the following configuration:
     ```xml
     <configuration>
       <property>
         <name>fs.defaultFS</name>
         <value>hdfs://namenode:9000</value>
       </property>
     </configuration>
     ```

2. **Configure `hdfs-site.xml` (on all VMs)**
   - This file controls the replication of blocks and storage settings.
     ```bash
     sudo nano /usr/local/hadoop/etc/hadoop/hdfs-site.xml
     ```
   - Add the following configuration:
     ```xml
     <configuration>
       <property>
         <name>dfs.replication</name>
         <value>2</value>
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

3. **Configure `yarn-site.xml` (on all VMs)**
   - This file configures YARN, the resource manager.
     ```bash
     sudo nano /usr/local/hadoop/etc/hadoop/yarn-site.xml
     ```
   - Add this configuration:
     ```xml
     <configuration>
       <property>
         <name>yarn.nodemanager.aux-services</name>
         <value>mapreduce_shuffle</value>
       </property>
     </configuration>
     ```
