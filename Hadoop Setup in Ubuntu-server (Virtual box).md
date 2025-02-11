---
title: Hadoop Setup in Ubuntu-server (Virtual box)
date: 2025-02-10
author: Akash
tags:
  - Hadoop
  - Yarn
  - Big_Data
  - Ubuntu
summary: Step-by-Step Guide to Setup Hadoop Single Node in the local environment
---

This guide will walk you through setting up Hadoop on an Ubuntu Server running in VirtualBox. Follow the step-by-step instructions to install prerequisites, configure your system, and get Hadoop up and running.

---
## Prerequisites 
- [x] **Setup VirtualBox** 
- [x] **Install Ubuntu Server** 

## Updating and Upgrading Ubuntu

Open the Ubuntu terminal and run the following commands:
```bash
sudo apt-get update
sudo apt-get upgrade
```

## Installing SSH and Parallel Shell

Install SSH and `pdsh` (Parallel Distributed Shell):
```bash
sudo apt-get install ssh
sudo apt-get install pdsh
```

### Check SSH Status
```bash
sudo systemctl status ssh
```

### Start SSH Service
```bash
sudo systemctl start ssh
```

### Enable SSH at Boot
```bash
sudo systemctl enable ssh
```

---
## Enable Port Forwarding in VirtualBox Networks

