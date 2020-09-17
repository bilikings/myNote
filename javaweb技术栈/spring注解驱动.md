# 注解驱动开发

## 容器

### 组件注册

#### AnnotationConfigApplicationContext

注解开发之前，使ClassPathXmlApplicationContext获取xml中的配置文件与信息

之后可以使用配置类，声明注解`@Configuration`，在使用中`AnnotationConfigApplicationContext`

```java
@Configuration
public class DemoConfig{
     pubili Person person(){
         @Bean //可以@Bean(“组件名字”) 按组件名字在容器中注入这个名字的组件
         retrun new Person("xxx",23);//属性
     }
}
```

```java
    public static void main(String[] args) {
		ApplicationContext appc = new AnnotationConfigApplicationContext(DemoConfig);
        Person person = appc.getBean(Person.class);
    }
```

#### 包扫描@ComponentScan

因为在持久层、业务层和控制层中，分别采用@Repository、@Service和@Controller对分层中的类进行凝视，而用@Component对那些比较中立的类进行凝视。

当注解作用在类上时，表明这些类是交给spring容器进行管理的，而当使用@Autowired和@Resource时，表明我需要某个属性、方法或字段，但是并不需要我自己容去new一个，只需要使用注解， spring容器会自动的将我需要的属性、方法或对象创造出来。这就是通常所说的依赖注入和控制反转。

以前的包扫描是配在了bean.xml中；

现在的只要标注了@Controller（控制）@Service（服务）@Repository（仓库）@Component（组件）都会扫描到之后注入容器

##### 包排除规则

```java
@ComponentScan(value = "package",excludeFileters={
    //excludeFileters =Filter[]表示不包括
    //includeFilters = Filter[]表示包括，使用这个时候还注意配置属性， useDefaultFilters = false
    @Filter(type =FilterType.ANNOTATION,classes={Conterller.class})//排除Conterller标注的类
    //FilterType.ANNOTATION 按照注解
    //FilterType.ASSIGNBLE_TYPE 按照给定的类型
    //FilterType.REGEX 按照正则表达式
    //FilterType.CUSTOM 按照自定义规则，先写一个TypeFilter的实现类
   
})
```

#### @Scope设置组件作用域

@Scope("prototype") 多实例的。ioc容器启动的时候并不会去调用方法去创建对象在容器中，每次获取的时候才好调用方法创建对象

@Scope("sigleton") 单实例的（默认值）：ioc容器启动时就把对象创建并放到了ioc容器中，以后就是每次获取直接从容器中（map.get()）中拿

request 同一个请求创建一个实例

session 同一次session创建一层实例

#### 懒加载 @Lazy

单实例bean：默认在容器启动的时候创建对象

懒加载：容器启动时候不创建对象，第一次使用的时候才创建对象

#### 按照条件注册bean @Conditional

【Conditional (附带条件的，依……而定的)】

放在类上，只有满足当前条件，这个类中配置的所有bean才能生效

放在方法上，需要配置判断类，实习condition借口，重写matches函数，返回ture时生效

#### @Import给容器中快速导入一个组件

@Import("vaule")，容器就会自动注册这个组件，id是默认全类名

ImportSelector 需要实现一个接口，在其中编写实现逻辑。@Import("importSelector.class")

### 生命周期

#### 指定初始化和销毁方法

可以在@Bean注入容器中的时候配置初始和销毁方法

```java
@Bean(initMethod="init",distroyMethod="destory")
```

`initMethod`在创建时候调用，`distroyMethod`在容器关闭时候调用，注销容器使用`容器名.close();`

销毁的时候：

+ 单实例：在容器关闭的时候
+ 多实例：容器不会管理这个bean

#### Bean实现接口

 **initializingBran**，定义初始化逻辑：

有一个方法`afterPropertiseSet()`，属性设置完成之后调用

**DisposableBean**，定义销毁逻辑：

有一个方法`destroy()`，在销毁时候调用

#### 可以使用JSR250

用注解标注在需要实现的方法之前

##### @PostConstruct，在bean初始化并赋值完成之后

##### @preDestroy，在bean即将被移除之前

#### BeanPostProcessor接口

这是bean的后置处理器，在bean初始化前后进行一些处理工作

**postProcessBeforeInitialization**，在初始化之前工作

**postProcessAfterInitialization**，在初始化调用之后

### 属性赋值

使用**@Value**赋值

+ 基本数值
+ 可以写SpEL：#{}
+ 可以写${}，取出配置文件中的值，**需要指定文件路径@PropertySource(value={"classpath:/...","..."})**

### 自动装配

Spring 利用依赖注入（DI），完成IOC容器中各个组件的依赖赋值关系

@Autowired：自动注入：

+ 优先按着类型去寻找对应的组件
+ 如果找到多个相同类型的组件，在讲属性的名称在去作为传去容器中查找
+ 使用@Qualifier("...")来指定需要装配的组件id，而不是使用属性名
+ 自动装配默认一定要把属性赋值好，没有就会报错
  + 可以使用@Autowired(required = false)，可以把属性赋值设为费必须的（找得到就装，找不到就拉倒）
+ @Primary，在spring自动装配的时候，指定默认使用首选的bean，**@Qualifier("...")会覆盖这个设置**

