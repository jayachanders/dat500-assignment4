# dat500-assignment4

To form groups and to start with this assignment you need all 3 previous assignments approved.

This assignment is more important, but also more complex, compared to first 3 labs, because you are going to use this setup for your project. So, make sure you complete the task and keep it ready for your projects.  

For this assignment you form groups of 3, which you then keep for the project. Use group features on Canvas. 

Part 1: 

1. Login in to http://cloud.cs.ux.uis.no/horizon (Links to an external site.) using the details provides to you group. [Works only from UiS network]
2. Go to Project->Network->Network  [You will perform this step only once] 
   2.1 Create network: Choose name , Subnet Name, Network address (10.10.1.0/24 or you can choose any other private ip range), Gatewat IP (10.10.1.1 or you can leave it).  --- Create the Network. 

   2.2 Create router: Choose router name, choose external network. --- Create the Router. 

   2.3 Add interface: Choose router (click on router name) -> In Interface Tab -> Add Interface -> Select Subnet Name -> Submit (create the interface). 

   2.4 You can check the Network Topology for each step. 
   
   2.5Manage Security Group Rules: Project -> Network -> Security Groups -> Manage Rules ->Add Rule -> Rule (select SSH) , CIDR (choose IP from where you want to connect from) -> Add.   (If you don’t have this rule, you will not be able to SSH to the VM created)

3. Create a test VM: Go to Project -> Compute -> Instances 
   3.1 Launch -> Select Name -> Source (Ubuntu or Centos) -> Flavor (m1.small or m1.medium ) -> -> -> (Key Pair – Only first time, create your ssh key or upload existing ssh key).

   3.2 Creating floating ip (first time only): Project -> Instance -> options from instance --> associate floating ip -> create IP -> allocate IP .

   3.3 To SSH to VM use (if Ubuntu image ) user name : ubuntu ,  (CentOS image) user name : centos  --->      ssh username@floating_ip -i ssh_key

Part 2: 

Setup Hadoop from scratch in a distributed mode using multiple VMs. 

Create a Master VM and 3 slave VMs. 
Setup the Hadoop cluster in distributed mode. (Choose your Hadoop version carefully if you are going to use Spark later for your project. Not all versions of Hadoop are compatible. Spark installation is not part of this assignment). 
You deliver the assignment by showing it to student assistants or me during lab or group work hours. Any delivery outside these hours have to be agreed on beforehand. 

This task is significantly more difficult than the earlier three. You need to complete this task and keep it in good condition for your final project. 

Extra Info : Save all the steps, in case you are facing some problem, it will be easy to recreate the setup faster. 

[Steps for 3.2.1 are given, Spark in not avilable for this version. If you are going to use Spark, please do some research and choose your Hadoop version carefully]

```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update && sudo apt-get install -y build-essential python oracle-java8-set-default
```

Now, let's download Hadoop and set it up.
```
wget https://archive.apache.org/dist/hadoop/common/hadoop-3.2.1/hadoop-3.2.1.tar.gz
tar -xzvf hadoop-3.2.1.tar.gz
sudo mv hadoop-3.2.1 /usr/local/hadoop
```
set environment variables
```
sudo vi /etc/environment

PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/usr/local/hadoop/bin:/usr/local/hadoop/sbin"
JAVA_HOME="/usr/lib/jvm/java-8-oracle/jre"
```

Update it:
```
source /etc/environment
```
