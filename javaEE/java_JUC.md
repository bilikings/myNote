# 线程

## 线程同步控制

```java
public class ProducerConsumer{
    class Ticket{
        int number = 0;
        int size;
        boolean available = false;
        public Ticket(int size){
            this.size = size;
        }
    }
    public static void main(String[] arg){
        Ticket t = new Tickets(10);
        Thread save = new Comsumer(t);
        save.start();
        Thread sell = new Producer(t);
        sell.start();
    }
}

public class producer extends Thread{
    Ticket t =null;
    public Producer(Tickets t){this.t=t;}
    public run(){
        while(t.numble<t.size){
            sout("Producer puts ticket"+(++t.number));
            t.available = true;
        }
    }
}

public class Comsumer extends Thread{
    Ticket t = null;
    int i;
    public Comsumer(Ticket t){this.t = t;}
    public run(){
        while(i < t.size){
            if(t.available == true && i <= t.number)
                sout("comsumer buy ticket"+(++i));
            if(i>t.number)
                t.available =false;
        }
    }
}

```



## 线程同步关键字<font color="#666600"> **synchronized**</font> 实现互斥

1. 用于指定需要同步的代码段或方法，也就是监视区

2. 实现一个锁的交互

   + synchronized(对象){code}

3. 功能是：先判断对象锁是否存在，如果存在就获得锁，之后执行他的代码段；如果被其他拿走，就等待，直到获得锁

4. 将synchronized内的代码变为原子操作

   ```java
   public class producer extends Thread{
       Ticket t =null;
       public Producer(Tickets t){this.t=t;}
       public run(){
           while(t.numble<t.size){
               synchronized(t){
               sout("Producer puts ticket"+(++t.number));
               t.available = true;
               }
           }
       }
   }
   ```

   + 只能同步方法，不能同步变量
   + 要明确对什么对象同步
   + 类可以有同步方法和非同步方法，非同步方法可以被多个线程自由访问
   + 两个线程用同一实例来调用synchronized方法，一次只能一个线程执行，另一个要等待锁

   ## 线程的等待——wait()方法

   * wait()也是阻塞和等待，但是需要notify来唤醒。

     ```java
       public synchronized void put(){
           if(available)
               try{wait();}catch(Exception e){}
               System.out.println("Producer puts ticket"+(++number));
               available = true;
           	notify();
       	}
           public synchronized void sell(){
                   if(!available)
               try{wait();}catch(Exception e){}
                   if(available && i <= number)
                       System.out.println("consumer buy ticket"+(++i));
                   if(i == number)
                       available =false;
           	notify();
               if(number == size) number = size + 1；//number大于size表示售票结束
           }
     ```

  ##  线程的调度

Runnable队列

```java
public class Ex_13{
    public static void main(String[] args){
        TestThread[] runners = new TestThread[2];//创建一个容量为2的数组存放线程
        for(int i = 0; i<2; i++) runners[i] = new testThread(i);//建立线程
        runners[0].setPriority(2);//设置优先级为2
        runners[1].setPriority(3);//设置优先级为3
        for(a:runners) a.start();
    }
}

public class TestTread extends Thread{
    private int tick = 1;
    private int num;
    pubulic TestTread(int i ){
        this.num = i;
    }
    @override
    public void run(){
        while(tick<400000){
            tick++;
            if((tike%50000)==0){
                sout("Thread # "+ num+ " , tick=" + tick);
                yield();//放弃执行权（就是让java线程调度器去找同优先级的线程，要是没有就继续执行）
            }
        }
    }
}
```



   ## 线程安全

+ 不可变对象
+ 绝对线程安全
+ 相对线程安全
+ 线程兼容与对立
  + 线程兼容：对象本身不是线程安全的，但是可以通过在调用端正确的使用同步手段了保证对象在并发的环境中可以安全使用
  + 线程对立：无论是否采取同步措施，都无法在多线程的环境在并发使用的代码

### 互斥同步

+ Synchonized为原生语法层面的互斥锁，ReentrantLook为API层面的互斥锁

1. 使用 synchronized()             //会在.class文件里面生成monitorenter和monitorend

2. 重入锁ReentrantLook         //（**性能好**）

   + 可实现：等待可中断、公平锁、锁可以绑定多个条件

     ```java
     import java.util.concurrent,lock.ReentrantLOck;
     public class BufferInerruptibly{
         private ReentrantLock lock = new ReentrantLock();
         public void write(){
             lock.lock();
             try{
                 long startTime = System.currentTimeMillis();
                 sout("写入数据到buff");
                 for(;;){//模拟处理很长时间
                     if(System.currentTimeMillis() - startTime>integer.MAX_VALUE)
                         break;
                 }
                 sout("write end!");
             }finally{//作为异常处理的一部分之后的语句段一定会执行，这里就是不管有没有异常都可以解锁
                 lock.unlock();
             }
         }
         public void read() throws Exception{
        lock.lockInerruptibly();//这里可以相应中断
             try{
                 sout("从这里读取数据")；
             }finally{
                 lock.unlock();
             }
         }
     }
     ```

