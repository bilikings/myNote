

# SpringBoot

## 核心注解

### spring相关

>**@Configuration**
>
>用于定义配置类，代替以前的xml文件

>**@ComponentScan**
>
>包扫描组件，

>**@Conditional**
>
>为根据代码中的不同条件来注册bean

>**@Import**
>
>把没有被spring容器管理的类添加到Spring容器中

>**@Component**
>
>带有这个注解的类被称为组件，为@Controller，@Service, @Respositoryu的共同注解（这三个注解也就是这个注解的特殊注解），

### SpringBoot相关

> **@SpringBootApplication**
>
> 用于springboot的主类上，标识这是一个springboot应用，是**@Configuration,@EnableAutoConfiguration,@ComponentScan**的共同注解。

>**@EnableAutoConfigration**
>
>允许springboot自动配置注解,尝试根据你添加的jar依赖自动配置你的Spring应用。

>**@SpringbootConfiguration**
>
>等价于@Configuration

>**@ConditionalOnBean**
>
>条件化创建Bean，@ConditionalOnBean（A.class)，只有上下文（applicationContext）存在A.class时，才注入这个bean

>**@ConditionalOnMissingBean**
>
>和上面那个相反

>**@ConditionalOnClass**
>
>顾名思义，只有在存在某个类时候才创建这个bean

> **@ConditionalOnMissingClass**
>
> 和上面那个相反

>**@ConditionalOnWebApplication**
>
>是web应用时候去配置

>**@ConditionalOnNotWebApplication**
>
>和上面那个相反

>**@ConditionalOnProperty**
>
>有指定属性的值才开启这个bean，其有两个属性name以及havingValue

>**@ConditionalOnExpression**
>
>满足SpEL表达式时创建

> **@ConfigurationProperties**
>
> 可以把自定义的配做文件映射到实体类的bean中，application.properties中修改那些基础属性映射到这个标注的类中

> **@EnableConfigurationProperties**
>
> 任何使用@ConfigurationProperties的beans都会被环境属性配置

>**@AutoConfigAfter/@AutoConfigBefore**
>
>这个配置需要在某个配置配置完成之后（前）进行配置

### 应用相关

>**@ResponseBody**
>
>这个方法返回的结果直接写入Http response body中，一般配合@RequestMapping一起使用

>**@RequestMapping**
>
>提供路由信息，负责URL到Controller之间具体函数之间的映射

>**@Autowired**
>
>自动导入依赖的bean

>**@Bean**
>
>往容器中注入bean

>**@Resource(name=”name”,type=”type”)**
>
>没有括号内内容的话，默认byName。与@Autowired干类似的事。

### MVC相关

>**@RequestMapping**
>
>上面有说
>
>```java
>@RestController
>public class HelloController {
> @RequestMapping(value="/hello",method= RequestMethod.GET)
> public String sayHello(@RequestParam("id") Integer id){
> return "id:"+id;
> }
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
> 组合注解，是 @RequestMapping(method = RequestMethod.GET) 的缩写，同样的还有@PostMapping

>**@PathVaribale** 获取url中的数据
>
>```java
>@RestController
>public class HelloController {
> @RequestMapping(value="/hello/{id}/{name}",method= RequestMethod.GET)
> public String sayHello(@PathVariable("id") Integer id,@PathVariable("name") String name){
> return "id:"+id+" name:"+name;
> }
>}
>```
>
>在浏览器中输入地址： localhost：8080/hello/100/helloworld 然后会在html页面上打印出：
>
>id：100 name：helloworld

## 配置

**从.properties里面获取value**

### ConfigurationProperties

ConfigurationProperties(prefix = "文件")

|      | @ConfigurationProperties | @Value |
| ---- | ---- | ---- |
| 功能 | 批量注入配置文件的属性 |一个个指定|
| 松散绑定 | 支持 |不支持|
| SpEL校验 | 不支持 |支持|
| JSR303数据校验 | 支持 |不支持|
| 复杂类型封装（Map等） | 支持 |不支持|

如果说我们只是在业务逻辑中获取一下配置文件中的值，就使用@value即可；

如果专门写了一个javabean来配置文件映射，拿使用ConfigurationProperties

### @PropertySource&@ImportResource

```java
@PropertySource（value={"classpath:xxx.properties"};//加载其他的properties
```

**@ImportResource**:导入Spring的配置文件，让配置文件的内容生效

SpringBoot里面没有Spring的配置文件，我们自己的配置文件也不能自动识别

需要把@ImportResource标注在一个配置类上

```java
@ImportResource(locations = {"classpath:beans.xml"})
```

**SpringBoot推荐使用全注解的方式来配置**

使用@Configuration指明当前类是一个配置类，来代替之前是Spring配置文件

```java
@Configuration
public class MYAppConfig{
    //把方法的返回值添加到容器中
    @Bean
    public HelloService heloService(){
        return new HelloService();
    }
}
```

### 配置文件占位符

RandomValuePropertySource 配置文件中可以使用随机数

### Profile

#### 多Profile文件

在主配置文件时候，文件名可以是application-{profile}.properties/yml

默认使用application.properties,

之后可以在application.propertie中设置`spring.profile.active = profile`的方式来切换

也可以通过命令行和jvm的方式切换。

#### yml支持多文档块

用`---`来分隔文档块

```yaml
server:
	port :8081
