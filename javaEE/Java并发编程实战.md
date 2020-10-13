## 简述

+ 线程也成为轻量级进程

+ 进程是操作系统资源分配的最小单位，线程是cpu调度的最小单位

+ 同一个进程中的线程是都将共享进程的内存地址空间，因此这些形成都可以访问相同的变量并在一个堆上分配对象

## 线程安全性

如果多个线程访问同一个可变的状态变量时，没有使用合适的同步，程序就出现错误，解决方案：

1. 不在线程中共享这个状态变量
2. 把状态变量修改为不可变的变量
3. 在访问状态时候使用同步

**安全性**是由**正确性**决定的，正确性的定义是某个类和其行为完全一致，在良好的规范中，通常会定义各种不变的条件来约束对象的状态，以及描述对象操作的结果的后验状态

+ 当多个线程访问一个类时，不管运行时采用何种调度方法或者这些线程如何交替进行，并且在主代码中不用任何额外的同步或者协同，这个类都会表现出正确的行为，这个类就是线程安全的（线程安全类就是在并发环境下和单线程环境中都不会被破坏的类）
+ **一个无状态的对象一定是线程安全的**：他既不包含任何域，也没有对其他类中域的引用。他的操作仅仅操作他的线程栈上的局部变量

### 原子性

> count++并不是一个操作，而是包含了“读取——修改——写入”的操作序列，他的结果依赖于之前的状态。

#### 竞态条件

在并发编程中，这种由于不恰当的执行时序而出现不同结果是一种非常重要的情况。

由于存在多个竞态条件，是结果变得不可靠

+ 在单例中，有可能线程在A创建对象时候，线程B也开始调用这个方法创建对象，按理来说应该返回同一个对象。

  ```java
  public class LasyInit{
      private LasyInit instance;
      priveate LasyInit(){};
      public LasyInit getInstance{
          if(instance==null){//A执行到这时候开始创建，B还没开始
              instance = new LasyInit();//A开始创建了，B这时候进入了if(instance==null)，发现为false，也开始创建新对象。
          }
          return instance;
      }
  }
  ```

#### 复合操作

要避免静态条件，必须确保其他线程只能在修改操作之前或者之后读取或者修改，而而不是在修改状态的过程中。

> 可以使用原子类型`AtomicXXX`类的`incrementAndGet()`方法代替count++

####  内置锁

+ 每个java对象都有一个实现同步的锁，这些锁称为内置锁（或监视器）。

+ JAVA的内置锁相当于一个互斥锁，就是一个线程只能持有这个锁。

#### 重入锁

每一个线程请求一个其他线程所持有的锁的是时候，发出请求的线程就会block，而内置锁是可重入的，意思是这线程再次请求一个由他自己所已经持有的锁的时候，这个请求就会成功。

> 获取锁的操作粒度是“线程”，而不是”调用“，

## 对象的共享

同步还有一个重要的方面，内存可见性(Memory Visibility)，简而言之，我们需要对一个对象在进行修改时，其他线程不会对这个对象进行修改，而且在修改完成之后，其他线程也能看到这个对象状态的变化。

### 可见性

**重排序**：在编译器和处理时与运行时，可能对操作进行顺序进行进行重排序，所以对内存操作的顺序无法正确判断。

**非原子的64位操作**：在Java中，long与double类型，JVM允许把其拆分成2个32位的进行计算。如果对这个变量的读操作和写操作在不同的线程中进行，可能就读取到错误的数据。

#### Volatile变量

这个通过内存栅栏的方式在Jvm上实现的关键字。提供了一种稍弱的同步机制，把变量声明成volatile类型之后，编译器和运行时都会注意到这个变量需要共享，我已不会吧这个变量上的操作和其他内存一起**重排序**。

+ 使用场景：
  1. 确保其自身状态的可见性
  2. 确保他们所引用的状态对象的可见性
  3. 标识一起重要程序的生命周期事件的发生（初始化或关闭）
+ 局限性：
  1. volatile的语义不足以满足原子性操作，（例如：count++）
  2. 加锁可以既确保可见性也可以确保原子性，而volatile只能确保可见性
+ 正确使用方式（当且仅当）：
  1. 对变量的写入不依赖当前值，或者程序员保证只有单个线程更新变量的值
  2. 这幅变量不会与其他状态一起纳入不变性条件中
  3. 访问变量时不需要加锁

