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
1. The following code was run on every node that was created on SNIC Science Cloud
        \begin{verbatim}
            sudo apt-get update
            sudo apt-get upgrade 
        \end{verbatim}

2. Next, from the master node. A security key was generated in order to connect to the worker node with passwordless login (ssh). The key was appended to the file "autorized\_keys". The same file was the copied to both the worker nodes.

3. All of the nodes and their priavate IP were then added to the /etc/hosts file on the master node.

JDK1.8 was installed on all nodes using the commande apt-get

Hadoop was installed on all nodes using the commande wget

Hadoop was configured by adding the following text to the bashrc file
        \begin{verbatim}
            export HADOOP_HOME=/home/ubuntu/hadoop
            export PATH=$PATH:$HADOOP_HOME/bin
            export PATH=$PATH:$HADOOP_HOME/sbin
            export HADOOP_MAPRED_HOME=${HADOOP_HOME}
            export HADOOP_COMMON_HOME=${HADOOP_HOME}
            export HADOOP_HDFS_HOME=${HADOOP_HOME}
            export YARN_HOME=${HADOOP_HOME}
        \end{verbatim}
        
Next, several files were edited in order to setup the Hadoop cluster. It was done on the master node and then copied to the worker nodes used the command scp. The files that were edited are as follows:
    \begin{itemize}
        \item ~/hadoop/etc/hadoop/hadoop-env.sh
        \item ~/hadoop/etc/hadoop/core-site.xml
        \item  ~/hadoop/etc/hadoop/hdfs-site.xml
        \item ~/hadoop/etc/hadoop/yarn-site.xml
    \end{itemize}
     and on the master node, only, the following file was edited
     \begin{itemize}
         \item ~/hadoop/etc/hadoop/mapred-site.xm
     \end{itemize}
     
A data folder was created on all nodes.

"masters" and "workers" files were created on all nodes. the private IP of master and worker nodes were added to the files.

The HDFS was formated and then the hdfs was started from the master node.

In order to see that the correct nodes have been started the commande jps was run.
