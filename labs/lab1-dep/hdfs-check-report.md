
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
   Configured Capacity: 100 GB
   DFS Used: 20 GB
   Non DFS Used: 5 GB
   DFS Remaining: 75 GB
   Live datanodes (2):

   Name: 192.168.0.231:50010 (datanode1)
   Hostname: datanode1
   Decommission Status : Normal
   Configured Capacity: 50 GB
   DFS Used: 10 GB
   DFS Remaining: 40 GB
   ...

   Dead datanodes (0):
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

