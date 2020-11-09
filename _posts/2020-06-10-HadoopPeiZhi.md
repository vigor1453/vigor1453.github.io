---
layout: post
title: "Hadoop配置"
date: 2020-06-10
tags: ["技术"]
comments: true
author: oneman233
excerpt: "实在有点麻烦"
toc: true
---

# root登录

初学者用root，避免出现各种权限问题。

登录后，请立即修改自己的密码。

`tar zxvf` 解压缩

# hosts、hostname

集群中各节点需要互相访问，因此各主机都要有自己的名字（hostname），各主机都要有一份通讯录（hosts），通讯录要写明各主机的名字（hostnames）与对应的IP地址。

HDFS的主节点可命名为master，子节点可命名为slave1（2、3、4、5、6）。

配置路径：

|配置名称|路径|
|---|---|
|hosts|/etc/hosts|
|hostname|/etc/hostname|

hosts储存集群所有主机IP地址与对应主机名。

格式：IP（空格）主机名

把主机名直接写入hostname，定义本机的主机名。

此处假设把机器名修改为master，后面的master均代表本机。

最后运行hostname检查是否成功修改主机名称。

|vim快捷键|作用|
|---|---|
|d|delete|
|i|insert|
|EXC|close insert|
|:q|close directly|
|:q!|close and drop all changes|
|:wq|close and save all changes|

# ssh

集群之间的连接依靠ssh，脚本调用ssh是不会输入密码验证的，所以要做到集群之间各主机的ssh互相免密码。

对于伪分布式环境只需要本机链接本机即可：

1. 主节点执行：ssh-keygen -t rsa 一路回车即可，最后显示的图形是公钥的指纹加密。

2. 生成公钥后需要将公钥发到本机的authorized_keys的列表，执行：

    `ssh-copy-id -i ~/.ssh/id_rsa.pub root@master` 或 `ssh-copy-id -i ~/.ssh/id_rsa.pub master`

3. 也可以使用cat命令复制公钥到authorized_keys中，执行：
   
   `cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys`

4. 如果是多机器需要需要通过scp命令赋值到主节点中，再分发至子节点。

最后根据系统提示信息，测试ssh自己看是否需要输入密码，如果不需要则说明成功。

# javaJDK

**注意：**

安装之前要卸载openJDK，下载oracle的官方jdk，建议版本为JDK 8。

一般把环境变量配置到`/etc/profile`中，但是可能出现读不到JAVA_HOME的问题，有人说要把环境变量配置到`/etc/environment`里。

`/etc/environment`是设置整个系统的环境，而`/etc/profile`是设置所有用户的环境，前者与登录用户无关，后者与登录用户有关。

但是在上面两种配置方法都使用了的情况下，依然有某些脚本报读不到JAVA_HOME,可以试试在`/etc/sudoers`的最后一行增加：`Defaults env_keep+=JAVA_HOME`，此处的JAVA_HOME换成你的javaJDK路径。

修改举例：

```
export JAVA_HOME=jdk路径
export JRE_HOME=jre路径
export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib
export HADOOP_HOME=hadoop路径
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

hadoop路径：`/root/hadoop-2.6.0-cdh5.9.3`

执行`source /etc/profile`让修改生效。

最后测试java看是否正常工作否则检查原因。

# 防火墙

因为CDH包含了很多工具，因此集群各主机都要开放很多端口，可以对照Cloudera官方文档配置防火墙，也可以先关掉防火墙往下做，日后再配。

linux的防火墙包括selinux、firewall，早期版本的centos不使用firewall而是使用iptables。

selinux配置文件路径：`/etc/selinux/config`

把其中参数SELINUX的值改为disabled即可。

防火墙命令两条：
1. 禁止开机启动防火墙：`systemctl disable firewalld.service`
2. 关闭防火墙：`systemctl stop firewalld.service`

最后检查状态是否正确。

# 安装Hadoop-cdh

首先解压hadoop，并且配置如下六个文件（都位于hadoop的etc目录），并根据配置文件建立相应的目录。

**1. 修改slaves**

在文件末尾添加`master`。

指定当前机器同时运行DataNode，NodeManager。

**2. 修改hadoop-env.sh和yarn.env.sh**

使用以下命令：

```
vi hadoop-env.sh / vi yarn-env.sh
export JAVA_HOME=/jdk目录 #加入java环境变量
```

jdk目录：`/downloads/jdk1.8.0_251`
jre目录：`/downloads/jdk1.8.0_251/jre`

**3. 修改core-site.xml**

在configuration标签中添加如下属性：

```
fs.defaultFS hdfs://master:8020
hadoop.tmp.dir /hadoop目录/data
```

hadoop目录：`/hadoopDir/data`

该配置设置HDFS服务的主机名和端口号以及临时文件夹位置。

**4. 修改hdfs-site.xml**

执行命令：

```
dfs.replication 1
```

在configuration标签中添加如下属性，需要创建相应的文件夹：

```xml
<property>
      <name>dfs.name.dir</name>
      <value>/hadoopDir/namenode</value>
</property>
<!--hdfs中datanode在linux下的存储路径-->
<property>
      <name>dfs.data.dir</name>
      <value>/hadoopDir/datanode</value>
</property>
```

**5. 修改mapred-site.xml**

在configuration标签中添加如下属性：

```
mapreduce.framework.name yarn
```

**6. 修改yarn-site.xml**

添加以下代码：

```
yarn.resourcemanager.hostname master
yarn.nodemanager.aux-services mapreduce_shuffle
yarn.resourcemanager.webapp.address master:8088
```

# 启动dfs文件系统和yarn资源管理器

格式化namenode：

```
hadoop namenode -format
```

启动hadoop并查看进程状态：

```
hadoop-daemon.sh start namenode
hadoop-daemon.sh start datanode
yarn-daemon.sh start resourcemanager
yarn-daemon.sh start nodemanager
```

使用jps查看linux上运行的如下守护进程：

```
NameNode
ResourceManager
NodeManager
DataNode
```

# 测试HDFS文件系统

在分布式文件系统中，建立一个新目录tmp，并且执行以下命令将hadoop的配置文件拷贝到该目录中，并查看是否拷贝成功：

```
hdfs dfs -mkdir /tmp 
hdfs dfs -put $HADOOP_HOME/etc/hadoop/*.xml /tmp
hdfs dfs -ls /tmp
```

# 参考资料

1. [jsp异常](https://www.cnblogs.com/braveym/p/7365588.html)
2. [linux安装JDK](https://blog.csdn.net/mynameissls/article/details/51458300)
3. [报错cannot execute binary file](https://blog.csdn.net/myNameIssls/article/details/51457809)
4. [卸载OpenJDK](https://www.cnblogs.com/yyjf/p/10287301.html)
5. [运行hadoop的一个warn](https://stackoverflow.com/questions/23338509/unable-to-load-native-hadoop-library-for-your-platform-using-builtin-java-cla)（可能是版本不对）
6. [hadoop的helloworld](https://www.cnblogs.com/asker009/p/10337598.html)（一个简单的单词计数mapper和reducer编写）