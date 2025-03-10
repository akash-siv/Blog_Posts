---
title: Spark Installation and Setup Tutorial
date: 2025-03-10
author: Akash
tags:
  - Spark
  - Hadoop
  - pyspark
summary: This guide covers installing Apache Spark and running a Spark job with Hadoop and HDFS.
---
## Prerequisites 
- [x] **Setup VirtualBox** 
- [x] **Install Ubuntu Server** 
- [x] Install and Setup Hadoop
# Spark Installation and Setup Tutorial

This guide will walk you through installing Apache Spark, setting up environment variables, testing your installation, and running a Spark job with Hadoop and HDFS.

---

## 1. Install Spark

Download the Spark package:

```bash
wget https://dlcdn.apache.org/spark/spark-3.5.5/spark-3.5.5-bin-hadoop3.tgz
```

---

## 2. Extract Files

Extract the downloaded archive:

```bash
tar -xzvf spark-3.5.5-bin-hadoop3.tgz
```

---

## 3. Setup Path Variables

### a. Edit your Bash configuration

Open the bash configuration file:

```bash
nano ~/.bashrc
```

### b. Add the following lines at the bottom

```bash
# Spark Related options
export SPARK_HOME=/home/hdoop/spark-3.5.5-bin-hadoop3
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin

# Setup hadoop config directory
export HADOOP_CONF_DIR=/home/hdoop/hadoop-3.4.1/etc/hadoop
```

### c. Apply the Changes

Reload your bash configuration:

```bash
source ~/.bashrc
```

---

## 4. Test Spark Installation

Start PySpark to verify the installation:

```bash
pyspark
```

---

## 5. Start the Hadoop Node

Launch the Hadoop services:

```bash
start-all.sh
```

---

## 6. Prepare for Running a Spark Job

After creating your Spark program (e.g., `spark_pokemon.py`), follow these steps:

### a. Create a Directory in HDFS for CSV Files

```bash
hdfs dfs -mkdir -p /home/hdoop/assignment_2
```

### b. Upload the CSV to HDFS

```bash
hdfs dfs -put /home/hdoop/assignment_2/pokemon.csv /home/hdoop/assignment_2
```

### c. Verify the CSV Upload

Check if the file has been successfully moved to HDFS:

```bash
hdfs dfs -ls /home/hdoop/assignment_2
```

---

## 7. Running a Spark Job

### a. Run with YARN (Cluster Mode)

Submit your Spark job with YARN as the Resource manager:

```bash
spark-submit \
  --master yarn \
  --deploy-mode cluster \
  --num-executors 1 \
  --executor-memory 1G \
  --executor-cores 1 \
  --files /home/hdoop/assignment_2/spark_pokemon.py \
  spark_pokemon.py
```

### b. Fallback: Run in Local Mode (just for testing)

If you encounter issues running in cluster mode, try local mode:

```bash
spark-submit --master local[*] spark_pokemon.py
```

---

## 8. Check and Retrieve the Output

### a. Verify Output in HDFS

List the contents of the output directory:

```bash
hdfs dfs -ls output/pokemon_feistiest
```

### b. Copy the Output from HDFS to Local Directory

Retrieve the results from HDFS:

```bash
hdfs dfs -get output/pokemon_feistiest /home/hdoop/assignment_2/output
```

---

This structured guide should help you easily follow through each step of setting up Spark and running your job with Hadoop and HDFS with YARN. ✨