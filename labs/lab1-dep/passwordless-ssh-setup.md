
# LAP 1 - Annexe C: Passwordless SSH Setup for Hadoop Cluster

This guide explains how to configure **passwordless SSH** between the nodes in your Hadoop cluster. Hadoop uses SSH to start and stop services across the cluster, so passwordless SSH is essential for the NameNode to communicate with the DataNodes and the Secondary NameNode.

---

## Step 1: Set Up Passwordless SSH on the Master (NameNode)

1. **Generate SSH keys** on the NameNode (Master VM):
   - If you don’t already have an SSH key pair, generate one:
     ```bash
     ssh-keygen -t rsa -P ""
     ```
     - This will generate a public/private RSA key pair. By default, it will be saved in `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub`.

2. **Copy the public key** to all nodes (NameNode, DataNodes, and Secondary NameNode):
   - You need to copy the public key to **yourself** (the NameNode) and the **DataNodes** to enable passwordless SSH.
   
   - First, copy the key to the **NameNode** (itself):
     ```bash
     ssh-copy-id -i ~/.ssh/id_rsa.pub hdfs@master
     ```
   - Then, copy the key to each **DataNode** and the **Secondary NameNode**:
     ```bash
     ssh-copy-id -i ~/.ssh/id_rsa.pub hdfs@datanode1
     ssh-copy-id -i ~/.ssh/id_rsa.pub hdfs@datanode2
     ssh-copy-id -i ~/.ssh/id_rsa.pub hdfs@hdfsnode
     ```

3. **Test passwordless SSH**:
   - Test if passwordless SSH works by connecting from the NameNode (Master) to itself and each DataNode:
     ```bash
     ssh hdfs@master
     ssh hdfs@datanode1
     ssh hdfs@datanode2
     ssh hdfs@hdfsnode
     ```
   - If the password prompt no longer appears, passwordless SSH is working.

---

## Step 2: Ensure Correct User Permissions

1. Ensure that the **SSH key pair** (`id_rsa` and `id_rsa.pub`) and the `.ssh` directory have the correct permissions:
   ```bash
   chmod 700 ~/.ssh
   chmod 600 ~/.ssh/id_rsa
   chmod 644 ~/.ssh/id_rsa.pub
   ```
   This ensures that only the `hdfs` user can read and write to the private key.

---

## Step 3: Verify SSH Configurations

1. **Check `sshd_config` on each node**:
   - Ensure that the SSH daemon is configured to allow passwordless login. Check the SSH configuration file (`/etc/ssh/sshd_config`) and make sure the following settings are present:
     ```bash
     PasswordAuthentication yes
     PubkeyAuthentication yes
     ```
   - Restart SSH after making any changes:
     ```bash
     sudo systemctl restart ssh
     ```

---

## Step 4: Start HDFS Again

Once passwordless SSH is properly configured, try starting HDFS again:
```bash
start-dfs.sh
```

You should no longer see the `Permission denied (publickey,password)` error, and the NameNode should be able to communicate with the DataNodes and Secondary NameNode without issues.

---

### Additional Notes:
- **Public key authentication** is how Hadoop communicates between nodes to start DataNodes and Secondary NameNodes.
- The message `"Warning: Permanently added 'localhost' (ED25519) to the list of known hosts."` is not an error; it’s just informing you that the SSH connection is adding the host’s key to the `~/.ssh/known_hosts` file for future authentication. This is normal.

---

By following these steps, you should have a fully functional **passwordless SSH** setup across all your Hadoop nodes.