### 安全的构建一个对象

对象在构建函数中创建一个线程时，无论是隐式创建还是显示创建，this都会被新创建的线程共享。所以不推荐立即启动的方式。

可以使用工厂方法来防止this在引用在构造的过程中逸出

```java
public class SafeListen{
    private final EvenrListener listener;
    
    private SafeListener(){
        listener = new EventListener(){
            public void onEvent(Event e){
                doSomething(e);
            }
        }
    }
    
    public static SafeListen newInstance(EventSource source){
        SafeListen safe new SafeLisenten();
        source.registerLisener(safe.listener);
        return safe;
    }
}
```

### 线程封闭

一种避免使用同步方式就是不共享数据。

+ 当一个对象封闭在一个线程中时，这种方法将自动实现线程安全性，即使被封闭的对象不是线程安全的。（单线程环境下，无需考虑线程安全的情况)

#### 栈封闭

这些变量位于执行线程的栈中。**栈封闭**也被称为线程内部使用或者线程局部使用

#### ThreadLocal

+ 这个类有set与get方法

可以把**ThreadLocal<T>**视为包含了**Map<Thread,T>**的对象。（不全对）

这些线程的值保存在Thead对象中，线程终止之后，这些值会被作为垃圾回收

+ 当需要把一个单线程应用移植到多线程环境是，可以把共享的全局变量转换成`ThreadLocal`对象，以维持这个线程的安全性。

+ 框架可以通过这个类读取事务上下文

### 不变性

> 不可变对象一定是线程安全的。

满足不可变性的条件是：

1. 对象在创建之后其状态就不能修改
2. 对象的所有域都是final类型（String类是不可变对象，但是你可以不用final修饰）
3. 对象都是正确创建的（在对象的创建过程中，this引用没有逸出）

**保存在不可变对象中的状态仍然可以更新——即通过创建或者保存一个新的状态类“替换”原有的不可变对象。**

#### final

在Java的内存模型中，final域可以保证初始化时候的过程是安全的，从而可以不受限的访问不可变对象，并在共享这项对象时候仍旧无需同步

### 安全Public

因为没有确保对象对于其他线程可见，所以仅仅是把对象引用保存到公有域中，那还不注意安全的发布这个对象。

#### 不可变对象与初始化安全性

Java内存模型为不可变对象提供了一种特殊的初始化来保证初始化安全性

> 任何线程都可以在不需要额外同步的情况下安全的访问不可变对象，技术在发布这些对象时并没使用同步

#### 安全发布的常用模式

要安全发布一个对象，对象的引用以及对象的状态必须同时对其他线程可见，可选方法有：

>1. 在静态初始化函数中初始化一个对象的引用
>
>  ```java
>  public static Holder hoder = new Holder();
>  ```
>
>2. 把对象的引用保存到一个volatile类型的域或者`AtomicRefance`对象中
>
>3. 把对象的引用保存到一个正确构造的final类型域中
>
>4. 把对象引用保存到一个由锁保护的域中
>
>  ```java
>  new ConcurrentMap(){};
>  new BlockingQueue(){};
>  ```

#### 事实不可变对象

比如Date类是可变的，可以把它维护成一个不可变对象来使用

```java
public Map<String,Date> mapDate = Collections.synchronizedMap(new HashMap<String,Date>());//Collections.synchronizedMap让集合变成多线程安全的
```

### 实用并发策略

+ 线程封闭。线程封闭的对象只能有一个线程持有，对象封闭在这个线程中，并只能由这个线程进行修改

+ 只读共享。没有额外同步的情况下，共享的只读对象可以由多个线程并发访问，但是任何线程都不能对其修改，共享的只读对象包括不可变对象和事实不可变对象

+ 线程安全共享。线程安全对象在其内部实现同步，所以多个线程可以通过对象的public接口来访问而不需要进一步实现同步。（方法内部加锁）

+ 保护对象。被保护的对象只能通过持有特定锁来进行访问，保护对象包括在封装在其他线程安全对象中的对象，以及已经发布并且由某特定锁保护的对象。

  

## 对象的组合

实际生产环境，我们不希望对每次内存的访问进行分析以实现线程安全，鹅绒希望吧线程安全组件进行组合以成为更大规模的组件。