**Spring还支持@Resourse（JSR250）和@Inject（JSR330) [java规范的注解]**

#### @Autowired

@Autowired还可以标注在方法，属性，构造器，参数

#### 使用底层组件

自定义组件想要使用Spring容器底层的一些组件（ApplicationContext，BeanFactory,...)

Spring中有很多继承于`aware`中的接口，`Aware`接口也是为了能够感知到自身的一些属性。
 比如实现了`ApplicationContextAware`接口的类，能够获取到`ApplicationContext`，实现了`BeanFactoryAware`接口的类，能够获取到`BeanFactory`对象。

自定义组件实现xxxAAware，创建对象时候，会把Spring底层方法按接口规定注入自定义bean中，

xxxAware：功能使用xxxProcessor处理【processor:处理器，工人】

### AOP

**在程序运行期间把某段代码切入到指定方法指定位置的编程方式**

导入名为`spring-aspects`的依赖(aop模块)

通知方法：

1. 前置通知（@Before）

2. 后置通知（@After），无论方法是正常结束还是异常结束

3. 返回通知（@AfterReturning)

4. 异常通知（@AfterThrowing）

5. 环绕通知（@Around）

   ```java
   @Before("public int method()")//这里是方法的绝对路径
   public void before(){
       System.out.println("前置方法")
   }
   ```

   如果使用`@PointCut`设置切入点

   ```java
   /** 
   * 可以本类引用
   * 也可以其他切面引用
   */
   @PointCut("execution(public int pakege.*(..))")//*(..)表示匹配带2个参数的方法
   public viod pointCut(){}
   
   @Before("pointCut")//这里是方法的绝对路径
   public void before(){
       System.out.println("前置方法")
   }
   ```

如何使用：

1. 给切面类的目标方法标注何时何地运行

2. 把切面类和目标逻辑类都要加入容器中

3. 必须告诉spring哪个类是容器类（给切面类上加一个注解@Aspect，告诉spring这是一个切面类）

4. **在Aop配置类中添加@EnableAspectAutoProxy，【开启基于注解的aop模式】**
5. 然后不要自己创建对象，使用容器中创建的对象

获得参数：

JoinPoint一定要放在参数表的第一位，否则会出现错误

```java
public void test(JoinPoint jp){
    Object[] obj = jp.getArgs();//获取参数列表
    System.out.println(jp.getSingnature().getName());//打印方法签名
    System.out.println(Arrays.asList(obj));
}
```

接受返回值：

```java
@AfterReturning(Value="pointCut()",returning="res")//res接收返回值
public void test2(Object res){
     System.out.println(res);
}
```

接收异常：

```java
@AfterTrowing(Value="pointCut()",throwing="excp")//rexcp接收异常
public void test2(Object excp){
     System.out.println(res);
}
```

#### Aop原理

@EnableAspectAutoProxy

【Registrar：登记员】

```
@Import({AspectJAutoProxyRegistrar.class})//给容器中导入这个组件
```

这个类实现了ImportBeanDefinitionRegistrar接口，目的是自定义给容器中注册bean

主要关注后置处理器（在bean初始化完成前后做的事情），自动注入BeanFactory

**BeanFactory：**

BeanFactory在启动的时候不会去实例化Bean，中有从容器中拿Bean的时候才会去实例化；

**ApplicationContext：**

ApplicationContext在启动的时候就把所有的Bean全部实例化了。它还可以为Bean配置lazy-init=true来让Bean延迟实例化； 

#### 总结

1. 利用**@EnableAspectAutoProxy**开启Aop的功能，这个注解会给容器中注入一个组件，**AnnotationAwareAspectAtuoProxyCreator**，这是一个后置处理器
2. 容器的创建流程：
   + refresh（刷新容器），其中有一步使用**registerBeanPostProcessor**了注册后置处理器，这里创建**AnnotationAwareAspectAtuoProxyCreator**对象；
   + finishBeanFactoryInitiazation，初始化剩下的单实例bean
     + 创建业务逻辑组件和切面组件
     + **AnnotationAwareAspectAtuoProxyCreator**拦截组件创建过程
     + 组件创建完成之后，判断组件是否需要增强
       + 是：切面通知方法，包装成增强器（Advisor）；给业务逻辑一个代理对象
3. 执行目标方法：
   + 代理对象执行目标方法
   + 使用CglibAopProxy.intercept():
     + 得到目标的拦截器链，（增强器包装成拦截器MethodIntercepor）
     + 利用拦截器的链式集中，依次进入每一个拦截器进行执行
     + 效果：
       + 正常执行：前置通知-》目标方法-》后置通知-》返回通知
       + 异常执行：前置通知-》目标方法-》后置通知-》异常通知

### 声明式事务

1. 环境搭建：
   + 导入依赖：数据源，数据库，Spring-jdbc
2. 配置数据源，JDBCTemplate（一个spring提供的简化数据库操作的工具）操作数据
3. 





## 扩展原理

### BeanFactoryPostProcessor

### BeanDefinitionRegistruPostProcessor

### ApplicationLinsener

### Spring容器创建

## web

### servlet 3.0

### 异步请求