## BIO

blocking I/O（阻塞同步的io）

服务器实现模式为一个连接连接一个线程，客户端确保请求是服务器就需要启动一个线程进行处理，如果连接不做事情就会浪费资源（可以通过线程池优化）

#### 简单流程

1. 服务器端启动一个serverSocket
2. 客户端启动Socket对服务器进行通信，默认情况下服务器需要对每个客户建立一个线程与之通信
3. 客户端发送一个请求之后，先咨询服务器是否有线程响应
   + 如果没有就会wait或者被拒绝
   + 如果有客户端就会等待请求结束后继续执行

> 一个bio的服务器端

```java
package bio;

import java.io.IOException;
import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.Executor;
import java.util.concurrent.Executors;

public class BIOServer {
    public static void main(String[] args) throws IOException {
        Executor threadPool = Executors.newCachedThreadPool();
        ServerSocket serverSocket = new ServerSocket(6666);
        System.out.println("服务器启动了");
        while (true) {
            //监听等待客户端连接
            Socket socket = serverSocket.accept();
            System.out.println("已连接一个客户端");
            threadPool.execute(() -> {
                //可以和客户端通信的
                handler(socket);
            });
        }
    }

    //一个handle方法与客户端通信
    public static void handler(Socket socket) {
        byte[] bytes = new byte[1024];
        //通过一份socket宏碁一个输入
        try {
            InputStream inputStream = socket.getInputStream();

            //循环读取客户端发送的数据
            while (true){
                int read = inputStream.read(bytes);
                if(read!=-1){
                    System.out.println(new String(bytes,0,read));
                }else {
                    break;
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                socket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }
}

```

## NIO

java non_blocking IO 是一个同步非阻塞的

**核心组件**

+ Channal（通道）（Read/WRITE)
+ Buffer（缓冲区）底层实现是一个数组
+ Selector（选择器）

client和buffer交互，buffer和Channal之间读写，selector选择准备好的通道

> NIO是面向**缓冲区**，或者**面向块**的编程的，数据读取到一个它稍后处理的缓冲区，需要时候可以在缓冲器中前后移动，这句增加了处理过程中的灵活性能，使用它可以提供**非阻塞**的高伸缩网络

### Buffer

本质上是一个可以读写数据的内存块，是使用数组实现的容器对象，这个对象提供了一组方法，可以更加轻松的使用内存块，缓冲区设置了一些参数，可以跟踪和记录缓冲器状态的变化。

**参数**

+ mark（标记，是读还是写）
+ position（位置，下一个要被读或写的元素索引）
+ limit（最大大小，在创建buffer时候设置）
+ capacity （容量）

### Channal

+ 类似流但是通道可以同时进行读写，而流只能读或者写
+ 可以异步读写数据
+ 可以从缓冲区读写数据，也可以写数据到缓冲区

> DatagramChannel是UDP的读取

#### 案例1：把string通过nio的方式写入本地文件

1. 创建一个输出流
2. 使用输出流对象，创建一个通道
3. 创建一个大小为1024的buffer
4. 把数据写入缓冲区
5. 把缓冲区的数据杜阮流

```java
package nio;

import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

public class Channal1 {
    public static void main(String[] args) {
        String str ="ioioioi";
        //创建一个输出流
        try(FileOutputStream fileOutputStream = new FileOutputStream("e:\\file.txt");
            FileChannel channel = fileOutputStream.getChannel()) {
            //创建一个缓冲区
            ByteBuffer allocate = ByteBuffer.allocate(1024);
            allocate.put(str.getBytes());
            allocate.flip();//把limit的值设置为position，position设置为0，或是position设置为limit，持续--
            //把buffer数据写入到channel
            channel.write(allocate);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 案例2：通过nio的方式读取本地文件

```java
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

