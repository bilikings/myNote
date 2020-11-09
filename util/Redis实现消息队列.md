Redis实现轻量级的消息队列与消息中间件相比，没有高级特性也没有ACK保证，无法做到数据不重不漏，如果业务简单而且对消息的可靠性不是那么严格可以尝试使用。

Redis中列表`List`类型是按照插入顺序排序的字符串链表，和数据结构中的普通链表一样，可以在头部`left`和尾部`right`添加新的元素。插入时如果键不存在Redis将为该键创建一个新的链表。如果链表中所有元素均被删除，那么该键也会被删除。

Redis提供了两种方式来做消息队列，一种是**生产消费模式**，另一种是**发布订阅模式**。

### 生产消费模式

适用于秒杀一类的场景

生产消费模式会让一个或多个客户端监听消息队列，一旦消息到达，消费者马上消费，谁先抢到算谁的。如果队列中没有消息，消费者会继续监听。

使用`brpop`和`blpop`实现阻塞读取

消费者就需要轮询来获取消息

一个作为生产者使用`LPUSH`写队列，一个作为消费者使用`RPOP`读队列。由于消费者并不知道什么时候会有消息过来，所以消费者需要一直循环读取数据。两者的消息可以使用JSON进行封装协议传输。由于消费者在没有读到数据的情况下，会一直循环读取，对系统来说十分耗费资源，此时可利用Redis提供的阻塞读取命令`BRPOP`进行改进。使用`BRPOP`改进后，消费者不会一直循环读取，而是一直阻塞等到有消息过来时才读取。

#### 阻塞读取

>  消费者

```
public class MessageConsumer implements Runnable {
    public static final String MESSAGE_KEY = "message:queue";
    private volatile int count;
    private Jedis jedis = JedisPoolUtils.getJedis();

    public void consumerMessage() {
        List<String> brpop = jedis.brpop(0, MESSAGE_KEY);//0是timeout,返回的是一个集合，第一个是消息的key，第二个是消息的内容
        System.out.println(brpop);
    }

    @Override
    public void run() {
        while (true) {
            consumerMessage();
        }
    }

    public static void main(String[] args) {
        MessageConsumer messageConsumer = new MessageConsumer();
        Thread t1 = new Thread(messageConsumer, "thread6");
        Thread t2 = new Thread(messageConsumer, "thread7");
        t1.start();
        t2.start();
    }
}
```

> 生产者

```java
/**
 * @Author: qlq
 * @Description
 * @Date: 21:29 2018/10/9
 */
public class MessageProducer extends Thread {
    public static final String MESSAGE_KEY = "message:queue";
    private volatile int count;

    public void putMessage(String message) {
        Jedis jedis = JedisPoolUtils.getJedis();
        Long size = jedis.lpush(MESSAGE_KEY, message);
        System.out.println(Thread.currentThread().getName() + " put message,size=" + size + ",count=" + count);
        count++;
    }

    @Override
    public synchronized void run() {
        for (int i = 0; i < 5; i++) {
            putMessage("message" + count);
        }
    }

    public static void main(String[] args) {
        MessageProducer messageProducer = new MessageProducer();
        Thread t1 = new Thread(messageProducer, "thread1");
        Thread t2 = new Thread(messageProducer, "thread2");
        Thread t3 = new Thread(messageProducer, "thread3");
        Thread t4 = new Thread(messageProducer, "thread4");
        Thread t5 = new Thread(messageProducer, "thread5");
        t1.start();
        t2.start();
        t3.start();
        t4.start();
        t5.start();
    }
}
```

### 发布订阅模式

发布订阅模式是一个或多个客户端订阅消息频道，只要发布者发布消息，所有订阅者都能收到消息，订阅者都是平等的。

PUB/SUB

Redis自带`pub/sub`机制即发布订阅模式，此模式中生产者`producer`和消费者`consumer`之间的关系是一对多的，也就是一条消息会被多个消费者所消费，当只有一个消费者时可视为一对一的消息队列。

发布订阅机制模型

首先发布者将消息发布到频道，客户端订阅频道后就能获得频道的消息。

发布订阅模式命令

- `psubscribe` 订阅一个或多个符合给定模式的频道
- `publish` 将消息发布到指定的频道
- `pubsub`查看订阅与发布系统状态
- `pubsub channels pattern` 列出当前的活跃频道
- `pubsub numsub channel-1 channel-n` 获取给定频道的订阅者数量
- `pubsub numpat` 获取订阅模式的数量
- `punsubscribe` 指示客户端退订所有给定模式
- `subscribe` 订阅给定的一个或多个频道的消息
- `unsubscribe` 指示客户端退订给定的频道

### 1.客户端发布/订阅

#### 1.1  普通的发布/订阅

 　除了实现任务队列外，redis还提供了一组命令可以让开发者实现"发布/订阅"(publish/subscribe)模式。"发布/订阅"模式同样可以实现进程间的消息传递，其原理如下:

　　"发布/订阅"模式包含两种角色，分别是发布者和订阅者。订阅者可以订阅一个或者多个频道(channel),而发布者可以向指定的频道(channel)发送消息，所有订阅此频道的订阅者都会收到此消息。