### 设计线程安全的类

基本要素：

1. 找出构成对象的所有变量
2. 找出约束状态条件的不变性条件
3. 建立对象状态的并发访问管理策略

### 实例封闭

封装简化线程安全累的实现过程，提供了一种**实例封闭机制**

实例封闭是构建线程安全类的一个最简单的方式，使得在锁的选择上面有更多灵活性

> 把数据封闭在对象内部可以把数据的访问限制在对象的方法上，从而更容易保证线程在访问数据是总可以持有正确位置的锁

### Java的监视器模式

synchronize在字节码中会编译成**monitorenter**和**monitorexit**，用于进入和退出同步代码块。

> 通过一个私有锁来保护状态
>
> ```java
> public class priLock{
>     private final Object myLock = new Object;
>     private Wight wight;
>     void doSomethings(){
>         synchronized(myLock){
>             //修改wight的状态
>         }
>     }
> }
> ```

委托模型

如果一个类是由多个独立且线程安全的状态变量组成，并且在所有的操作中都不包括无效的状态转换那么可以把线程安全性委托给底层的状态变量。

比如把线程安全性委托给底层的`ConcurrentHashMap`，这个Map中的元素是线程安全的且可变的，

## 基础构建模块

### 同步容器类

同步容器类包括早期的Vector（基本弃用）和`Hashtable`（**ConcurrentHashMap**（一致性hashmap）他不香吗），这些类的创建基本就是由`Collections.synchronizedXxx`等工厂方法创建的。

### 迭代容器

现在的许多容器也没有消除**复合操作**的问题，对容器进行直接迭代的标准方式都是使用Iterator。但是这不能避免在迭代的过程中，其他线程也会并发的修改容器，所以很多容器的解决方案就是发现迭代时被修改了就马上抛出`concurrentModificationException`，但只能当做并发问题的预警警示器

```java
		//使用迭代器来遍历list
		List<String> s = new ArrayList<>();

		...//add操作
    
        Iterator<String> iterator = s.iterator();
        while (iterator.hasNext()){
            String next = iterator.next();
            System.out.println(next);
        }
```

### 并发容器

`ConcurrentHashMap`（使用分段锁），`CopyOnWriteArrayList`（写入时复制，一般在读操作远多于写操作时使用），

+ 使用并发容器来代替同步容器，可以极大的提高伸缩性和降低风险

### BlockingQueue和生产者与消费者模式

生产者不需要知道消费者的数量和状态，只需要把东西生产了交付个阻塞队列，同理，消费者也自己从阻塞队列中取值简化了生产者和消费者模式，例为单消费者，单生产者的例子

```java
package bq;

import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;

public class BlockingQ {
    BlockingQueue<apple> bq = new ArrayBlockingQueue<apple>(5);

    public static void main(String[] args) throws InterruptedException {
        BlockingQ blockingQ = new BlockingQ();
        new Thread(blockingQ::product).start();
        new Thread(blockingQ::customer).start();
    }

    public void product() {
        try {
            for (int i = 0; i < 5; i++) {
                bq.put(new apple("red", i));
                // TimeUnit.SECONDS.sleep(1);
                System.out.println(Thread.currentThread().getName() + "放置了apple");
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

    }

    public void customer() {
        try {
            for (int i = 0; i < 5; i++) {
                bq.take();
                System.out.println(Thread.currentThread().getName() + "拿走了apple");
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

class apple {
    String color;
    int size;

    public apple(String color, int size) {
        this.color = color;
        this.size = size;
    }
}

```

#### BlockingQueue的四组API

1. 抛出异常
2. 不抛出异常
3. 阻塞等待
4. 超时等待

| 方式     | 抛出异常 | 不抛出异常       | 阻塞等待 | 超时等待                    |
| -------- | -------- | ---------------- | -------- | --------------------------- |
| 添加操作 | add      | offer            | put      | offer(obj,timeOut,TimeUnit) |
| 移除操作 | remove   | poll（返回null） | take     | poll(timeOut,TimeUnit)      |
| 查看队首 | element  | peek             |          |                             |

### 阻塞方法和中断方法

Thread提供了interrupt()中断方法，用于查询线程已经被中断或者中断线程，返回一个boolean属性。

