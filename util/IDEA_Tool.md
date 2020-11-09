# 常用工具

### Material Theme UI

是一个切换idea主题的工具

### Lombok

在编辑实体类时可以通过lombok注解减少getter、setter等方法的编写，在更改实体类时只需要修改属性即可，不过这个有大坑，

自己用的爽了，别人没这个插件时候回很自闭，是个侵入性极强的插件

### Translation

翻译工具

### CodeGlance

在右侧生成代码地图

### Rainbow brackets

括号彩虹

### Indent Rainbow

代码缩进提示

### RestfulToolKit

一套 RESTful 服务开发辅助工具集。
1.根据 URL 直接跳转到对应的方法定义 ( Ctrl \ or Ctrl Alt N );
2.提供了一个 Services tree 的显示窗口;
3.一个简单的 http 请求工具;
4.在请求方法上添加了有用功能: 复制生成 URL;,复制方法参数...
5.其他功能: java 类上添加 Convert to JSON 功能，格式化 json 数据 ( Windows: Ctrl + Enter; Mac: Command + Enter )。

### **Alibaba Cloud Toolkit**

个人经常会有这样的需求, 每次自己更新完测试环境之后, 就需要 `maven` 打包`clean install`, 然后`copy` `jar` 包, 利用`ftp`工具上传`jar`包到测试服务器, 然后`kill` 服务, 在启动服务 `java -jar` , 有时更新频繁 这就是一件非常麻烦的事

`Cloud Toolkit` 是本地 `IDE` 插件，帮助开发者更高效地开发、测试、诊断并部署应用。通过插件，您可以将本地应用一键部署到云端`（ECS、EDAS 和 Kubernetes 等`）和任意服务器；并且它还内置了 `Arthas` 程序诊断、`Dubbo工具`、`Terminal Shell` 终端和 `MySQL` 执行器等工具。

[官网链接](https://www.aliyun.com/product/cloudtoolkit)

### Alibaba Java Coding Guidelines 

实时监测代码的规范性, 从代码层面减少空指针等异常的出现

阿里巴巴出品的`java代码`规范插件, 可以扫描整个项目找到不规范的地方 并且大部分可以自动修复

虽说检测功能没有 `findbugs` 强大，但是可以自动修复, 阿里巴巴Java编码指南插件支持。

### Grep Console

高亮不同级别的日志

### Codota

增强代码提示

### GsonFormat

> 快速的讲一个 `json `转换为一个实体 安装完插件后 `alt + s` 放入正确的 `json `格式

# 常用设置

### 右下查看内存

双击shift，输入memory，打开之后去右下右键把其显示

### 鼠标滚轮+ctrl临时切换字体大小

![image-20200416104210122](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200416104210122.png)

### 自动导包

![image-20200416103959585](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200416103959585.png)

### 配置git

![image-20200416111137571](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200416111137571.png)

### Maven阿里镜像配置

![image-20200416104914063](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200416104914063.png)

![image-20200416105301943](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200416105301943.png)

```xml
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
```

### 快捷键

#### 通用类快捷键

**方法参数提示 **`ctrl + p`

> 非常实用的快捷键, 有的时候我们自己写的方法, 或者在看一些源码时, 都非常有用

[![img](https://raw.githubusercontent.com/xiaoxiunique/Web-Tip/master/20191119090347.png)](https://raw.githubusercontent.com/xiaoxiunique/Web-Tip/master/20191119090347.png)

**折叠代码/展开代码**

```
ctrl + - / ctrl + +
```

**全局查找文本**

```
ctrl + shift + F
```

**快速查找和打开最近使用过的文件码**

```
ctrl + E
```

**自动代码片**

```
ctrl + j
```

**实现接口方法**

```
ctrl + i
```

**查看当前类的子类**

```
ctrl + h
```

**将当前行和下一行进行合并**

```
ctrl + shfit + j
```

**将光标跳到当前行的上一行**

> 有时候在写完一行代码的时候需要添加注释, 或者为类属性添加注释的时候需要跳到当前行的上一行, 这个快捷键就非常方便

```
ctrl + alt + enter
```

**idea git 提交**

```
ctrl + k
```

**删除当前行**

```
ctrl + y
```

**重写 或者 实现接口或父类方法**

```
ctrl + o
```

**显示类之间的关系**

```
ctrl + alt + u
```

**删除类中没有用到的package**

```
ctrl + alt + o
```

**进入设置界面**

```
ctrl + alt + s
```

**在当前光标在的这样一行的下一行添加一行**

```
ctrl + shfit + enter
```

**弹出， 当前类中的方法集合**

```
ctrl + F12
```

> 最常用的快捷键之一, 快速的查找方法

**添加书签**

```
ctrl + f11
```

**搜索文件**

```
ctrl + shift + n
```

**搜索类合**

```
ctrl + n
```

> 最常用的快捷键之一, 项目慢慢的变大, 文件越来越多, 每次用鼠标去找 就太低效了

##### 快速生成 `try, if` 等语句

```
alt + shift + t
```

> 当你试用了之后, 你会爱上这个快捷键的

**抽取局部变量**

```
ctrl + alt + v
```

> 将当前选中的代码抽取为一个局部变量

**进入到实现子类中**

```
ctrl + alt + b
```

> 在使用`mvc`框架的时候, 往往我们只有一个接口的实例 这个快捷键可以直接到实现类中

**格式化代码**

```
ctrl + alt + L
```

> 让代码变得优美, 是每个程序员都应该注意的事, 方便自己和他人阅读, 利人利己

**idea 多光标选择**

```
按下滚轮上下拖动鼠标即可，
```

##### idea 批量修改相同内容

> 有的时候数据需要批量处理, 比如, 正常来说我们的实体类, 在使用`mybatis` 等逆向工程进行生成的时候, 一般属性是有注释的, 但是在针对如果我们使用了`swagger` 等插件需要来显示传递实体所代表的含义的时候, 就需要我们自己一个个的去写, 就会显得异常麻烦

```
ctrl + alt + shift + j
```

**运行当前类**

```
ctrl + shift + F10
```

> 在写一些测试代码的时候 这个快捷键就显得特别方便

**从多项目中启动一个 debug 模式**

```
alt + shfit + F9
```

> 在微服务中 多个工程在一个项目中的时候, 这个方法就比较的好用, 这样就不用自己一个一个的去点省去很多没必要的操作

**从多项目中启动一个 正常模式**

```
alt + shfit + F10
```

##### 重新编译当前项目

```
ctrl + shift + F9
```

> 当你发现有的问题 特别的奇怪, 命名表面上没问题, 但就是项目运行不了的时候, 重新编译一下获取就好了

查看当前类在哪些地方被使用过

##### 快速的查看选中类, 选中方法的定义

> 有的时候我们不想进入方法内部, 或者进入类的内部查看细节, 想要在外面就探查清楚, 就可以使用此种方法

```
ctrl + shift + i
```

![image-20200416105909433](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200416105909433.png)

比较强大的几个快捷键之一 `Ctrl + ~`(感叹号旁边的按键)

```
ctrl + ~
```