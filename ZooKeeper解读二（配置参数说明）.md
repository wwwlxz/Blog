title: ZooKeeper解读二（配置参数说明）
date: 2015-07-29 15:52:53
tags: ZooKeeper
---

# ZooKeeper解读二（配置参数说明）

标签（空格分隔）： 云计算-ZooKeeper

---

#ZooKeeper配置文件的参数说明
**dataDir**
用于存放内存数据库快照的文件夹，同时用于集群的myid文件也存在这个文件夹里。

**dataLogDir**
用于单独设置transaction log的目录，transaction log分离可以避免和普通的log还有快照的竞争。

**clientPort**
客户端监听端口

**globalOutstandingLimit**
client请求队列的最大长度，防止内存溢出，默认值为1000

**preAllocSize**
预分配的Transaction log空间block为proAllocSize KB，默认block为64M，一般不需要更改，除非snapshot过于频繁。

**snapCount**
在snapCount个snapshot后写一次transaction log，默认值是100,000。

**traceFile**
用于记录请求的log，打开会影响性能，用于debug，最好不要定义

**maxClientCnxns**
最大并发客户端数，用于防止DOS的，默认值是10，设置为0是不加限制。

**clientPortBindAddress**
可以设置指定的client ip以及端口，不设置的话等于ANY:clientPort

**minSessionTimeout**
最小的客户端session超时时间，默认值为2个tickTime，单位是毫秒

**maxSessionTimeout**
最大的客户端session超时时间，默认值为20个tickTime，单位是毫秒

**electionAlg**
用于选举的实现的参数，0为以原始的基于UDP的方式协作，1为不进行用户验证的基于UDP的快速选举，2为进行用户验证的基于UDP的快速选举，3为基于TCP的快速选举，默认值为3.

**initLimit**
多少个tickTime内，允许其他server连接并初始化数据，如果zookeeper管理的数据较大，则应相应增大这个值。

**syncLimit**
多少个tickTime内，允许follower同步，如果follower落后太多，则会被丢弃。

**leaderServes**
leader是否接受客户端连接。默认值为yes。leader负责协调更新。当更新吞吐量远高于读取吞吐量时，可以设置为不接受客户端连接，以便leader可以专注于同步协调工作。

**server.x=[hostname]:nnnnn[:nnnnn]**
配置集群里面的主机信息，其中server.x的x要写在myid文件中，决定当前机器的id，第一个port用于连接leader，第二个用于leader选举。如果electionAlg为0，则不需要第二个port。hostname也可以填ip。

**group.x=nnnnn[:nnnnn]**
分组信息，表明哪个组有哪些节点，例如group.1=1:2:3 group.2=4:5:6 group.3=7:8:9。

**weight.x=nnnnn**
权重信息，表明哪个节点的权重是多少，例如weight.1=1 weight.2=1 weight.3=1。