> 解决`InterruptedException`的方案
>
> + 传递`InterruptionException`，可以把这异常传递给这个方案的调用者
>
> + 恢复中断
>
>   ```java
>   class runables implements Runnable{
>       BlockingQueue<String> blockingQueue;
>   
>       @Override
>       public void run() {
>           try {
>               String take = blockingQueue.take();
>           } catch (InterruptedException e) {
>               //恢复被中断的状态
>               Thread.currentThread().interrupt();
>           }
>       }
>   }
>   ```

### 同步工具类

同步工具类可以是任何一个对象，只要他根据自身的状态了协调线程的控制流。

+ 阻塞队列
+ 信号量（Semaphore）
+ 栅栏（Barrier）
+ 闭锁（Latch）

> 也可以通过一些机制来创建自己的同步工具类

#### 闭锁

他作为一种同步工具类， 可以延迟线程的进度直到到达终止状态。

可以用来保证模型或者直到其他活动都完成后再继续执行

+ **CountDownLatch**是一种灵活的闭锁实现，有一个递减计数器，先初始为一个正数，当一件事发生之后count递减，当count==0时为结束状态，如果一直不为0，就一直阻塞，或线程中断或超时。

+ **FutureTask**是计算Callable来实现的，是一种可以生成结果的Runable，有三种状态

  + Waiting to Run：等待运行
  + Complete：运行完成
  + Running ：正在运行

  当运行完成时，`FutureTask`会停在这状态上

#### 信号量

计算信号量的简化操作是二值信号量（初始值为1的信号量），用作互斥体，并拥有不可重入的语义，所以，谁拥有这个唯一的许可，谁就拥有互斥锁。

个人感觉就是PV操作。

#### 栅栏

juc包中提供了`CyclicBarrier`（循环栅栏）。

```java
public int await() throws InterruptedException, BrokenBarrierException
public int await(long timeout, TimeUnit unit) throws InterruptedException, BrokenBarrierException, TimeoutException
```

- 线程调用 await() 表示自己已经到达栅栏
- `BrokenBarrierException` 表示栅栏已经被破坏，破坏的原因可能是其中一个线程 await() 时被中断或者超时

栅栏类似于闭锁，他可以阻塞一个线程直到摸个事件的发生，栅栏于闭锁的区别在于，**所有线程必须通道到达栅栏维持，才能继续执行，**

闭锁用于等待事件，而栅栏用于等待其他线程。

### 开发缓存组件（高效可伸缩）

> 基础组件
>
> ```java
> public interface Computable<A,V> {
>         V computer(A arg) throws InterruptedException;
> }
> 
> public class ExpensiveFunction implements Computable<String , BigInteger> {
> 
>     @Override
>     public BigInteger computer(String arg) throws InterruptedException {
>         return new BigInteger(arg);
>     }
> }
> ```



#### 使用HashMap和同步机制

hashMap不是线程安全的，所以为了确保线程安全性，但是会带来一个明显的**可伸缩**问题：每次只能一个线程调用，其他线程会阻塞。阻塞多了，可能会比不使用记忆的方式还要慢。

#### 使用ConcurrentHashMap替代

虽然是使用了行级锁的`ConcurrentHashMap`，但是同样带来了并发性问题，而且科技风会让一个值被获得多次，

#### 基于FutureTask

```java
package cache;

import java.util.Map;
import java.util.concurrent.*;

/**
 * @author atom.hu
 * @version V1.0
 * @Package cache
 * @date 2020/9/27 20:10
 */
public class Memoizer<A, V> implements Computable<A, V> {
    private final Map<A, Future<V>> cache = new ConcurrentHashMap<>();
    private final Computable<A, V> c;

    public Memoizer(Computable<A, V> c) {
        this.c = c;
    }

    @Override
    public V computer(A arg) throws InterruptedException {
        while (true) {
            Future<V> f = cache.get(arg);
            if (f == null) {
                Callable<V> eval = () -> c.computer(arg);
                FutureTask<V> ft = new FutureTask<>(eval);
                f = cache.putIfAbsent(arg, ft);//没有则添加
                if (f == null) {
                    ft.run();
                    f = ft;
                }
            }
            try {
                return f.get();
            } catch (ExecutionException e) {
                throw launderThrowable(e.getCause());
            } catch (CancellationException e){
                cache.remove(arg,f);//执行错误时清除缓存，保证计算正确
            }
        }
    }

    public static RuntimeException launderThrowable(Throwable t) {
        if (t instanceof RuntimeException) {
            return (RuntimeException) t;
        } else if (t instanceof Error) {
            throw (Error) t;
        } else {
            throw new IllegalStateException("Not unchecked", t);
        }
    }
}
```

