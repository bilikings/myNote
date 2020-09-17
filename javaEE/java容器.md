

# java容器

## 容器的概念

java API提供的一系列类的实例，用于在程序在存放对象

## 容器的API

![image-20200225175659843](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200225175659843.png)

Collection接口定义了存储一组对象的方法，其子接口Set和List定义了储存方式

+ Set中的数据对象没有顺序且不可重复
+ List的对象有顺序且可以重复（equals方法相等即可）

Map接口定义了存储“（key）-（value)映射对”的方法

读/改效率：

+ Array读快改慢
+ Linked改快读慢
+ Hash两者之间

## Collection接口

![image-20200225193807409](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200225193807409.png)



## Iterator接口

Iterator对象称作迭代器，方便对容器中的元素进行遍历操作

定义的方法：

```java
boolean hasNext();//判断游标右边是否有元素
Object next();//返回游标右边的元素并且将游标移动到下一个位置
void remove();//删除游标左边的元素，在执行next之后
```

![image-20200225204525698](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200225204525698.png)



## 增强的for循环

就是for each

+ 缺陷：

  + 数组：

    不能方便的访问下标值

  + 集合：

    + 与使用Iterator相比，不能方便的删除集合中的内容（在内部也是用Iterator）

## Set接口

与数学中集合的概念相对应

## List接口和Comparable接口

+ List容器中的元素都对应一个整数形的序号记载在其容器的位置，可以根据序号存取容器中的元素

### list的常用算法

```java
import java.util.Collections;

void sort(List);//排序
void shuffle(List);//随机排序
void reverse(List);//逆序排序
void fill (List,Object);//重写容器
void copy(List dest,List src);//将src内容copy到dest容器中
int binarySearch（List,Object);//对于孙顺序的List，二分查找Object的对象，输出的是下标 
```

### Comparable

```java
public int compareTo(Object obj);
/**
* 返回0表示 this == obj
* 返回正数表示 this > obj
* 返回负数表示 this < obj
**/

public class Test{ 
   public static void main(String args[]){
      Integer x = 5;
      System.out.println(x.compareTo(3));
      System.out.println(x.compareTo(5));
      System.out.println(x.compareTo(8));            
     }
}
/**
* 输出：
* 1
* 0
* -1
**/
```

## Map接口

![image-20200225214854591](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200225214854591.png)

Map规定两边必须都是对象

## 自动打包/解包

+ 在合适的时候自动打包/解包
  + 自动将基础类型转换成为对象
  + 自动将对象转换成基础类型



## 泛型 Generic

在定义集合的时候同时定义集合中的对象类型，增强程序的可读性和可维护性