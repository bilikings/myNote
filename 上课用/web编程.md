# 复习大纲

## 名词概念

端口：端口是TCP/IP协议簇中，应用层与传输层协议实体间的通信接口，每个端口都有一个端口号的整形标识

SOCKET： 套接字，是一个抽象层，应用程序可以通过他发送或者接受数据，套接字提供在应用层进程利用网络协议栈交换数据的机制，网络套接字是IP地址和端口的组合

INADDR_ANY：代指IP为0.0.0.0，泛指本机，也就是表示本机所有的ip

网络字节顺序：网络数据的一种编排方式，网络自己顺序采用big endian（大端最高有效位存于内存低处，最低有效位存于内存最高处）的排序方式。

本机字节顺序：本地数据的一种编排方式，一般x86的处理器都采用小端

WM_SOCKET_NOTIFY：异步网络事件消息

进程阻塞：正在进行的进程由于发生某种事件而无法继续执行时候，便饭圈处理机而处于暂停状态，也就是说进程的执行受到阻塞

HINTERNET句柄：WinINet函数创建和使用的句柄为HINTERNET类型。WinINet函数返回HINTERNET句柄，此句柄不能与其他句柄类型互换。因此，它们不能作为ReadFile或CloseHandle之类的方法参数。同样，其他句柄类型不能与WinlNet函数-起使用。 例如，CreateFile返回的句柄不能传递给InternetReadFile。

## 简答

#### **简述基于消息的传输协议和基于流的传输协议的特点**

**面向消息的协议以消息位单位**在网络上传输，消息由发送段一条条发送，接受端一条条接收，各个消息都是独立的

**基于流的协议不保护消息边界**，将数据当做字节流进行连续传输，不管实际的消息边界是否存在，如果连续发送数据。接收端的每次接受的数据包不固定，允许发送段把原始信息分割后发送。或者把好几个消息集合成一个大的数据包，一次送出。一般发送的数据都有编号，接收端有缓存。



#### **C/S模式的优缺点**

优点：

1. 结构简单，在系统中不同类型的任务分别由服务器和客户承担，由利于发挥不同机器平台的优势
2. 支持分布式，并发环境，特别是客户端和服务器是多对多的时候可以有效提供资源利用率和共享程度
3. 服务器资源集中管理，由利用权限系统的控制和系统安全

缺点：

1. 在大多数C/S系统中，构建的连接通过（远程）过程调用，接近于代码一级，表达能力弱

#### 结构体socketaddr_in是专门针对Internet的通信延时，请给出这个结构体的原型

   ```c
   struck socketaddr_in{
       short int sin_family;//协议族
       unsigned short int sin_port;//端口号
       struck in_addr;//ip地址
       unsigned char sin_zero[8];//全为0
   }
   ```

####    简述select()的功能

使用函数进行I/O管理，select函数可以判断套接字上是否有数据，或者是否可以向套接字上写数据，设计这个函数的目的是防止套接字在阻塞模式中，I/O调用过程处于阻塞模式，或者在非阻塞模式中，产生WSAEWOULDBOLOCK错误，若不满足实现给定的参数条件，那么select在执行I/O操作时候会阻塞。

#### 简述WinSock规范和Berkeley在哪些方面有区别

winsock是是以bsd套接字为规范的，因此在这个基础上添加了对windows的扩展库函数，使用者可以充分利用windows的消息驱动进行编程

#### 简述TCP的三次握手

+ 第一次握手：建立连接时，客户端发送syn包到服务器，便今日syn-send状态，等待服务器确认
+ 第二次握手：服务器收到syn包，必须确认客户的syn（ack=j+1），同时也发送一个数据包syn（syn=k），此时服务器进入syn-recv状态
+ 第三次握手：
+ 客户端收到syn+ack包，向服务器打算确认包ack（ack=k+1），发送完毕，客户端和服务器都进入ESTABLISHED（已建立状态）状态，完成三次握手。

![image-20200609110342839](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200609110342839.png)

#### 如何理解套接口