当缓存的是future而不是值的时候，将导致缓存污染，所以计算被取消时要在cache中移除或者取消，如果有运行时异常，也会移除，这样FutureTask 的计算才可能成功。

## 小结1

+ 可变状态是至关重要的

  所有的并发访问问题都可以归结在如克协调对于并发状态的访问，可变状态越少，就越容易保证安全性

+ 尽量把域声明为**final**类型，除非需要他们是可变的

+ 不可变对象一定是线程安全的

  不可变对象可以极大的降低并发编程的复杂性，而且可以任意共享而无需**加锁**或者**保护性复制**

+ 合适的封装可以有助于管理复杂性

+ 用锁来保护每一份可变变量

+ 保护同一个不变性条件中的所有变量是，要使用同一个锁

+ 执行复合操作时，需要持有锁

+ 如果从多个线程中访问一个可变变量时没有同步机制，程序可能出问题

+ 不要自以为的可以不使用同步（在使用线程安全的类时，遇到复合操作，仍然需要使用同步）

+ 设计过程中考虑线程安全或者在文档中指出他不是线程安全的

+ 把同步策略文档化

## 任务执行

**任务**通常是一项抽象并且离散的一些单元。

可以把应用程序分解到多个任务中，可以简化程序的组织结构，提供一种自然的事务边界来优化错误的恢复过程

### 在线程中执行任务

1. 找出任务边界
2. 任务并不依赖其他的任务的状态、结果、边界效应。
3. 多数web服务器的任务边界是一独立的客户请求为边界

#### 串行的处理任务

在应用服务中，使用串行处理机无法提供高吞吐量和快速响应性。

#### 显式的创建线程

缺陷：

+ 线程的生命周期的开销非常高
+ 资源消耗，活跃的线程会消耗系统资源，大量的空闲资源会占用许多内存
+ 稳定性
+ 可创建线程的数量上存在一个限制，可能因为这个限制，在高请求下会产生运行时异常

### Executor框架（用户级的调度器/线程池）

```java
public interface Executor{//线程池的Executor接口
    void executor(Runnable command);
}
```

他提供了一种标准的方法被任务的提交过程可执行过程解耦开来，使用**Runnable**来表示任务

#### 线程池

构建线程池的几种方法，一共有4种，具体是什么没答出来

​			Executors类中有四种**静态工厂**方法

+ **newCachedThreadPool** 创建一个可以缓存的线程池，如果线程池长度超过处理需要，可以回收线程或者新建线程

+ **newFixThreadPool**创建定长线程池，超出的线程在queue队列等待

+ **newScaneduledThreadPool** 创建一个定长线程池，支持定时和周期性任务执行

+ **newSingleThreadExecutor**创建一个单线程的线程池，在唯一的工作线程执行任务，保证按指定顺序进行。（FIFO，LIFO，优先级）

  都是对`ThreadPoolExecutor`的使用，传了不同的参数

#### Executor的生命周期

生命周期有三种状态：

+ 运行
+ 关闭
+ 终止

有`ExecutorService`接口,继承了Executor接口，添加了一些生命周期的管理方法

`shutdown`方法平缓的执行结束

`shutdownNow`粗暴的执行结束

### 任务的并行性

#### 携带结果的Callable与Future

对于Runnable，Callable是一种更好的抽象，例如执行数据库的查询时，一般是需要从主入口点返回一个值。

Runnable和callable都是描述的抽象的任务计算。有四个生命周期：创建、提交、开始、完成。在Executor框架中，已经开始的任务需要需求，需要他们自身响应中断（interrupt）

Future表示一个任务的生命周期，并提供了响应的方案；判断师傅已经完成了取消以及获取任务的结果。**任务的生命周期只能前进**，某个任务完成时，就停留在“完成”状态上。

`get()`方法用于**状态依赖**的内置特性，因此调用者不需要知道任务的状态，此外在提交和获得结果中包含了安全发布属性也确保了这个方法的调用是线程安全的。

