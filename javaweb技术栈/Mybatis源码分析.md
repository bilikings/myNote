## 配置文件

MyBatis的配置文件为一个 XML文件，通常被命名为 mybatis-config.xml。该 XML文件的根节点为 configuration，根节点内可以包含的一级节点及其含义如下所示。在springBoot中，配置文件可以使用配置类代替。

1. properties：属性信息，相当于全局变量
2. setting：设置信息，对功能进行调整
3. typeAliases：设置类型别名
4. typeHanders：类型处理器，可以为不同类型设置相应的处理器
5. objectFactory：对象工厂，创建新对象时使用的工厂
6. objectWrapperFactory：对象包装器工厂
7. reflectFactory：反射器工厂
8. plugins：插件，可以为Mybatis配置插件，修改或者扩展其行为
9. environment：环境，可以配置运行环境信息
10. databaseProvider：数据库编号，可对不同的数据库进行不同的编号，可以对不同类型的数据库设置不同的数据库操作语句
11. mappers：映射文件，可以配置映射文件或者映射尽快文件的地址

## Mybatis特点

它使用java方法可SQL语句关联起来，使得数据库的查询操作编程了对对象的操作，是一个纯面向对象的过程

![image-20201018132310467](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20201018132310467.png)



## Mybatis初始化

Mybatis初始化时候主要进行了以下工作：

1. 根据配置文件的位置，获取他的输入流
2. 从配置文件的根节点开始逐层的解析配置文件与映射文件，解析结果放入`Condigruation`对象
3. 以配置好的Configuration对象为参数获取一个`SQLSessionFactory`对象

## Mybatis读写阶段

###  获得SQLSession

数据库操作过程中需要一个SQLSession对象

> SqlSession session = sqlSessionFacory.openSession();

### mapper.class和mapper.xml的绑定

查找当前映射接口中的抽象方法的数据库操作节点，根据该节点生成接口的实现

### 映射接口的代理

返回一个mapperProxyFactory的对象

是一个基于反射的动态代理的对象，在execute方法中按照不同数据库操作类型调用不同的处理方法

### SQL语句的查找

BoundSql是经过层层转化后去除掉 if、where等标签的 SQL语句，而 CacheKey是为该次查询操作计算出来的缓存键。

如果命中缓存，就从缓存中获取数据结果，否抓就掉头query方法

### 数据库查询

关键流程：

+ 先查询缓存，如果需要查询数据库，那么查询的结果也放入缓存中
+ SQL语句经过层层转换，经过了`MappedStatement `对象、`Statement`对象和` PreparedStatement`对象，最后才得以执行。
+ 数据库查询得到的结果交给 `ResultHandler`对象处理。

### 结果处理集

接受结果的接口方法是`handleResultSets`

调用链路：

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20201019121443906.png" alt="image-20201019121443906" style="zoom: 67%;" />

+ createResultObject（ResultSetWrapper，ResultMap，List＜Class＜？＞＞，List＜Object＞，String）方法：该方法创建了输出结果对象。在示例中，为 User对象。·
+ applyAutomaticMappings 方法：在自动属性映射功能开启的情况下，该方法将数据记录的值赋给输出结果对象。
+ applyPropertyMappings方法：该方法按照用户的映射设置，给输出结果对象的属性赋值。

## Mybatis中包的解析

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20201019121702211.png" alt="image-20201019121702211" style="zoom:67%;" />

源码阅读顺序：

外围源码（基础功能包）> 配置解析包 > 核心源码（核心操作包）

## 基础功能包

+ 异常包（exception）
+ 反射包
+ 