+ 发布消息

　　发布者发布消息的命令是 publish,用法是 publish channel message，如向 channel1.1说一声hi

```
127.0.0.1:6379> publish channel:1 hi
(integer) 0
```

　　这样消息就发出去了。返回值表示接收这条消息的订阅者数量。发出去的消息不会被持久化，也就是有客户端订阅channel:1后只能接收到后续发布到该频道的消息，之前的就接收不到了。

 

+ 订阅频道

　　订阅频道的命令是 subscribe，可以同时订阅多个频道，用法是 subscribe channel1 [channel2 ...],例如新开一个客户端订阅上面频道:(不会收到消息，因为不会收到订阅之前就发布到该频道的消息)

```
127.0.0.1:6379> subscribe channel:1
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "channel:1"
3) (integer) 1
```

　　执行上面命令客户端会进入订阅状态，处于此状态下客户端不能使用除subscribe、unsubscribe、psubscribe和punsubscribe这四个属于"发布/订阅"之外的命令，否则会报错。

　　进入订阅状态后客户端可能收到3种类型的回复。每种类型的回复都包含3个值，第一个值是消息的类型，根据消类型的不同，第二个和第三个参数的含义可能不同。

消息类型的取值可能是以下3个:

  1. subscribe。表示订阅成功的反馈信息。第二个值是订阅成功的频道名称，第三个是当前客户端订阅的频道数量。

  2. message。表示接收到的消息，第二个值表示产生消息的频道名称，第三个值是消息的内容。

  3. unsubscribe。表示成功取消订阅某个频道。第二个值是对应的频道名称，第三个值是当前客户端订阅的频道数量，当此值为0时客户端会退出订阅状态，之后就可以执行其他非"发布/订阅"模式的命令了。


+ 第一个客户端重新向channel:1发送一条消息

```
127.0.0.1:6379> publish channel:1 hi
(integer) 1
```

上面订阅的客户端:

```
127.0.0.1:6379> subscribe channel:1
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "channel:1"
3) (integer) 1
1) "message"
2) "channel:1"
3) "hi"
```

　　红字部分表示成功的收到消息(依次是消息类型，频道，消息内容)

#### 发布订阅

> 生产者

```
public class MessageProducer extends Thread {
    public static final String CHANNEL_KEY = "channel:1";
    private volatile int count;

    public void putMessage(String message) {
        Jedis jedis = JedisPoolUtils.getJedis();
        Long publish = jedis.publish(CHANNEL_KEY, message);//返回订阅者数量
        System.out.println(Thread.currentThread().getName() + " put message,count=" + count+",subscriberNum="+publish);
        count++;
    }

    @Override
    public synchronized void run() {
        for (int i = 0; i < 5; i++) {
            putMessage("message" + count);
        }
    }

    public static void main(String[] args) {
        MessageProducer messageProducer = new MessageProducer();
        Thread t1 = new Thread(messageProducer, "thread1");
        Thread t2 = new Thread(messageProducer, "thread2");
        Thread t3 = new Thread(messageProducer, "thread3");
        Thread t4 = new Thread(messageProducer, "thread4");
        Thread t5 = new Thread(messageProducer, "thread5");
        t1.start();
        t2.start();
        t3.start();
        t4.start();
        t5.start();
    }
}
```

> 消费者

```java
public class MessageConsumer implements Runnable {
    public static final String CHANNEL_KEY = "channel:1";//频道

    public static final String EXIT_COMMAND = "exit";//结束程序的消息

    private MyJedisPubSub myJedisPubSub = new MyJedisPubSub();//处理接收消息

    public void consumerMessage() {
        Jedis jedis = JedisPoolUtils.getJedis();
        jedis.subscribe(myJedisPubSub, CHANNEL_KEY);//第一个参数是处理接收消息，第二个参数是订阅的消息频道
    }

    @Override
    public void run() {
        while (true) {
            consumerMessage();
        }
    }

    public static void main(String[] args) {
        MessageConsumer messageConsumer = new MessageConsumer();
        Thread t1 = new Thread(messageConsumer, "thread5");
        Thread t2 = new Thread(messageConsumer, "thread6");
        t1.start();
        t2.start();
    }
}


class MyJedisPubSub extends JedisPubSub {
    @Override
    /** JedisPubSub类是一个没有抽象方法的抽象类,里面方法都是一些空实现
     * 所以可以选择需要的方法覆盖,这儿使用的是SUBSCRIBE指令，所以覆盖了onMessage
     * 如果使用PSUBSCRIBE指令，则覆盖onPMessage方法
     * 当然也可以选择BinaryJedisPubSub,同样是抽象类，但方法参数为byte[]
     **/
    public void onMessage(String channel, String message) {
        System.out.println(Thread.currentThread().getName()+"-接收到消息:channel=" + channel + ",message=" + message);
        //接收到exit消息后退出
        if (MessageConsumer.EXIT_COMMAND.equals(message)) {
            System.exit(0);
        }
    }
}
```