#### 完成服务

`CompletionService`可以实现判断任务是否完成

它把Executor和BlockingQueue的功能融合在了一起可预测把Callable任务交付，然后使用类似队列的操作获取已经完成的结果（Future类型）

## 任务的取消与关闭

Java里面没有任何一种机制可以让线程安全的终止。但是他提供了中断（Interruption），这是一种**协助机制**，可以让一个线程停止另一个线程的当前工作

### 任务取消

导致任务取消的原因有：

+ 用户请求取消
+ 有时间限制的取消
+ 应用程序事件
+ 错误
+ 关闭

### 中断

**中断，通常是实现取消的最合理的方式**

每一个线程都有一个boolean类型的中断状态，线程中断时，这个线程的中断状态将被设置为true，Thread类中包含了中断线程以及查询中断线程的方法，

```java
public class Thread{
	public void interrupt(){};//中断目标线程
    public boolean isInterrupted(){};//返回目标线程中断状态
    public static boolean interrupted(){};//q清除当前线程的中断状态
}
```

使用阻塞库时，`Thread.sleep()`,`Object.wait()`,都会检查线程何时中断，并在发现从中断时提前返回。

 **调用interrupt并不意味着立即停止目标线程正在进行的工作，而是传递了请求中断的信息**

interrupt并不会真正的中断一个线程，而是发出一个请求，然后由线程在合适的地方中断自己。

```java
import java.util.concurrent.BlockingQueue;

public class Producer extends Thread {
    private final BlockingQueue<Integer> queue;

    public Producer(BlockingQueue<Integer> queue) {
        this.queue = queue;
    }

    @Override
    public void run() {
        try {
            Integer p = 1;
            while (!Thread.currentThread().isInterrupted()){
                queue.put(p);
            }
        } catch (InterruptedException e) {
            //允许线程退出
            e.printStackTrace();
        }
    }
    
    public void cancel(){
        this.interrupt();
    }
}
```

#### 中断策略

处理`InterruptedException`有两种策略：

+ 传递异常
+ 恢复中断状态

最合理的中断策略应该是以某种形式的线程级操作取消和服务级操作取消。

如果要把中断异常传递给调用者之外，还需要进行其他操作，那么应该在捕获异常之后恢复中断状态

```java
Thread.currentThread.interrupt();
```

#### 响应中断

#### 对阻塞的事件处理中断

##### 改写interrupt

```java

```



##### 使用newTaskFor来封装取消操作

```java

```

### 停止基于线程的服务

> 对于持有线程的服务，只要服务的时间大于创建线程的方法存在的时间，那么就应该提供生命周期方法

## 线程池的使用

有些任务需要明确的指定执行策略：

+ 依赖性任务：如果线程池中的任务需要依赖其他任务的活动，那么就隐含的给执行策略带来了约束
+ 使用线程封闭机制的任务：单线程的Executor可以对并发性做出更强的承诺，对象封闭在任务线程中，使得线程中的任务访问这个对象时不会同步，
+ 对响应时间敏感的任务：把每个运行时间长的任务交给单线程的Executor中，会降低Executor管理服务的响应性
+ 使用ThreadLocal的任务：ThreadLocal可以让每个线程都拥有一个变量的私有复制。只有当线程本地值的什么周期受限于任务的生命周期时，在线程池中使用ThreadLocal才有意义，而且在线程池中的线程不应该使用ThreadLocal在任务之间传递值

### 线程饥饿死锁

一个任务依赖于其他任务，那么可能产生死锁。

若所有正在执行任务的线程因为等待其他仍在工作队列中的任务而发生阻塞，那么就会发生**线程饥饿死锁**。

### 设置线程池的大小

对于计算密集型任务而言，在有N个CPU的环境中，一般设在N+1个线程为最优。

对于使用I/O操作或是其他阻塞操作的任务时，线程池应该更大

### 配置ThreadPoolExecutor

通过Executors中的工厂方法，基于`ThreadPoolExecutor`实现了一个稳定灵活的线程池

