# 基本概念合集

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

spring提供两种方式实现事务类型：

+ 声明式(基于AOP)
+ 编程式

当需要用到事务操作的地方很少的时候，那么就可以使用编程方式` TransactionTemplate`，它不会建立很多事务代理。但是，如果程序中用到大量的事务操作，声明式事务方式更适合，它使得事务管理和业务逻辑分离。

声明式事务管理只需要用到`@Transactional` 注解和`@EnableTransactionManagement`。它是基于 Spring AOP 实现的，并且通过注解实现，实现起来简单，对原有代码没有入侵性。并需要注解的方法为Public。

### 解释AOP模块

个人认为，spring aop就是用代理类来包裹切面，把他们织入到Spring管理的bean中，也就是说

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

| 注解              | 用法                                                         |
| ----------------- | ------------------------------------------------------------ |
| `@Before`         | 前置通知，在连接点方法前调用                                 |
| `@Around`         | 环绕通知，它将覆盖原有方法，但是允许你通过反射调用原有方法，后面会讲 |
| `@After`          | 最终通知，在连接点方法后调用                                 |
| `@AfterReturning` | 后置通知，在连接点方法执行并正常返回后调用，要求连接点方法在执行过程中没有发生异常 |
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
### 对AOP织入的理解

把**目标对象（target）**使用**切面应用（advice）**包裹起来创建新的代理对象的过程，spring采用的是运行时

+ 编译时：当一个类文件被编译时进行织入，这需要特殊的编译器才可以做的到，例如AspectJ的织入编译器；

+ 运行时动态代理 ：spring会自动搜索其实现接口并织入advice
+ 使用cglib动态修改类：cglib可以实现字节码级地修改,执行效率比jdk动态代理要高,但创建实例时没有前者快

#### 织入的顺序

+ 在进入连接点，高优先级的将被先织入，离开连接点时后织入
+ 当不同的切面里的两个增强处理需要在同一个连接点被织入时，Spring AOP将以随机的顺序来织入这两个增强处理
+ 如果应用需要指定不同切面类里增强处理的优先级，Spring提供了如下两种解决方案：
  +  让切面类实现org.springframework.core.Ordered接口，实现该接口只需实现一个**int getOrder( )**方法，该方法返回值越小，则优先级越高。
  + 使用注解@Order(value=int)注解一个aspect类，这样属性越小，优先级越高

### AOP，拦截器，过滤器的区别

三者功能类似，但各有优势，从过滤器--》拦截器--》切面，拦截规则越来越细致

执行顺序依次是过滤器、拦截器、切面。一般情况下数据被过滤的时机越早对服务的性能影响越小，因此我们在编写相对比较公用的代码时，优先考虑过滤器，然后是拦截器，最后是aop。

比如权限校验，一般情况下，所有的请求都需要做登陆校验，此时就应该使用过滤器在最顶层做校验；日志记录，一般日志只会针对部分逻辑做日志记录，而且牵扯到业务逻辑完成前后的日志记录，因此使用过滤器不能细致地划分模块，此时应该考虑拦截器，然而拦截器也是依据URL做规则匹配，因此相对来说不够细致，因此我们会考虑到使用AOP实现，AOP可以针对代码的方法级别做拦截，很适合日志功能。

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

### 简述jdbcTemplate

是为了jdbc更加易于使用，在jdbc上定义了一个抽象层，对jdbc的API进行了简单的封装，以此建立的一个jdbc存取框架。

### 什么是切面Aspect

是AOP中的一种思想，由pointcount和advice组成，包含了横切逻辑和连接点的定义，使用@Aspect标注的类就是Aspect类

### 简述SpringMVC的流程

<img src="https://img2018.cnblogs.com/blog/1121080/201905/1121080-20190509202147059-745656946.jpg" alt="img" style="zoom: 67%;" />

