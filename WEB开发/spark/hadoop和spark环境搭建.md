---
title: "hadoop和spark环境搭建"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["spark"]
categories: ["spark"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
# ~/.bashrc配置
```
export PATH=$PATH:$HOME/.pyenv/bin
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"

source ~/.myshell

export JAVA_HOME=/home/liuquan/tools/jdk1.8.0_151    
export JRE_HOME=/home/liuquan/tools/jdk1.8.0_151/jre 
export CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/
export SPARK_HOME=/home/liuquan/tools/spark-2.2.0-bin
export PATH=$PATH:$JAVA_HOME/bin:$JRE/bin:$SPARK_HOME

export HADOOP_HOME=/home/liuquan/tools/hadoop-2.7.4  
export HADOOP_INSTALL=$HADOOP_HOME                   
export HADOOP_MAPRED_HOME=$HADOOP_HOME               
export HADOOP_COMMON_HOME=$HADOOP_HOME               
export HADOOP_HDFS_HOME=$HADOOP_HOME                 
export YARN_HOME=$HADOOP_HOME                        
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/"
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin 
```

# hadoop
### $HADOOP_HOME/etc/hadoop/slaves
```
Slave1
Slave2
```

### $HADOOP_HOME/etc/hadoop/core-site.xml
```
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://Master:9000</value>
    </property>
    <property>
        <name>io.file.buffer.size</name>
        <value>131072</value>
    </property>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/home/liuquan/tools/hadoop-2.7.4/tmp</value>
    </property>
</configuration>
```

### $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```
<configuration>
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>Master:50090</value>
    </property>
    <property>
        <name>dfs.replication</name>
        <value>2</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/home/liuquan/tools/hadoop-2.7.4/hdfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/home/liuquan/tools/hadoop-2.7.4/hdfs/data</value>
    </property>
</configuration>

```

### $HADOOP_HOME/etc/hadoop/mapred-site.xml
```
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>Master:10020</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>Master:19888</value>
    </property>
</configuration>
```

### $HADOOP_HOME/etc/hadoop/yarn-site.xml
```
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.resourcemanager.address</name>
        <value>Master:8032</value>
    </property>
    <property>
        <name>yarn.resourcemanager.scheduler.address</name>
        <value>Master:8030</value>
    </property>
    <property>
        <name>yarn.resourcemanager.resource-tracker.address</name>
        <value>Master:8031</value>
    </property>
    <property>
        <name>yarn.resourcemanager.admin.address</name>
        <value>Master:8033</value>
    </property>
    <property>
        <name>yarn.resourcemanager.webapp.address</name>
        <value>Master:8088</value>
    </property>
</configuration>
```

# spark
### $SPARK_HOME/conf/spark-env.sh
```
export JAVA_HOME=/home/liuquan/tools/jdk1.8.0_151
export HADOOP_HOME=/home/liuquan/tools/hadoop-2.7.4
export HADOOP_CONF_DIR=/home/liuquan/tools/hadoop-2.7.4/etc/hadoop
export SPARK_MASTER_IP=172.93.35.151
export SPARK_MASTER_HOST=172.93.35.151
export SPARK_LOCAL_IP=172.93.35.151
export SPARK_WORKER_MEMORY=1g
export SPARK_WORKER_CORES=2
export SPARK_HOME=/home/liuquan/tools/spark-2.2.0-bin-hadoop2.7
export SPARK_DIST_CLASSPATH=$(/home/liuquan/tools/hadoop-2.7.4/bin/hadoop classpath)
```

# 启动和关闭

```
./bin/spark-submit --class org.apache.spark.examples.SparkPi  --master spark://Master:6066  --deploy-mode cluster  --supervise --executor-memory 1G  --total-executor-cores 10 /usr/spark/examples/jars/spark-examples_2.11-2.1.0.jar 100000
```

```
vim /etc/hosts

主服务器如下
10.104.157.113 master
119.29.250.47 slave1
119.29.251.99 slave2
从1
10.104.9.181 slave1
119.29.186.83 master
119.29.251.99 slave2

注意 本机的hostname与内网ip对应
    其他的hostname与外网ip对应
然后依次ping master,ping slave1
```

# hdfs
### 创建用户目录
- ./bin/hdfs dfs -mkdir -p /user/hadoop
### 相对路径
- ./bin/hdfs dfs -mkdir input
- hdfs dfs -copyFromLocal ~/123.txt /xytest/testdata001/


### zookeepercha看启动过程
- zkServer.sh start-foreground

# mesos
```
mkdir /opt/mesos-master
#启动master
mesos-start --work_dir=/opt/mesos-master --ip=172.19.143.111
mkdir /opt/mesos-slave
#启动agent
GLOG_v=1 mesos-agent --master=192.168.211.131:5050 --isolation=docker/runtime,filesystem/linux --work_dir=/opt/mesos-slave --image_providers=docker --docker_registry=http+socket://192.168.211.131:5000/ --containerizers=mesos,docker
mesos-execute --master=192.168.211.1131:5050 --name=test1 --command="docker pull 192.168.211.131:5000/letv_es/only_for_test:0.0.2"
```
- ./bin/mesos-master.sh--ip=127.0.0.1 --work_dir=/var/lib/mesos 
- ./bin/mesos-slave.sh--master=127.0.0.1:5050  

### 提交任务
- spark-submit --class org.apache.spark.examples.SparkPi --master mesos://106.14.194.134:7077 --deploy-mode cluster /home/liuquan/tools/spark-2.2.0-bin-hadoop2.7/examples/jars/spark-examples_2.11-2.2.0.jar 100
- spark-submit --class org.apache.spark.examples.SparkPi --master mesos://172.19.143.111:7077 --deploy-mode cluster /home/liuquan/tools/spark-2.2.0-bin-hadoop2.7/examples/jars/spark-examples_2.11-2.2.0.jar 100