套接口是对网络中不同主机的应用进程之间进行双向通信的端点的抽象，一款套接口就是网络上进行网络通信的一端，提供了应用层利用网络协议栈交换数据的机制

#### 简述MFC和windows的关系

windows对象是win32由句柄表示的windows操作系统对象，MFC是指MFC中封装的C++对象，是C++中类的实例，MFC中很多类是通过对系统句柄的封装对windows的实现操作的

## 套接字编程

#### TCP服务器编程的操作

1. 加载套接字字库（WSAStartip()），创建套接字（socket()）;
2. 绑定套接字到一个ip和一个端口上（bind（））；
3. 把套接字设为监听模式（listen（））；
4. 请求到来之后，接受连接请求，返回一个对于于此的连接套接字（accept（））；
5. 用发送的套接字进行和客户端通信（recv（）/send（））；
6. 返回，等待另一个连接请求；
7. 关闭套接字，关闭加载的套接字库(closesocket(/WSACleanup0)。

#### TCP客户端的编程

1. 加载套接字库，通过服务器域名获得服务器的IP地址，创建套接字（WSAStartip()（socket()））;
2. 发出请求（connect（））；
3. 与服务器进行通信（send（）/recv（））
4. 关闭套接字，关闭加载的套接字库(closesocket(/WSACleanup0)。

#### 基于udp（用户数据报）的步骤：

1. 加载套接字字库（WSAStartip()），创建套接字（socket()）;
2. 绑定套接字到一个ip和一个端口上（bind（））；
3. 等待和接受数据（send（）/recv（））；
4. 关闭套接字，关闭加载的套接字库(closesocket(/WSACleanup0)。

#### 基于UDp的socket客户端：

1. 创建一个套接字（socket（））；
2. 发送数据（sendto（））
3. 关闭套接字



## 程序部分

#### NetClient

```c++
#include <Winsock2.h>
#include <stdio.h>

void main()
{
	WORD wVersionRequested;
	WSADATA wsaData;
	int err;
	
	wVersionRequested = MAKEWORD( 1, 1 );
	
	err = WSAStartup( wVersionRequested, &wsaData );
	if ( err != 0 ) {
		return;
	}
	

	if ( LOBYTE( wsaData.wVersion ) != 1 ||
        HIBYTE( wsaData.wVersion ) != 1 ) {
		WSACleanup( );
		return; 
	}

	SOCKET sockClient=socket(AF_INET,SOCK_DGRAM,0);

	SOCKADDR_IN addrSrv;
	addrSrv.sin_addr.S_un.S_addr=inet_addr("127.0.0.1");
	addrSrv.sin_family=AF_INET;
	addrSrv.sin_port=htons(6000);

	char recvBuf[100];
	char sendBuf[100];
	char tempBuf[200];

	int len=sizeof(SOCKADDR);

	while(1)
	{
		printf("Please input data:\n");
		gets(sendBuf);
		sendto(sockClient,sendBuf,strlen(sendBuf)+1,0,
			(SOCKADDR*)&addrSrv,len);
		recvfrom(sockClient,recvBuf,100,0,(SOCKADDR*)&addrSrv,&len);
		if('q'==recvBuf[0])
		{
			sendto(sockClient,"q",strlen("q")+1,0,
				(SOCKADDR*)&addrSrv,len);
			printf("Chat end!\n");
			break;
		}
		sprintf(tempBuf,"%s say : %s",inet_ntoa(addrSrv.sin_addr),recvBuf);
		printf("%s\n",tempBuf);

	}
	closesocket(sockClient);
	WSACleanup();
}
```

#### NetSrv

```c++
#include <Winsock2.h>
#include <stdio.h>

void main()
{
	WORD wVersionRequested;
	WSADATA wsaData;
	int err;
	
	wVersionRequested = MAKEWORD( 1, 1 );
	
	err = WSAStartup( wVersionRequested, &wsaData );
	if ( err != 0 ) {
		return;
	}
	

	if ( LOBYTE( wsaData.wVersion ) != 1 ||
        HIBYTE( wsaData.wVersion ) != 1 ) {
		WSACleanup( );
		return; 
	}
	SOCKET sockSrv=socket(AF_INET,SOCK_DGRAM,0);

	SOCKADDR_IN addrSrv;
	addrSrv.sin_addr.S_un.S_addr=htonl(INADDR_ANY);
	addrSrv.sin_family=AF_INET;
	addrSrv.sin_port=htons(6000);

	bind(sockSrv,(SOCKADDR*)&addrSrv,sizeof(SOCKADDR));

	char recvBuf[100];
	char sendBuf[100];
	char tempBuf[200];

	SOCKADDR_IN addrClient;
	int len=sizeof(SOCKADDR);

	while(1)
	{
		recvfrom(sockSrv,recvBuf,100,0,(SOCKADDR*)&addrClient,&len);
		if('q'==recvBuf[0])
		{
			sendto(sockSrv,"q",strlen("q")+1,0,(SOCKADDR*)&addrClient,len);
			printf("Chat end!\n");
			break;
		}
		sprintf(tempBuf,"%s say : %s",inet_ntoa(addrClient.sin_addr),recvBuf);
		printf("%s\n",tempBuf);
		printf("Please input data:\n");
		gets(sendBuf);
		sendto(sockSrv,sendBuf,strlen(sendBuf)+1,0,(SOCKADDR*)&addrClient,len);
	}
	closesocket(sockSrv);
	WSACleanup();
}
```

#### TcpClient

```c++
#include <Winsock2.h>
#include <stdio.h>

void main()
{
	WORD wVersionRequested;
	WSADATA wsaData;
	int err;
	
	wVersionRequested = MAKEWORD( 1, 1 );
	
	err = WSAStartup( wVersionRequested, &wsaData );
	if ( err != 0 ) {
		return;
	}
	

	if ( LOBYTE( wsaData.wVersion ) != 1 ||
        HIBYTE( wsaData.wVersion ) != 1 ) {
		WSACleanup( );
		return; 
	}
	SOCKET sockClient=socket(AF_INET,SOCK_STREAM,0);

	SOCKADDR_IN addrSrv;
	addrSrv.sin_addr.S_un.S_addr=inet_addr("127.0.0.1");
	addrSrv.sin_family=AF_INET;
	addrSrv.sin_port=htons(6000);
	connect(sockClient,(SOCKADDR*)&addrSrv,sizeof(SOCKADDR));
/*
	char recvBuf[100];
	recv(sockClient,recvBuf,100,0);
	printf("%s\n",recvBuf);
	send(sockClient,"This is lisi",strlen("This is lisi")+1,0);
*/
    char sendBuf[100];
    memset(sendBuf,0,sizeof(sendBuf));
	for(int i=1; i<=100;i++)
	{
		sprintf(sendBuf,"QUREY TIME ORDER: %d\n",i);
		send(sockClient,sendBuf,strlen(sendBuf),0);
	}
	closesocket(sockClient);
	WSACleanup();
}
```

#### TcpSrv

```c++
#include <Winsock2.h>
#include <stdio.h>

void main()
{
	WORD wVersionRequested;
	WSADATA wsaData;
	int err;
	
	wVersionRequested = MAKEWORD( 1, 1 );
	
	err = WSAStartup( wVersionRequested, &wsaData );
	if ( err != 0 ) {
		return;
	}
	

	if ( LOBYTE( wsaData.wVersion ) != 1 ||
        HIBYTE( wsaData.wVersion ) != 1 ) {
		WSACleanup( );
		return; 
	}
	SOCKET sockSrv=socket(AF_INET,SOCK_STREAM,0);

	SOCKADDR_IN addrSrv;
	addrSrv.sin_addr.S_un.S_addr=INADDR_ANY;
	addrSrv.sin_family=AF_INET;
	addrSrv.sin_port=htons(6000);

	bind(sockSrv,(SOCKADDR*)&addrSrv,sizeof(SOCKADDR));

	listen(sockSrv,5);

	SOCKADDR_IN addrClient;
	int len=sizeof(SOCKADDR);
/*
	while(1)
	{
		SOCKET sockConn=accept(sockSrv,(SOCKADDR*)&addrClient,&len);
		char sendBuf[100];
		sprintf(sendBuf,"Welcome %s to http://www.chd.edu.cn",
			inet_ntoa(addrClient.sin_addr));
		send(sockConn,sendBuf,strlen(sendBuf)+1,0);
		char recvBuf[100];
		recv(sockConn,recvBuf,100,0);
		printf("%s\n",recvBuf);
		closesocket(sockConn);
	}
*/
	char recvBuf[100];
    int i=0,recvlen=0;
	memset(recvBuf,0,sizeof(recvBuf));
	while(1)
	{
		SOCKET sockConn=accept(sockSrv,(SOCKADDR*)&addrClient,&len);
	
	    while(recvlen=recv(sockConn,recvBuf,100,0)!=0){
           
		   printf("No.%d ==========================\n",i++);
		   printf("%s\n",recvBuf);
           memset(recvBuf,0,sizeof(recvBuf));
		   
		}
		closesocket(sockConn);
	}
}
```

#### UDPClient

```c++
#include <Winsock2.h>
#include <stdio.h>

void main()
{
	WORD wVersionRequested;
	WSADATA wsaData;
	int err;
	
	wVersionRequested = MAKEWORD( 1, 1 );
	
	err = WSAStartup( wVersionRequested, &wsaData );
	if ( err != 0 ) {
		return;
	}
	

	if ( LOBYTE( wsaData.wVersion ) != 1 ||
        HIBYTE( wsaData.wVersion ) != 1 ) {
		WSACleanup( );
		return; 
	}

	SOCKET sockClient=socket(AF_INET,SOCK_DGRAM,0);
	SOCKADDR_IN addrSrv;
	addrSrv.sin_addr.S_un.S_addr=inet_addr("127.0.0.1");
	addrSrv.sin_family=AF_INET;
	addrSrv.sin_port=htons(6000);
    char sendBuf[100];
	for(int i=1; i<=100;i++)
	{
		sprintf(sendBuf,"QUREY TIME ORDER: %d\n",i);
		sendto(sockClient,sendBuf,strlen(sendBuf),0,(SOCKADDR*)&addrSrv,sizeof(SOCKADDR));
	}
	closesocket(sockClient);
	WSACleanup();
}
```

#### UDPSrv

```c++
#include <Winsock2.h>
#include <stdio.h>

void main()
{
	WORD wVersionRequested;
	WSADATA wsaData;
	int err;
	
	wVersionRequested = MAKEWORD( 1, 1 );
	
	err = WSAStartup( wVersionRequested, &wsaData );
	if ( err != 0 ) {
		return;
	}
	

	if ( LOBYTE( wsaData.wVersion ) != 1 ||
        HIBYTE( wsaData.wVersion ) != 1 ) {
		WSACleanup( );
		return; 
	}

	SOCKET sockSrv=socket(AF_INET,SOCK_DGRAM,0);
	SOCKADDR_IN addrSrv;
	addrSrv.sin_addr.S_un.S_addr=INADDR_ANY;
	addrSrv.sin_family=AF_INET;
	addrSrv.sin_port=htons(6000);

	bind(sockSrv,(SOCKADDR*)&addrSrv,sizeof(SOCKADDR));

	SOCKADDR_IN addrClient;
	int len=sizeof(SOCKADDR);
	char recvBuf[100];
    int recvlen=0;
	memset(recvBuf,0,sizeof(recvBuf));
	while(recvlen=recvfrom(sockSrv,recvBuf,100,0,(SOCKADDR*)&addrClient,&len)!=0){

        printf("%s\n",recvBuf);
        memset(recvBuf,0,sizeof(recvBuf));
	}
	
	closesocket(sockSrv);
	WSACleanup();
}
```

