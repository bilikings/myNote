# SpringCloud

## 简介

继续简化springBoot，模块化，把all in one拆分成模块

### 微服务架构核心问题

1. 服务很多，客户端应该怎么访问
2. 很多服务，服务之间应该如何通行
3. 服务之间应怎么治理
4. 服务崩了，应该怎么处理

### 解决方案

springcloud 生态

1. springcloud netFilx

   + api网关，zuul组件
   + Feign ----HTTPclient
   + 服务注册发现： Eureka
   + 熔断机制： Hystrix

2. Dubbo Zookeeper

   + Dubbo

   + 服务注册发现：Zookeeper

   + 熔断机制： Hystrix

     这个不是很完善

3. springCloudAlibaba
   
   + 

## 面向问题学习

1. 什么是微服务
2. 微服务是如何建立独立通信的
3. springCloud和Dubbo之间有什么区别
4. springboot和springCloud，对他们的理解
5. 什么是服务熔断？什么是服务降级
6. 微服务的优缺点是什么，项目中有哪些坑
7. 你所知道的微服务的技术栈有哪些
8. eurka和zookeeper都可以提供服务注册和发现的功能，说说之间的区别

> 以前的项目架构路线

![image-20200828211334272](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200828211334272.png)

## CAP原则

+ C（强一致性）Consistent

+ A（可用性）Availability

+ P（分区容错性）Partition tolerance

  >最多只能满足其中之二，CA、AP、CP
  >
  >理论核心：一个分布式系统不能同满足这三个特性

+ CA：单点集群
+ CP：性能不是特别高的一致性，分区容错的系统，有RAIN0那味
+ AP：对一致性要求低一些，能满足最终一致性就好

>zookeeper满足的是CP
>
>Eureka保证的是AP

### Master节点pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.oxx</groupId>
    <artifactId>SpringCloudMaster</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    <name>oxxSpringCloudDemo</name>
    <description>Spring Cloud learn</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.3.RELEASE</version>
        <relativePath/>
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchley.RELEASE</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>5.1.47</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid</artifactId>
                <version>1.1.23</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>1.18.12</version>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.10</version>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>


    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

### 子模块配置

在Master下创建一个名为SpringCloudApi的工程，当引入依赖时候

```xml
    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
    </dependencies>
```

这里直接去父依赖中找，同时父工程中会自动添加子模块标识

### 构建微服务核心思路

1. 导入依赖（pom.xml)
2. 编写配置文件（application.yml)
3. 开启这个功能 @EnableXXXX44
4. 配置类（SpirngCloud很多默认用着就好）

## Eureka

> 编写配置文件（application.yml)

```yaml
eureka:
 instance:
   hostname: localhost
 client:
   register-with-eureka: false #是否注册自己
   fetch-registry : false #false 自己是注册中心
   service-url:
     defaultZone: http://${eureka,instance,hosetname}:{server.port}/eureka/
```

> 主启动类

@EnableEurekaServer

#### 其他服务注入到Eureka

在其他服务中添加Eureka依赖

> 主启动类

@EnableEurekaClient

## Ribbon

基于Netflix Ribbon实现的的一套**客户端负载均衡工具**

通过微服务名字去实现负载均衡是Ribbon的思想

通过注解和接口实现是Feign的思想

#### 负载均衡

>实现

+ 轮询
+ hash
+ 随机

>目的

+ 可以把用户的请求平均分摊到多个服务器上，从而实现高可用

>分类

+ 集中式LB
+ 进程式LB

#### 自定义负载均衡算法

Ribbon自带的负载均衡实现

![image-20200830121722212](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200830121722212.png)

> + RandomRule表示随机策略、
> + RoundRobinRule表示轮询策略、
> + WeightedResponseTimeRule表示加权策略、
> + BestAvailableRule表示请求数最少策略
> + AvailabilityFilteringRule 会过来掉不可用的，剩下的轮询
> + RetryRule 会先按照轮询来活动服务，如果服务失败就会在指定的时间内进行重试