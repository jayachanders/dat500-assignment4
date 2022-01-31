# dat500-assignment4

To form groups and to start with this assignment you need all 3 previous assignments approved.

This assignment is more important, but also more complex, compared to first 3 labs, because you are going to use this setup for your project. So, make sure you complete the task and keep it ready for your projects.  

For this assignment you form groups of 2, which you then keep for the project. Use group features on Canvas. 

## Part 1: 

1. Login in to https://cloud.cs.ux.uis.no/horizon (Links to an external site.) using the details provides to you group. [Works only from UiS network]
2. Go to Project->Network->Network  [You will perform this step only once] 

   2.1 Create network: Choose name , Subnet Name, Network address [X = Group Number.] (10.10.X.0/24 or you can choose any other private ip range), Gateway IP (10.10.X.1 or you can leave it).  --- Create the Network. 
```
# For example Group 1: 
Name : Group1_Network
Subnet Name: Group1_subnet
Network address: 10.10.1.0/24
Gateway IP: 10.10.1.1
```

   2.2 Create router: Choose router name, choose external network. --- Create the Router. 
```
# For example Group 1: 
Router name : Group1_router
External network : public-network1  # Avilable from drop down options
```

   2.3 Add interface: Choose router (click on router name) -> In Interface Tab -> Add Interface -> Select Subnet Name -> Submit (create the interface). 
```
# For example Group 1: 
Router name : Group1_router
External network : public-network1  # Avilable from drop down options
```

   2.4 You can check the Network Topology for each step. 

   2.5 Manage Security Group Rules: Project -> Network -> Security Groups -> Manage Rules ->Add Rule -> Rule (select SSH) , CIDR (choose IP from where you want to connect from) -> Add.   (If you don’t have this rule, you will not be able to SSH to the VM created)


3. Create a test VM: Go to Project -> Compute -> Instances 

   3.1 Launch -> Select Name -> Source (Ubuntu or Centos) -> Flavor (m1.small or m1.medium ) -> -> -> (Key Pair – Only first time, create your ssh key or upload existing ssh key).

   3.2 Creating floating ip (first time only): Project -> Compute -> Instance -> options from instance --> associate floating ip -> create IP -> allocate IP .

   3.3 To SSH to VM use (if Ubuntu image ) user name : ubuntu ,  (CentOS image) user name : centos  --->      
```
ssh username@floating_ip -i ssh_key
```

## Part 2: 

Setup Hadoop from scratch in a distributed mode using multiple VMs. 

Create a Namenode VM and 3 Datanode VMs. 
Setup the Hadoop cluster in distributed mode. (Choose your Hadoop version carefully if you are going to use Spark later for your project. Not all versions of Hadoop are compatible. Spark installation is not part of this assignment). 
You deliver the assignment by showing it to student assistants or me during lab or group work hours. Any delivery outside these hours have to be agreed on beforehand. 

This task is significantly more difficult than the earlier three. You need to complete this task and keep it in good condition for your final project. 

Extra Info : Save all the steps, in case you are facing some problem, it will be easy to recreate the setup faster. 

[Steps for 3.2.1 are given, Spark in not avilable for this version at the moment. If you are going to use Spark, please do some research and choose your Hadoop version carefully]

```
# On all nodes
sudo apt-get update && sudo apt-get install -y build-essential unzip python3 python3-pip openjdk-8-jdk
pip3 install mrjob
```

Now, let's download Hadoop and set it up.
```
# On all nodes
wget https://archive.apache.org/dist/hadoop/common/hadoop-3.2.1/hadoop-3.2.1.tar.gz
tar -xzvf hadoop-3.2.1.tar.gz
sudo mv hadoop-3.2.1 /usr/local/hadoop
```

set environment variables
```
# On all nodes
sudo tee /etc/environment > /dev/null << EOL
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/usr/local/hadoop/bin:/usr/local/hadoop/sbin"
JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"
HADOOP_HOME="/usr/local/hadoop"
EOL
```

Update it:
```
# On all nodes
source /etc/environment
```

Set proper hostnames and instead of using the IP address.

sudo vi /etc/hosts
```
# On all nodes
10.10.X.Y namenode
10.10.X.a datanode1
10.10.X.b datanode2
10.10.X.c datanode3
```

## Hadoop environment setup
# On all nodes
```
sudo tee /usr/local/hadoop/etc/hadoop/hdfs-site.xml > /dev/null << EOL
<configuration>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>/usr/local/hadoop/data/nameNode</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>/usr/local/hadoop/data/dataNode</value>
  </property>
  <property>
    <name>dfs.replication</name>
    <value>2</value>
  </property>
</configuration>
EOL
```

# On all nodes
```
sudo tee /usr/local/hadoop/etc/hadoop/core-site.xml > /dev/null << EOL
<configuration>
  <property>
    <name>fs.default.name</name>
    <value>hdfs://namenode:9000</value>
  </property>
</configuration>
EOL
```

# On all nodes
```
sudo tee /usr/local/hadoop/etc/hadoop/yarn-site.xml > /dev/null << EOL
<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
      <name>yarn.nodemanager.aux-services.mapreduce_shuffle.class</name>
      <value>org.apache.hadoop.mapred.ShuffleHandler</value>
  </property>
  <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>namenode</value>
  </property>
  <property>
    <name>yarn.nodemanager.vmem-check-enabled</name>
    <value>false</value>
  </property>
</configuration>
EOL
```

# On all nodes
```
sudo tee /usr/local/hadoop/etc/hadoop/mapred-site.xml > /dev/null << EOL
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>yarn.app.mapreduce.am.env</name>
        <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
    </property>
    <property>
        <name>mapreduce.map.env</name>
        <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
    </property>
    <property>
        <name>mapreduce.reduce.env</name>
        <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
    </property>
    <property> 
      <name>mapreduce.application.classpath</name>
      <value>$HADOOP_HOME/share/hadoop/mapreduce/*,$HADOOP_HOME/share/hadoop/mapreduce/lib/*,$HADOOP_HOME/share/hadoop/common/*,$HADOOP_HOME/share/hadoop/common/lib/*,$HADOOP_HOME/share/hadoop/yarn/*,$HADOOP_HOME/share/hadoop/yarn/lib/*,$HADOOP_HOME/share/hadoop/hdfs/*,$HADOOP_HOME/share/hadoop/hdfs/lib/*</value>
    </property>
</configuration>
EOL
```

# On all nodes
```
sudo tee /usr/local/hadoop/etc/hadoop/workers > /dev/null << EOL
datanode1
datanode2
datanode3
EOL
```

# On all nodes
```
sudo tee /usr/local/hadoop/etc/hadoop/masters > /dev/null << EOL
namenode
EOL
```

# Only on namenode
Format the HDFS, this needs to be done only in the first step. Once the HDFS is setup, don't run this command. It will delete data in HDFS.
```
hdfs namenode -format
```

# Only on namenode
Start dfs and yarn
```
start-dfs.sh
start-yarn.sh
hadoop fs -ls /
```

# Only on namenode
Check hdfs status: Live datanodes should be 3
```
hdfs dfsadmin -report
```

# Only on namenode
Create folder on HDFS, copy files (hdfs dfs -- can also be used)
```
hadoop fs -mkdir /dis_materials
hadoop fs -put dis_materials/*.txt dis_materials/*.csv /dis_materials

```
# Only on namenode
Remove a folder and contents from hdfs (hadoop fs -- can also be used )
```
hdfs dfs -rm -r /dis_materials/output1
```

Once the installation is successful, please check your able to run previous assignment files on this cluster.
