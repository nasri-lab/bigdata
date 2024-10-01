
# Lab 1: Hadoop HDFS Distributed Setup on Ubuntu (3 VMs)

This lab demonstrates how to install and configure Hadoop HDFS in distributed mode on 3 Ubuntu virtual machines using VirtualBox.

## Prerequisites:
- **VirtualBox** installed on your machine.
- **Ubuntu Server ISO** (You can download from [Ubuntu](https://ubuntu.com/download/server)).
- **At least 8 GB of RAM** (allocated across the 3 VMs).

---

## Step 1: Create and Configure One Master Virtual Machine (to be cloned later)

In this step, you will first create a **Master VM**. Once this VM is fully configured, we will **clone it to create 2 additional VMs** and customize them as needed for the Hadoop cluster.

### 1.1 Create the First VM (Master VM)
1. Open VirtualBox and create a new VM called `master`.
2. Assign **2 GB of RAM** and **2 CPU cores**.
3. Attach the **Ubuntu Server ISO** to the VM and start the installation.
4. Follow the Ubuntu installation process and install the minimal system.
5. Set up a username and password for SSH access.
6. Install the OS updates:
  ```bash
   sudo apt update
   sudo apt upgrade
   ```

### 1.2: Install OpenSSH Server on the VM and allow remote access

In order to connect to the VM via SSH clients (like **PuTTY**), you need to install and activate SSH on the VM, we will install **OpenSSH server**.

1. **Log in** to the VM.
2. Install the **OpenSSH server**:
   ```bash
   sudo apt install openssh-server
   ```
3. Check if the service is running:
   ```bash
   sudo systemctl status ssh
   ```
4. In case it is not running, start it:
  ```bash
   sudo systemctl start ssh
   ```

5. Enable the SSH service to start on boot:
  ```bash
  sudo systemctl enable ssh
  ```

### 1.3 Configure Network Settings (Static IP and Bridged Adapter)
1. **Set up a static IP** for this VM (see the [Static IP Setup Guide](./static-ip-setup.md)).
2. Ensure that the network is set to **Bridged Adapter** mode (see [Bridged Adapter Setup](./network-setup.md)).
3. Download [**PuTTY**](https://www.putty.org/) and connect to your VM via SSH.

### 1.4 Install Hadoop on the VM
Hadoop requires Java, so install OpenJDK on the Master VM:
   ```bash
   sudo apt install openjdk-8-jdk
   ```

Then download Hadoop, 
   ```bash
   wget https://downloads.apache.org/hadoop/common/hadoop-3.4.0/hadoop-3.4.0.tar.gz
   ```
   Extract Hadoop and move it to `/usr/local/hadoop`:
   
   ```bash
   tar -xvzf hadoop-3.4.0.tar.gz
   sudo mv hadoop-3.4.0 /usr/local/hadoop
   ```

### 1.5 Configure Environment Variables on the Master VM
1. On the Master VM, edit the `~/.bashrc` file to add the Hadoop environment variables:
   ```bash
   sudo nano ~/.bashrc
   ```
   Add the following lines at the end of the file (Pay attention, **DON'T REPLACE** the content):
   ```bash
   export HADOOP_HOME=/usr/local/hadoop
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
   export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
   ```

For some cases, the JAVA_HOME variable is not picked by Hadoop, so add it to Hadoop envirement file:

  ```bash
   sudo nano /usr/local/hadoop/etc/hadoop/hadoop-env.sh
   ```
   And then modify the JAVA_HOME variable:

   ```bash
  export JAVA_HOME= 
  ```

2. Apply the changes:
   ```bash
   source ~/.bashrc
   ```

3. Ensure the envirenement is well configured:
  ```bash
  echo $HADOOP_HOME
  ```
  This command line should print **/usr/local/hadoop**

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
2. Assign a **unique static IP** to each cloned VM (refer to the [Static IP Setup Guide](./static-ip-setup.md)).
3. To make it easier to communicate between the VMs by hostname (instead of IP address), you can update the `/etc/hosts` file on each VM. Update the `/etc/hosts` file on all VMs (Master, DataNode1, DataNode2) to include the IP addresses and hostnames of each VM.

  ```bash
   sudo nano /etc/hosts
   ```

   And put the following content

  ```bash
  # NameNode (Master VM)
  192.168.0.230   master

  # DataNode1
  192.168.0.231   datanode1

  # DataNode2
  192.168.0.232   datanode2
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
On the **master** VM (Only on the master), format the NameNode:
   ```bash
   hdfs namenode -format
   ```

### 3.5 Start HDFS and YARN Services
1. **Start HDFS** on all VMs:
   ```bash
   start-dfs.sh
   ```

   In case you encounter problems of kind **Permission denied** refer to [Passwordless SSH Setup for Hadoop Cluster](./passwordless-ssh-setup.md)

2. **Start YARN**:
   ```bash
   start-yarn.sh
   ```

---

## Step 4:Final Step: Verify HDFS Health

Once you've completed the Hadoop and HDFS setup, it's essential to verify that everything is functioning correctly.

You can follow the guide in the next section to run health checks on your HDFS setup and ensure that all components are working as expected:

[Check HDFS Health: HDFS Report and Validation](./hdfs-check-report.md)

This guide will walk you through the process of verifying DataNode connectivity, block distribution, file operations, and the overall health of your Hadoop cluster.

---

## Final Step: Test Your Knowledge

After completing the setup and checking the health of your HDFS, it's time to assess your understanding of Hadoop and HDFS.

You can take this quiz to reinforce the key concepts you’ve learned:

- [Take the Quiz](./hadoop-hdfs-quiz-questions.md)
- [Check Quiz Answers](./hadoop-hdfs-quiz-answers.md)

This will help you evaluate your comprehension of the key topics covered in this lab.

---

## Summary
In this lab, you:
- Configured a single master VM (NameNode).
- Cloned the master VM to create two DataNodes.
- Configured HDFS for a distributed mode on all VMs.
- Started HDFS and YARN services and verified the setup.

