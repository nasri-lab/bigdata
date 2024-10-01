
# HDFS Health Check: Verifying the Hadoop Cluster Status

After setting up your Hadoop cluster and starting the HDFS services, it's important to verify that everything is working as expected. This guide explains how to check the health of your HDFS using the `hdfs dfsadmin -report` command and other essential commands.

---

## Step 1: Run the HDFS Report

The `hdfs dfsadmin -report` command provides a comprehensive overview of the health of your HDFS system, including DataNode status, storage usage, and block information.

1. **Run the following command on the NameNode**:
   ```bash
   hdfs dfsadmin -report
   ```

2. **Examine the output**:
   - The report will display the following key information:
     - **Live datanodes**: Number of DataNodes that are connected and functioning properly.
     - **Dead datanodes**: Number of DataNodes that are not responding.
     - **Configured capacity**: Total storage capacity of all DataNodes.
     - **DFS Used**: Amount of storage used by HDFS.
     - **DFS Remaining**: Free storage remaining in HDFS.
     - **Block information**: Number of blocks stored and replicated.

   Example output:
   ```text
   Configured Capacity: 17220509696 (16.04 GB)
   Present Capacity: 3490349056 (3.25 GB)
   DFS Remaining: 3490283520 (3.25 GB)
   DFS Used: 65536 (64 KB)
   DFS Used%: 0.00%
   Replicated Blocks:
           Under replicated blocks: 0
           Blocks with corrupt replicas: 0
           Missing blocks: 0
           Missing blocks (with replication factor 1): 0
           Low redundancy blocks with highest priority to recover: 0
           Pending deletion blocks: 0
   Erasure Coded Block Groups:
           Low redundancy block groups: 0
           Block groups with corrupt internal blocks: 0
           Missing block groups: 0
           Low redundancy blocks with highest priority to recover: 0
           Pending deletion blocks: 0

   -------------------------------------------------
   Live datanodes (2):

   Name: 192.168.0.231:9866 (datanode1)
   Hostname: hdfsnode
   Decommission Status : Normal
   Configured Capacity: 8610254848 (8.02 GB)
   DFS Used: 32768 (32 KB)
   Non DFS Used: 6403076096 (5.96 GB)
   DFS Remaining: 1747660800 (1.63 GB)
   DFS Used%: 0.00%
   DFS Remaining%: 20.30%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 0
   Last contact: Tue Oct 01 11:13:53 UTC 2024
   Last Block Report: Tue Oct 01 10:54:35 UTC 2024
   Num of Blocks: 0


   Name: 192.168.0.232:9866 (datanode2)
   Hostname: hdfsnode
   Decommission Status : Normal
   Configured Capacity: 8610254848 (8.02 GB)
   DFS Used: 32768 (32 KB)
   Non DFS Used: 6408114176 (5.97 GB)
   DFS Remaining: 1742622720 (1.62 GB)
   DFS Used%: 0.00%
   DFS Remaining%: 20.24%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 0
   Last contact: Tue Oct 01 11:13:51 UTC 2024
   Last Block Report: Tue Oct 01 10:54:11 UTC 2024
   Num of Blocks: 0
   ```

3. **Check for any dead DataNodes**:
   - The section **Dead datanodes** should show `0`. If any DataNodes are listed as dead, it means they are not functioning, and you will need to troubleshoot them.

---

## Step 2: Check DataNode Status Using the Web UI

You can also verify the status of DataNodes using Hadoop's web interface.

1. **Open a web browser** and go to the NameNode’s web interface:
   ```
   http://192.168.0.230:9870
   ```

2. **Check the Datanodes section**:
   - On the left-hand sidebar, click on **"Datanodes"** to see the status of all DataNodes in the cluster.
   - You should see all connected DataNodes listed under **Live Datanodes**. 

---

## Step 3: Verify Block Distribution

To ensure that the blocks are being distributed across the DataNodes, you can use the following command:

1. **Check block distribution**:
   ```bash
   hdfs fsck /
   ```

2. This command will show the block locations and replication factor for all files in your HDFS.

---

## Step 4: Test File Operations

You can further verify that your HDFS is working by performing file operations.

1. **Create a directory** in HDFS:
   ```bash
   hdfs dfs -mkdir /test-directory
   ```

2. **Upload a file** to HDFS:
   ```bash
   hdfs dfs -put /path/to/local/file.txt /test-directory
   ```

3. **List files in the directory**:
   ```bash
   hdfs dfs -ls /test-directory
   ```

4. **Retrieve the file** from HDFS:
   ```bash
   hdfs dfs -get /test-directory/file.txt /local/path
   ```

---

## Step 5: Confirm the Replication Factor

1. To check the replication factor for a file:
   ```bash
   hdfs dfs -stat %r /test-directory/file.txt
   ```

2. To change the replication factor:
   ```bash
   hdfs dfs -setrep 2 /test-directory/file.txt
   ```

---

## Final Check

After completing the steps above, your Hadoop cluster should be fully operational. Ensure that:
- All DataNodes are live.
- Blocks are distributed and replicated across DataNodes.
- File operations (create, upload, retrieve) are functioning as expected.

If everything looks good in the report and Web UI, your HDFS setup is healthy and ready for use!

---

