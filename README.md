# DE_18_Project

## Nodes 

### Public
Master  130.238.29.197

Worker1 130.238.29.125

Worker2 130.238.29.223

Code 130.238.29.186

### Private
Master 192.168.2.52

Worker1 192.168.2.54

Worker2 192.168.2.230

Code 192.168.2.21


## Implementation

### SNIC Science cloud setup

-	Create four instances, out of which two will serve as workers for the cluster and one of the remaining will be the master and the last one will be the VM to host Jupyter Notebook.
-	Each of the worker nodes will have flavor, “ssc.medium”, and image as their boot source, with version: “Ubuntu 22.04 - 2023.01.07”.
-	Apply an additional security group for the worker nodes, shown in figure 1. 
-	The master node will have the flavor, “ssc.small”, and also image as their boot source, with version: “Ubuntu 22.04 - 2023.01.07”. The same goes for the host VM
-	Similarly, to the worker nodes the master node will also have an additional security group, shown in figure 2. 
-	The VM host will also have an additional security group, shown in figure 3. 