For instructions on configuring port forwarding, follow this reference:  
[SSH into VirtualBox Machine](https://dev.to/developertharun/easy-way-to-ssh-into-virtualbox-machine-any-os-just-x-steps-5d9i)

---

## Creating a User for Hadoop

Create a new user (named `hdoop`) to run Hadoop:
```bash
sudo adduser hdoop
```

### Add User to the Sudo Group
```bash
sudo adduser hdoop sudo
```

### Switch to the New User
```bash
su - hdoop
```

---


## Connecting to Ubuntu via VSCode

Using VSCode to SSH into your Ubuntu server simplifies copying and pasting commands.

### Connect Using VSCode
```bash
ssh -p 3022 hdoop@127.0.0.1
```

### Alternative Connection Method
```bash
ssh hdoop@127.0.0.1:3022
```


## Installing Java 8 (Recommended for Hadoop)

Install Java 8:
```bash
sudo apt install openjdk-8-jdk
```


### Check Java Path
```bash
whereis java
```

---


## Downloading Hadoop

Download Hadoop (version 3.4.1 in this example):
```bash
wget https://dlcdn.apache.org/hadoop/common/hadoop-3.4.1/hadoop-3.4.1.tar.gz
```

### Extract the package
```bash
tar xzf hadoop-3.4.1.tar.gz
```

---


## Configuring Hadoop
Follow these steps to configure Hadoop:

### Step 1: Edit the `.bashrc` File

Open the `.bashrc` file:
```bash
nano ~/.bashrc
```
Add these lines at the bottom:
```bash
#Hadoop Related Options  
export HADOOP_HOME=/home/hdoop/hadoop-3.4.1  
export HADOOP_INSTALL=$HADOOP_HOME  
export HADOOP_MAPRED_HOME=$HADOOP_HOME  
export HADOOP_COMMON_HOME=$HADOOP_HOME  
export HADOOP_HDFS_HOME=$HADOOP_HOME  
export YARN_HOME=$HADOOP_HOME  
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native  
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin  
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"

# JAVA Home Path
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

Apply the Changes
```bash
source ~/.bashrc
```

### Step 2: Edit `hadoop-env.sh`

Open the Hadoop environment configuration file:
```bash
sudo nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```

Add the Java Path at the End of the File:
```bash
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

### Step 3: Edit `core-site.xml`

Open the core configuration file:
```bash
sudo nano $HADOOP_HOME/etc/hadoop/core-site.xml
```

Insert the Following Lines Between the `<configuration>` Tags:
```bash
<property>  
	<name>hadoop.tmp.dir</name>  
	<value>/home/hdoop/tmpdata</value>  
	<description>A base for other temporary directories.</description>  
 </property>  
 <property>  
	<name>fs.default.name</name>  
	<value>hdfs://localhost:9000</value>  
	<description>The name of the default file system></description>  
</property>
```

### Step 4: Edit `hdfs-site.xml`

Open the HDFS configuration file:
```bash
sudo nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```

Insert the Following Lines Between the `<configuration>` Tags:
```bash
<property>  
	<name>dfs.data.dir</name>  
	<value>/home/hdoop/dfsdata/namenode</value>  
</property>  
<property>  
	<name>dfs.data.dir</name>  
	<value>/home/hdoop/dfsdata/datanode</value>  
</property>  
<property>  
	<name>dfs.replication</name>  
	<value>1</value>  
</property>
```

### Step 5: Edit `mapred-site.xml`

Open the MapReduce configuration file:
```bash
sudo nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```

Insert the Following Line Between the `<configuration>` Tags:
```bash
<property>  
	<name>mapreduce.framework.name</name>  
	<value>yarn</value>  
</property> 
```

### Step 6: Edit `yarn-site.xml`

Open the YARN configuration file:
```bash
sudo nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
```

Insert the Following Lines Between the `<configuration>` Tags:
```bash
<property>  
	<name>yarn.nodemanager.aux-services</name>  
	<value>mapreduce_shuffle</value>  
</property>  
<property>  
	<name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>  
	<value>org.apache.hadoop.mapred.ShuffleHandler</value>  
</property>  
<property>  
	<name>yarn.resourcemanager.hostname</name>  
	<value>127.0.0.1</value>  
</property>  
<property>  
	<name>yarn.acl.enable</name>  
	<value>0</value>  
</property>  
<property>  
	<name>yarn.nodemanager.env-whitelist</name>  
<value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PERPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>  
</property>

<property> 
	<name>yarn.resourcemanager.webapp.address</name>
	<value>127.0.0.1:8088</value> 
</property>
<property>
	<name>yarn.nodemanager.vmem-check-enabled</name>
	<value>false</value> </property>
<property>
	<name>yarn.nodemanager.pmem-check-enabled</name>
	<value>false</value>
</property>
```

---
## Initialize Hadoop

Format the Hadoop filesystem:
```bash
hdfs namenode -format
```

---
## Creating SSH Key Pair for Passwordless SSH

Generate an SSH key pair and set up passwordless SSH:
```bash
ssh-keygen -t rsa -N "" -f ~/.ssh/id_rsa

cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

Verify the keys
```bash
chown -R hdoop:hdoop ~/.ssh
```
---
## Starting Hadoop

Start all Hadoop services:
```bash
start-all.sh
```

---
### Testing the Hadoop Node

Open your browser to access the Hadoop web interfaces:

- **HDFS Web UI:** [http://localhost:9870](http://localhost:9870)
- **YARN Web UI:** [http://localhost:8088](http://localhost:8088)

Alternate access (if the above one not working):
- **HDFS Web UI:** [http://127.0.0.1:9870](http://127.0.0.1:9870)
- **YARN Web UI:** [http://127.0.0.1:8088](http://127.0.0.1:8088)
---

## Stopping Hadoop Services

When you need to stop Hadoop, run:
```bash
stop-all.sh
```

---------------------------------------------------
## Common Troubleshooting Commands

### View YARN Resource Manager Logs
```bash
tail -n 50 $HADOOP_HOME/logs/hadoop-hdoop-resourcemanager-cluster-node-1.log
```

### Check Running Hadoop Services
```bash
jps
```

### Check Java Version
```bash
java -version
```

### Verify Java Path
```bash
echo $JAVA_HOME
```

---
### References:
[Hadoop Official documentation](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)
[Useful Blog](https://medium.com/@sattar.husnain123/hadoop-single-node-installation-on-ubuntu-d73da510b57e)

---

Everything is Done. Enjoy tinkering with Hadoop🐘.