spring:
	proflies:
	#激活dev文本块
		actives:dev 
---
server:
	port :8082
spring:
	proflies:dev
	
---
server:
	port :8083
spring:
	proflies:prod
	

```

### 外部配置加载顺序

官方文档有17个，常用11个（按优先级如下），所有配置形成互补配置

1. **命令行参数**
2. 来自java:comp/env的JNDI属性
3. java系统属性（System,getProperties()）
4. 操作系统的环境变量
5. RandomValuePropertiesSource配置的random.*属性
6. **jar包外部的application-{profile}.properties或yml带spring.profire属性的配置文件**
7. **jar包内部的application-{profile}.properties或yml带spring.profire属性的配置文件**
8. **jar包外部的application-{profile}.properties或yml不带spring.profire属性的配置文件**
9. **jar包内部的application-{profile}.properties或yml不带spring.profire属性的配置文件**
10. @Configuration注解类上的@PropertySource
11. 通过SpringApplication.setDefaultProperties指定默认属性

### 自动配置原理

配置文件能配置的属性参照Spring文档

1. springBoot启动的时候加载主配置类，开启了自动配置功能`@EnableAutoConfiguration`

2. @EnableAutoConfiguration的作用：

   + 利用EnableAutoConfigurationImportSelection给容器导入一些组件

   + selectImports()的内容维护一个list（**把类路径下META-INF/spring.factories配置的所有的EnableAutoConfiguration添加到了容器中**）

     ```java
     List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.      getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
     //扫描所有jar包类路径下的 "META-INF/spring.factories"文件
     //把扫描到的这些文件包尘properties对象
     //然后从properties值获取到EnableAutoConfiguration.class的值，然后添加到容器中
     ```

   + 之后自动配置类进了容器中，就是自动配置的开始

3. 每一个自动配置类执行自动配置功能

   ```java
   @Configuration //表示这是一一个配置类，以前编写的配置文件一 样，也可以给容器中添加组件
   @EnableConfigurationProperties (HttpEncodingProperties.class) //启动指定类的ConfigurationProperties功能;
   @ConditionalOnWebApplication //底层是一个Conditional注解，根据不同条件，只有满足调建，这个配置类才会生效 ，这里是判断这里是不是web应用
   @ConditionalOnClass (CharacterEncodingFilter.class)//判断当前项目有没有这个类
   //CharacterEncodingFilter SpringMVC中解决乱码的过滤器
   @ConditionalOnProperty(prefix =”spring . http. encoding", value = "enabled", matchIfMissing =ture)//判断配置文件中是否存在莫格配置，matchIfMissing =ture不存在，判断也成立，默认生效
   public class HttpEncodingProperties {                      
   ```

   + 根据当前不同的条件判断自动配置是否生效

4. 所有的配置文件能够配装文件对在`xxxProperties`中封装这；配置文件能够配置说明就参照这个功能对应的配置类



#### springBoot的精髓

1. SpringBoot启动会加载大量的自动配置类
2. 我们可以看需要的功能有没有在SpringBoot做默认写好的自动配置类
3. 我们再看这个自动配置类值到底写了哪些组件（只要有，就不需要再配置了）
4. 给容器中添加自动配置类组件的时候，会从Properties类中获取某些属性，我们就可有再配置文件中指定这些属性的值。

xxxAutoConfiguration ：就是自动配置类

xxxProperties就是封装在配置文件中相关的属性



| @Condition扩展注解            | 作用（判断是否满足当前指定条件）                 |
| ----------------------------- | ------------------------------------------------ |
| @ConditionOnJava              | 系统的java版本是否符合要求                       |
| @ConditionOnBean              | 容器中存在指定的Bean                             |
| @ConditionOnMissingBean       | 容器中不存在指定的Bean                           |
| @ConditionOnExpression        | 满足SpEL表达式                                   |
| @ConditionOnClass             | 有指定的类                                       |
| @ConditionOnMissingClass      | 没有指定的类                                     |
| @ConditionOnSingleCandidate   | 容器中只有一个指定的Bean，或者这个Bean是首选bean |
| @ConditionOnProperty          | 系统中指定的属性是否有指定的值                   |
| @ConditionOnResourse          | 类路径下是否存在指定的资源文件                   |
| @ConditionOnWebApplication    | 当前是web环境                                    |
| @ConditionOnNotWebApplication | 不是web环境                                      |
| @ConditionOnJndi              | JNDI存在指定项                                   |

## 日志

### 日志框架

使用框架记录系统运行时候的信息

日志门面就是一个抽象层

**市面上的一些日志框架**：

![image-20200414221033297](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200414221033297.png)

### SL4j的使用

以后开发的时候，日志方法的调用，不应该直接调用日志的实现类，而是调用日志抽象层的接口

[SLF4j的官方文档](http://www.slf4j.org/)

应该导入slf4j的jar就elogback的实现jar

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```

![click to enlarge](http://www.slf4j.org/images/concrete-bindings.png)

使用slf4j之后，配置文件还是做层实现日志框架的自己配置文件

### springBoot的日志

1. SpringBoot底层也使用Slf4j加logback方式进行日志记录
2. SpringBoot用适配器模式把其他的日志也换成了slf4j
3. 若引入其他的框架，一定要把其他框架的依赖要移除掉

### 日志的使用

```java
   	@Test
	void contextLoads() {
	final Logger logger = LoggerFactory.getLogger(getClass());
//级别由低到高
   logger.trace("这是trace日志");
   logger.debug("debug日志");
   //默认在日志在info级别
   logger.info("这是info日志");
   logger.warn("这是warn日志");
   logger.error("这是error日志");

}
```

#### 日志保存

在application.properties里面指定

+ logging.path =/spring/log ，在/spring下的/log创建名为sping.log的日志
+ logging.file =xxx.log ，不指定路径，这个日志就回保存在当前项目的根目录下 ，也可以指定路径

## 拦截器

1. 实现HandlerInterceptor接口创建拦截器

2. 实现WebMvcConfigurer接口配置拦截器

**拦截器中方法的执行顺序是 preHandle -> Controller -> postHandle -> afterCompletion**
**只有preHandle返回true,才会执行后面的方法**

## SpringBoot与web开发

1. **创建SpringBoot应用，选中我们需要的模块**
2. **这些模块SpringBoot已经默认配置好了，只需要在配置文件指定少量的配置即可**
3. **然后就可以自己编写应用代码**

### SpringBoot对静态资源的映射规则

```java
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
    } else {
        Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
        CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
        if (!registry.hasMappingForPattern("/webjars/**")) {
            this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{"/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
        }

        String staticPathPattern = this.mvcProperties.getStaticPathPattern();
        if (!registry.hasMappingForPattern(staticPathPattern)) {
            this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(WebMvcAutoConfiguration.getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
        }

    }
}
```

1. 所有的webjars/**，都去classpath:/META-INF/resources/webjars/找资源

   以jar的方式导入静态 资源

2. “/**”访问当前项目的任何资源，（静态资源文件夹）

   ```java
   private static final String[] CLASSPATH_RESOURCE_LOCATIONS = new String[]{"classpath:/META-INF/resources/",
   "classpath:/resources/",
   "classpath:/static/", 
   "classpath:/public/"};
   //还有当前目录下的根路径："/"
   ```

3. 欢迎页面的静态资源文件下的所有index.html文件；被“/**”映射

   localhost:8080/index.html

4. 页面图标也是反正静态资源文件夹下，

### 模板引擎

在前后端不分离的情况下，springboot推荐用html做页面，然后用thymeleaf做模板引擎，做数据渲染，但是这种方式还是要用js或者jquery手动去操作dom，很难受

前后端分离的情况下直接使用vue、react等

#### thymeleaf

语法比JSP简单，功能更强大

引入thymeleaf

```xml
	<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
```

默认规则

```java
private static final Charset DEFAULT_ENCODING;
public static final String DEFAULT_PREFIX = "classpath:/templates/";
public static final String DEFAULT_SUFFIX = ".html";
private boolean checkTemplate = true;
private boolean checkTemplateLocation = true;
private String prefix = "classpath:/templates/";
private String suffix = ".html";
private String mode = "HTML";
```

只要把html放在/templates/里面就可以自动渲染

##### thymeleaf语法

```html
<div th:text="${hello}">
    这是欢迎信息
</div>
```

1.  th:text： 改变文本内容
2. th：任意属性 ，替换原生属性的值

![image-20200415215607619](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200415215607619.png)

### 使用SpringMVC的自动配置

SpringBoot中可以直接使用默认状态的SpringMVC

1. 自动配置视图解析器ViewResolve，根据视图返回值得到视图对象，视图对象决定了如何渲染/转发/重定向

   + 可以自己给容器中添加一个视图解析器自动的把其组合进来（实现ViewResolver接口）

2. 静态资源访问

3. 自动注册了`Converter`,`GenericConverter`,`Formatter`beans

   + Converter：转换器，类型转换使用这个组件
   + Formatter ：格式化器

4. `HttpMessageConverter` SpringMVC用来转换请求和响应的（User —> Json），

   + 从容器中确定，获取所有的HttpMessageConverte
   + **自己添加HttpMessageConverte，只要把组件放在容器中（@Bean）**

5. `ConfigurableWebBinglingInitializer`

   + 用于初始化WebDataBinder，用于数据绑定

   + 可以配置一个到容器中来替换默认的

### 修改SpringMVC的自动配置

【interceptors 拦截】

SpringBoot在自动配置的时候，会先看容器中有没有用户自己配置的（@Bean，@Component）如果有就用用户配置的，如果没有，才自动配置，如果有些组件可以有多个（ViewResolver）就可以把配置和自己弄的组合起来。

以前用xml配置

现在编写一个配置类（@Configuration），这个配置类的类型是WebMvcConfigurerAdapter，而且不能标注@EnableWebMVC

这样既保留了所有的自动配置，也能用我们的扩展配置

```java
@Configuration
class MyMvcConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        //浏览器发送/hello请求也来到succ页面
        registry.addViewController("/hello" ).setViewName("hello");
    }
}
```

原理

1. 在做其他自动配置的时候会导入**：@Import(EnableWebMvcConfiguration.class)**

2. 容器中所有的WebMvcConfig都会一起起作用

   效果：我们的配置和springMvc的自动配置也会一起起作用

   #### @EnableWebMVC

   SpringBoot对SpringMVC的自动配置就不需要了，所有的配置就自己掌控

   在控制类上加上`@EnableWebMVC`

在SpringBoot中会有很多的XXXConfiger来帮助我们配置

