## 解读zoo.cfg

 文件中参数含义

1. tickTime：通信心跳数，Zookeeper服务器心跳时间，单位毫秒
   + Zookeeper使用的基本时间，服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个tickTime时间就会发送一个心跳，时间单位为毫秒。
   + 它用于心跳机制，并且设置最小的session超时时间为两倍心跳时间。(session的最小超时时间是2*tickTime)

2. initLimit：LF初始通信时限
   + 集群中的follower跟随者服务器(F)与leader领导者服务器(L)之间初始连接时能容忍的最多心跳数（tickTime的数量），用它来限定集群中的Zookeeper服务器连接到Leader的时限。
   + 投票选举新leader的初始化时间
   + Follower在启动过程中，会从Leader同步所有最新数据，然后确定自己能够对外服务的起始状态。
   + Leader允许F在initLimit时间内完成这个工作。

3. syncLimit：LF同步通信时限
   + 集群中Leader与Follower之间的最大响应时间单位，假如响应超过syncLimit * tickTime，
   + Leader认为Follwer死掉，从服务器列表中删除Follwer。
   + 在运行过程中，Leader负责与ZK集群中所有机器进行通信，例如通过一些心跳检测机制，来检测机器的存活状态。
   + 如果L发出心跳包在syncLimit之后，还没有从F那收到响应，那么就认为这个F已经不在线了。

4. dataDir：数据文件目录+数据持久化路径保存内存数据库快照信息的位置，如果没有其他说明，更新的事务日志也保存到数据库。

5. clientPort：客户端连接端口
   + 监听客户端连接的端口

##  数据结构

ZooKeeper数据模型的结构与Unix文件系统很类似，整体上可以看作是一棵树，每个节点称做一个ZNode。

 很显然zookeeper集群自身维护了一套数据结构。这个存储结构是一个树形结构，其上的每一个节点，我们称之为"znode"，每一个znode默认能够存储1MB的数据，每个ZNode都可以通过其路径唯一标识

### 节点类型

1）Znode有两种类型：

短暂（ephemeral）：客户端和服务器端断开连接后，创建的节点自己删除

持久（persistent）：客户端和服务器端断开连接后，创建的节点不删除

2）Znode有四种形式的目录节点（默认是persistent ）

（1）持久化目录节点（PERSISTENT）

客户端与zookeeper断开连接后，该节点依旧存在

（2）持久化顺序编号目录节点（PERSISTENT_SEQUENTIAL）

客户端与zookeeper断开连接后，该节点依旧存在，只是Zookeeper给该节点名称进行顺序编号

（3）临时目录节点（EPHEMERAL）

客户端与zookeeper断开连接后，该节点被删除

（4）临时顺序编号目录节点（EPHEMERAL_SEQUENTIAL）

客户端与zookeeper断开连接后，该节点被删除，只是Zookeeper给该节点名称进行顺序编号