1. 用户向服务端发送一次请求，这个请求会先到前端控制器DispatcherServlet(也叫中央控制器)。
2. DispatcherServlet接收到请求后会调用HandlerMapping处理器映射器。由此得知，该请求该由哪个Controller来处理（并未调用Controller，只是得知）
3. DispatcherServlet调用HandlerAdapter处理器适配器，告诉处理器适配器应该要去执行哪个Controller
4. HandlerAdapter处理器适配器去执行Controller并得到ModelAndView(数据和视图)，并层层返回给DispatcherServlet
5. DispatcherServlet将ModelAndView交给ViewReslover视图解析器解析，然后返回真正的视图。
6. DispatcherServlet将模型数据填充到视图中
7. DispatcherServlet将结果响应给用户

#### 组件说明

- DispatcherServlet：前端控制器，也称为中央控制器，它是整个请求响应的控制中心，组件的调用由它统一调度。
- HandlerMapping：处理器映射器，它根据用户访问的 URL 映射到对应的后端处理器 Handler。也就是说它知道处理用户请求的后端处理器，但是它并不执行后端处理器，而是将处理器告诉给中央处理器。
- HandlerAdapter：处理器适配器，它调用后端处理器中的方法，返回逻辑视图 ModelAndView 对象。
- ViewResolver：视图解析器，将 ModelAndView 逻辑视图解析为具体的视图（如 JSP）。
- Handler：后端处理器，对用户具体请求进行处理，也就是我们编写的 Controller 类。

### MVC设置转发与重定向

1. 在返回值的前面加”forward”,就可以实现让结果转发；
2. 在返回值的前面加上”redirect”，就可以让返回值重定向。

### spring配置文件

+ 在resource中写application.xml文件
+ 写config配置类，使用`@Configuration`注解说明这是一个配置类
+ 创建一个配置类，在配置类上添加 `@ComponentScan` 注解。该注解默认会扫描该类所在的包下所有的配置类

### @RequestMapping 注解的作用

@RequestMapping 是一个注解，用来标识 http 请求地址与 Controller 类的方法之间的映射。

+ 标注类上的作用：用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。
+ 标注在方法上的作用：拟合url和方法之间的映射

#### @RequestMapping属性

+ value: 请求的URL的路径（defult：匹配所有的url）
+ path: 和value一样
+ method: 请求的方法
+ consumes: 允许的媒体类型，也就是Content-Type
+ produces: 相应的媒体类型，也就是Accept
+ params: 请求参数
+ headers: 请求头部

# 应用场景面试题集

### Spring配置Bean实例化的方式

+ xml配置使用
+ 静态工厂方法：XML配置和factory类，使用静态工厂方法实例化
+ 实例工厂实例化
+ 使用setter方式：这种方式，只要写上对应的set、get方法，然后再bean.xml文件中利用property注入值即可。

### SpringBoot配置Bean实例化的方式

+ @ComponentScan() ：`ComponentScan`做的事情就是告诉`Spring`从哪里找到`bean`
+ 继承ImportSelector类，实现selectImports方法

### Bean注入属性哪几种方式

+ 属性注入：@Value
+ 构造方法注入：这里不需要添加@Autowired注解,也不需要在添加@Bean注解,在要使用数据源的类,使用他的构造方法进行注入
+ bean方法的形参注入：在方法上的形参上进行定义要注入的数据源,方法对数据源初始化处理后,通过bean注解将方法的返回值注入到容器中
+ 从配置文件注入：

### 简述几个设计模式在Spring中的使用

#### 简单工厂模式（不属于23种GOF设计模式）

BeanFactory：由一个工厂类根据传入的参数，动态决定应该创建哪一个产品类。

#### 工厂方法模式

通常由应用程序直接使用new创建新的对象，为了将对象的创建和使用相分离，***采用工厂模式,即应用程序将对象的创建及初始化职责交给工厂对象。\***

FactoryBean接口

实现了FactoryBean接口的bean是一类叫做factory的bean。其特点是，spring会在使用getBean()调用获得该bean时，会自动调用该bean的getObject()方法，所以返回的不是factory这个bean，而是这个bean.getOjbect()方法的返回值

#### 单例模式

Spring下默认的bean均为singleton，可以通过singleton=“true|false” 或者 scope=“？”来指定

Spring的依赖注入（包括lazy-init方式）都是发生在AbstractBeanFactory的getBean里。getBean的doGetBean方法调用getSingleton进行bean的创建。

MVC的处理器：