public class Channel {
    public static void main(String[] args) {
        File file = new File("e:\\file.txt");
        try(FileInputStream fileInputStream = new FileInputStream(file);
            FileChannel channel = fileInputStream.getChannel()) {
            ByteBuffer byteBuffer =ByteBuffer.allocate((int) file.length());
            //把通道的数据放入缓冲区
            channel.read(byteBuffer);
            //把byteBuffer中的字节转成字符串
            byteBuffer.flip();
            System.out.println(new String(byteBuffer.array()));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 案例3：使用一个buffer进行文件的读写操作（复制文件）

文件—>Channel1—>buffer—>Channal2—>文件

```java
package nio;

import java.io.*;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

public class Channel3 {
    public static void main(String[] args) {
        File file = new File("e:\\file.txt");
        try (FileInputStream fileInputStream = new FileInputStream(file);
             FileChannel channel1 = fileInputStream.getChannel();
             FileOutputStream fileOutputStream = new FileOutputStream("e:\\file2.txt");
             FileChannel channel2 = fileOutputStream.getChannel()) {
            ByteBuffer byteBuffer = ByteBuffer.allocate(512);
            while (true) {//循环读取
                //复位,重置标志位，清空buffer
                byteBuffer.clear();//不加这个会死循环
                int read = channel1.read(byteBuffer);
                if (read == -1) {//读取结束
                    break;
                }
                byteBuffer.flip();
                channel2.write(byteBuffer);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 案例4,：直连管道文件最快拷贝

```java
package nio;

import java.io.*;
import java.nio.channels.FileChannel;

public class Channel4 {
    public static void main(String[] args) {
        fastCopy("e://last.rar","e://copy.rar");
    }

    public static boolean fastCopy(String readFile, String writeFile) {
        try (FileInputStream in = new FileInputStream(readFile);
             FileChannel readin = in.getChannel();
             FileOutputStream out = new FileOutputStream(writeFile);
             FileChannel writeout = out.getChannel()) {
            writeout.transferFrom(readin,0,readin.size());//管道直连
            return true;
        } catch (IOException e) {
            e.printStackTrace();
        }
        return false;
    }
}
```

#### MappedByteBuffer

可以让文件直接在内存（堆外内存）修改，操作系统不需要拷贝一次

### Selector

1. java的NIO，使用非阻塞的IO方式，可以使用一个线程，处理多个客户端的连接，就会使用到Selector选择器
2. Selector可以检测到多个注册通道上是否有事件发生，（多个Channel以事件的方式注册到同一个Selector），如果有事件发生，便获取事件，然后进行相应的处理，这样就可以只用一个单线程去管理多个通道，也就是管理多个连接和请求
3. 只用连接真正有读写事件发生时，才会进行读写，减少系统的开销，变扭不必为每个连接都创建一个线程，不用去维护多个线程
4. 避免了多线程上下文切换导致的开销

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20201029165705826.png" alt="image-20201029165705826" style="zoom:67%;" />

+ 当客户端连接时会通过`ServerSocketChannel`得到对应的`SocketChannel`
+ 把SocketChannel注册到一个Selector上
+ 注册后返回一个`SelectionKey`，这个key会返回一个Selector通过一个Set管理起来
+ Selector进行监听select方法，返回用事件的通道数
+ 进一步得到各个有事件发生的`SelectionKey`
+ 通过这个key反向获取`SocketChannel`
+ 通过得到的channel进行相应的io操作 

#### 服务器代码

```java
package nio;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.util.Iterator;
import java.util.Set;

public class ServerNIO {
    public static void main(String[] args) {
        try (//创建ServerSocketChannel——>ServerSocket
             final ServerSocketChannel ServersocketChannel = ServerSocketChannel.open();
             final Selector selector = Selector.open();
        ) {
            ServersocketChannel.socket().bind(new InetSocketAddress(6666));
           ServersocketChannel.configureBlocking(false);//为非阻塞模式
            //把serverServersocketChannel注册到selector，关心事件为OP_ACCEPT
            ServersocketChannel.register(selector, SelectionKey.OP_ACCEPT);

            //循环等待客户端连接
            while (true){
                if(selector.select(1000)==0){
                    System.out.println("无连接，等待了1s");
                    continue;
                }
                //拿到有事件发生的selectionKey
                //如果>0，表示已经获取到关注的时间
                final Set<SelectionKey> selectionKeys = selector.selectedKeys();
                final Iterator<SelectionKey> iterator = selectionKeys.iterator();
                while (iterator.hasNext()) {
                    final SelectionKey key = iterator.next();
                    //根据key的事件做相应的处理
                    if(key.isAcceptable()){//有新的客户端进行请求连接
                        //为这个客户端进行生成一个ServersocketChannel
                        //现在是阻塞的，不过没有关系，因已经是有请求的连接
                        final SocketChannel socketChannel = ServersocketChannel.accept();
                        System.out.println("客户端连接成功");
                        socketChannel.configureBlocking(false);
                        //把当前的socketChannel注册到selector中
                        socketChannel.register(selector,SelectionKey.OP_READ, ByteBuffer.allocate(1024));
                    }
                    if(key.isReadable()){//是一个读事件
                        //通过key，反向获取到对应的Channel
                        final SocketChannel channel = (SocketChannel) key.channel();
                        //获取的这个channel关联的buffer
                        final ByteBuffer buffer = (ByteBuffer) key.attachment();
                        channel.read(buffer);
                        System.out.println("从客户端"+new String(buffer.array()));
                    }
                    //手动从集合中移除当前的selectKey，防止重复操作
                    iterator.remove();
                }
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 客户端代码

```java
package nio;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SocketChannel;
import java.util.concurrent.TimeUnit;

public class ClientNOI {
    public static void main(String[] args) {
        //得到一个网络通道
        try {
            SocketChannel socketChannel = SocketChannel.open();
            socketChannel.configureBlocking(false);
            InetSocketAddress address = new InetSocketAddress("127.0.0.1", 6666);
            if (!socketChannel.connect(address)) {
                while (!socketChannel.finishConnect()) {
                    System.out.println("连接需要时间，客户端可以进行其他工作");
                }
            }//连接成功，发送数据
            String str = " hello,world";
            final ByteBuffer buffer = ByteBuffer.wrap(str.getBytes());
            //发送数据把buffer写入channel
            socketChannel.write(buffer);
            // Thread.sleep(5);
            System.in.read();
            // TimeUnit.SECONDS.sleep(5);//暂停5s，方便观察
        } catch (
                IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### SelectionKey

SelectionKey表示Seletor和网络通道的注册关系

+ OP_ACCEPT 有新的网络可以连接是为accept，值为16
+ OP_CONNECT代表有连接已经建立，值为8
+ OP_READ读操作，值为1
+ OP_WRITE写操作， 值为4

```java
 public abstract SelectableChannel channel();//获得与之关联的通道
 public abstract Selector selector();//获得与之关联的选择器对象
 public abstract int interestOps();//设置或改变监听事件
 public final Object attachment() {return attachment;}//获得遇到关联的共享数据
```

#### 案例：简易聊天室

##### 客户端

```java
package nio.group;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.SocketChannel;
import java.util.Iterator;
import java.util.Scanner;
import java.util.concurrent.Executor;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class Client {
    private static final String HOST = "127.0.0.1";
    private static final int PORT = 6667;
    private Selector selector;
    private SocketChannel socketChannel;
    private String userName;

    public Client() throws IOException {
        selector = Selector.open();
        socketChannel = SocketChannel.open(new InetSocketAddress(HOST, PORT));
        socketChannel.configureBlocking(false);
        //注册到selector
        socketChannel.register(selector, SelectionKey.OP_READ);
        //得到userName
        userName = socketChannel.getLocalAddress().toString().substring(1);
        System.out.println(userName + "is ok");
    }

    //向服务器发送消息
    public void sendInfo(String msg) {
        msg = userName + " say: " + msg;
        try {
            final int write = socketChannel.write(ByteBuffer.wrap(msg.getBytes()));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //读取从服务器的消息
    public void readInfo() {
        try {
            final int readChannel = selector.select(2000);
            if (readChannel > 0) {
                final Iterator<SelectionKey> iterator = selector.selectedKeys().iterator();
                while (iterator.hasNext()) {
                    final SelectionKey key = iterator.next();
                    if (key.isReadable()) {//可读的
                        final SocketChannel channel = (SocketChannel) key.channel();
                        final ByteBuffer buffer = ByteBuffer.allocate(1024);
                        channel.configureBlocking(false);
                        channel.read(buffer);//从通道中读取
                        final String msg = new String(buffer.array());
                        System.out.println(msg.trim());
                    }
                }
            } else {
                System.out.println("没有可用的通道。。。");
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) throws IOException {
        //启动客户端
        final Client client = new Client();
        //启动一个线程
        Executor pool = Executors.newCachedThreadPool();
        pool.execute(()->{
            while (true){
                client.readInfo();
                try {
                    System.out.println("当前"+Thread.currentThread().getName()+"等待2s");
                    TimeUnit.SECONDS.sleep(2);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNextLine()){
            String s = scanner.nextLine();
            client.sendInfo(s);
        }
    }
}
```

##### 服务器

```java
package nio.group;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.net.ServerSocket;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.util.Iterator;

public class Server {
    private Selector selector;
    private ServerSocketChannel serverSocketChannel;
    private static final int PORT = 6667;

    public Server() {
        try {
            //得到选择器
            selector = Selector.open();
            //打开ServerSocketChannel
            serverSocketChannel = ServerSocketChannel.open();
            //绑定端口
            serverSocketChannel.socket().bind(new InetSocketAddress(PORT));
            //设置非阻塞模式
            serverSocketChannel.configureBlocking(false);
            //把serverSocketChannel注册到select上
            serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //监听
    public void listen() {
        try {
            //循环处理
            while (true) {
                int count = selector.select(2000);
                if (count > 0) {
                    Iterator<SelectionKey> iterator = selector.selectedKeys().iterator();
                    while (iterator.hasNext()) {
                        SelectionKey key = iterator.next();
                        //监听到accept事件
                        if (key.isAcceptable()) {
                            final SocketChannel sc = serverSocketChannel.accept();
                            //把sc注册到select
                            sc.configureBlocking(false);
                            sc.register(selector, SelectionKey.OP_READ);
                            System.out.println(sc.getRemoteAddress() + "上线");
                        }
                        if (key.isReadable()) {//通道发生可读事件
                            //处理读
                            readData(key);
                        }
                        iterator.remove();//防止重复操作
                    }
                } else {
                    System.out.println("服务器等待中");
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //读客户端消息
    private void readData(SelectionKey key) {
        SocketChannel channel = null;
        try {
            //得到channel
            channel = (SocketChannel) key.channel();
            //创建buffer
            final ByteBuffer buffer = ByteBuffer.allocate(1024);
            int count = channel.read(buffer);
            if (count > 0) {
                String msg = new String(buffer.array());
                //输出消息
                System.out.println("客户端：" + msg);
                //转发消息(排除自己)
                sendInfo(msg,channel);
            }
        } catch (IOException e) {
            try {
                System.out.println(channel.getRemoteAddress()+"离线了");
                key.cancel();//取消注册
                channel.close();//关闭通道
            } catch (IOException ioException) {
                ioException.printStackTrace();
            }
        }
    }

    //转发消息
    private void sendInfo(String msg, SocketChannel self) {

        try {
            System.out.println("服务器转发消息中。。。");
            //遍历所有注册到selector上的channel，并排除自己
            for (SelectionKey key : selector.keys()) {
                final Channel targetChannel = key.channel();
                if (targetChannel instanceof SocketChannel && targetChannel != self) {
                    //保证是一个socketChannel并保证不是自己
                    final SocketChannel dest = (SocketChannel) targetChannel;
                    //把msg放入buffer
                    final ByteBuffer buffer = ByteBuffer.wrap(msg.getBytes());
                    //把buffer转入通道
                    dest.write(buffer);
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        final Server server = new Server();
        server.listen();//启动监听
    }
}
```

## 零拷贝

+ 减少用户空间和内核空间的复制
+ 在java中，常见的零拷贝有mmap（内存映射）和sendFile
+ 我们说的零拷贝，是在操作系统的角度来说的
+ 在内核空间缓存区中，没有数据是重复的（只有kernel buffer上的一份数据）。
+ 零拷贝不仅仅带来的更少的数据复制，还可以带来其他的性能优势，比如上下文切换，更少的cpu缓存共享，无cpu效验与计算

### 传统拷贝

DMA拷贝：direct memory access：直接内存拷贝，不使用cpu

**传统io**：（经过4次拷贝，4次上下文状态切换）

+ 通过DMA硬件copy到内核空间
+ 然后内核空间cpu拷贝到用户空间
+ 然后cpu拷贝到内核空间上socket上的缓存空间
+ 从DMA拷贝到硬件上的protocol engine（网络处理引擎）

### 内存映射优化（mmap）

+ 通过内存映射，把文件映射到内核缓冲区，同时，用户空间可以共享内核空间的数据，这样在进行网络传输是，就可以减少内核空间到用户空间的拷贝次数

**io操作**：（3次拷贝，4次状态切换）

+ 通过DMA，硬件copy到内核空间
+ 建立内存映射（内核空间中的buffer和用户空间中的buffer建立映射关系），修改直接通过cpucopy在内核空间中完成
+ 从DMA拷贝到硬件上的protocol engine（网络处理引擎）

mmap适合小数据量的读写。

### sendFile优化

+ Linux 2.1提供了sendFile函数
+ 数据不进入用户空间，直接从内存缓冲区进入到SocketBuffer
+ 因为与用户态完全无关，所以减少了一次上下文切换

**io操作**：（3次拷贝，4次状态切换）

+ 通过DMA copy直接到内核空间buffer上
+ 内核空间buffer经过cpu拷贝到socket buffer上，并进行修改
+ 从DMA拷贝到硬件上的protocol engine（网络处理引擎）

sendFile适合大文件的传输

#### 2.4版本中的优化

**修改了sendFIle，避免内核空间拷贝到Socket buffer的操作，直接拷贝到协议栈中，减少一次数据拷贝**

没有cpu拷贝参与，实现了真正的零拷贝

**io操作**：（2次拷贝，两次状态切换）

+ 通过DMA copy直接到内核空间buffer上
+ 内核空间buffer通过DMA拷贝直接到硬件上的protocol engine（网络处理引擎）

#### 案例：传输一个大文件

+ NIO零拷贝使用了transferTo的方式传递一个大文件

> long 发送字节数 =  channel.transferTo(开始位置，结束位置，目标channel)；

## AIO

jdk7提供了Asynchronous I/O，即为AIO，异步非阻塞模式

在进行io编程式常用地两种模式，Reactor和Proactor 

+ NIO采用了Reacor，当有事件触发时，服务器端得到通知，进行相应的处理
+ AIO采用了Proactor模式，有效的请求才启动线程，特点是由操作系统完成之后才通知服务器去启动线程去处理，一般适用于连接数多并且长连接的引用

异步非阻塞无需一个线程去轮询所有IO操作的状态改变，在相应的状态改变后，系统会通知对应的线程来处理。对应到烧开水中就是，为每个水壶上面装了一个开关，水烧开之后，水壶会自动通知我水烧开了。

#### 使用AIO进行文件读写

```java
public class ReadFromFile {
  public static void main(String[] args) throws Exception {
    Path file = Paths.get("/usr/a.txt");
    AsynchronousFileChannel channel = AsynchronousFileChannel.open(file);
 
    ByteBuffer buffer = ByteBuffer.allocate(100_000);
    Future<Integer> result = channel.read(buffer, 0);
 
    while (!result.isDone()) {
      ProfitCalculator.calculateTax();
    }
    Integer bytesRead = result.get();
    System.out.println("Bytes read [" + bytesRead + "]");
  }
}

class ProfitCalculator {
  public ProfitCalculator() {
  }
  public static void calculateTax() {
  }
}
 
public class WriteToFile {
 
  public static void main(String[] args) throws Exception {
    AsynchronousFileChannel fileChannel = AsynchronousFileChannel.open(
        Paths.get("/asynchronous.txt"), StandardOpenOption.READ,
        StandardOpenOption.WRITE, StandardOpenOption.CREATE);
    CompletionHandler<Integer, Object> handler = new CompletionHandler<Integer, Object>() {
 
      @Override
      public void completed(Integer result, Object attachment) {
        System.out.println("Attachment: " + attachment + " " + result
            + " bytes written");
        System.out.println("CompletionHandler Thread ID: "
            + Thread.currentThread().getId());
      }
 
      @Override
      public void failed(Throwable e, Object attachment) {
        System.err.println("Attachment: " + attachment + " failed with:");
        e.printStackTrace();
      }
    };
 
    System.out.println("Main Thread ID: " + Thread.currentThread().getId());
    fileChannel.write(ByteBuffer.wrap("Sample".getBytes()), 0, "First Write",
        handler);
    fileChannel.write(ByteBuffer.wrap("Box".getBytes()), 0, "Second Write",
        handler);
 
  }
}
```

