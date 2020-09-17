

## Tomcat

简单的说就是一个运行Java的网络服务器，底层是Socket的一个程序，它也是JSP和Serlvet的一个容器。

## Servlet

```java
class ServletDemo implements Servlet{

   @Override
   public void init(ServletConfig servletConfig) throws ServletException {
      
   }

   @Override
   public ServletConfig getServletConfig() {
      return null;
   }

   @Override
   public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
	//最重要，是处理response和request的核心接口
   }

   @Override
   public String getServletInfo() {
      return null;
   }

   @Override
   public void destroy() {

   }
}
```

```java
// WebServlet注解表示这是一个Servlet，并映射到地址/:
@WebServlet(urlPatterns = "/")
public class HelloServlet extends HttpServlet {
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {
        // 设置响应类型:
        resp.setContentType("text/html");
        // 获取输出流:
        PrintWriter pw = resp.getWriter();
        // 写入响应:
        pw.write("<h1>Hello, world!</h1>");
        // 最后不要忘记flush强制输出:
        pw.flush();
    }
}
```

一个Servlet总是继承自`HttpServlet`，然后覆写`doGet()`或`doPost()`方法。注意到`doGet()`方法传入了`HttpServletRequest`和`HttpServletResponse`两个对象，分别代表HTTP请求和响应。

- 在Servlet中定义的实例变量会被多个线程同时访问，要注意线程安全；
- `HttpServletRequest`和`HttpServletResponse`实例是由Servlet容器传入的局部变量，它们只能被当前线程访问，不存在多个线程访问的问题；
- 在`doGet()`或`doPost()`方法中，如果使用了`ThreadLocal`，但没有清理，那么它的状态很可能会影响到下次的某个请求，因为Servlet容器很可能用线程池实现线程复用。

### ServletContext

+ 是一个接口，表示Servlet上下文对象

+ 一个模块里面只有一个ServletContext对象实例

+ 这是一个域对象（可以像Map一样存取数据的对象，这里的域是指存取数据的操作范围）

  |        | 存数据         | 取数据         | 删除数据          |
  | ------ | -------------- | -------------- | ----------------- |
  | Map    | put()          | get()          | remove            |
  | 域对象 | setAttribute() | getAttribute() | removeAttribute() |

#### 作用

1. 获取web.xml的上下文参数
2. 获取工程路径
3. 获取在服务器上的绝对路径
4. 像Map一样存储数据

#### HTTP协议

##### GET

1. 请求行

   + 请求的方式
   + 请求的资源路径
   + 协议的版本号

2. 请求头

   key：vaiue 组成不同的键值对，表示不同含义

   ![image-20200405203515614](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200405203515614.png)

   

3. 请求体

##### POST

1. 请求行

   + 请求的方式
   + 请求的资源路径
   + 协议的版本号

2. 请求头

   key：vaiue 组成不同的键值对，表示不同含义

3. 请求体

### HttpServletRequest类

每次只要有请求进入Tomcat服务器，服务器就会把请求的http封装的Request对象中，然后传递到service方法（doGet和doPost）中，可以通过这个`HttpServletRequest`对象，获取到所有请求的信息

getParameter() 获取请求的参数

getParameterValues() 获取多个值时候使用 

getRequestDispatcher() 获取请求转发对象

### HttpServletResponse类

#### 两个输出流

| 字节流     | getOutputStream(); | 用于下载         |
| ---------- | ------------------ | ---------------- |
| **字符流** | **getWrite();**    | **用于回传数据** |

不能同时使用两个响应源

#### 回传数据

设置浏览器的字符编码

`resp.setContentType("text/html;UTF-8");`同时在服务器和客户端都设置了字符集，还设置了响应头

重写doGet方法

```java
PrintWriter wr =resp.getWriter();
wr.write("字符串");
```

#### 重定向

`resp.sendRedirect("url")`

##### Forward

内部转发，当一个Servlet出来请求时候，可以决定自己不继续处理，而是转交给另一个servlet处理

```
  req.getRequestDispatcher("/locat").forward(req, resp);
```