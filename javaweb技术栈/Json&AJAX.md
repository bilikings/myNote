# JSON

json的一种轻量级的数据交换格式（轻量级指根xml做比较，数据交换是指客户端和服务器之间的数据传输格式）

## Json在JavaScript中的使用

json有两种存在形式

+ 对象的形式存在，叫做json对象
+ 字符串的形式存在，叫他json字符串

可以相互转换

**一般需要操作json数据的时候使用json对象**

**在客户端与服务器之间姐姐数据交换的时候，使用json字符串**

`JSON.stringify() `把json对象转换成字符串

`JSON.parse()` 把字符串转换成json对象

## Json在java中的使用

要导入json的jar包Gson

### javabean和json的转换

```java
public void test(){
    Person person =new person(1,"xx");
    Gson gson = new Gson();
    Straing personJson = gson.toJson(person);//可以把java对象通过toJson转换
    //gson.fromJson(Json字符串,目标类);
    gson.fromJson(personJson,Person.class);//可以通过fromJson把json对象转回去
}
```

### list和json的转换

.class是给一个对象转换的

如果需要转集合（LIst，Map），则需要使用`fromJson(String Json，Type typeOfT)`，

需要创建一个类去继承TypeToken

```java
public void test2(){
    List<Preson> pList = new ArrayList<>();
    pList.add(new person(1,"xx"));
    pList.add(new person(2,"bob"));
    Gson gson = new Gson();
    String personJson = gson.toJson(pList);//可以把java对象通过toJson转换
    
   //这里是通过Gson提供的反射包获取类型
    List<Person> pList2 = gson.fromJson(personJson,new PersonTYpe.getType());//可以通过fromJson把json对象转回去
}
```

```java
public void PersonType extends TypeToken<ArraryList<Person>>{
    //需要构建一个type类继承TypeToken<目标类型>
}
```

### map和json的转换

```java
public void test3(){
    Map<Integer,Preson> pMap = new HashMap<>();
    pMap.put(1,new person(1,"xx"));
    pMap.put(2,new person(2,"bob"));
    Gson gson = new Gson();
    Straing personJson = gson.toJson(pMap);//可以把Map对象通过toJson转换
    
   //这里是通过Gson提供的反射包获取类型
    Map<Integer,Person> pMap2 = gson.fromJson(personJson,new PersonType.getType());//可以通过fromJson把json对象转回去
    //使用匿名内部类的方法,这样省去了写type类的方法
     Map<Integer,Person> pMap3 = gson
         .fromJson(personJson,new TypeToken<HashMap<Integer,Person>(){}.getType());
}
```

```java
public void PersonType extends TypeToken<HashMap<Integer,Person>>{
    //需要构建一个type类继承TypeToken<目标类型>
}
```

## AJAX

ajax即为异步的js和xml，是指一种创建交互式的网页应用开发技术

ajax是一种浏览器通过js异步发起请求，局部更新页面的技术

**在页面使用js发起ajax请求，访问服务器AjaxServlet中的jsAjax**：

1. 首先创建`XMLHttpRequest`

2. 调用`open()`设置参数

   ```javascript
   var xmlhttprequest = new XMLHttpRequest();
   
   xmlhttprequest.open("GET","url",true);//true为异步，false为同步
   ```

3. 调用`send()`发送请求,

   用XMLHttpRequest对象的`responseText`以获取字符串的形式的响应数据

   ```javascript
   xmlhttprequest.onreadystatechage =function(){
       if(xmlhttprequest.readyState ==4&& xmlhttprequest.status){
           //把响应的数据显示到页面上
           var jsonObj = JSON.parse(xmlhttprequest.responsText);
           document.getElementById("div1").innerHTML = jsonObj.id+" "+jsonObj.name;
       }
   }
   ```

   ```javascript
   xmlhttprequest.sent();
   ```

   **在send方法前绑定`onreadystatechange`事件，处理请求完成之后的操作**

**java服务器发送Ajax**

```java
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Gson gson =new Gson();
        Person person =new person(1,"xx");
        Gson gson = new Gson();
        Straing personJson = gson.toJson(person);
        
        resp.getWriter().write(personJson );
    }
```

**获取来自服务器的响应**

用XMLHttpRequest对象的`responseText`以获取字符串的形式的响应数据

### AJAX框架——jQuery

| 方法                                                         | 作用                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [$.ajax()](https://www.runoob.com/jquery/ajax-ajax.html)     | 执行异步 AJAX 请求                                           |
| $.ajaxPrefilter()                                            | 在每个请求发送之前且被 $.ajax() 处理之前，处理自定义 Ajax 选项或修改已存在选项 |
| [$.ajaxSetup()](https://www.runoob.com/jquery/ajax-ajaxsetup.html) | 为将来的 AJAX 请求设置默认值                                 |
| $.ajaxTransport()                                            | 创建处理 Ajax 数据实际传送的对象                             |
| [$.get()](https://www.runoob.com/jquery/ajax-get.html)       | 使用 AJAX 的 HTTP GET 请求从服务器加载数据                   |
| [$.getJSON()](https://www.runoob.com/jquery/ajax-getjson.html) | 使用 HTTP GET 请求从服务器加载 JSON 编码的数据               |
| [$.getScript()](https://www.runoob.com/jquery/ajax-getscript.html) | 使用 AJAX 的 HTTP GET 请求从服务器加载并执行 JavaScript      |
| [$.param()](https://www.runoob.com/jquery/ajax-param.html)   | 创建数组或对象的序列化表示形式（可用于 AJAX 请求的 URL 查询字符串） |
| [$.post()](https://www.runoob.com/jquery/ajax-post.html)     | 使用 AJAX 的 HTTP POST 请求从服务器加载数据                  |
| [ajaxComplete()](https://www.runoob.com/jquery/ajax-ajaxcomplete.html) | 规定 AJAX 请求完成时运行的函数                               |
| [ajaxError()](https://www.runoob.com/jquery/ajax-ajaxerror.html) | 规定 AJAX 请求失败时运行的函数                               |
| [ajaxSend()](https://www.runoob.com/jquery/ajax-ajaxsend.html) | 规定 AJAX 请求发送之前运行的函数                             |
| [ajaxStart()](https://www.runoob.com/jquery/ajax-ajaxstart.html) | 规定第一个 AJAX 请求开始时运行的函数                         |
| [ajaxStop()](https://www.runoob.com/jquery/ajax-ajaxstop.html) | 规定所有的 AJAX 请求完成时运行的函数                         |
| [ajaxSuccess()](https://www.runoob.com/jquery/ajax-ajaxsuccess.html) | 规定 AJAX 请求成功完成时运行的函数                           |
| [load()](https://www.runoob.com/jquery/ajax-load.html)       | 从服务器加载数据，并把返回的数据放置到指定的元素中           |
| [serialize()](https://www.runoob.com/jquery/ajax-serialize.html) | 编码表单元素集为字符串以便提交，以name=value&name=value的形式拼接 |
| [serializeArray()](https://www.runoob.com/jquery/ajax-serializearray.html) | 编码表单元素集为 names 和 values 的数组                      |

这里不细说了，慢慢使用vue或者react了

·