```java
public ThreadPoolExecutor(int corePoolSize,//线程池的大小
                          int maximumPoolSize,//线程池的最大线程数
                          long keepAliveTime,//没有线程任务执行时最多保持多久时间才会停止
                          TimeUnit unit,//参数keepAliveTime的时间单位，有7种取值
                          BlockingQueue workQueue,//一个阻塞队列，用于存储等待执行的任务，影响线程池的排队策略
                          ThreadFactory threadFactory,//用于设置线程的工厂，
                          RejectedExecutionHandler handler);//当拒绝处理任务时的策略（饱和策略），有以下四种策略
```

```text
1、AbortPolicy：直接抛出异常。
2、CallerRunsPolicy：只用调用者所在线程来运行任务。
3、DiscardOldestPolicy：丢弃队列里最近的一个任务，并执行当前任务。
4、DiscardPolicy：不处理，丢弃掉。
5、也可以根据应用场景需要来实现RejectedExecutionHandler接口自定义策略。如记录日志或持久化不能处理的任务。
```

#### 线程的创建与消耗

线程池的基本大小，最大大小，以及存活时间等硬说共同负责线程的创建于销毁

```java

    public static void main(String[] args) {
        //创建使用单个线程的线程池
        ExecutorService es1 = Executors.newSingleThreadExecutor();
        for (int i = 0; i < 10; i++) {
            es1.submit(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName() + "正在执行任务");
                }
            });
        }
        //创建使用固定线程数的线程池
        ExecutorService es2 = Executors.newFixedThreadPool(3);
        for (int i = 0; i < 10; i++) {
            es2.submit(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName() + "正在执行任务");
                }
            });
        }
        //创建一个会根据需要创建新线程的线程池
        ExecutorService es3 = Executors.newCachedThreadPool();
        for (int i = 0; i < 20; i++) {
            es3.submit(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName() + "正在执行任务");
                }
            });
        }
        //创建拥有固定线程数量的定时线程任务的线程池
        ScheduledExecutorService es4 = Executors.newScheduledThreadPool(2);
        System.out.println("时间：" + System.currentTimeMillis());
        for (int i = 0; i < 5; i++) {
            es4.schedule(new Runnable() {
                @Override
                public void run() {
                    System.out.println("时间："+System.currentTimeMillis()+"--"+Thread.currentThread().getName() + "正在执行任务");
                }
            },3, TimeUnit.SECONDS);
        }
        //创建只有一个线程的定时线程任务的线程池
        ScheduledExecutorService es5 = Executors.newSingleThreadScheduledExecutor();
        System.out.println("时间：" + System.currentTimeMillis());
        for (int i = 0; i < 5; i++) {
            es5.schedule(new Runnable() {
                @Override
                public void run() {
                    System.out.println("时间："+System.currentTimeMillis()+"--"+Thread.currentThread().getName() + "正在执行任务");
                }
            },3, TimeUnit.SECONDS);
        }
```

+ `newFixedThreadPool`和`newSingleThreadPool`默认使用一个无界的`LinkedBlockingQueue`。当所有线程都处于忙碌状态是，任务将在队列中等候。

+ 使用有界队列来存放时，队列的大小与线程池的大小需要同步调节。
+ `SynchronousQueue`用于非常大或是无界的线程池，让任务避免排队，但这不是一个真正的队列，而是一种在线程中进行移交的机制，`newCachedThreadPool`就使用中这个机制

> 当线程中存在依赖时，这时就应该选用无界的线程池，已避免导致线程饥饿死锁的问题

#### 线程工厂

当线程需要创建线程时的，都是通过线程工厂方法。

默认的线程工厂方法将创建一个新的、非守护的线程，创建一个线程调用`newThread`

```java
public interface ThreadFactory{
    Thread newThead(Runnable r);
}
```

可以通过自定义的线程工厂，创建了新的myThread。

#### 定制ThreadPoolExecutor

调用完构造函数之后，仍然可以通过set来修改大多数传递给它的构造参数。 

## 活跃性、性能、测试

+ 我们使用锁来保证线程安全，但是可能导致锁**顺序死锁**

+ 我们使用线程池和信号量来限制对资源的使用，但是这些限制的行为可能导致**资源死锁**

## 活跃性危险

### 死锁

产生死锁的条件:

