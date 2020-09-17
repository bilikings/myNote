# Redis

## Nosql

+ 不仅仅是数据
+ 没有固定的查询语言
+ 键值对存储，列存储，文档存储，图像数据库
+ 最终一致性
+ CAP定理，BASE（异地多活）
+ 高可用，高性能，高可扩

## redis入门

> redis 远程服务字典  

+ 内存存储，持久化
+ 效率高，可以高速缓存
+ 发布订阅系统
+ 地图信息分析
+ 计时器，计数器，缓存

> redis 是单线程的，基于内存操作，cpu不是内存瓶颈，瓶颈是机器内存和网络带宽，可以使用单线程实行就单线程了
>
> 

### 安装

```shell
wget http://download.redis.io/releases/redis-5.0.7.tar.gz
tar -zvxf redis-5.0.7.tar.gz
mv /root/redis-5.0.7 /usr/local/redis
```

> 编译

make

指定安装目录

```shell
make PREFIX=/usr/local/redis install
```

> 启动

```shell
./bin/redis-server& ./redis.conf
```

#### docker 启动

```shell
docker run --name some-redis1 -p 6379:6379  -d redis --requirepass "123456"
```

```shell
docker exec -it RedisID redis-cli
```



### 测试

```shell
redis-benchmark -h localhost -p 9379 -c 100 -n 100000 
```

### redis基本命令

+ 16个数据库，使用select 来切换，默认0号
+ dbsize 查看数据库的大小
+ keys * 查看数据库所有的key 
+ flushdb 数据库清空/flushall 清空所有的16个数据库内容
+ move 按key移除
+ expire key +秒数 设定过期时间（移除）
+ ttl key 查看剩余过期时间
+ auth 输入密码
+ type 查看当前可以的类型

## 五大基本数据

+ Strings
+ hashes
+ lists
+ sets
+ zsets

### String

```bash
set key1 v1
set key2 v2
get key2
append key1 v3 #return :v1+v3 ；如果不存在就等于append
strlen key1 #获取key1对应值的字符串长度
exists #查询数据key是否存在
incr key # v++
decr key # v--
incrby key 5 # v += 5
decrby key 5 # v -= 5
GETRANGE name 0 5 # 截取字符串
setrange key1 2 xx # 对key1的值index=2开始替换成xx，其余字符不变
getset key1 #先获取再设置
######在分布式锁常用
setex key3 30 "xxx" # 设置key3的值为hello，30s过期，可以用在短信验证
setnx #若不存在就设置val，若存在就创建失败，不会替换原值
##批量操作 
mset
mget
```

### List

所有的list命令都是l开头的

可用于消息队列

```bash
Lpush  #是栈的push，插入到列表的头部
Rpush ##是queue的push，插入到列表的尾部
lrange # 和string的一样用
Lpop # 移除最左端的值
Rpop #移除最右的值
Lindex key index #按下标获得值
Lrem list 3 2 #在list中按”元素==2“移除，移除3个
trim key 1 4 #按index =1截取指定的长度（4）
RPOPLPUSH list newlist #list中rpop一个值 ，然后在newlist中lpush
lset key 0 val # 对key中index =0的值进行修改，原值需要存在，否则“(error) ERR index out of range”
linsert key before|after pivot val# 在pivot前|后插入val，若pivot没有就不能插入

```



### Set

一般是S开头的命令

```bash
sadd key val # 往set中插入val
smembers key #查看set中的所有值
sismember key val #val是否在key中
scard key #查看元素个数
srem key member #按member移除key中的成员
SRANDMEMBER key [count]#随机抽出一个元素（不移除），count默认为1
spop key #随机删除并输出一个元素
smove set1 set2 member#把set1中的member放到set2中
#########
#数字集合类 
sinter key1 key2 #-交集
sunion key1 key2 #-并集 
sdiff key1 key2 #-差集 key1中有，key2中没有的值
```

### Hash

Map集合，key -map （key-<key-val>)

命令为h开头

```bash
hset key field val # 往key里面设置field【（成员变量）相当于key】和val
hget key field #根据key 和field 查找val
hmset/hmget #是一样的，同时多个值
hgetall key #按field-val获取全部数据
hdel key field #按field删除字段
hlen key #获取size
hexists key field #判断hash中的指定字段是否存在
hkeys key #获取所有的filed
hvals key#获取所有的val
hincrby key field val count #对val +count，需要是integer类型（自动判断）
hsetnx #如果不存在则设置


```