- DispatcherServlet：前端控制器，也称为中央控制器，它是整个请求响应的控制中心，组件的调用由它统一调度。
- HandlerMapping：处理器映射器，它根据用户访问的 URL 映射到对应的后端处理器 Handler。也就是说它知道处理用户请求的后端处理器，但是它并不执行后端处理器，而是将处理器告诉给中央处理器。
- HandlerAdapter（不是单例模式）：处理器适配器，它调用后端处理器中的方法，返回逻辑视图 ModelAndView 对象。
- ViewResolver：视图解析器，将 ModelAndView 逻辑视图解析为具体的视图（如 JSP）。
- Handler：后端处理器，对用户具体请求进行处理，也就是我们编写的 Controller 类。

#### 适配器模式

上面说的`HandlerAdapter`：处理器适配器，它调用后端处理器中的方法，根据Handler的规则不同，返回不同的逻辑视图 ModelAndView 对象。

#### 装饰器模式

Spring中用到的包装器模式在类名上有两种表现：一种是类名中含有Wrapper（包装），另一种是类名中含有Decorator（装潢）。

为了动态的给一个对象添加一些额外的职责（像AOP编程，但aop的实现是动态代理）

#### 代理模式

AOP底层，就是动态代理模式，

切面在运行时织入，AOP容器为目标创建一个代理对象，就是为了给之前的核心业务包一层。

从结构上来看和Decorator模式类似，但Proxy是控制，更像是一种对功能的限制，而Decorator是增加职责。 

#### 观察者模式

spring事件驱动型模型

事件机制的实现需要三个部分,事件源,事件,事件监听器

ApplicationEvent抽象类[事件]

继承自jdk的EventObject,所有的事件都需要继承ApplicationEvent,并且通过构造器参数source得到事件源.

该类的实现类ApplicationContextEvent表示ApplicaitonContext的容器事件.

#### 策略模式

定义一系列的算法，把它们一个个封装起来，并且使它们可相互替换。使得算法可独立于使用它的客户而变化。

具体就是实例化bean是按需要选择实例方法

#### 模板方法模式

其实就是继承（大雾）

父类定义了骨架（调用哪些方法及顺序），某些特定方法由子类实现。

最大的好处：代码复用，减少重复代码。除了子类要实现的特定方法，其他方法及方法调用顺序都在父类中预先写好了。

所以父类模板方法中有两类方法：

共同的方法：所有子类都会用到的代码

不同的方法：子类要覆盖的方法，分为两种：

- 抽象方法：父类中的是抽象方法，子类必须覆盖
- 钩子方法：父类中是一个空方法，子类继承了默认也是空的

注：为什么叫钩子，子类可以通过这个钩子（方法），控制父类，因为这个钩子实际是父类的方法（空方法）！

#### 建造者模式

某些命名为XXXbuilder的来

### 依赖注入的三种方式以及优缺点

在Spring 3.x 中，Spring团队建议我们使用setter来注入：

而在Spring 4.x 中，Spring团队不再建议我们使用setter来注入，改为了constructor：



1. 强制性的依赖性或者当目标不可变时，使用构造函数注入（**应该说尽量都使用构造器来注入**）
2. 可选或多变的依赖使用setter注入（**建议可以使用构造器结合setter的方式来注入**）
3. 在大多数的情况下避免field域注入（**感觉大多数同学可能会有异议，毕竟这个方式写起来非常简便，但是它的弊端确实远大于这些优点**）

#### 构造方法注入

**优点：**

- 在构造方法中体现出对其他类的依赖，一眼就能看出这个类需要其他那些类才能工作。
- 脱离了IOC框架，这个类仍然可以工作，POJO的概念。
- 一旦对象初始化成功了，这个对象的状态肯定是正确的。

**缺点：**

- 构造函数会有很多参数（Bad smell）。
- 有些类是需要默认构造函数的，比如MVC框架的Controller类，一旦使用构造函数注入，就无法使用默认构造函数。
- 这个类里面的有些方法并不需要用到这些依赖（Bad smell）。

#### set方法注入

**优点：**

- 在对象的整个生命周期内，可以随时动态的改变依赖。
- 非常灵活。