1. **互斥条件**。即某个资源在一段时间内只能由一个进程占有，不能同时被两个或两个以上的进程占有。这种独占资源如CD-ROM驱动器，打印机等等，必须在占有该资源的进程主动释放它之后，其它进程才能占有该资源。这是由资源本身的属性所决定的。如独木桥就是一种独占资源，两方的人不能同时过桥。
2. **不可抢占条件**。进程所获得的资源在未使用完毕之前，资源申请者不能强行地从资源占有者手中夺取资源，而只能由该资源的占有者进程自行释放。如过独木桥的人不能强迫对方后退，也不能非法地将对方推下桥，必须是桥上的人自己过桥后空出桥面（即主动释放占有资源），对方的人才能过桥。
3. **占有且申请条件**。进程至少已经占有一个资源，但又申请新的资源；由于该资源已被另外进程占有，此时该进程阻塞；但是，它在等待新资源之时，仍继续占用已占有的资源。还以过独木桥为例，甲乙两人在桥上相遇。甲走过一段桥面（即占有了一些资源），还需要走其余的桥面（申请新的资源），但那部分桥面被乙占有（乙走过一段桥面）。甲过不去，前进不能，又不后退；乙也处于同样的状况。
4. **循环等待条件**。存在一个进程等待序列{P1，P2，...，Pn}，其中P1等待P2所占有的某一资源，P2等待P3所占有的某一源，......，而Pn等待P1所占有的的某一资源，形成一个进程循环等待环。就像前面的过独木桥问题，甲等待乙占有的桥面，而乙又等待甲占有的桥面，从而彼此循环等待。

死锁的解除就是破坏这几种条件

+ Java解决死锁并不会像数据库系统（资源剥夺）这么强大，死锁是，恢复程序的唯一方式就是重启并终止他。

**应该在设计时就避免产生顺序死锁：确保线程在获取多个锁时采用一致性的顺序。**

### 其他活跃性危险

+ 饥饿
+ 糟糕的响应性
+ 活锁（一般在事务响应的程序中）尝试引入随机性，解决活锁问题

## 性能可伸缩性

线程的额外开销：

+ 线程之间的协调（加锁、触发信号、内存同步……）
+ 增加的上下文切换（需要保存寄存器状态到线程自己的栈中，上下文切换的信息会一直被保存在CPU的内存中，直到被再次使用）
+ 线程的创建以及销毁
+ 线程的调度

### 可伸缩性

> 当增加计算资源时（cpu、io，内存……），程序的吞吐量或者处理能力也在响应的增加

#### 上下文切换

引起线程上下文切换的原因如下 

1. 当前正在执行的任务完成，系统的CPU正常调度下一个任务。
2. 当前正在执行的任务遇到I/O等阻塞操作，调度器挂起此任务，继续调度下一个任务。
3. 多个任务并发抢占锁资源，当前任务没有抢到锁资源，被调度器挂起，继续调度下一个任务。
4. 用户的代码挂起当前任务，比如线程执行sleep方法，让出CPU。
5. 硬件中断。 

#### 锁的竞争

有3种方式可以减少锁的竞争程度：

1. 减少锁持有的时间

2. 降低锁的请求的频率

3. 使用带有协调机制的独占锁，这些机制运行更高的并发性

   

# 高级篇

## 显式锁

### Lock与ReentrantLock

```java
public interface Lock{
    void lock();
    void lockInterruptibly();//获取可中断的锁
    boolean tryLock();
    boolean tryLock(long timeout,TimeUint unit);//带有时间限制的锁，持有锁时间超时（没在规定时间完成操作就让代码失败，并需要释放锁）
    void unlock();
    Condition newCondition();
}
```

`ReentrantLock`实现了lock接口，并提供了与Synchronize相同的互斥性和内存可见性

### 公平性

`ReentrantLock`提供了两种公平性的选择，创建一个非公平的锁**（默认）**，或是‘一个公平的锁，

+ 公平：FIFO
+ 非公平：先请求的锁不一定先取得

默认使用非公平的原因：性能更高：在恢复一个被挂起的线程与这个线程正在开启运行的时间中存在严重的延迟。

内置的synchronize的锁也是非公平的

### 读写锁

`ReadWriteLock`

## 构建显式的同步工具

### 显式的Condition对象

把Condition对象和一个lock，连接在一起。

+ await
+ signal

相当于Object类中提供的wait和notify方法



### AQS



#### JUC中的AQS实现

+ ReentranLock
+ Semaphore
+ CountDownLatch
+ FutureTask
+ ReentrantReadWriteLock
+ 

 