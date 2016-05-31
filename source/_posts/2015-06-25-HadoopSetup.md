title: 集群分布式Hadoop系统安装基本步骤
date: 2015-06-25 00:00:00
categories: Hadoop
tags: [Big Data, Hadoop]
description: 1. Linux准备<br>1.1 修改机器名<br>1.2 修改主机名和IP的映射关系(/etc/hosts)<br>1.3 关闭防火墙<br>1.4 重启Linux(reboot)<br><br>2. JDK安装<br>2.1 上传<br>2.2 解压<br>2.3 将java添加到环境变量中
---
## 1. Linux准备
### 1.1 修改机器名
- hostname<机器名>
- vim /etc/sysconfig/network
- HOSTNAME=<机器名> 保存退出，重启
### 1.2 修改主机名和IP的映射关系(/etc/hosts)
### 参考：
- 192.168.110.128 hadoop0
- 192.168.110.144 hadoop1
- 192.168.110.154 hadoop2
### 1.3 关闭防火墙
- service iptables status 查看防火墙状态
- service iptables stop 关闭防火墙
- chkconfig iptables --list 查看防火墙启动状态
- chkconfig iptables off 关闭防火墙开机启动
### 1.4 重启Linux(reboot)
## 2. JDK安装
### 2.1 上传
### 2.2 解压
    tar -xzvf jdk-8u45-linux-x64.tar.gz -C /usr/java
### 2.3 将java添加到环境变量中
    vim /etc/profile
#### 在文件最后添加：
    export JAVA_HOME=/usr/java/jdk1.8.0_45
    export PATH=$PATH:$JAVA_HOME/bin
#### 刷新配置
    source /etc/profile
## 3. Hadoop安装
### 3.1 上传Hadoop安装包
    mkdir /usr/hadoop
### 3.2 解压Hadoop安装包
    tar -xzvf hadoop-2.6.0.tar.gz -C /usr/hadoop
### 3.3修改配置文件
第一个：hadoop-env.sh
    
    export JAVA_HOME=/usr/java/jdk1.8.0_45
第二个：core-site.xml

    <configuration>
       	<!--指定HDFS中namenode的通信地址-->
    	<property>
    		<name>fs.defaultFS</name>
    		<value>hdfs://hadoop0:9000</value>
    	</property>
    </configuration>
第三个：hdfs-site.xml

    <configuration>
    	<!--设置hdfs副本数量-->
    	<property>
    		<name>dfs.replication</name>
    		<value>1</value>
    	</property>
    </configuration>
第四个：mapred-site.xml

    <configuration>
    	<!--通知框架MR使用YARN-->
    	<property>
    		<name>mapreduce.framework.name</name>
    		<value>yarn</value>
    	</property>
    </configuration>
第五个：yarn-site.xml

    <configuration>
    	<!--reduce获取数据的方式是mapreduce_shuffle-->
		<property>
			<name>yarn.nodemanager.aux-services</name>
			<value>mapreduce_shuffle</value>
		</property>
	</configuration>
### 3.4 将Hadoop添加到环境变量
    vim /etc/profile
	export JAVA_HOME=/usr/java/jdk1.8.0_45
	export HADOOP_HOME=/usr/hadoop/hadoop-2.6.0
	export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
	source /etc/profile
### 3.5设置SSH免密码登录
    vim /etc/selinux/config
	SELINUX=disabled
	ssh-keygen -t dsa
	cd ~/.ssh
	cat id_dsa.pub >> authorized_keys
### 3.6 格式化HDFS(namenode)第一次使用要格式化
    hdfs namenode -format
### 3.7 启动Hadoop
#### 先启动HDFS
    sbin/start-dfs.sh
#### 再启动YARN
    sbin/start-yarn.sh
### 3.8 验证是否启动成功
#### 使用jps命令
    NameNode
    Jps
    SecondaryNameNode
    NodeManager
    ResourceManager
    DataNode
#### HDFS管理界面：
[http://hadoop0:50070](http://hadoop0:50070)