**缺点：**

- 对象在创建后，被设置依赖对象之前这段时间状态是不对的。
- 不直观，无法清晰地表示哪些属性是必须的

#### 方法参数注入

方法参数注入的意思是在创建对象后，通过自动调用某个方法来注入依赖。

**优点：**

- 比较灵活。

**缺点：**

- 新加入依赖时会破坏原有的方法签名，如果这个方法已经被其他很多模块用到就很麻烦。
- 与构造方法注入一样，会有很多参数。

### 定义类的作用域

它可以通过bean 定义中的 scope 属性来定义。

当 Spring 要在需要的时候每次生产一个新的 bean 实例，bean 的 scope 属性被指定为 prototype（原型）。另一方面，一个 bean每次使用的时候必须返回同一个实例，这个 bean 的 scope 属性 必须设为singleton。

默认bean生成的是单例

### Bean的作用域

作用域限定了Spring Bean的作用范围

默认(defult)时bean生成的是单例

| Scope       | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| singleton   | （默认的）在每个`Spring IoC`容器中，一个`bean`定义对应只会有唯一的一个`bean`实例。 |
| prototype   | 一个`bean`定义可以有多个`bean`实例。                         |
| request     | 一个`bean`定义对应于单个`HTTP` 请求的生命周期。也就是说，每个`HTTP` 请求都有一个`bean`实例，且该实例仅在这个`HTTP` 请求的生命周期里有效。该作用域仅适用于`WebApplicationContext`环境。 |
| session     | 一个`bean` 定义对应于单个`HTTP Session` 的生命周期，也就是说，每个`HTTP Session` 都有一个`bean`实例，且该实例仅在这个`HTTP Session` 的生命周期里有效。该作用域仅适用于`WebApplicationContext`环境。 |
| application | 一个`bean` 定义对应于单个`ServletContext` 的生命周期。该作用域仅适用于`WebApplicationContext`环境。 |
| websocket   | 一个`bean` 定义对应于单个`websocket` 的生命周期。该作用域仅适用于`WebApplicationContext`环境。 |

### 在Spring中注入一个JAVA集合

+ **<list>**类型用于注入一列值，允许有相同的值。
+ **<set>**类型用于注入一组值，不允许有相同的值。
+ **<map>**类型用于注入一组键值对，键和值都可以为任意类型。
+ **<props>**类型用于注入一组键值对，键和值都只能为 String 类型。

### SpringMvc中如何使用拦截器

拦截器的主要作用是拦截用户请求并进行相应的处理,比如可以用于权限验证

使用方法：

1. 定义一个Interceptor实现类
2. 实现HandlerInterceptor接口/实现WebRequestInterceptor接口

+ handler方法是链式调用的

```java
public class SpringMVCInterceptor implements HandlerInterceptor { 
    
    //在请求前调用
    @Override  
    public boolean preHandle(HttpServletRequest request,  
            HttpServletResponse response, Object handler) throws Exception {  
        // TODO Auto-generated method stub  
        return false;  
    }  

    //在请求后调用，并需要签字拦截器return true
        @Override  
    public void postHandle(HttpServletRequest request,  
            HttpServletResponse response, Object handler,  
            ModelAndView modelAndView) throws Exception {  
        // TODO Auto-generated method stub  
          
    }  
    
        /** 
     * 该方法也是需要当前对应的Interceptor的preHandle方法的返回值为true时才会执行。该方法将在整个请求完成之后，也就是DispatcherServlet渲染了视图执行， 
     * 这个方法的主要作用是用于清理资源的，当然这个方法也只能在当前这个Interceptor的preHandle方法的返回值为true时才会执行。 
     */  
    @Override  
    public void afterCompletion(HttpServletRequest request,  
            HttpServletResponse response, Object handler, Exception ex)  
    throws Exception {  
        // TODO Auto-generated method stub  
          
    }  

}
```

