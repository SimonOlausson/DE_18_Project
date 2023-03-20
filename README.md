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

1. Run the following code on every node that has been created on SNIC Science Cloud in order to update all the necessary packages.

```ruby
sudo apt-get update
sudo apt-get upgrade 
```        

2. Next, from the master node. Generate a security key in order to connect the master node to the worker nodes with passwordless login (ssh). Also append the security key to the file "autorized\_keys" on all nodes.

```ruby
ssh-keygen -t rsa -P '' -f ~/.ssh/<name_of_key>
```

3. Add the name of each node and as well as its private IP to the file /etc/hosts on the master node.

4. Install JDK1.8 on all nodes using the command apt-get

5. Install hadoop on all nodes using the command wget.

6. Configure Hadoop by adding the following text to the /bashrc file.

```ruby
export HADOOP_HOME=/home/ubuntu/hadoop
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
export HADOOP_MAPRED_HOME=${HADOOP_HOME}
export HADOOP_COMMON_HOME=${HADOOP_HOME}
export HADOOP_HDFS_HOME=${HADOOP_HOME}
export YARN_HOME=${HADOOP_HOME}
```
        
7. Next, setup the hadoop cluster by editing and adding relevant text to five files. This is to be done on the master node and then copied to the worker nodes using the command scp. The files that are to be edited are as follows:

- ~/hadoop/etc/hadoop/hadoop-env.sh
- ~/hadoop/etc/hadoop/core-site.xml
- ~/hadoop/etc/hadoop/hdfs-site.xml
- ~/hadoop/etc/hadoop/yarn-site.xml

and on the master node, only, edit the following file:

- ~/hadoop/etc/hadoop/mapred-site.xm
     
8. Create a folder /usr/local/hadoop/hdfs/data on all nodes 


9. Create the file ~/hadoop/etc/hadoop/masters and add the private IP of the master node. Then create ~/hadoop/etc/hadoop/workers and add the private IP of the two worker nodes.

10. The HDFS was formated and then the hdfs was started from the master node using the following commands:

```ruby
hdfs namenode -format
start-dfs.sh
```

11. In order to see that the correct nodes have been started run the command jps. The master node should be running NameNode and SecondaryNameNode. The workers should run DataNode.

12. When the HDFS is up and running add the MillionSong dataset from the master node using the command:

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
        
### Host VM setup
        
1. Start the VM using following command: 

```ruby
ssh ubuntu@<HostVMPublicIP> -i <key.pm>\ 
-L 4040:localhost:4040 \ 
-L 4041:localhost:4041 \ 
-L 4042:localhost:4042 \ 
-L 4043:localhost:4043 \ 
-L 4044:localhost:4044 \ 
-L 9870:privateipofsparkmasternode:9870 
```

2. After having entered the host VM, the HDFS web GUI will be accessible through the URL, http://<Public_IP_Master>:9870. 

3. Update the VM and install packages.

```ruby
apt update -y
apt install python3-pip
apt install net-tools
apt install openjdk-11-jdk-headless
```

4. Given that Uppmax doesn’t have a DNS facility, the host file will be used as a complement to that with the following code in the command shell:
-	for i in {1..4}; 
-	do 
-	 for j in {1..255}; 
-	 do 
-	     echo "192.168.$i.$j host-192-168-$i-$j" | sudo tee -a /etc/hosts 
-	 done 
-	Done
        
5. The hostname of the Host VM should be set in the following way in the command shell:

```ruby
sudo hostname host-$(hostname -I | awk '{$1=$1};1' | sed 's/\./-/'g)
``` 

Set it for future logs as well:

```ruby
echo "sudo hostname host-$(hostname -I | awk '{$1=$1};1' | sed 's/\./-/'g)" | sudo tee -a /home/ubuntu/.profile
```  
	
6. Configure the JAVA_HOME environment in the command shell:
```ruby
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64 
echo JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64 | sudo tee -a /etc/environment
```      

7. Install pyspark, it needs to be the same version as the other nodes:
        
```ruby
pip3 install pyspark==3.2.3
```         
        
8. Lastly, install jupyter notebook and then run it.



