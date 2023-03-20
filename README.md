# DE_18_Project

## Nodes 

### Public
Master  130.238.29.197

Worker1 130.238.29.125

Worker2 130.238.29.223

Host 130.238.29.186

### Private
Master 192.168.2.52

Worker1 192.168.2.54

Worker2 192.168.2.230

Host 192.168.2.21


## Implementation

### SNIC Science cloud setup

-	Create four instances, out of which two will serve as workers for the cluster and one of the remaining will be the master and the last one will be the VM to host Jupyter Notebook.
-	Each of the worker nodes will have flavor, “ssc.medium”, and image as their boot source, with version: “Ubuntu 22.04 - 2023.01.07”.
-	Apply an additional security group for the worker nodes, Simonsec
-	The master node will have the flavor, “ssc.small”, and also image as their boot source, with version: “Ubuntu 22.04 - 2023.01.07”. The same goes for the host VM
-	Similarly, to the worker nodes the master node will also have an additional security group, Sg18master.
-	The VM host will also have an additional security group, Spark-cluster-client-for-student-machines

### Connecting the nodes and hdfs setup
The following steps are only to be applied to the master node and in some cases, when specified, the two worker nodes. The host node will not be used here.

1. The following code was run on every node that was created on SNIC Science Cloud

```ruby
sudo apt-get update
sudo apt-get upgrade 
```        

2. Next, from the master node. A security key was generated in order to connect to the worker node with passwordless login (ssh). The key was appended to the file "autorized\_keys". The same file was the copied to both the worker nodes.

3. All of the nodes and their priavate IP were then added to the /etc/hosts file on the master node.

4. JDK1.8 was installed on all nodes using the commande apt-get

5. Hadoop was installed on all nodes using the commande wget

6. Hadoop was configured by adding the following text to the bashrc file

```ruby
export HADOOP_HOME=/home/ubuntu/hadoop
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
export HADOOP_MAPRED_HOME=${HADOOP_HOME}
export HADOOP_COMMON_HOME=${HADOOP_HOME}
export HADOOP_HDFS_HOME=${HADOOP_HOME}
export YARN_HOME=${HADOOP_HOME}
```
        
7. Next, several files were edited in order to setup the Hadoop cluster. It was done on the master node and then copied to the worker nodes used the command scp. The files that were edited are as follows:

- ~/hadoop/etc/hadoop/hadoop-env.sh
- ~/hadoop/etc/hadoop/core-site.xml
- ~/hadoop/etc/hadoop/hdfs-site.xml
- ~/hadoop/etc/hadoop/yarn-site.xml

and on the master node, only, the following file was edited

- ~/hadoop/etc/hadoop/mapred-site.xm

     
8. A data folder was created on all nodes.

9. "masters" and "workers" files were created on all nodes. the private IP of master and worker nodes were added to the files.

10. The HDFS was formated and then the hdfs was started from the master node.

11. In order to see that the correct nodes have been started the commande jps was run.

12. When the HDFS is up and running the MillionSong dataset was transferred from the master node using the command:

```ruby
hdfs dfs -put <MillionSongDataset> <PathToHdfsDir>
```

### Spark cluster setup
Following steps are for all nodes until told otherwise.

1. Install the required packages using apt-get, in this case python3 and net-tools. Making sure that the version is the same on all nodes.


2. Only for the master node and the worker nodes. Download and install Spark Apache, make sure all the nodes have the same version. This is done using the command wget. Unpacking the .tgz file is done with the command tar.

3. Only for the master node and the worker nodes. Edit necessary files.
- in /bashrc add
```ruby
export SPARK_HOME=/opt/spark
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
```
and to activate the changes write the command 

```ruby
source ~/.bashrc
```

4. Only on the master node, create a new file in the path /opt/spark/conf called spark-env.sh and add the following.

```ruby
SPARK_MASTER_HOST=<master private IP>
```
5. Start the master node using the following command

```ruby
start-master.sh
```

This will also start a spark node web pages with the following URL: http://<master float IP>:8080
        
6. Only on the worker nodes, start the spark nodes using the following command:

```ruby
start-worker.sh spark://<master private IP>:7077
```
        
A Spark cluster is now created.

