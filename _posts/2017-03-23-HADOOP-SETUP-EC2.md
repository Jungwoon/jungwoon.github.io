---
layout: post
title: "AWS EC2에 Hadoop 설치하기"
image: '/assets/img/'
description: 'AWS EC2에 Hadoop 설치하기'
tags:
- AWS
- Hadoop
- EC2
categories:
- Bigdata
---

AWS EC2에 하둡 설치하는 방법에 대해 알아보겠습니다.

# Hadoop 설치하기

### Hadoop Download

```
$ cd
$ wget http://apache.claz.org/hadoop/common/hadoop-2.7.3/hadoop-2.7.3.tar.gz
$ tar -zxvf hadoop-2.7.3.tar.gz
```


### Pseudo-Distributed Operation

```
$ vi ~/hadoop-2.7.3/etc/hadoop/core-site.xml
```


#### 설정 내용 (~/hadoop-2.7.3/etc/hadoop/core-site.xml)

```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```


#### 설정 내용 (~/hadoop-2.7.3/etc/hadoop/hdfs-site.xml)

```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>

```


### SSH 설정

```
$ ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
$ chmod 0600 ~/.ssh/authorized_keys
```


### 실행

```
$ ~/hadoop-2.7.3/bin/hdfs namenode -format
$ ~/hadoop-2.7.3/sbin/start-dfs.sh # NameNode 시작
$ ~/hadoop-2.7.3/bin/hdfs dfs -mkdir /user
$ ~/hadoop-2.7.3/bin/hdfs dfs -mkdir /user/ec2-user
$ ~/hadoop-2.7.3/bin/hdfs dfs -mkdir input
$ ~/hadoop-2.7.3/bin/hdfs dfs -put etc/hadoop/*.xml input
$ ~/hadoop-2.7.3/bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar grep input output 'dfs[a-z.]+'
$ ~/hadoop-2.7.3/bin/hdfs dfs -get output output
$ cat ~/hadoop-2.7.3/output/*
```


### YARN 싱글 모드

```
$ cp ~/hadoop-2.7.3/etc/hadoop/mapred-site.xml.template ~/hadoop-2.7.3/etc/hadoop/mapred-site.xml
$ vi ~/hadoop-2.7.3/etc/hadoop/mapred-site.xml
```


#### 설정 내용 (~/hadoop-2.7.3/etc/hadoop/mapred-site.xml)

```
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```


```
$ vi ~/hadoop-2.7.3/etc/hadoop/yarn-site.xml
```


#### 설정 내용 (~/hadoop-2.7.3/etc/hadoop/yarn-site.xml)

```
<?xml version="1.0"?>

<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.nodemanager.env-whitelist</name>
        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
    </property>
</configuration>
```


### Yarn Stop/Stop
```
$ ~/hadoop-2.7.3/start-yarn.sh # Start
$ ~/hadoop-2.7.3/stop-yarn.sh # Stop
```


### AWS 설정
- EC2 Dashboard -> Running Instances
- 오른쪽으로 옮겨서 Security Groups를 찾아서 클릭

![img](https://cdn-images-1.medium.com/max/2000/1*PrqjkC0TLQKSWeAa2WrlkQ.png)

- Security Groups에 들어가면 하단에 Imbound를 누르고 Edit 버튼으로 포트를 열어줌  

![img](https://cdn-images-1.medium.com/max/2000/1*bkucpTM_XBF212mGnwuwTg.png)

- ALL TCP, ALL UDP, Source를 Anywhere로 줬음

![img](https://cdn-images-1.medium.com/max/2000/1*MvoIVZ-Se8kMC3z9-Y57HA.png)

- http://[본인 EC2 공개 IP]:8088 를 들어가면 다음과 같은 화면이 보임  
(대신 먼저 위에서 Yarn을 Start 시켜야 함)

![img](https://cdn-images-1.medium.com/max/2000/1*CCKFoyaLlskfYKC3DfsyZw.png)