### Zset（有序集合）

在set的基础上加了一个值，【set v1 v1 zset k1 score1 v1】

可以按照score的值进行排序

```bash
zadd k1 score1 v1 #添加
zrevrange key index # 反向输出
zrange key index #正向输出
zrangebyscore #按score大小正向输出
zRevRangeByScore #按score大小反向输出

zrangebyscore key min max withscores #带着scores一起输出
zcard #获取集合中的个数
zcount key min max#获取区间中的值的数量

```

可以带权执行

排行榜，top N

## 三大特殊数据类型

### geospatial 地理位置

可以实现地理位置，朋友的定位，附近的人，打车距离计算

```bash
geoadd key 经度（-180,180） 纬度（-85,85）  名称
geodist
geohash
geopos
georadius
georadiusbymember
```

```bash
127.0.0.1:6379> GEOADD mapchina:city 116.40 39.90 beijing 121.47 31.23 shanghai 
(integer) 2
127.0.0.1:6379> GEOADD mapchina:city 160.50 29.53 chongqin
(integer) 1
127.0.0.1:6379> GEOADD mapchina:city 114.05 29.53 zhenzhen
(integer) 1
127.0.0.1:6379> GEODIST mapchina:city chongqin zhenzhen
"4463931.1422"
127.0.0.1:6379> 

```



### hyperloglog

基数，用作统计

```bash
pfadd#添加
pfcount#返回大小
pfmerge #合并集合
```

### bitmaps

位运算，位存储

## Redis事务

+ 事务本质：一组命令的集合，一个事务中的所有命令都会被序列化，所以在事务中会按照数据执行

```bash
-----queue set set ...set start ------
```

+ **redis事务没有隔离级别的概念**

+ redis单条命令是保证原子性的，但是事务不保证原子性的

  Redis 事务：

  + 开启事务（multi）
  + 命令入队（get/set/...）
  + 执行事务   (exec)
  + 放弃事务 （discard）

#### redis监控

> redis 实现乐观锁

认为什么时候都不会出现问题所以不会上锁，更新数据之前看看是否有其他线程修改了这个线程

+ 使用监控 watch key 如果在其他线程修改了这个key，之后的multi就会失败
+ 放弃监视 unwatch
  +  如果发现事务执行失败，就先unwatch
  + 然后获取最先的锁 watch key

> 正常操作

```bash
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> set money 19
QUEUED
127.0.0.1:6379> INCRBY money 40
QUEUED
127.0.0.1:6379> DECRBY money 30
QUEUED
127.0.0.1:6379> exec
1) OK
2) (integer) 59
3) (integer) 29
127.0.0.1:6379> 
```

> 失败操作

```bash
127.0.0.1:6379> watch money
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> INCRBY money 40
QUEUED
127.0.0.1:6379> DECRBY money 30
QUEUED
127.0.0.1:6379> exec
(nil)
127.0.0.1:6379> 
```

## Jedis

使用java来操作resis，是java连接redis的中间件

> 导包

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.3.0</version>
</dependency>
```

```java
public void userMulti(){
    Jedis jds = new Jedis("127.0.0.1",6379);
	jds.ping // PONG
    //命令是一致的
    //开启事务
    Transction multi =jds.multi();
	try{
        multi.set("key1","val");
        multi.set("key2","val");
        multi.exec;
    }catch{
        multi.discard();//执行失败放弃执行
    }finally{
        jds.close();
    }
}
    