### 非阻塞同步

   + 阻塞同步：这是一种悲观的并发策略，也是互斥同步

   + 非阻塞同步：基于冲突检测的乐观并发策略，先操作，如果没有其它线程怎样数据，则操作成功；否则就是产生了冲突，需要不断重试了直到成功的策略，不需要把线程挂起。

     + 在AtommicInteger这个类来实现的

       ```java
       class Counter{
           private AtomicInteger count = new AtomicInteger();
           public void increment(){
               count.incrementAndGet();
           }
       }
       //在使用AtomicInteger之后，不需要加锁也能线程安全
       
       
       ```

### 无同步方案

+ 可重入代码

+ 线程本地存储

  代码中数据必须与其他代码共享，试着将这些需要共享数据的代码放到同一个线程中去执行，就能将可见范围限定在同一个线程内，这样无需同步也能保证数据之间不出现数据征用问题



### 锁优化

+ 自旋锁
+ 自适应锁
+ 锁消除
+ 锁粗化
+ 偏向锁





# java.util.concurrent

这是一个jdk1.5之后的一个有关多线程的包

## volatile关键字

内存可见性：不同线程对一个共享数据进行操作的时候，都是先把数据读到自己方法区缓存中去，所以当线程1更改了数据时在 ，线程2是对这个更改的数据不可见的，所以在声明这个数据时候，可以添加一个关键字`volatile`，保证某一个线程在更改这个数据时候，会马上把结果返回主存中（另一个效率很低的方法是每次指向前进行同步，加`synchronized`互斥   锁）

volatile使用底层的内存栅栏

## 原子性

java.util.concurrent.atomic：

+ 使用volatile保证内存可见性
+ CAS（compare-And-Swap）算法保证数据原子性
  + cas有三个操作数：
    1. 内存值 V
    2. 预估值A
    3. 更新值B
  + 当且仅当V==A时，V=B，else，不做任何操作

i++不是原子性的，AtomicXXX（比如：AtomicInteger）类的 getAndIncrement()解决了这个问题，

i--就使用getAndDecrement()解决

## 同步容器类

ConcurrentHashMap采用锁分段机制【Concurrent：并存的】

## Callable接口

可以有返回值

结果需要用FutureTask接收

```
public static void main(String[] args) throws ExecutionException, InterruptedException {
    DemoThread demoThread = new DemoThread();
    FutureTask<Integer> futureTask = new FutureTask<>(demoThread);
    new Thread(futureTask).start();
    final Integer sum = futureTask.get();
    System.out.println(sum);

}
```

```java
class DemoThread implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        final int[] range = IntStream.range(1, 100).toArray();
        for (Integer i:range) {
            System.out.println(i);
            return i;
        }
        return 0;
    }
}
```

## lock同步锁

ReentrantLock，这是一个显示的锁，需要通过lock()方法上锁，用unlock()释放锁。 

### 自动释放锁

```java
import java.util.concurrent.locks.Lock;

public class AutoLock implements AutoCloseable {
    private final Lock lock;

    public AutoLock(Lock lock) {
        this.lock = lock;
        lock.lock();
    }
 
    @Override
    public void close() throws Exception {
        lock.unlock();
    }
}
```

## 生成者和消费者

+ 使用wait（）让线程等待

+ notify()/notifyAll()唤醒其他wait的线程

+ 直接使用会造成虚假唤醒，所以应该总是使用在循环中

  ```java
  synchronized(obj){
      while (){
          obj.wait();
      }
  }
  ```

## 读写锁

ReadWriteLock

+ **解决了写写/读写需要互斥而读读不需要互斥的操作**

## 线程8锁

+ 非静态方法的锁默认为thhis，静态方法的锁默认为对应的Class实例
+ 某一个时刻内，只有一个线程持有锁，无论几个方法 

## 线程池

提供了一个线程队列。队列中保存着所有的等待状态的线程，避免了创建和销毁的开销，提高了性能

### interface Executor

+ ExecutorService newFixsdThreadPool():创建固定大小的线程池
+ ExecutorService newCachedThreadPool()  创建缓存线程池，可以根据需要自动更改数量
+ ExecutorService newSingleThreadExecutor() 创建单个线程池，这里面只有一个线程

**线程调度方法**

+ ScheduledExecutorService newScheduledThreadPool() 创建固定大小的线程，可以延时或者定时执行任务

```java
//创建线程池
ExecutorService pool =Executor.newFixsdThreadPool();
ThreadDemo task  = new ThreadDemo();
//为线程池中的线程分配任务
pool.submit(task);
//关闭线程池
pool.shutdown();
```

### 线程调度

## Fork/Join框架

在必要的情况下，把一个大任务拆分（fork）成若干个小任务（拆到不可以在拆的时候），再把一个个小任务运算的结果进行join汇总