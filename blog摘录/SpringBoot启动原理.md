# SpringBoot是怎么启动的

在main方法中，`SpringAppication.run()`就把项目运行起来了

那这个run方法背后隐藏了什么秘密

### 初始化

![image-20201103231454614](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20201103231454614.png)

+ webApplicationType有三种类型 REACTIVE，SERVLET，NONE的莫一种
+ setInitializers（设置初始化）是使用`SpringFactoriesLoader`查找并加载`classpath下META-INF/spring.factories`文件中所有可用的 `ApplicationContextInitializer`，初始化这个类要导的包，然后递归的向上调包
+ setListeners（添加监听器）是使用`SpringFactoriesLoader`查找并加载`classpath下META-INF/spring.factories`文件中的所有可用的 `ApplicationListener`
+ mainApplicationClass是设置main方法的定义类

### run()方法

![image-20201103232512290](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20201103232512290.png)

1. 通过 `SpringFactoriesLoader` 加载 `META-INF/spring.factories` 文件，获取并创建 `SpringApplicationRunListener` 对象
2. 然后由 `SpringApplicationRunListener` 来发出 `starting` 消息
3. 创建参数，并配置当前 `SpringBoot` 应用将要使用的 `Environment`
4. 完成之后，依然由 `SpringApplicationRunListener` 来发出 `environmentPrepared` 消息
5. 创建 `ApplicationContext`
6. 初始化 `ApplicationContext`，并设置 `Environment`，加载相关配置等
7. 由 `SpringApplicationRunListener` 来发出 `contextPrepared` 消息，告知Spring Boot 应用使用的 `ApplicationContext` 已准备OK
8. 将各种 `beans` 装载入 `ApplicationContext`，继续由 `SpringApplicationRunListener` 来发出 `contextLoaded` 消息，告知 Spring Boot 应用使用的 `ApplicationContext` 已装填OK
9. `refresh ApplicationContext`，完成IoC容器可用的最后一步
10. 由 `SpringApplicationRunListener` 来发出 `started` 消息
11. 调用`callRunners(...)`方法，让实现了`ApplicationRunner`和`CommandLineRunner`接口类的`run` 方法得以执行，用于在 Spring 应用上下文准备完毕后，执行一些额外操作。从而完成最终的程序的启动。
12. 由 `SpringApplicationRunListener` 来发出 `running` 消息，告知程序已运行起来了