```java
public class AllInterceptor implements WebRequestInterceptor {  
      
    /** 
     * 在请求处理之前执行，该方法主要是用于准备资源数据的，然后可以把它们当做请求属性放到WebRequest中 
     */  
    @Override  
    public void preHandle(WebRequest request) throws Exception {  
        // TODO Auto-generated method stub  
        System.out.println("AllInterceptor...............................");  
        request.setAttribute("request", "request", WebRequest.SCOPE_REQUEST);//这个是放到request范围内的，所以只能在当前请求中的request中获取到  
        request.setAttribute("session", "session", WebRequest.SCOPE_SESSION);//这个是放到session范围内的，如果环境允许的话它只能在局部的隔离的会话中访问，否则就是在普通的当前会话中可以访问  
        request.setAttribute("globalSession", "globalSession", WebRequest.SCOPE_GLOBAL_SESSION);//如果环境允许的话，它能在全局共享的会话中访问，否则就是在普通的当前会话中访问  
    }  
  
    /** 
     * 该方法将在Controller执行之后，返回视图之前执行，ModelMap表示请求Controller处理之后返回的Model对象，所以可以在 
     * 这个方法中修改ModelMap的属性，从而达到改变返回的模型的效果。 
     */  
    @Override  
    public void postHandle(WebRequest request, ModelMap map) throws Exception {  
        // TODO Auto-generated method stub  
        for (String key:map.keySet())  
            System.out.println(key + "-------------------------");;  
        map.put("name3", "value3");  
        map.put("name1", "name1");  
    }  
  
    /** 
     * 该方法将在整个请求完成之后，也就是说在视图渲染之后进行调用，主要用于进行一些资源的释放 
     */  
    @Override  
    public void afterCompletion(WebRequest request, Exception exception)  
    throws Exception {  
        // TODO Auto-generated method stub  
        System.out.println(exception + "-=-=--=--=-=-=-=-=-=-=-=-==-=--=-=-=-=");  
    }  
      
}  
```

### SpringMVC中常见注解

>**@RequestMapping**
>
>上面有说
>
>```java
>@RestController
>public class HelloController {
>@RequestMapping(value="/hello",method= RequestMethod.GET)
>public String sayHello(@RequestParam("id") Integer id){
>return "id:"+id;
>}
>}
>```
>
>在浏览器中输入地址： localhost:8080/hello?id=1000 ，可以看到如下的结果： 
>
>id:1000

>**@RequestParam**
>
>用于方法参数的前面，是接收普通参数的注解，当@GetMapping带参数时，获取其参数，可以在required=false设置没参数时候也不会报错，也可以设置默认值

>**@GetMapping**
>
>组合注解，是 @RequestMapping(method = RequestMethod.GET) 的缩写，同样的还有@PostMapping

>**@PathVaribale** 获取url中的数据
>
>```java
>@RestController
>public class HelloController {
>@RequestMapping(value="/hello/{id}/{name}",method= RequestMethod.GET)
>public String sayHello(@PathVariable("id") Integer id,@PathVariable("name") String name){
>return "id:"+id+" name:"+name;
>}
>}
>```
>
>在浏览器中输入地址： localhost：8080/hello/100/helloworld 然后会在html页面上打印出：
>
>id：100 name：helloworld

### Spring框架事务管理的优点

在以往的JDBCTemplate中事务提交成功，异常处理都是通过Try/Catch 来完成，而在Spring中。Spring容器集成了TransactionTemplate，封装了所有对事务处理的功能，包括异常时事务回滚，操作成功时数据提交等复杂业务功能。这都是由Spring容器来管理，大大减少了程序员的代码量，也对事务有了很好的管理控制。Hibernate中也有对事务的管理，hibernate中事务管理是通过SessionFactory创建和维护Session来完成。而Spring对 SessionFactory配置也进行了整合，不需要在通过hibernate.cfg.xml来对SessionaFactory进行设定。 
　　这样的话就可以很好的利用Sping对事务管理强大功能。避免了每次对数据操作都要现获得Session实例来启动事务/提交/回滚事务还有繁琐的Try /Catch操作。这些也就是Spring中的AOP（面向切面编程）机制很好的应用。一方面使开发业务逻辑更清晰、专业分工更加容易进行。另一方面就是应用Spirng AOP隔离降低了程序的耦合性使我们可以在不同的应用中将各个切面结合起来使用大大提高了代码重用度。