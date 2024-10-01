
#  Lab1 - Quiz: Assess Your Understanding of Hadoop and HDFS Setup

---

### **Question 1: What is the purpose of formatting the NameNode?**
- A. To delete all the data from the DataNodes.
- B. To initialize the file system metadata and prepare the NameNode for managing HDFS.
- C. To start the HDFS services.
- D. To connect the NameNode to the DataNodes.

---

### **Question 2: Which file do you need to edit to specify the directory for storing HDFS data on a DataNode?**
- A. `core-site.xml`
- B. `mapred-site.xml`
- C. `hdfs-site.xml`
- D. `yarn-site.xml`

---

### **Question 3: How can you verify that HDFS is running properly on your Hadoop cluster?**
- A. By checking the DataNodes' block locations using the `jps` command.
- B. By running `hdfs dfsadmin -report` to check the status of the DataNodes and HDFS capacity.
- C. By stopping all Hadoop services and checking the logs.
- D. By pinging the NameNode and DataNodes.

---

### **Question 4: What command would you use to upload a file from your local machine to HDFS?**
- A. `hdfs put /local/path/to/file /hdfs/path`
- B. `hdfs dfs -put /local/path/to/file /hdfs/path`
- C. `hdfs dfsadmin -upload /local/path/to/file /hdfs/path`
- D. `hdfs upload /local/path/to/file /hdfs/path`

---

### **Question 5: How do you set up passwordless SSH for a Hadoop cluster?**
- A. Generate a key pair using `ssh-keygen` and manually copy the keys to each DataNode.
- B. Use the `hdfs dfsadmin -setup` command to set up passwordless SSH.
- C. Generate a key pair using `ssh-keygen` and use `ssh-copy-id` to distribute the public key to each DataNode.
- D. Configure the passwordless option in `core-site.xml`.

---

### **Question 6: What is the default port for accessing the NameNode web interface?**
- A. 8080
- B. 50070
- C. 9870
- D. 9000

---

### **Question 7: What is the role of the DataNode in the Hadoop architecture?**
- A. It manages the file system metadata and directory structure.
- B. It stores and manages data blocks.
- C. It schedules and manages resource allocation for Hadoop jobs.
- D. It monitors and reports on the status of other nodes.

---

### **Question 8: If you encounter the error `Permission denied (publickey,password)` while starting HDFS, what is the most likely issue?**
- A. HDFS is not installed properly on the DataNodes.
- B. The NameNode is not formatted correctly.
- C. Passwordless SSH is not properly configured between the NameNode and DataNodes.
- D. The Java version is not compatible with Hadoop.

---

### **Question 9: What does the `hdfs fsck /` command do?**
- A. Formats the NameNode.
- B. Checks the health of the HDFS file system and reports block distribution and replication status.
- C. Uploads files to HDFS.
- D. Checks the status of DataNode processes.

---

### **Question 10: How can you check the replication factor of a file in HDFS?**
- A. Use the command `hdfs dfsadmin -stat %r /path/to/file`.
- B. Use the command `hdfs fsck -replication /path/to/file`.
- C. Use the command `hdfs dfs -stat %r /path/to/file`.
- D. Use the command `hdfs dfsadmin -report`.

