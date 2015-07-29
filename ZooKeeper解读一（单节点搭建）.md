title: ZooKeeper解读一（单节点搭建）
date: 2015-07-29 15:51:51
tags: ZooKeeper
---

# ZooKeeper解读一（单节点搭建）

标签（空格分隔）： 云计算-ZooKeeper

---

#ZooKeeper单节点的搭建
ZooKeeper的搭建还是比较简单的，所需要修改的配置文件比较少，而且也不容易出错。
##1、获取ZooKeeper安装包并解压
从官网上下载ZooKeeper的安装包，我这里下载的版本是`zookeeper-3.4.6.tar.gz`，对其进行解压，我这里的安装目录为：
 > /home/zookeeper
 
解压命令：
```cmd 
tar -zxf zookeeper-3.4.6.tar.gz
```

##2、修改配置文件zoo.cfg
进入`/home/zookeeper/zookeeper-3.4.6/conf`目录
```cmd
cd zoo_sample.cfg zoo.cfg
vim zoo.cfg
```
对zoo.cfg做如下修改：
```shell
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take
initLimit=5
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement
syncLimit=2
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just 
# example sakes.
dataDir=/home/zookeeper/zookeeper-3.4.6/data
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the 
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1
```
然后在`/home/zookeeper/zookeeper-3.4.6/`目录下创建data文件夹：
```cmd
mkdir /home/zookeeper/zookeeper-3.4.6/data
```

##3、启动server
进入目录`/home/zookeeper/zookeeper-3.4.6/bin`，执行如下命令
```cmd
./zkServer.sh start
```

##4、查看进程是否启动
有两种方法查看进程是否启动

 1. jps
 如果有` QuorumPeerMain`进程则表示ZooKeeper启动。
 2. ./zkServer.sh status
```cmd
JMX enabled by default
Using config: /home/zookeeper/zookeeper-3.4.6/bin/../conf/zoo.cfg
Mode: standalone
```
其中`standalone`表示ZooKeeper是单节点启动的。

