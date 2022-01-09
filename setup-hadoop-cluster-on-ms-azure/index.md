# Hadoop Setup

# Report: Setup Hadoop Cluster on MS Azure

https://klasserom.azurewebsites.net/Lessons/Binder/2410

Notice: In this report, the ssh .pem is named SSH_keypair.pem, and the user name in Linux by default is set to PceWlkr.

## set up virtual machine

### Basics

use the B1s as the bare bone std

The first vm set in the group will need to generate a new key pair of SSH public key, and give it a name.

Others can just use the SSH we had already.

Set the public inbound ports.

(we set this up for testing purpose only, so the fact that all IP addrs will be allowed to access my vms doesn't bother.)

### Disks

go with standard SSD

encryption type: default

### Networking

virtual network: default

subnet: default

public IP: use the namenode ip

NIC network security group: Basic

Public inbound ports: allow selected ports

inbound ports

### Management

auto-shutdown: do this since we may forgot shut then down and lose money for that

### Advanced

### Tags

### Review and create

review the price with extra cautions

### Additional info

it takes some time to create/

Download the private SSH key pair and store it carefully



#### Better to have WinSCP available

SFTP

host name is the public ip

port num is what we set before

input the .pem file, which is the OpenSSH private key

- WinSCP will convert it to .ppk

call this node namenode



when connected, you can see the /home/PceWlkr/

#### Putty

use putty to login the server

`sudo apt-get update ` //update the linux sys and everything installed

## Add VMI to your Virtual Network

Now we should have a public IP addr 
Everytime you restart the VM, it will give you a new public IP.
Remember to update the IP you used for WinSCP.

We are going to create more VMs as the DataNodes.

Resource group: VirtualMachines
name: DataNode001
image: Ubuntu Server 20.04 LTS Gen 1 
Size: Std B1s

Username: PceWlkr 
SSH: SSH_keypair
inbound ports: SSH(22)

OS disk type: std
subnet: make it on the same network as last time
security group: Basic (will adjust later)

enable the auto-shutdown like before

Go to Virtual Machines on the dash board, check if everything is there. 
Go to the namenode and copy the IP addr and then go the WinSCP to change the host.
Clone it to a new site. 
Edit the newsite to register the datanode001 on WinSCP like before.

Go the terminal to check if things are there.

![VM Network](img/VM_Network.png)

## Add a DNS to your VMI

Go to the namenode and click the DNS name.
Enter a DNS name label which must be unique that nobody else has reserved.
Now we can use the DNS in the WinSCP instead of IP.

## Network Security Ports 

Go to the VM and then go to the Networking setting.
Add inbound port rule.
Service shoule be changed to SSH.
Priority: 100
Name: SSH_22

Then add another inbound port for the remote desktop.
Service:RDP
Priority: 110 (lower then SSH)
Name: RDP_3389

![Common Setting for Ports](/img/Common_Setting_for_Ports.png)

Also set up a PPK SSH for the namenode.
Go to Advanced and Authentication, and go for the .pub in the private key file.
Convert it and then save and login.

## Update/Upgrade Packages

### Disable Unattended Upgrades
`sudo apt -y remove unattended-upgrades`
or if you do not want to remove Unattended-Upgrades, use
`sudo dpkg-reconfigure unattended-upgrades` to turn on or off the features.

### Update and Upgrade Packages
`sudo apt-get update -y && sudo apt-get upgrade -y`

## Hadoop Installation and Configuration

You will learn the following:

Administrate users in Ubuntu
Manage environment variables in Ubuntu
Use Secure Shell (SSH) to communicate with Virtual Machines
Hadoop Prerequistes
  Java Developers Kit (JDK)
  pdsh
  rsync
Install and Configure Hadoop
Administrate Hadoop configuration files
Test HDFS commands
Test MapReduce using sample applications

### Cluster Environment Setup
We have done this:
`sudo apt -y remove unattended-upgrades && sudo apt-get update -y && sudo apt-get upgrade -y`

#### Secure Admin Account
In this case, my admin account's name is PceWlkr.
`sudo passwd PceWlkr`

#### Common Environment Variables for Hadoop Clusters

In general, these include:

Java - JAVA_HOME
Hadoop - HADOOP_HOME
Hive - HIVE_HOME
Pig - PIG_HOME

The `etc/profile.d` dir holding shell script will be run at start-up and install fuatures for all users.
So we'll create a file `bigdata.sh` for later installations.

`sudo touch /etc/profile.d/bigdata.sh`

```bash
export BigDataSH=/etc/profile.d/bigdata.sh
export IdentityFile="/.ssh/SSH_keypair.pem"
export SSHConfigFile="/.ssh/config"

sudo rm $BigDataSH
echo -e '#!/bin/bash \n' | sudo tee --append $BigDataSH > /dev/null
echo -e "# Environment Variables for Big Data tools\n" | sudo tee --append $BigDataSH > /dev/null
echo -e "export BigDataSH=${BigDataSH}" | sudo tee --append $BigDataSH > /dev/null
echo -e "export IdentityFile=~${IdentityFile}" | sudo tee --append $BigDataSH > /dev/null
echo -e "export SSHConfigFile=~${SSHConfigFile}\n" | sudo tee --append $BigDataSH > /dev/null

sudo chmod 644 $BigDataSH
```

`cat $BigDataSH`

The result should be:
```bash
#!/bin/bash

# Environment Variables for Big Data tools

export BigDataSH=/etc/profile.d/bigdata.sh
export IdentityFile=~/.ssh/SSH_keypair.pem
export SSHConfigFile=~/.ssh/config
```

`sudo reboot`

After rebooting, confirm the vars exist.
`echo $BigDataSH $IdentityFile $SSHConfigFile`

The result should be:
/etc/profile.d/bigdata.sh /home/PceWlkr/.ssh/SSH_keypair.pem /home/PceWlkr/.ssh/config

#### Map Virtual Machine Cluster Environment Variables 

1. Collect the Public DNS and "Internal" IP addresses from your Virtual Machine Instances
2. Copy and paste the following commands into a text editor:
```
export NameNodeDNS="Namenode"
export DataNode001DNS="Datanode001"
export DataNode002DNS="Datanode002"
export DataNode003DNS="Datanode003"
export DataNode004DNS="Datanode004"

export NameNodeIP="10.0.0.4"
export DataNode001IP="10.0.0.5"
export DataNode002IP="10.0.0.6"
export DataNode003IP="10.0.0.7"
```

Notice:
you can use `hostname` and `hostname -I` to find the hostname and private IP 

Copy all of the commands above from the text editor
Paste the commands into the SSH session at the command line for your Virtual Machine Instance
When you pasted the commands, they were executed automatically.
Try to use one of the variables we set:
`echo $DataNode001DNS`

The result should look something like this:

`Datanode001`

Execute the following to add the schema to the bigdata.sh file:
```bash
echo -e "# Cluster Variables START" | sudo tee --append $BigDataSH > /dev/null
echo -e "export NameNodeDNS=\"${NameNodeDNS}\"" | sudo tee --append $BigDataSH > /dev/null
echo -e "export DataNode001DNS=\"${DataNode001DNS}\"" | sudo tee --append $BigDataSH > /dev/null
echo -e "export DataNode002DNS=\"${DataNode002DNS}\"" | sudo tee --append $BigDataSH > /dev/null
echo -e "export DataNode003DNS=\"${DataNode003DNS}\"" | sudo tee --append $BigDataSH > /dev/null
echo -e "export DataNode004DNS=\"${DataNode004DNS}\"" | sudo tee --append $BigDataSH > /dev/null
echo -e "" | sudo tee --append $BigDataSH > /dev/null
echo -e "export NameNodeIP=\"${NameNodeIP}\"" | sudo tee --append $BigDataSH > /dev/null
echo -e "export DataNode001IP=\"${DataNode001IP}\"" | sudo tee --append $BigDataSH > /dev/null
echo -e "export DataNode002IP=\"${DataNode002IP}\"" | sudo tee --append $BigDataSH > /dev/null
echo -e "export DataNode003IP=\"${DataNode003IP}\"" | sudo tee --append $BigDataSH > /dev/null
echo -e "export DataNode004IP=\"${DataNode004IP}\"" | sudo tee --append $BigDataSH > /dev/null
echo -e "# Cluster Variables END" | sudo tee --append $BigDataSH > /dev/null
```

Then Display the contents of the $BigDataSh file:
`cat $BigDataSH`

result should be like:
```
#!/bin/bash

# Environment Variables for Big Data tools

export BigDataSH=/etc/profile.d/bigdata.sh
export IdentityFile=~/.ssh/SSH_keypair.pem
export SSHConfigFile=~/.ssh/config

# Cluster Variables START
export NameNodeDNS="Namenode"
export DataNode001DNS="Datanode001"
export DataNode002DNS="Datanode002"
export DataNode003DNS="Datanode003"
export DataNode004DNS="Datanode004"

export NameNodeIP="10.0.0.4"
export DataNode001IP="10.0.0.5"
export DataNode002IP="10.0.0.6"
export DataNode003IP="10.0.0.7"
export DataNode004IP="10.0.0.8"
# Cluster Variables END
```

`sudo reboot`
test if the var is still accessible:
`echo $Datanode001DNS`
result should be like:
Datanode001

#### Passwordless SSH for Cluster: Using .config and .pem files

Edit a file named config:
The file maintains information about SSH connections for our cluster:

HostName - entry is the Public DNS or IPV4 value for the Virtual Machine instance.
User - the default user name for your Virtual Machine instance is ubuntu
IdentityFile - path to the Private Key (.pem) file you created while setting up your instances
  The default key files that are created by SSH are: 
    id_rsa - private key file
      The .pem file is simply the id_rsa file with the extension of .pem 
    id_rsa.pub - public key file generated during ssh-keygen or the creation of the first Virtual Machine Instance
    id_rsa.ppk - private key file created by PuTTY when you connect through WinSCP

```

Host *
  User PceWlkr
  IdentityFile ~/.ssh/SSH_keypair.pem

Host 0.0.0.0
  HostName 127.0.0.1

Host Namenode
  Hostname 10.0.0.4

Host Datanode001
  Hostname 10.0.0.5

Host Datanode002
  Hostname 10.0.0.6

#Host Datanode00n
#  Hostname xxx.xxx.xxx.xxx
```
Then put `config` and `.pem file` into the ~/.ssh/



The following code will build the config file from the command line:

Previously, we set up the $SSHConfigFile variable in bigdata.sh. It should point to ~/.ssh/config.

The following commands will build a config file based on environment variables that were set up earlier:
```bash
sudo rm -rf $SSHConfigFile

echo -e "Host *" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "  User PceWlkr" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "  IdentityFile ${IdentityFile}" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "Host ${NameNodeDNS}" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "  Hostname ${NameNodeIP}" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "Host ${DataNode001DNS}" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "  Hostname ${DataNode001IP}" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "Host ${DataNode002DNS}" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "  Hostname ${DataNode002IP}" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "Host ${DataNode003DNS}" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "  Hostname ${DataNode003IP}" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "Host ${DataNode004DNS}" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "  Hostname ${DataNode004IP}" | sudo tee --append $SSHConfigFile > /dev/null
echo -e "" | sudo tee --append $SSHConfigFile > /dev/null
```
Set the permissions levels of the $SSHConfigFile file:
`sudo chmod 644 $SSHConfigFile`

Then copy the config file and .pem from namenode to other nodes using:
`scp ~/.ssh/config ~/.ssh/SSH_keypair.pem PceWlkr@Datanode001:~/.ssh/`
or `scp -p $SSHConfigFile $IdentityFile PceWlkr@Datanode001:~/.ssh/`

Then use `ssh Datanode001` to access a node.
`exit` to quit.

**Repeat till all other nodes realize the same functions**


### Java Developers Kit (JDK)
`sudo apt-get -y update`
`sudo apt-get -y install default-jdk`

confirm you have it:
`cd /usr/lib/jvm/ && ls`
result should be like:
default-java  java-1.11.0-openjdk-amd64  java-11-openjdk-amd64  openjdk-11

#### Add Environment Variables to /etc/profile.d/bigdata.sh

Open the file /etc/profile.d/bigdata.sh
`sudo nano $BigDataSH`

Add these lines to the end of the bigdata.sh file:
```bash
export JAVA_HOME=/usr/lib/jvm/default-java
PATH=$PATH:$JAVA_HOME/bin
```

Or, copy the following code and paste it at the command line. This will echo the entries into the bigdata.sh file
```bash
echo "# JAVA Variables START" | sudo tee --append $BigDataSH > /dev/null
echo "export JAVA_HOME=/usr/lib/jvm/default-java" | sudo tee --append $BigDataSH > /dev/null
echo "PATH=\$PATH:\$JAVA_HOME/bin" | sudo tee --append $BigDataSH > /dev/null
echo "# JAVA Variables END" | sudo tee --append $BigDataSH > /dev/null
```
Confirm that your Java variables were added, open the /etc/profile.d/bigdata.sh file:
`cat $BigDataSH`

Your bigdata.sh file output should look like this now:
```bash
#!/bin/bash
# Environment Variables for Big Data tools
export BigDataSH=/etc/profile.d/bigdata.sh
export IdentityFile=/etc/ssh/ssh_config.d/SSH_keypair.pem
export SSHConfigFile=/etc/ssh/ssh_config.d/config.conf

# JAVA Variables START
export JAVA_HOME=/usr/lib/jvm/default-java
PATH=$PATH:$JAVA_HOME/bin
# JAVA Variables END
```

`sudo reboot`

#### Confirm the Java Version and Environment Variables

`java -version`

result should be like:
```
openjdk version "11.0.11" 2021-04-20
OpenJDK Runtime Environment (build 11.0.11+9-Ubuntu-0ubuntu2.18.04)
OpenJDK 64-Bit Server VM (build 11.0.11+9-Ubuntu-0ubuntu2.18.04, mixed mode, sharing)
```

confirm you have it:
`echo $JAVA_HOME`
result should be like:
/usr/lib/jvm/default-java


#### Java Developers Kit (JDK) for DataNodes in a Cluster

To send a command to a DateNode using SSH, we wrap the command with quotes "" and ssh into the Node:
`ssh DataNode001 "{command}"`

##### Update packages on the server
`ssh Datanode001 "sudo apt-get -y update"`
`ssh Datanode001 "sudo apt-get -y install default-jdk"`

Notice:
You will synchronize your DataNodes with the NameNode later, so you do not need to set up environment variables yet.

#### pdsh 
```
pdsh is a variant of the rsh(1) command. Unlike rsh(1), which runs commands on a single remote host, pdsh can run multiple remote commands in parallel. pdsh uses a "sliding window" (or fanout) of threads to conserve resources on the initiating host while allowing some connections to time out.
```

`sudo apt-get -y install pdsh`
Add the following variable to your bigdata.sh file:
`export PDSH_RCMD_TYPE=ssh`

or run this commands:
```bash
echo -e "# PDSH Variables START" | sudo tee --append $BigDataSH > /dev/null
echo -e "export PDSH_RCMD_TYPE=ssh" | sudo tee --append $BigDataSH > /dev/null
echo -e "# PDSH Variables END" | sudo tee --append $BigDataSH > /dev/null
```
`sudo reboot`

#### rsync
```
rsync is a utility for efficiently transferring and synchronizing files across computer systems by checking the timestamp and size of files. It is commonly found on Unix-like systems and functions as both a file synchronization and file transfer program. The rsync algorithm is a type of delta encoding and is used for minimizing network usage. Zlib may be used for additional compression, and SSH or stunnel can be used for data security. rsync(1) - Linux man page
```
`sudo apt-get -y install rsync`

`sudo reboot`

## Install and Configure Hadoop as a Single Node Cluster

### Download Hadoop from Apache

`wget http://apache.forsale.plus/hadoop/common/hadoop-3.3.1/hadoop-3.3.1.tar.gz -P ~/Downloads/Hadoop`

### Uncompress the Hadoop tar file into the /usr/local folder
`sudo tar -zxvf ~/Downloads/Hadoop/hadoop-*.tar.gz -C /usr/local`

### Rename the hadoop-* directory to /usr/local/hadoop
`sudo mv /usr/local/hadoop-* /usr/local/hadoop/`

### Modify permissions on /usr/local/hadoop/
Allow read+write on the /usr/local/hadoop/ directory for anyone in the hadoop user group.

#### Double Check!
Before you run this command, confirm that you have created a hadoop user group. See Hadoop User and Group

##### Hadoop User named (hduser) and User Group named (hadoop)

###### Add the hadoop User Group

`sudo addgroup hadoop`
`sudo adduser hduser`

###### Add hduser to User Groups
Run this command to add hduser to the hadoop user group:
`sudo usermod -a -G hadoop hduser`

Run this command to add hduser to the sudo (superuser) user group:
`sudo usermod -a -G sudo hduser`

We will also add the  user to the hadoop user group.
`sudo usermod -a -G hadoop PceWlkr`

Now you can switch to hduser when you type this command:
`su - hduser`

Confirm which groups hduser is a member of:
`groups hduser`

The result should look something like this:
hduser : hduser sudo hadoop

`sudo reboot`
`su - hduser`

#### Back to Topic
This command will set ownership of all files and directories in the /usr/local/hadoop/ directory:
`sudo chown PceWlkr:PceWlkr -R /usr/local/hadoop/`

Set the permissions on the /usr/local/hadoop/ directory to rwxrwxr--:

rwxrwxr--
User and Group: Read + Write + Execute
Other users: read

`sudo chmod -R 774 /usr/local/hadoop/`

#### Set Environment Variables
Use this command to edit the /etc/profile.d/bigdata.sh file:

`sudo nano $BigDataSH`

```
export HADOOP_HOME="/usr/local/hadoop"
export HADOOP_CONF_DIR="${HADOOP_HOME}/etc/hadoop"
export YARN_EXAMPLES="${HADOOP_HOME}/share/hadoop/mapreduce"
PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

or run these:
```bash
echo -e "# HADOOP Variables START" | sudo tee --append $BigDataSH > /dev/null
echo -e "export HADOOP_HOME=\"/usr/local/hadoop\"" | sudo tee --append $BigDataSH > /dev/null
echo -e "export HADOOP_CONF_DIR=\"\${HADOOP_HOME}/etc/hadoop\"" | sudo tee --append $BigDataSH > /dev/null
echo -e "export YARN_EXAMPLES=\"\${HADOOP_HOME}/share/hadoop/mapreduce\"" | sudo tee --append $BigDataSH > /dev/null
echo -e "PATH=\$PATH:\$HADOOP_HOME/bin:\$HADOOP_HOME/sbin" | sudo tee --append $BigDataSH > /dev/null
echo -e "# HADOOP Variables END" | sudo tee --append $BigDataSH > /dev/null
```

#### Instantiate Environment Variables
The following command will instantiate the new variables available immediately.  You can use this method to instantiate variables in any of the modified shell scripts .sh files:

`source $BigDataSH`

test if it's there:
`echo $HADOOP_HOME`

result should be:
/usr/local/hadoop 

`sudo reboot`

#### Test Environment Variables
`echo $HADOOP_HOME`

#### Test Hadoop Version
`hadoop version`

### Hadoop Configuration Files

#### Add JAVA_HOME Variable to $HADOOP_CONF_DIR/hadoop-env.sh

`echo $JAVA_HOME`

`sudo nano $HADOOP_CONF_DIR/hadoop-env.sh`

Locate the area in the hadoop-env.sh file that indicates the JAVA_HOME variable. For Hadoop 3.3.1, this is on line 54.

The line should look something like this:

`# export JAVA_HOME=`

Change the line entry to look like this statement; this value should be the same as your JAVA_HOME environment variable:

`export JAVA_HOME=/usr/lib/jvm/default-java`
`sed -i 's/# export JAVA_HOME=/export JAVA_HOME=\/usr\/lib\/jvm\/default-java # export JAVA_HOME=/g' $HADOOP_CONF_DIR/hadoop-env.sh`

`cat $BigDataSH`
result should be:
```
# The java implementation to use. By default, this environment
# variable is REQUIRED on ALL platforms except OS X!
export JAVA_HOME=/usr/lib/jvm/default-java
```

#### Modify $HADOOP_CONF_DIR/core-site.xml

`sudo nano $HADOOP_CONF_DIR/core-site.xml`
Replace the contents of the core-site.xml file <configuration></configuration> section with the following lines: 
```xml
<configuration>

	<!--Custom Properties-->
	<property>

		<name>thisnamenode</name>

		<value>localhost</value>

		<description>This used as a variable throughout the configuration files.
		localhost may be replaced with a DNS that points to the NameNode.
		</description>

	</property>

	<property>

		<name>homefolder</name>

		<value>/home/${user.name}</value>

	</property>

	<property>

		<name>fs.defaultFS</name>

		<value>hdfs://${thisnamenode}:9000</value>

		<description>localhost may be replaced with a DNS that points to the NameNode.</description>

	</property>

	<!-- Do not enable permission check -->
	<property>

		<name>dfs.permissions.enabled</name>

		<value>false</value>

		<description>If "true", enable permission checking in HDFS. If "false", permission checking is turned off, but all other behavior is unchanged.
		Switching from one parameter value to the other does not change the mode, owner or group of files or directories.
		</description>

	</property>


	<!-- The current user is all set to root -->
	<property>

		<name>hadoop.http.staticuser.user</name>

		<!-- For Virtual Machine Instances in the Cloud, use ubuntu -->
		<!-- <value>Pcewlkr</value> -->
		<value>Pcewlkr</value>

		<description>Pcewlkr is the default user for our NameNode. This property sets the WebUI user for file browsing.
For Virtual Machine Instances in the Cloud, use Pcewlkr
		</description>

	</property>

</configuration>
```
#### Modify $HADOOP_CONF_DIR/yarn-site.xml
`sudo nano $HADOOP_CONF_DIR/yarn-site.xml`

```xml
<configuration>

	<property>

		<name>yarn.nodemanager.aux-services</name>

		<value>mapreduce_shuffle</value>

	</property>

	<property>

		<name>mapred.job.tracker</name>

		<value>${thisnamenode}:9001</value>

	</property>

	<property>

		<name>yarn.nodemanager.env-whitelist</name>

		<value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>

	</property>

</configuration>
```
#### Modify $HADOOP_CONF_DIR/mapred-site.xml
`sudo nano $HADOOP_CONF_DIR/mapred-site.xml`

```xml
<configuration>

	<property>

		<name>mapreduce.jobtracker.address</name>

		<value>local</value>

	</property>

	<property>

		<name>mapreduce.framework.name</name>

		<value>yarn</value>

	</property>

</configuration>
```

#### Modify $HADOOP_CONF_DIR/hdfs-site.xml
`sudo nano $HADOOP_CONF_DIR/hdfs-site.xml`

```xml
<configuration>

	<property>

		<name>dfs.replication</name>

		<!--<value>3</value>-->
		<value>1</value>

		<description>Default block replication.

The actual number of replications can be specified when the file is created.

The default is used if replication is not specified in create time.

When migrating your cluster to a Fully Distributed Cluster, change this to 3.

		</description>

	</property>

	<property>

		<name>dfs.permissions.enabled</name>

		<value>false</value>

		<description>If "true", enable permission checking in HDFS. If "false", permission checking is turned off, but all other behavior is unchanged.
		Switching from one parameter value to the other does not change the mode, owner or group of files or directories.
		</description>

	</property>

	<property>

		<name>dfs.namenode.name.dir</name>

		<value>file:///home/${user.name}/hadoop/dfs/name</value>

		<description>Determines where on the local filesystem the DFS name node should store the name table(fsimage).
		If this is a comma-delimited list of directories then the name table is replicated in all of the directories, for redundancy.
		</description>

	</property>

	<property>

		<name>dfs.datanode.data.dir</name>

		<value>file:///home/${user.name}/hadoop/dfs/data</value>

		<description>Determines where on the local filesystem an DFS data node should store its blocks. If this is a comma-delimited list of directories,
		then data will be stored in all named directories, typically on different devices. Directories that do not exist are ignored.
		</description>

	</property>

	<property>

		<name>dfs.namenode.checkpoint.dir</name>

		<value>file:///home/${user.name}/hadoop/dfs/namesecondary</value>

		<description>Determines where on the local filesystem the DFS secondary name node should store the temporary images to merge.
		If this is a comma-delimited list of directories then the image is replicated in all of the directories for redundancy.
		</description>

	</property>

	<property>

		<name>dfs.journalnode.edits.dir</name>

		<value>file:///home/${user.name}/hadoop/dfs/journalnode</value>

		<description>
		The directory where the journal edit files are stored.
		</description>

	</property>

</configuration>
```

### Format the HDFS
`hdfs namenode -format`



### Start up your Hadoop Cluster

`start-dfs.sh`

check the namenode  `http://DNSofNameNode:9870`

`start-yarn.sh`

`mapred --daemon start historyserver`

check the history website  `http://DNSofNameNode:19888`

check the resource manager website  `http://DNSofNameNode:8088`

`jps` will show you the running java processes



On windows, edit `C:\Windows\System32\drivers\etc\hosts`, add

```
xxx.xxx.xxx.xxx   namenode.internal.cloudapp.net  namenode
xxx.xxx.xxx.xxx   datanode001.internal.cloudapp.net  datanode001
xxx.xxx.xxx.xxx   datanode002.internal.cloudapp.net  datanode002
```



So that Windows can find the interal website http://namenode:9870 or http://namenode.internal.cloudapp.net:9870



### Test the HDFS

`hdfs dfsadmin -report`

`hdfs dfs -mkdir -p /user/PceWlkr/hdfs/tests`

`mkdir -p ~/Documents/HDFS/Tests`

`touch ~/Documents/HDFS/Tests/test.txt`

`hdfs dfs -put ~/Documents/HDFS/Tests/test.txt /user/PceWlkr/hdfs/tests`

`hdfs dfs -ls /user/PceWlkr/hdfs/tests`



## Fully Distributed Hadoop Cluster on a Cloud Provider

![Fully Distributed Mode Config Files](/img/Fully_Distributed_Mode_Config_Files.png)

These configuration files include:

- SSH
  - config
  - SSH_keypair.pem
- Environment Variables
  - bigdata.sh
- Environment Setup
  - Java JDK
  - pdsh
  - rsync
- Hadoop Configuration - the Hadoop entire directory is usually copied directly to the new DataNodes
  - hadoop-env.sh
  - hdfs.site.xml
  - core-site.xml
  - yarn-site.xml
  - mapred-site.xml
  - masters
  - workers

### Configure .masters File 

`sudo touch $HADOOP_CONF_DIR/masters`
Add the DNS of your Master Node:
```
Namenode
Datanode001 
Datanode002 
```
`sudo chown ubuntu $HADOOP_CONF_DIR/masters`
`sudo chmod 0644 $HADOOP_CONF_DIR/masters`

### Configure .workers File 

'sudo nano $HADOOP_CONF_DIR/workers'
Add the entries for the Data Nodes:

```
Datanode001 
Datanode002 
```
`sudo chown ubuntu $HADOOP_CONF_DIR/workers`
`sudo chmod 0644 $HADOOP_CONF_DIR/workers`

### Modify configuration files for Fully Distributed Cluster 

`sudo nano $HADOOP_CONF_DIR/core-site.xml`

Change the thisnamenode property value to NameNode in the core-site.xml file. 
```xml
<property>

    <name>thisnamenode</name>

    <value>NameNode</value>

    <description>NameNode is the hostname specified in the config file and etc/hosts file.
It may be replaced with a DNS that points to your NameNode.</description>

  </property>
```

`sudo nano $HADOOP_CONF_DIR/hdfs-site.xml`

Edit the dfs.replication property in hdfs-site.xml. 

```xml
<property>

    <name>dfs.replication</name>

    <value>3</value>

    <description>Default block replication.

The actual number of replications can be specified when the file is created.

The default is used if replication is not specified in create time.

    </description>

  </property>
```

### Add a DataNode to your Hadoop Cluster 

`stop-dfs.sh`

`scp -p $SSHConfigFile $IdentityFile PceWlkr@Datanode001:~/.ssh/`

`scp -p $SSHConfigFile $IdentityFile PceWlkr@Datanode002:~/.ssh/`



`ssh Datanode001`

`sudo apt -y remove unattended-upgrades &&
sudo apt-get -y update &&
sudo apt-get -y upgrade &&
sudo apt-get -y install default-jdk pdsh rsync`

`exit`



`ssh Datanode002`

`sudo apt -y remove unattended-upgrades &&
sudo apt-get -y update &&
sudo apt-get -y upgrade &&
sudo apt-get -y install default-jdk pdsh rsync`

`exit`



If the `hadoop` directory does not exist on the DataNode, you must first create it. Do this for each DataNode in your Cluster.
Create the `hadoop` directory:

`ssh PceWlkr@Datanode001 "sudo mkdir -p ${HADOOP_HOME}/"`

`ssh PceWlkr@Datanode001 "sudo chown -R PceWlkr:PceWlkr ${HADOOP_HOME}/"`

`ssh PceWlkr@Datanode001 "sudo chmod -R 774 ${HADOOP_HOME}/"`



`ssh PceWlkr@Datanode002 "sudo mkdir -p ${HADOOP_HOME}/"`

`ssh PceWlkr@Datanode002 "sudo chown -R PceWlkr:PceWlkr ${HADOOP_HOME}/"`

`ssh PceWlkr@Datanode002 "sudo chmod -R 774 ${HADOOP_HOME}/"`



`cat $BigDataSH | ssh PceWlkr@Datanode001 "sudo tee ${BigDataSH}"`

`cat $BigDataSH | ssh PceWlkr@Datanode002 "sudo tee ${BigDataSH}"`



`sudo rm $HADOOP_HOME/logs/*.*`



`rsync -ravl $HADOOP_HOME PceWlkr@Datanode001:/usr/local/`

`rsync -ravl $HADOOP_HOME PceWlkr@Datanode002:/usr/local/`



Then reboot the Datanode001 and Datanode002



### Test the HDFS Again



### Test MapReduce 

`hadoop jar $YARN_EXAMPLES/hadoop-mapreduce-examples-3.3.1.jar`

`hdfs dfs -mkdir -p /user/PceWlkr/wordcount/input`

`hdfs dfs -chmod -R 777 /user`

`hdfs dfs -put $HADOOP_HOME/*.txt    /user/PceWlkr/wordcount/input`



`hadoop jar $YARN_EXAMPLES/hadoop-mapreduce-examples-3.3.1.jar  wordcount /user/ubPceWlkrntu/wordcount/input /user/PceWlkr/wordcount/output`



`hdfs dfs -cat /user/PceWlkr/wordcount/output/*`





# Env Vars

#### The hadoop config directory

HADOOP_CONF_DIR

#### The directory of the progtram

JAVA_HOME

HADOOP_HOME

HIVE_HOME

PIG_HOME

HADOOP_YARN_HOME

HADOOP_MAPRED_HOME

#### The path of the files under ~/.ssh/

BigDataSH=/etc/profile.d/bigdata.sh

IdentityFile=~/.ssh/SSH_keypair.pem

SSHConfigFile=~/.ssh/config

#### The following are DNS and IP for all nodes

NameNodeDNS="Namenode"

DataNode001DNS="Datanode001"

DataNode002DNS="Datanode002"

NameNodeIP="10.0.0.4"

DataNode001IP="10.0.0.5"

DataNode002IP="10.0.0.6"



# Configs

- SSH
  - config --> give a identity to anyone enter through ssh, and give all host a IP and DNS so that ssh can recognize them
  - SSH_keypair.pem --> The ssh key
- Environment Variables
  - bigdata.sh --> storing env vars for ssh config and files, java vars, pdsh vars, and other vars
- Hadoop Configuration - the Hadoop entire directory is usually copied directly to the new DataNodes
  - hadoop-env.sh --> some env vars
  - hdfs.site.xml --> config for HDFS
  - core-site.xml --> config for Hadoop and Namenode
  - yarn-site.xml --> config for yarn
  - mapred-site.xml --> config for MapReduce
  - masters --> specify the master nodes
  - workers --> specify the slave nodes

# Daemons

HDFS Daemons: **They are configured by masters and workers and hdfs.site.xml and core-site.xml**

Name Node 

Secondary Name Node 

Data Node



YARN Daemons: **They are configured by yarn-site.xml**

Resource Manager 

Node Manager



MapReduce Daemon: **They are configured by mapred-site.xml**

Job History Server



# Cluster Tests

## HDFS Test Result

## MapReduce Test Result