```

## SpringBoot整合

springData在里面整合 了springData redis

在springboot2.x之后jedis被替换成了lettuce

lettuce：采用netty，实例可以在多个线程中共享，性能更高

redis对象都需要序列化，直接传递对象会报错

```java
User user =new User("name",3);
String jsonU  =new ObjectMapper().writeValueAsString(user);
redisTemplate.opsForValue.set("user",jsonU);//不实例化会报错
```

## Redis持久化

默认是RDB持久化，不是AOF模式的

### RDB

> 什么是RDB

```bash
save 900 1  # 900s内有一次更改
save 300 10 # 100s内有10次更改
save 60 10000 # 60s内有10000次更改
```

+ 我们默认的就是RDB，一般情况下不需要修改这个配置!
+ Redis会单独创建(fork )一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何IO操作的。这就确保了极高的性能。如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。
+ 持久化文件为drum.rdb

> 触发机制

1. save规则满足
2. flushall
3. 退出redis

>恢复数据

+ 只要rdb文件在redis目录下就好

> 优点

1. 适合大规模的数据恢复
2. 对数据要求不高

> 缺点

1. 需要时间初始化
2. fork时候占用资源

### AOF

+ 以日志的形式来保存每个写操作
+ 默认不开启



## redis发布订阅

可以用作——微信微博关注系统

![image-20200824181731163](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200824181731163.png)



> 命令



| 命令                | 描述                              |
| ------------------- | --------------------------------- |
| psubscribe pattern  | 订阅一个或是多个符合pattern的频道 |
| pubsub subcommand   | 查看订阅发布的系统状态            |
| publish channel msg | 把信息发送到指定频道              |
| subscribe channel   | 订阅一个或者多个的频道信息        |
| unsubscribe         | 退订频道                          |
| punsubscribe        | 退订所有给定模式的频道            |

订阅者会收到publish的消息

#### 使用场景

1. 实时消息系统
2. 可以做群发聊天
3. 订阅、关注系统

+ 要是更复杂就使用消息中间件MQ了

## redis主从复制

> 真实的主从配置应该在配置文件中配置
>
> ```bash
> #根据自己的情况调整就可以（笔者使用的默认配置）
> bind 0.0.0.0
> #后台运行
> daemonize no
> #日志存储路径
> logfile "/var/log/redis/redis-server.log"
> #redis访问密码
> requirepass yourpass
> #集群安全验证
> masterauth yourpass
> 
> #是否开启append-only Log日志
> appendonly yes
> #启用集群模式
> cluster-enabled yes
> #集群节点日志路径
> cluster-config-file nodes.conf
> #集群节点超时时间
> cluster-node-timeout 5000
> ```

![image-20200824204412392](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200824204412392.png)



+ 数据的复制是单向的
+ 一个主节点可以有多个子节点，一个从节点只能有一个主节点

##### 主从复制的主要作用：

1. 数据冗余：实现了数据的热备份，是持久化之外的一种数据冗余方式
2. 数据恢复：Master节点出现问题时，可以让从节点提供服务，实现快速的数据恢复功能
3. 负债均衡：在主从复制的基础上，配合读写分离，可以有主节点提供写服务，从节点提供从服务
4. 高可用基石： 主从复制是哨兵和集群可以实现的基础，所以说主从复制是高可用基石

+ 查看主从复制的信息

  ```bash
  127.0.0.1:6379> info replication #查看主从复制的信息
  # Replication
  role:master #是主节点
  connected_slaves:0 #连接的从机数量
  master_replid:c93426b7ee12fe7f808396d402c6c27cd82b8aac
  master_replid2:0000000000000000000000000000000000000000
  master_repl_offset:0
  second_repl_offset:-1
  repl_backlog_active:0
  repl_backlog_size:1048576
  repl_backlog_first_byte_offset:0
  repl_backlog_histlen:0
  ```

##### 节点的redis.conf配置(模拟配置)

[原文连接]: https://www.cnblogs.com/Tu9oh0st/p/11205281.html

+ 也可以在外部进行redis的配置映射到docker中

> 其他的保持原样就好

```bash
aemonize yes    # 表示以后台的方式进行运行 不然就会出现下图所示，就不能够进行其他命令的配置
cluster-enabled yes    # 表示开启集群，默认是关闭的
cluster-config-file nodes.conf    # 表示集群的配置文件名称
cluter-node-timeout 15000    # 表示集群之间的超时时间，若是在这个时间段之内没有收到回复，表示节点出现了问题。
appendonly yes    # redis的持久化有两种RDB与AOF，具体的可以去搜索，这里开启aof 性能更加高，出现问题数据丢失更加少（只会丢失一秒种的数据）
```

> 启动

```bash
docker run --name m1 -p 6380:6379 -d redis
#"IPAddress": "172.17.0.5",
```

```bash
docker run --name m1 -p 6381:6379 -d redis 
#"IPAddress": "172.17.0.6",
```

```bash
docker run --name m1 -p 6381:6379 -d redis
# "IPAddress": "172.17.0.7",
```

```bash
19a5c986c48f    s2
83854bfceedf    s1
0dcadb0e2a4c    m1
```

+ 分别使用`dokcer inspect 容器ID`命令，查看3个Redis内网IP地址

![image-20200824214510308](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200824214510308.png)

> 进入Docker容器内部
>
> + 使用redis-6379为主机，其余两台为从机
> + docker exec -it RedisID redis-cli
> + 连接服务后，使用 info replication 查看当前机器的角色
> + 未配置前，三台redis均为 master主机

+ 分别在s1和s2使用 `SLAVEOF 172.17.0.5 6379` 命令

+ 在m1 使用 info replication 命令，验证主从关系是否配置成功

  ![image-20200824215630857](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200824215630857.png)

+ 在s1、s2使用 info replication，查看是不是已经设置为从机

  ![image-20200824220029094](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200824220029094.png)

> 尝试使用set对s1里面写值

![image-20200824221416205](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200824221416205.png)

+ 如果主机断了，可以使用`slaveof no one`把一个从机升级为主节点

#### 主从复制原理

Slave启动成功连接到master后会发送一个sync同步命令，整个数据文件到slave，并完成一次完全同步。

+ 全量复制:而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。
+ 增量复制:Master继续将新的所有收集到的修改命令依次传给slave，完成同步
+ 但是只要是重新连接master，一次完全同步（全量复制）将被自动执行

## 哨兵模式

（自动选择主节点）

> 哨兵模式是一个特殊的模式，用一个独立的进程来发送命令，等待redis服务器的响应，从而监控多个redis实例
>
> + 哨兵检测到master宕机，可以吧slave切换成master，然后通过**发布订阅模式**通知其他的从服务器，让他们切换主机

>可以设置多个哨兵提高稳定性

![image-20200824224158397](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200824224158397.png)

**哨兵有三个定时监控任务完成对各节点的发现和监控：**

**任务1**，每个哨兵节点每10秒会向主节点和从节点发送info命令获取最新拓扑结构图，哨兵配置时只要配置对主节点的监控即可(sentinel monitor mymaster 192.168.1.3 26379 2)，通过向主节点发送info，获取从节点的信息，并当有新的从节点加入时可以马上感知到；

**任务2**，每个哨兵节点每隔2秒会向redis数据节点的指定频道上发送该哨兵节点对于主节点的判断以及当前哨兵节点的信息，同时每个哨兵节点也会订阅该频道，来了解其它哨兵节点的信息及对主节点的判断，其实就是通过消息publish和subscribe来完成的；

**任务3**，每隔1秒每个哨兵会向主节点、从节点及其余哨兵节点发送一次ping命令做一次心跳检测，这个也是哨兵用来判断节点是否正常的重要依据；

### 配置哨兵模式

1. 进三个rocker容器内，cd进根目录

2. 使用`touch sentinel.conf`命令，创建哨兵配置文件,vim打开，

```shell
sentinel monitor host6379 172.17.0.2 6379 1
#sentinel monitor 被监控的名称 host port 1 
#后面这个1 代表只要有1个哨兵认为主机挂了，就开启投票让从机成为主机
```

3. 使用`redis-sentinel /sentinel.conf`启动Redis哨兵监控
   使用`ps –ef |grep redis`命令，可以看到redis-server和redis-sentinel正在运行

## Redis缓存穿透和雪崩

#### 缓存穿透（查不到）

##### 布隆过滤器

布隆算法：通过错误率换内存空间，数据标识算法

错误率体现在hash碰撞时候，会返回存在数据，过滤器返回不存在就一定不存在。



##### 缓存空对象

#### 缓存击穿（量太大，缓存过期）

##### 设置热点不过期

##### 加互斥锁

分布式锁：在数据库中设置一个val，当取得这个val时候，这个jvm才能去进行后续的资源抢占，之后把这个val值恢复，等于释放锁。

+ 要是抢占之后宕机了，就会造成死锁，可以建立哨兵进程，监控获取锁超时，若超时，强制恢复val

#### 缓存雪崩

在某一个时间段，缓存集中过期失效

> 解决方案

1. redis高可用
2. 限流降级
3. 数据预热

> 一致性hash算法

以前：hash(key)%n = 0

现在hash环