## 基本概念合集

### Spring框架是哪几部分组成

七个组件，可以单独存在，也可以几个组件联合使用

+ spring core：提供spring的基本功能，核心组件是`BeanFactory`，通过控制反转把应用程序的配置和依赖性规范和实际的应用代码分开

+ spring AOP：通过配置管理特性，aop直接把面向切面的编程功能集成到了spring框架中
+ spring orm：（描述对象与关系映射的细节）：集合了一些orm框架，提供了对象关系工具
+ spring DAO： dao层提供有意义的异常层次结构，简化了异常的错误处理，降低了编写异常代码的数量
+ spring context：上下文是一个配置文件，可以向spring提供上下文信息
+ spring web：webContext服务基于context构建了一个为web应用服务提供上下文的服务，web模块还简化了处理多部分请求和把请求参数编订到域对象的工作
+ spring MVC：mvc框架是一个全功能的构建web应用程序的mvc实现，通过策略接口，mvc成为高度可配置的。

![img](https://img.jbzj.com/file_images/article/202006/2020613102742838.png?2020513102756)

### 谈谈对spring IoC的理解

+ 谁控制谁？IOC容器控制对象
+ 控制什么？控制对象
+ 何为反转？依赖的对象自己由Ioc容器初级后注入到被注入的对象中，依赖的对象由本来的主动获取变成被动接受
+ 反转哪些方面？获得依赖对象的方式反转了

IoC 全称为 `Inversion of Control`，翻译为 “控制反转”，通过DI（依赖注入）实现控制反转。

所谓IOC，就是有Spring ioc容器来负责对象生命周期与对象之间的关系

没有引入 IoC 的时候，被注入的对象直接依赖于被依赖的对象，有了 IoC 后，两者及其他们的关系都是通过 Ioc Service Provider 来统一管理维护的。

可以说创建对象的控制控制权进行转移，一起创建对象的主动权和控制权实际由自己把控的，现在把这个权利转移到第三分（ioc）

### 谈谈对Spring DI的理解

+ 谁依赖谁？应用程序依赖IOC容器
+ 为什么需要依赖？应用需要Ioc来提供对象的外部资源
+ 谁注入谁？IOC容器把资源注入到了应用程序中的某些个对象（比如日志功能，过滤器……）
+ 注入了什么？注入某个对象所需要的外部资源

DI `Dependency Injection`依赖注入：组件之间的依赖关系在容器之间的运行期决定，（在java中通过反射的方式实现），容器动态的把每个依赖关系注入到每个组件之中，依赖注入的摸底并非为系统带来更多的功能，而是为了提升组件的重用的频率，变为系统搭建一个灵活可扩充的平台。

我们不需要关心具体的资源来自何处，由谁实现。

依赖注入明确描述了——`被注入对象`依赖`Ioc容器`配置`依赖对象`

### BeanFactory和ApplicationContext接口的不同点

`BeanFactory`是Spring里面的最底层的接口，提供了最简单的容器功能，只提供了实例化对象和拿对象的功能

+ 装载bean时候不会实例化beam资源从容器中拿bean中的时候才会去实例化，
+ 启动速度快，对于资源加载要求高的应用有优势

`ApplicationContext`的对`BeanFactory`的进一步实现。是一个更高级的容器，提供了更多有用的功能（国际化、访问资源载入、多个有继承关系的上下文（让每一个上下文都专注于一个特定的层次）、响应机制AOP（面向切面编程）

+ 在启动时候已经把所有bean都实例化了，还可以把Bean配置lazy-init来让bean延迟初始化
+ 系统运行时速度快
+ 在启动时发现系统中的配置问题，而不是在运行到bean时出错程序崩溃。
+ 事件机制是通过观察者事件模式实现的：
  + **ApplicationEvent**：容器事件，必须由ApplicationContext发布；
  + **ApplicationListener**：监听器，可由容器中的任何监听器Bean担任。
+ 如果要获取的对象依赖了另一个对象，那么其首先会创建当前对象，然后通过递归的调用ApplicationContext.getBean()方法来获取所依赖的对象，最后将获取到的对象注入到当前对象中。

### Spring 核心类，并说明有什么作用

+ `ApplicationListener`：设计模式是观察者模式

  应用程序可以定义观察者，作为bean注册到容器中，实现`ApplicationListener<ApplicationEvent>`接口中的方法`onApplicationEvent(ApplicationEvent arg0)`

+ `BeanFactory`基础容器组件，产生实例
+ `AppricationContext`提供框架的实现，包括`BeanFactory`的所有功能
+ `BeanWrapper`提供统一的get及set方法

### Spring中容器中事件机制与原理（未补全）

1. 容器刷新事件发布到监听器处理事件：

### 谈谈对Spring事务的理解

事务就是ACID（原子性，一致性，隔离性，持久性）

Spring在进行事务操作的时候，可用一个叫做`PlatformTransactionManager`的接口，表示事务管理器，就正则管理事务的对象

Spring并不直接管理事务，鹅绒通过这个接口，为各个组件提供了对应的事务管理器，也就是把事务的职责委托给其他管理持久化机制的对应平台了事务的实现。

### Spring实现事务的方式

spring提供两种方式实现事务：

+ 声明式(基于AOP)
+ 编程式

当需要用到事务操作的地方很少的时候，那么就可以使用编程方式` TransactionTemplate`，它不会建立很多事务代理。但是，如果程序中用到大量的事务操作，声明式事务方式更适合，它使得事务管理和业务逻辑分离。

声明式事务管理只需要用到`@Transactional` 注解和`@EnableTransactionManagement`。它是基于 Spring AOP 实现的，并且通过注解实现，实现起来简单，对原有代码没有入侵性。并需要注解的方法为Public。

### 解释AOP模块

AOP是Spring最为重要的功能之一了，是数据库事务中被广泛使用

AOP思想把业务分成**核心功能**和**周边功能**，周边功能在AOP中被定义为切面。

在面向切面编程AOP的思想里面，核心业务功能和切面功能分别独立进行开发，然后把切面功能和核心业务功能 "编织" 在一起，这就叫AOP。

可以把切面功能交给代理（Proxy）来做，程序自用负责核心业务就行

- 切入点（Pointcut）
  在哪些类，哪些方法上切入（**where**）
- 通知（Advice）
  在方法执行的什么实际（**when:**方法前/方法后/方法前后）做什么（**what:**增强的功能）
- 切面（Aspect）
  切面 = 切入点 + 通知，通俗点就是：**在什么时机，什么地方，做什么增强！**
- 织入（Weaving）
  把切面加入到对象，并创建出代理对象的过程。（由 Spring 来完成）

#### 使用方法

给实现切面的类加上`@Aspect`但仍然是一个bean组件，需要添加`@Componment`来加入容器

然后在功能上按应该在核心前后加上`@Before`和`@After`……

| `@Before`         | 前置通知，在连接点方法前调用                                 |
| ----------------- | ------------------------------------------------------------ |
| `@Around`         | 环绕通知，它将覆盖原有方法，但是允许你通过反射调用原有方法，后面会讲 |
| `@After`          | 后置通知，在连接点方法后调用                                 |
| `@AfterReturning` | 返回通知，在连接点方法执行并正常返回后调用，要求连接点方法在执行过程中没有发生异常 |
| `@AfterThrowing`  | 异常通知，当连接点方法异常时调用                             |

### Spring中的通知类型与使用场景

1. 前置通知：

   在目标方法执行之前执行的通知

   可以没有参数，也可以额外接受一个`JoinPoint`，代表了当前的连接点，可以通过这个对象**获取目标对象**和**目标方法相关信息**

   使用场景：记录日志（方法调用之前）

2. 环绕通知：

   在目标执行方法之前和之后都可以执行的额外通知，在环绕通知中必须形式调用目标的方法，否则方法不会执行

   使用场景：控制事务，权限控制

3. 后置通知：

   在方法执行之后的通知，同前置通知

   使用场景：记录日志（方法调用之后）

4. 异常通知：

   当目标出现异常时执行的通知

   使用场景：异常处理，控制事务

5. 最终通知：

   是在目标方法执行之后的通知，有点像finally语句块一样

   使用场景：记录日志（方法调用之后，方法不需要执行成功）

   

### 对Spring Bean的理解

Bean是存放于Spring IOC容器中的对象，所以称为Bean，在是spring中被IOC容器实例化并管理的java类都可以称之为bean。

   #### spring中创建bean的方式

   1. 使用构造器创建
      2. 使用静态工厂方法创建
      3. 调用实例方法创建

### spring解决循环依赖

Spring在实例化一个bean时，首先递归的实例化其依赖的bean，直到一个bean没有依赖其他的bean或是bean已经存在，此时把这个实例返回，然后把获取到的bean设置为之前各个bean的属性

- Spring是通过递归的方式获取目标bean及其所依赖的bean的；
- Spring实例化一个bean的时候，是分两步进行的，首先实例化目标bean，然后为其注入属性。

构造器的依赖没法解决，只能抛出异常

spring解决循环依赖的解决属性的循环依赖

1. a类初始化需要b，b初始化需要a，a进行初始化`getBean(a.class);`
2. a就去容器中找b，`getBean(b.class);`，没找到，容器调用b的构造器
3. b需要a，然后去容器中找a，`getBean(a.class);`，此时a只是一个半成品对象（内部没有属性），容器把这个对象返回给b，完成b的构建。

**Q: Spring是如何标记开始生成的A对象是一个半成品，并且是如何保存A对象的。**

A: 这里的标记工作Spring是使用`ApplicationContext`的属性`SetsingletonsCurrentlyInCreation`来保存的，而半成品的A对象则是通过`MapsingletonFactories`来保存的

```java
protected  T doGetBean(final String name, @Nullable final Class requiredType,
    @Nullable final Object[] args, boolean typeCheckOnly) throws BeansException {
  
  // 尝试通过bean名称获取目标bean对象，比如这里的A对象
  Object sharedInstance = getSingleton(beanName);
  // 我们这里的目标对象都是单例的
  if (mbd.isSingleton()) {
    
    // 这里就尝试创建目标对象，第二个参数传的就是一个ObjectFactory类型的对象，这里是使用Java8的lamada
    // 表达式书写的，只要上面的getSingleton()方法返回值为空，则会调用这里的getSingleton()方法来创建
    // 目标对象
    sharedInstance = getSingleton(beanName, () -> {
      try {
        // 尝试创建目标对象
        return createBean(beanName, mbd, args);
      } catch (BeansException ex) {
        throw ex;
      }
    });
  }
  return (T) bean;
}
```

第一个步骤的`getSingleton()`方法的作用是尝试从缓存中获取目标对象，如果没有获取到，则尝试获取半成品的目标对象；如果第一个步骤没有获取到目标对象的实例，那么就进入第二个步骤

第二个步骤的`getSingleton()`方法的作用是尝试创建目标对象，并且为该对象注入其所依赖的属性。

**Spring通过将实例化后的对象提前暴露（就是半成品）给Spring容器中的`singletonFactories`，解决了循环依赖的问题**。

### 对BeanFactory的理解与其实现类

BeanFactory负责配置、创建、管理Bean，它有一个子接口ApplicationContext，也被称为Spring上下文，容器同时还管理着Bean和Bean之间的依赖关系。

其最主要的方法就是`getBean(String beanName)`

真正可用的类是**DefaultListableBeanFactory**

### 对springWeb模块的理解

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20201021231606822.png" alt="image-20201021231606822" style="zoom: 50%;" />

是spring框架处理web请求的基础，是核心web功能的集成，比如IoC容器的初始化、Web应用上下文（context）的初始化、多部（multipart）文件上传功能、HTTP客户端、Servlet 过滤器、远程调用、用于集成其它 web 框架及技术（如Hessian，Burlap）的基础结构。

提供了@RestController、@ResponseBody等注解

### Bean装配与自动装配

Spring提供了三种装配bean的方式：

+ 自动装配bean
+ 通过java代码装配bean
+ 通过xml装配bean（过于繁琐）

#### 自动装配的步骤

+ 组件扫描（component scanning）：Spring会扫描@Component注解的类，并会在应用上下文中为这个类创建一个bean。
+ 自动装配（autowiring）：Spring自动满足bean之间的依赖。

#### 使用的注解

- @Component：表明这个类作为组件类，并告知Spring要为这个类创建bean。默认bean的id为第一个字母为小写类名，可以用value指定bean的id。
- @Configuration：代表这个类是配置类。
- @ComponentScan：启动组件扫描，默认会扫描所在包以及包下所有子包中带有@Component注解的类，并会在Spring容器中为其创建一个bean。可以用basePackages属性指定包。
- @RunWith(SpringJUnit4ClassRunner.class)：以便在测试开始时，自动创建Spring应用上下文。
- @ContextConfiguration：告诉在哪加载配置。
- @Autowired：将一个类的依赖bean装配进来。

#### 自动装配的限制

如果配备到多个bean，装配将失败，可以使用**@Qualifier**注解来指明注入的实例

#### 代码装配使用的注解

- @Bean：默认情况下配置后bean的id和注解的方法名一样，可以通过name属性自定义id。
- @ImportResourse：将指定的XML配置加载进来
- @Import：将指定的Java配置加载进来。
- @Resource(name="className")：按类名把资源注入经理

### @Autowired的三种使用方式

1. 通过构造器注入
2. 通过setter方法注入
3. 通过field反射注入

#### 弊端

**如果你使用的是构造器注入**

恭喜你，当你有十几个甚至更多对象需要注入时，你的构造函数的`参数个数`可能会长到无法想像。

**如果你使用的是field反射注入**

如果不使用Spring框架，这个属性只能通过反射注入，太麻烦了！这根本不符合`JavaBean`规范。还有，当你不是用过`Spring`创建的对象时，还可能引起`NullPointerException`。并且，你不能用`final`修饰这个属性。

**如果你使用的是setter方法注入**

那么你将不能将属性设置为`final`。

#### 两者取其轻

+ Spring3.0官方文档建议使用setter注入覆盖构造器注入。
+ Spring4.0官方文档建议使用构造器注入。

#### 结论

+ 如果注入的属性是`必选`的属性，则通过构造器注入。
+ 如果注入的属性是`可选`的属性，则通过setter方法注入。
+ 至于field注入，不建议使用。

### 自动装配bean的方式

+ 按配置文件xml方式装配：在resources文件夹下新建spring.xml，注意：beans 以及后面的声明要加上，否则会报错。id对应的名称，待会获取用这个自定的名字，class则是注入的类的路径
+ 注解装配（常用）：直接在对应类名或者方法前加注解