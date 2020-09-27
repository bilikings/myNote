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

> 可以使用原子类型AtomicXXX类的incrementAndGet()方法代替count++

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

**非原子的64位操作**：在java中，long与double类型，JVM允许把其拆分成2个32位的进行计算。如果对这个变量的读操作和写操作在不同的线程中进行，可能就读取到错误的数据。

#### Volatile变量

这个通过内存栅栏的方式在jvm上实现的关键字。提供了一种稍弱的同步机制，把变量声明成volatile类型之后，编译器和运行时都会注意到这个变量需要共享，我已不会吧这个变量上的操作和其他内存一起**重排序**。

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

+ 当需要把一个单线程应用移植到多线程环境是，可以把共享的全局变量转换成ThreadLocal对象，以维持这个线程的安全性。

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
>2. 把对象的引用保存到一个volatile类型的域或者AtomicRefance对象中
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

比如把线程安全性委托给底层的**ConcurrentHashMap**，这个Map中的元素是线程安全的且可变的，

## 基础构建模块

### 同步容器类

同步容器类包括早期的Vector（基本弃用）和Hashtable（**ConcurrentHashMap**（一致性hashmap）他不香吗），这些类的创建基本就是由Collections.synchronizedXxx等工厂方法创建的。

### 迭代容器

现在的许多容器也没有消除**复合操作**的问题，对容器进行直接迭代的标准方式都是使用Iterator。但是这不能避免在迭代的过程中，其他线程也会并发的修改容器，所以很多容器的解决方案就是发现迭代时被修改了就马上抛出concurrentModificationException，但只能当做并发问题的预警警示器

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

ConcurrentHashMap（使用分段锁），CopyOnWriteArrayList（写入时复制，一般在读操作远多于写操作时使用），

+ 使用并发容器来代替同步容器，可以极大的提高伸缩性和降低风险

### BlockingQueue和生产者与消费者模式

生产者不需要知道消费者的数量和状态，只需要把东西生产了交付个阻塞队列，同理，消费者也自己从阻塞队列中取值简化了生产者和消费者模式，

### 阻塞方法和中断方法

Thread提供了interrupt()中断方法，用于查询线程已经被中断或者中断线程，返回一个boolean属性。

> 解决InterruptedException的方案
>
> + 传递InterruptionException，可以把这异常传递给这个方案的调用者
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

  当运行完成时，FutureTask会停在这状态上

#### 信号量

计算信号量的简化操作是二值信号量（初始值为1的信号量），用作互斥体，并拥有不可重入的语义，所以，谁拥有这个唯一的许可，谁就拥有互斥锁。

个人感觉就是PV操作。

#### 栅栏

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

虽然是使用了行级锁的**ConcurrentHashMap**，但是同样带来了并发性问题，而且科技风会让一个值被获得多次，

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

