# 数据结构

## 稀疏数组

+ 二维转稀疏的思路

  1. 先遍历二维数组得到有效数据的个数sum
  2. 根据sum建立新数组sparseArr[sum+1] [3]
  3. 将有效数据导入稀疏数组

  ```java
  package SparseArray;
  
  import java.util.Arrays;
  
  public class SparseArray {
      public static void main(String[] args) {
          //创建一个原始的二维数组
          //0：没有子，1：黑子， 2，白子
          int[][] chessArr = new int[11][11];
          chessArr[1][2] = 1;
          chessArr[2][3] = 2;
          chessArr[2][6] = 2;
          int count = 0;
          for (int[] row : chessArr) {
              for (int data : row) {
                  System.out.printf("%d\t", data);
                  if (data != 0) count++;
              }
              System.out.println();
  
          }
          System.out.println(count);
  
          System.out.println(chessArr.length);
  
          int[][] sparseArr = new int[count + 1][3];
          sparseArr[0][0] = chessArr.length;
          sparseArr[0][1] = chessArr.length;
          sparseArr[0][2] = count;
  
          //遍历二维数组，将有效数放入稀疏数组中
          int h = 0;
          for (int i = 0; i < chessArr.length; i++) {
              for (int j = 0; j < chessArr.length; j++) {
                  if (chessArr[i][j] != 0) {
                      h++;
                      sparseArr[h][0] = i;
                      sparseArr[h][1] = j;
                      sparseArr[h][2] = chessArr[i][j];
                  }
              }
          }
  
          System.out.println(Arrays.deepToString(sparseArr));
      }
  }
  
  ```

+ 稀疏转二维的思路

  1. 读取稀疏数组的第一行，根据行和列建立二维数组
  2. 读取稀疏数组的其他数据后，赋值给对应位置上的二维数组即可

```java
        //恢复后的二维数组
        int[][] newChessArr = new  int[sparseArr[0][0]][sparseArr[0][1]];

        for(int i = 1;i<sparseArr.length;i++){
            newChessArr[sparseArr[i][0]][sparseArr[i][1]] =sparseArr[i][2];
        }

        for(int[] a :newChessArr){
            for (int data : a){
                System.out.printf("%d\t",data);
            }
            System.out.println();
        }
```

## 队列

+ 先入先出

+ 数组模拟队列

```java
  
  public class ArrQueue {
      public static void main(String[] args) {
          //创建queue
          ArrayQ arrayQ =new ArrayQ(4);
          char key;
          Scanner input =new Scanner(System.in);
          boolean loop =true;
          while (loop){
              System.out.println("s: 显示数据");
              System.out.println("e: 退出程序");
              System.out.println("a: 添加数据");
              System.out.println("g: 取出数据");
              System.out.println("h: 获取头部数据");
              key =input.next().charAt(0);//接受一个字符
              switch (key){
                  case 's':
                      arrayQ.showQueue2();
                      arrayQ.showQueue1();
                      break;
                  case 'a':
                      System.out.println("请输入一个数");
                      int val = input.nextInt();
                      arrayQ.addQueue(val);
                      break;
                  case 'g':
                      try {
                          int res =arrayQ.getQueue();
                          System.out.printf("取出的数据是：%d",res);
                      }catch (Exception e){
                          System.out.println(e.getMessage());
                      }
                      break;
                  case 'h':
                      try{
                          int re = arrayQ.headQ();
                          System.out.printf("取出的头数据是：%d",re);
                          System.out.println(" ");
                      }catch (Exception e){
                      System.out.println(e.getMessage());
                  }
                      break;
                  case 'e':
                      input.close();
                      loop =false;
                      break;
                  default:
                      break;
              }
          }
  
      }
  }
```

  

  ```java
  package Queue;
  
  import java.util.*;
  
  public class ArrQueue {
  //自以为的数组队列
      public static void main(String[] args) {
          int front =-1;
          int rear = -1;
          int[] arr = new int[10];
          Scanner in =new Scanner(System.in);
  
          for(int i =rear+1;i<=arr.length-1;i++){
              int val = in.nextInt();
              if(val!=0){
              arr[i] =val;
              rear++;}
              else break;
          }
  
          System.out.println(Arrays.toString(arr));
  
          for(int j =front+1;j<=arr.length-1;j++){
              System.out.println(arr[j]);
              front++;
          }
      }
  }
  
  ```

### 环形队列

+ 当rear =arr. length时，判断之前有没有空的位置，即front！=-1



+ front思路调整，即front就指向queue的第一个元素，初始值为0
+ rear即为queue的最后一个元素，初始值为0
+ 队列满时即（rear+1） % maxSize  =front
+ rear空时 rear ==front
+ queue中有效个数是（rear+maxSize-front）%maxSize

```java

class ArrayQ {
    private int maxSize;
    private int front;
    private int rear;
    private int[] arr;//用于模拟队列

    //队列构造器
    public ArrayQ(int arrMaxSize) {
        maxSize = arrMaxSize;
        arr = new int[maxSize];
        front = 0;//指向queue的第一个位置
        rear = 0;//指向queue尾部，即最后一个位置
    }

    public boolean isFull() {
        return (rear+1)%maxSize ==front;
    }

    public boolean isEmpty() {
        return rear == front;
    }

    //添加数据到队列
    public void addQueue(int n) {
        if (isFull()) {
            System.out.println("is full");
        } else {
            arr[rear] =n;
            rear = (rear+1)%maxSize;
        }
    }

    //出队列
    public int getQueue() {
        if (isEmpty()) {
            throw new RuntimeException("is empty");
        }
        /*
        先把front的值存到一个临时变量
        front后移
        返回原值
         */
        int temp = arr[front];
        front = (front+1)%maxSize;
        return temp;
    }

    //显示队列头，不取出数据
    public int headQ() {
//        if (isEmpty()) {
//            throw new RuntimeException("is empty");
//        }
        return arr[front];
    }
    public int size(){
        return (rear+maxSize-front)%maxSize;
    }

    public void showQueue1(){
        System.out.println(Arrays.toString(arr));
    }
    public void showQueue2(){
        if(isEmpty()){
            System.out.println("empty!!");
            return;
        }
        for(int i =front;i<front+size();i++){
            System.out.printf("arr[%d]=%d\n",i%maxSize,arr[i%maxSize]);
        }
    }
}
```



### 链表

#### 单链表

头指针head =120

| 地址 | data域 | next域 |
| ---- | ------ | ------ |
| 110  | a2     | 130    |
| 120  | a1     | 110    |
| 130  | a3     | NULL   |

链表是以节点的方式存储的，每个节点都包含data域，next域：指向下一节点，节点不一定连续存放。

分有无head节点的链表

```java
package LinkLIst;

public class LinkList {
    public static void main(String[] args) {
        HeroNode heroNode1 = new HeroNode(1, "宋江");
        HeroNode heroNode2 = new HeroNode(2, "卢俊义");
        HeroNode heroNode3 = new HeroNode(3, "吴用");
        HeroNode heroNode4 = new HeroNode(4, "林冲");
    //    HeroNode heroNode5 = new HeroNode(4, "林冲2");

        //c创建链表
        SingleLinkedList singleLinkedList = new SingleLinkedList();
//        singleLinkedList.add(heroNode1);
//        singleLinkedList.add(heroNode2);
//        singleLinkedList.add(heroNode3);
//        singleLinkedList.add(heroNode4);

        singleLinkedList.addByOrder(heroNode4);
        singleLinkedList.addByOrder(heroNode2);
        singleLinkedList.addByOrder(heroNode1);
        singleLinkedList.addByOrder(heroNode3);

//        singleLinkedList.update(heroNode5);
        HeroNode newHeroNode = new HeroNode(1,"song");
        singleLinkedList.update(newHeroNode);

        singleLinkedList.showList();
    }

    //定义SingleLinkedList
    static class SingleLinkedList{
        //初始化头节点
        private HeroNode head = new HeroNode(0, "");

        //当不考虑顺序是，找到链表的最后节点，将这个节点的next指向新的节点
        public void add(HeroNode heroNode){
            //因为head不能动，需要一个辅助节点
            HeroNode temp=head;
            while (temp.next != null) {
                temp = temp.next;
            }
            temp.next = heroNode;
        }

        //根据排名插入
        public void addByOrder(HeroNode heroNode){
            //因为head不能动，需要一个辅助节点
            //我们的temp找到的是添加位置的前一个节点
            HeroNode temp=head;
            boolean flag =false;//标记编号是否存在
            while (true){
                if(temp.next ==null)break;
                else if (temp.next.no>heroNode.no){//位置就找到了
                    break;
                }else if (temp.next.no == heroNode.no){
                    flag= true;
                    break;
                }else temp =temp.next;
            }
            if(flag){
                System.out.printf("这位置有人了,编号为%d",heroNode.no);
            }else{
                heroNode.next = temp.next;
                temp.next =heroNode;
            }
        }

        //删除
        public void deleteNode(int no){
            HeroNode temp =head;
            boolean flag =false;
            while (true){
                if(temp.next == null){
                    break;
                }
                else if (temp.next.no==no){
                    flag= true;
                    break;
                }else temp=temp.next;
            }
            if(flag){
                System.out.println("链表中存在这个node，已删除");
                temp.next =temp.next.next;
            }else{
                System.out.println("不存在这个节点");
            }
        }

        //修改
        private void update(HeroNode newHeroNode){
            if (head.next == null){
                System.out.println("链表为空");
                return;
            }
            HeroNode temp =head.next;
            boolean flag =false;
            while (true){
                if(temp  == null){
                    break;
                }
                else if (temp.no==newHeroNode.no){
                    flag= true;
                    break;
                }else temp=temp.next;
            }
            if(flag){
                temp.name = newHeroNode.name;
            }else{
                System.out.println("不存在这个节点");
            }
        }

        //显示链表
        public void showList(){
            HeroNode temp=head;
            if (temp.next == null){
                System.out.println("链表为空");
                return;
            }
            while (temp.next != null) {
                {
                    temp = temp.next;
                    System.out.println(temp);
                }
            }
        }
    }

     static class HeroNode{
        int no;
        String  name;
        HeroNode next;//指向下一个节点

        public HeroNode(int no, String name) {
            this.no = no;
            this.name = name;
         //   this.next = next;
        }

        //为了显示方便重写toString

        @Override
        public String toString() {
            return "HeroNode[node = "+ no+", name = "+name+"]";
        }
    }
}

```

#### 双向链表

双向链表的遍历、添加、修改的操作思路：

+ 遍历方式与单链表一样，
+ 添加（默认添加到双向链表的最后）：
  + 先找找双向链表的最后节点
  + temp.next =newNode;
  + newNode.pre = temp;(相当于绑两条线)
+ 修改思路和单项一样
+ 删除
  + 因为是双向链表，所以可以实现自我删除
  + 直接找到这节点，例如temp：
    + temp.pre.next=temp.next;
    + temp.next.pre =temp.pre;

```java
    public void add(Node node) {
            Node temp = head;
            boolean flag = false;//标记节点是否存在

            while (true) {
                if (temp.next == null) {
                    break;
                } else if (temp.next.no > node.no) {//找到了
                    break;
                } else if (temp.next.no == node.no) {
                    flag = true;
                    break;
                }else temp= temp.next;
            }
            if (flag) {
                System.out.printf("这位置有人了,编号为%d", node.no);
            } else {
                node.next = temp.next;
                temp.next = node;
                node.pre = temp;
                if (node.next != null) {
                    node.next.pre = node;
                }
            }
        }
```

#### 单项环形链表

+ Jusephu问题

## 栈

栈是一种有序链表，可以使用数组结构来模拟

```java
package Stack;

public class stack {
    public static void main(String[] args) {
        ArrayStack arrayStack =new ArrayStack(5);

        for(int i=1;i<=5;i++){
            arrayStack.inPut(i);
        }

        arrayStack.list();
    }
}

class ArrayStack{
    int maxSize;
    int[] arrstack ;
    private int top =-1;

    public ArrayStack(int maxSize){
        this.maxSize = maxSize;
        arrstack =new int[maxSize];
    }

    //栈满
    public boolean isfull(){
        return top ==maxSize-1;
    }

    //栈空
    public  boolean isempty(){
        return top == -1;
    }

    //入栈
    public void inPut(int val){
        if(isfull()){
            System.out.println("is full!!!");
        }else {
            top++;
            arrstack[top] =val;
        }
    }

    //出栈
    public int pop(){
        int val = 0;
        if(isempty()){
            System.out.println("is empty");
        }else {
            val = arrstack[top];
            top--;
        }
        return val;
    }

    //全出栈
    public void list(){
        if(isempty()){
            System.out.println("is empty!!!");
            return;
        }
        for (int i =top;i>=0;i--){
            System.out.printf("arrstack[%d] =%d\n",i,arrstack[i]);
        }
    }

}

```

+ 使用栈了计算一个表达式

  设计两个栈，分别一个存放数，一个存放运算符

  + 通过一个index'索引，遍历我们的表达式
  + 如果是一个数字就入栈，如果是一个符号：
    + 如果是符合栈为空，就直接入栈
    + 如果是有操作符，就比较，**如果当前操作符的优先级小于等于栈的操作符**，就从数据栈中取出两个数，从符号栈中pop一个一个符号进行运算，将得到的结果入数栈；**如果当前操作符的优先级大于栈的操作**，就直接入栈
    + 当扫描完毕时候，就顺序的从数和符号栈中pop出对应的数与符号，并运行
    + 最后数栈中只有一个数即为所求

  

### 前、中、后表达式

  #### 前缀表达式

  从**右至左**扫描表达式，遇到数字时候，将数字压入栈，遇到运算符时候pop出2个数，用运算符运算，然后结果入栈，重复这个过程，直到表达式最左，即为所求

  #### 中缀表达式

和正常读写一样

+ **中缀转后缀**

  1. 初始化两个栈，存运算符的S1，存数字的S2
  2. 从左至右扫描表达式
  3. 遇到数，压入s2
  4. 遇到运算符时候，比较其与S1的优先级：
     + s1空，或为左括号，直接入栈
     + 若优先级比栈顶的高也入栈
     + 否则就将S1的运算符鸵鸟压入s2，再执行3.的操作
  5. 遇到括号时候，左括号直接入s1；右括号时，弹出s1的运算符压入s2，直到遇到左括号为止，之后丢弃这括号
  6. 重复2-5，直到在表达式的最右边
  7. 将S1剩余的符号弹出压入s2
  8. 把s2出栈，**逆序**即为中缀表达式的后缀表达式

+ 例如：   1+((2+3)*4）-5    =>   1 2 3 +4 * + 5 -

  ``` java
      public static void toInfixExpressionList(String s){
          List<String> ls = new ArrayList<>();
          int i = 0;//用于遍历s
          String str;//对多位数拼接
          char c;
          do{
              if(((c =s.charAt(i))<48) ||((c =s.charAt(i))>57) ){
                  ls.add("" +c);
                  i++;
              }else {
                  //如果是多位数拼接
                  str = "";
                  while (i<s.length() && (((c =s.charAt(i))<48) ||((c =s.charAt(i))>57) )){
                      str.append(c);
                      i++;
                  }
                  ls.add(str);
              }
          }while (i<s.length());
      }
  }
  ```

  

  

#### 后缀表达式（逆波兰表达式）

 从**左至右**扫描表达式，遇到数字时候，将数字压入栈，遇到运算符时候pop出2个数，用运算符运算，然后结果入栈，重复这个过程，直到表达式最右，即为所求

+ 逆波兰计算器
  1. 输入一个逆波兰表达式，用Stack计算其结果，
  2. 支持小括号和多位数整数

 ## 递归

简单来说就是自己调用自己

```java
    public static int test(int n){
        if(n>1000){
            return n;
        }
        else n = n*n;
        return test(n);
    }
```

### 迷宫问题

```java
    /**
     * 功能描述: <br>
     * 〈〉
     *
     * @Param: [map, i, j][表示地图,[i,j]表示从哪个位置开始找]
     * @Param: map[6][5]找到，说明通路可行，1为墙，2为可以走，3为死路
     * @Return: 如果找到通路，就返回true，否则就false
     * @Author: admin
     * @Date: 2020/3/10 9:05
     */
    public static boolean setway(int[][] map, int i, int j) {
        if (map[6][5] == 2) {
            return true;
        } else {
            if (map[i][j] == 0) {
                //按下->右->上->左
                map[i][j] = 2;
                if (setway(map, i + 1, j)) {
                    return true;
                } else if (setway(map, i, j + 1)) {
                    return true;
                } else if (setway(map, i - 1, j)) {
                    return true;
                } else if (setway(map, i, j - 1)) {
                    return true;
                } else {
                    map[i][j] = 3;
                    return false;
                }
            }else {//如果map[i][j]！ =0
                return false;
            }
        }
    }
```



## 排序

![image-20200226111752377](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200226111752377.png)

### 选择排序

+ 最没用的算法

+ 一遍又一遍过滤这个数组，找到最小的放到前面来

  ```java
  public class TEst {
      public static void main(String[] args) {
          int[] arr = {3, 2, 4, 5, 7, 1, 6};
  
          for (int j = 0; j < arr.length; j++) {
              int minPos = j;
              for (int i = j + 1; i < arr.length; i++) {
                  if (arr[i] < arr[minPos])
                      minPos = i;
              }
              swap(arr,j,minPos);
          }
          showArr(arr);
      }
      public static void swap(int[] arr,int j ,int minPos){
          int temp = arr[j];
          arr[j] = arr[minPos];
          arr[minPos] = temp;
      }
  
      public static void showArr(int[] arr) {
          for (int value : arr) {
              System.out.println(value + " ");
          }
      
  }
  
  ```

  ### 大O分析

  只要最高阶

### 验证算法——对数器



### 算法稳定性



### 冒泡排序

```java
public static void bobbleSort(int[] arr) {
    boolean didSwap;
    for (int i = arr.length - 1; i > 0; i--) {
        didSwap = false;
        for (int j = 0; j < i; j++) {
            if (arr[j] > arr[j + 1]) {
                swap(arr, j, j + 1);
                didSwap = true;
            }
        }
        if(!didSwap){
            return;
        }
    }
```

### 插入排序

```java
   public static void insSort(int[] arr) {
        for (int i = 1; i < arr.length; i++) {
            for (int j = i; j > 0 && arr[j] < arr[j - 1]; j--) {
                swap(arr, j, j - 1);
            }
        }
    }
```

### 希尔排序

+ 改进的插入排序

先选一个间隔，间隔比较大的时候，移动次数少；间隔小的时候，移动距离小。

```java
public static void shellSort(int[] arr){
    int h = 1;
    while(h<= arr.length/3){
        h=h*3+1
    }
    for(h=arr.l)
    for(int i = h ;i<arr.length;i++){
        for(int j=i;j>h-1;j-=h){
            swap(arr,j,j-h)
        }
    }
}
```

### 归并排序

```java
    public static void mSort(int[] arr, int leftP, int rightBound) {
        if (leftP == rightBound) return;
        //分成两半
        int mid = leftP + (rightBound - leftP) / 2;
        //左排序
        mSort(arr, leftP, mid);
        //右排序
        mSort(arr, mid + 1, rightBound);
        mergeSort(arr, leftP, mid + 1, rightBound);
    }

    /**
     * mergeSort是一次归并，递归调用通过mSort()
     */
    public static void mergeSort(int[] arr, int leftP, int rightP, int rightBound) {
        int mid = rightP - 1;
        int[] newArr = new int[rightBound - leftP + 1];

        int i = leftP;//只在前半截的数组上
        int j = rightP;//只在后半截数组上
        int k = 0;//只在新数组的第一个位置上

        while (i <= mid && j <= rightBound) {
            newArr[k++] = arr[i] <= arr[j] ? arr[i++] : arr[j++];
//            if (arr[i] <= arr[j]) {
//                newArr[k++] = arr[i++];
//            } else {
//                newArr[k++] = arr[j++];
//            }
        }
        while (i <= mid) newArr[k++] = arr[i++];//检测当后数组遍历完的时候，把前数组接到newArr上
        while (j <= rightBound) newArr[k++] = arr[j++];//检测当前数组遍历完的时候，把后数组接到newArr上
        if (newArr.length >= 0) System.arraycopy(newArr, 0, arr, leftP , newArr.length);
        //for (int m = 0; m < newArr.length; m++) arr[leftP + m] = newArr[m];
    }
```

### java 对象排序

一般要求稳定排序，所以使用归并排序，还有改进的TimSort。

### 快速排序

 ```java
    public static void quickSort(int[] arr,int LP,int HP){
        if(LP>=HP)return;
        int lowP =LP;
        int highP =HP ;
        int flag =arr[lowP];

        while (lowP<highP){
            while (flag<=arr[highP] && lowP<highP)highP--;
            arr[lowP] =arr[highP];
            while (flag >=arr[lowP] && lowP<highP) lowP++;
            arr[highP] = arr[lowP];
        }

        arr[lowP]= flag;

        //递归前半分区
        quickSort(arr,LP,lowP-1);
        //递归后半分区
        quickSort(arr,highP+1,HP);
    }

 ```

### 计数排序

+ 非比较排序
+ 桶排序的一种
+ 适用范围：
  + 量大范围小

创建一个有限新数组来存数据。

```java
public static int[] countSort(int[] arr){
        int[] result = new int[arr.length];
        int[] count = new int[15];//设定只有15种数

        for (int value : arr) {
            count[value]++;
        }

        System.out.println(Arrays.toString(count));

        for(int i =0,j=0;i<count.length;i++){
            while (count[i]-- >0){//count不断递减，只要>0
                result[j++] = i;
            }
        }
        return result;
    }
```

## 查找

### 二分查找

+ 递归实现

```java
class Method {
    public static int binarySearch(int[] arr, int val, int LP, int RB) {
        if (LP > RB) {
            return -1;
        }
        int mid = LP + (RB - LP) / 2;

        if (arr[mid] > val ) {
            return binarySearch(arr, val, LP, mid-1);
        } else if (arr[mid] < val) {
            return binarySearch(arr, val, mid + 1, RB);
        } else {
            return mid;
        }
    }
}
```

+ 非递归实现

```java
 public static int binaryS2(int[] arr,int val){
        int left =0;
        int right =arr.length-1;
        int mid =0;

        if (arr[arr.length/2]==val){//比较中值，因为循环不到
            return arr.length/2;
        }
        while (left<=right){
            mid =left+(right-left)/2;
            if(arr[mid]<val){
                left =mid+1;
            }else if(arr[mid]>val){
                right= mid-1;
            }else {
                return mid;
            }
        }
        return -1;
    }
```

### 插值查找

```java
int midIndex = low +(high-low)*(key -arr[low])/(arr[high]-arr[low])//key是要查找的值
```

### 斐波那契中值

mid = low+F[k-1]-1

```java
public static int[] fib(){
    int[] f =new int[maxSize];
    f[0]=1;
    f[1]=1;
    for(int i =2;i<maxSize;i++){
        f[i] =f[i-1]+f[i-2];
    }
    return f;
}

public static int fibSeach(int[] arr, int key){
    int low =0;
    int high =a.length-1;
    int k=0;
    int mid =0;
    int f[] =fib();
    
    while(high > f[k]-1){
        k++;
    }
    
    //因为f[k]的值会大于arr的长度，需要构建一个Arrays来构建一个新的数字，并指向a[]
    int temp =Arrays.copyOf(arr,f[k]);
    for(int i =high +1;i<temp.length;i++){
        temp[i] =arr[high];
    }
    
     while (low<=high){
            mid =low +f[k-1]-1;
            if(arr[mid]<key){
                left =mid+1;
                k -=2;
            }else if(arr[mid]>key){
                high= mid-1;
                k--;
            }else {
                if(mid<=high){
                return mid;
                }else{
                    return high;
                }
            }
         return -1;
     }
}
```

## hash表

+ **散列表**（也叫哈希表）是根据**关键码**值拉长进行访问的数据结构。可以通过把关键码值**映射**到表中的一个位置了访问记录，以求加快查找的速度，这个映射函数叫做**散列函数**，存放记录的数组叫做**散列表**。
+ 其实是一个**数组链表**。

#### hash表的实现

```java

class Emp {
    int id;
    String name;
    public Emp next;

    public Emp(int id, String name) {
        //   super();
        this.id = id;
        this.name = name;
    }
}

class EmpLinkedList {
    //头指针，指向第一个Emp
    private Emp head;

    //添加雇员
    public void add(Emp emp) {
        if (head == null) {
            head = emp;
            return;
        }
        Emp temp = head;
        while (temp.next != null) {
            temp = temp.next;
        }
        temp.next = emp;
    }

    public void list(int i) {
        if (head == null) {
            System.out.printf("第%d链表为空\n", i + 1);
            return;
        }
        Emp temp = head;
        while (true) {
            System.out.printf("第%d链表: id=%d name  =%s\t", i + 1, temp.id, temp.name);
            if (temp.next == null) {
                break;
            }
            temp = temp.next;
        }
    }

    //根据id查找雇员
    public Emp seach(int id) {
        if (head == null) {
            System.out.println("is empty");
            return null;
        }

        Emp temp = head;
        //找到
        while (temp.id != id) {
            if (temp.next == null) {
                temp = null;
                break;
            } else temp = temp.next;
        }
        return temp;
    }
}

class HashTable {
    private EmpLinkedList[] empLinkedListArray;
    private int size;//这里size表示有几条链表

    //构造器
    public HashTable(int size) {
        //初始化 empLinkedListArray
        this.size = size;
        empLinkedListArray = new EmpLinkedList[size];
        //要分别初始化每个链表
        for (int i = 0; i < size; i++) {
            empLinkedListArray[i] = new EmpLinkedList();
        }
    }

    //添加
    public void add(Emp emp) {
        int empLinkedNO = hashFun(emp.id);

        empLinkedListArray[empLinkedNO].add(emp);
    }

    //编写散列函数
    public int hashFun(int id) {
        return id % size;
    }

    //查找
    public void seach(int id) {
        //用散列函数确定去哪一条链表上找
        int empLinkedNO = hashFun(id);
        Emp emp = empLinkedListArray[empLinkedNO].seach(id);
        if (emp != null) {
            System.out.printf("在%d找到 id =%d\n", empLinkedNO + 1, id);
        } else {
            System.out.println("not found");
        }
    }

    //遍历所有hashtable
    public void list() {
        for (int i = 0; i < size; i++) {
            System.out.println();
            empLinkedListArray[i].list(i);
        }
    }
}
```

## 树

### 二叉树

+ 树的结构能提供数据存储、读取的效率。比如二叉排序树，既可以保证检索速度和修改速度。

+ 创建节点

  ```java
  //创建节点
  class Node{
      private int no;
      private String name;
      private Node left;
      private Node right;
  
      public Node(int no, String name) {
          this.no = no;
          this.name = name;
      }
  }
  ```

+ 定义二叉树

  ```java
  class BinanyTree {
      private Node root;
  
      public void setRoot(Node root) {
          this.root = root;
      }
  
      //前序遍历
      public void preOrder() {
          if (this.root != null) {
              this.root.preOrder();
          } else {
              System.out.println("is empty");
          }
      }
  
      //中序
      public void infixOrder() {
          if (this.root != null) {
              this.root.infixOrder();
          } else {
              System.out.println("is empty");
          }
      }
  
      //后序
      public void postOrder() {
          if (this.root != null) {
              this.root.postOrder();
          } else {
              System.out.println("is empty");
          }
      }
  
  }
  ```

  

#### 前序遍历

先输出父节点，然后左子树之后右子树。

 1. 先输出当前节点（初始时候为root点）

 2. 如果左子节点不为空，继续递归前序

 3. 如果右子节点不为空，继续递归前序

    ```java
        public void preOrder() {
            System.out.println(this);//先输出父节点
            //递归左子树
            if (this.left != null) {
                this.left.preOrder();
            }
            //递归向右子树
            if (this.right != null) {
                this.right.preOrder();
            }
        }
    ```

    

#### 中序遍历

先左子树，然后父节点，之后右子树

 1. 如果左子节点不为空，继续递归中序

 2. 输出当前节点

 3. 如果右子节点不为空，继续递归中序

    ```java
        public void infixOrder() {
            if (this.left != null) {
                this.left.infixOrder();
            }
            System.out.println(this);
            if (this.right != null) {
                this.right.infixOrder();
            }
        }
    ```

    

#### 后序遍历

先左子树，然后右子树，之后父节点

 1. 如果左子节点不为空，继续递归后序

 2. 如果右子节点不为空，继续递归后序

 3. 输出当前节点

    ```java
        public void postOrder() {
            if (this.left != null) {
                this.left.postOrder();
            }
            if (this.right != null) {
                this.right.postOrder();
            }
            System.out.println(this);
        }
    ```

#### 查找

**前序查找**

```java
    //前序查找
    public void preSearch(int no){
        if(this.root!=null){
            Node a = this.root.preOrderSearch(no);
            System.out.println(a);
        }else{
            System.out.println("is empty");
        }

    }
```

```java
public Node preOrderSearch(int no) {
        if (this.no == no) {
            //  System.out.printf("找到了，是%d,name =%s",no,this.name);
            return this;
        }
        Node resNode =null;
        if (this.left != null) {
            resNode =this.left.preOrderSearch(no);
        }
        if (resNode!=null){
            return resNode;
        }
        if (this.right != null) {
            resNode =this.right.preOrderSearch(no);
        }
        return resNode;
    }
```

**中序、后序如法炮制**

#### 删除

+ 现在是无规则的树，所以删除非叶子节点时，可以吧左子或右子提上来都可。

#### 完全二叉树

+ 所有**叶子节点都在最后一层**，并且节点总数N=2^n-1，n为层数，为满二叉树，是特殊的完全二叉树

#### 顺序存储二叉树

+ **数组存储方式**和**二叉树存储方式**可以相互转换
+ 通常只考虑完全二叉树（n：表示二叉树的第n个元素，从0开始编号）
+ 第n个元素的左子节点为2*n+1
+ 第n个元素的右子节点为2*n+2
+ 第n个元素的父节点为(n-1)/2 （取整）

  #### 线索化二叉树

+ 说明：当线索化二叉树后，Node节点的属性left和right，有如下情况：
+ left指向的是左子树，也可能是指向的前驱节点
+ right指向的是右子树，也可能是指向后继节点

```java
    //中序线索化
    public void threadedNodes(Node node) {
        //如果node ==null，不能线索化
        if (node == null) {
            return;
        }

        //线索化左子树
        threadedNodes(node.getLeft());
        //线索化当前节点
        //处理当前节点的前驱节点
        if (node.getLeft() == null) {
            node.setLeft(pre);
            node.setLeftType(1);
        }
        //处理当前节点的后继
        if (pre != null && pre.getRight() == null) {
            //让前驱节点的右指针指向当前节点
            pre.setRight(node);
            pre.setRightType(1);
        }

        //每次处理一个节点后，让当前节点是下一个节点的前驱节点
        pre = node;
        //线索化右子树
        threadedNodes(node.getRight());

    }

```

#### 线索化二叉树的遍历

+ 一个节点的前一个节点，称为**前驱**节点
+ 一个节点的后一个节点，称为**后继**节点

+ 线索化后，各节点可以通过线形方式遍历，无需递归，这样提高了遍历的效率，遍历的次序应当与线索化（这里是中序）序列一致

```java
    //遍历线索化二叉树
    public void listTreadTree(){
        Node temp =root;
        while (temp!=null){
            //循环的找leftType ==1的点
            while (temp.getLeftType()==0){
                temp =temp.getLeft();
            }
            System.out.println(temp);
            //如果当前节点的右指针指向的是后继节点，就一直输出
            while (temp.getRightType()==1){
                temp =temp.getRight();
                System.out.println(temp);
            }
            //替换这个遍历的节点
            temp =temp.getRight();
        }
    }

```

### 树的实际应用

#### 堆

1. 堆是有以下性质的完全二叉树：每个节点的值都大于或者等于其左右孩子的值（arr[i]>=arr[2\*i + 1]&&arr[i]>=arr[2\*i + 2]），称为**大堆顶**。【没有要求左孩子的值和右孩子的值的大小关系】
2. 每个节点的值都小于或者等于其左右孩子的值，称为**小堆顶**

#### 堆排序

+ 堆排序是一种选择排序，他的最坏、最好、平均时间复杂度均为O(n log n)，他是不稳定排序。

+ 基本思想：
  1. 将待排序列构造成一个大堆顶
  2. 此时，整个序列的最大值就是堆顶根节点
  3. 与其末尾元素进行交换，此时末尾就为最大值
  4. 然后剩下的n-1个元素重新建堆，这样可以得到次小值

+ 步骤：
  1. 构建初始堆（一般升序采用大堆顶，降序采用小堆顶）
  2. 此时我们从最后一个非叶子节点开始，从左至右，从上到下进行调整。
  3. 堆顶与其arr末尾元素进行交换，让末尾元素最大，递归这个过程。

```java
public class Heapsort {
    public static void main(String[] args) {
        int[] arr = {4, 6, 5, 8, 3,1,-4,445};
        heapSort(arr);
    }

    public static void heapSort(int[] arr) {
        System.out.println("这是一个堆排序");
        for(int i = arr.length/2-1;i>=0;i--){
            adjustHeap(arr,i,arr.length);
        }

        for(int j =arr.length-1;j>0;j--){
            swap(arr,0,j);
            adjustHeap(arr,0,j);
        }
        System.out.println(Arrays.toString(arr));
    }

    public static void swap(int[] arr, int i,int j){
        int temp =arr[j];
        arr[j] = arr[i];
        arr[i] =temp;
    }

    //将一个数组二叉树调整称为一个大顶堆

    /**
     * 功能描述: <br>
     * 〈完成将以i对应的非叶子节点调整成为大顶堆〉
     *
     * @Param: arr是待调整的数组
     * @Param: i表示非叶子节点在数组中的索引
     * @Param: len表示对多少个元素继续调整，len是减少的
     * @Date: 2020/3/13 9:07
     */
    public static void adjustHeap(int[] arr, int i, int len) {
        int temp = arr[i];

        // k =i*2+1,k是i的左子节点
        for (int k = i * 2 + 1; k < len; k = k * 2 + 1) {
            if (k + 1 < len && arr[k] < arr[k + 1]) {//说明左子节点的值小于右子节点的值
                k++;//k就指向右子节点
            }
            if(arr[k]>temp){
                //如果子节点大于父节点
                arr[i] =arr[k];//把较大的值给当前节点
                i =k;//i指向k，继续循环比较
            }else{
                break;
            }
        }
        //for结束后，我们已经将i为父节点的树最大值放在了局部最顶
        arr[i] =temp;//将temp放到调整后的位置
    }
}
```

#### 霍夫曼树

+ 带权路径的长度：从根节点到该节点的路径长度与该节点权的乘积
+ 树带权路径的长度（WPL）：所有叶子节点的带权路径之和，权值越大离根节点越近的才是最优二叉树
+ WPL最小的就是霍夫曼树

```java
package huffmantree;

import org.jetbrains.annotations.NotNull;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

public class HuffmanTree {
    public static void main(String[] args) {
        int[] arr ={13,7,8,3,29,6,1};
        Node node =createHuffmanTree(arr);
        System.out.println(node);
        preOrder(node);
    }

    public static Node createHuffmanTree(int[] arr){
        //遍历arr，将每个元素成为node
        //将NOde放入ArrayList中
        List<Node> nodes = new ArrayList<Node>();
        for(int val :arr ){
            nodes.add(new Node(val));
        }
        System.out.println(Arrays.toString(arr));

        while (nodes.size()>1) {
            //       nodes.sort(Node::compareTo);
            Collections.sort(nodes);
            //取出根节点权值最小的两棵二叉树
            //取出
            Node leftNode = nodes.get(0);
            Node rightNode = nodes.get(1);

            //构建新二叉树
            Node parent = new Node(leftNode.val + rightNode.val);
            parent.left = leftNode;
            parent.right = rightNode;

            //从ArrayList删除二叉树
            nodes.remove(leftNode);
            nodes.remove(rightNode);
            //把这个加入
            nodes.add(parent);
        }
        return nodes.get(0);

    }

    public  static  void preOrder(Node root){
        if(root !=null){
            root.preTree();
        }else {
            System.out.println("is empty");
        }
    }
}

//为了让这个支持排序，需要实现一个Comparable接口
class Node implements Comparable<Node>{
    int val;
    Node left;//左子节点
    Node right;//右子节点

    public Node(int val) {
        this.val = val;
    }

    public int getVal() {
        return val;
    }

    //前序遍历
    public void preTree(){
        System.out.println(this.val);
        if(this.left !=null){
            this.left.preTree();
        }
        if(this.right!=null){
            this.right.preTree();
        }
    }

    @Override
    public String toString() {
        return "Node val ="+val +" ";
    }

    @Override
    public int compareTo(@NotNull Node o) {
        //从小到大排序
        return this.val - o.val ;
    }
}

```

#### 霍夫曼编码

+ 数出各字符对应的个数
+ 按照上面出现字符的次数构建一颗霍夫曼树，次数作为权值
+ 根据霍夫曼树，给各个字节编码（前缀编码），向左为0，向右为1

##### 字符串转霍夫曼树

```java
package huffmantree;

import org.jetbrains.annotations.NotNull;

import java.util.*;

public class HuffmanCode {
    public static void main(String[] args) {
        String str = "i better than !";
        byte[] bytes = str.getBytes();
        System.out.println(bytes.length);

        List<Noding> nodes = getNode(bytes);
        System.out.println("Node=[" + nodes + "]");

        System.out.println("huffman tree");
        Noding huffman = creaeHuffmanTree(nodes);
        System.out.println();
        preOrder(huffman);
    }

    /**
     * 功能描述: <br>
     * 〈〉
     *
     * @Param: [bytes] 接受一个字节数组
     * @Return: List的形式
     * @Author: admin
     * @Date: 2020/3/14 9:42
     */
    private static List<Noding> getNode(byte[] bytes) {

        ArrayList<Noding> nodes = new ArrayList<Noding>();
        Map<Byte, Integer> maps = new HashMap<>();
        //遍历bytes，统计byte的次数
        for (byte b : bytes) {
            //没有这个字符数据
            maps.merge(b, 1, Integer::sum);
            // Integer count =count.get(b)
//            if(count ==null){//没有这个字符数据
//                maps .put(b,1);
//            }else {
//                maps.put(b,count+1);
//            }
        }
        //把每一个键值对转成一个Node对象，加入Node集合
        //遍历map
        for (Map.Entry<Byte, Integer> entry : maps.entrySet()) {
            nodes.add(new Noding(entry.getKey(), entry.getValue()));
        }
        return nodes;
    }

    //通过List构建霍夫曼树
    private static Noding creaeHuffmanTree(List<Noding> nodings){
        while (nodings.size()>1){
            Collections.sort(nodings);
            Noding leftNode =nodings.get(0);
            Noding rightNode =nodings.get(1);
            //创建新的二叉树，他只有权值没有data
            Noding parent =new Noding(null,leftNode.weight+rightNode.weight);
            parent.left =leftNode;
            parent.right =rightNode;

            nodings.remove(leftNode);
            nodings.remove(rightNode);

            nodings.add(parent);
        }
        return nodings.get(0);
    }

    //前序遍历
    private  static void preOrder(Noding noding){
        if(noding != null){
            noding.perOrder();
        }else {
            System.out.println("is empty");
        }
    }
}


class Noding implements Comparable<Noding> {
    Byte data;
    int weight;
    Noding left;
    Noding right;

    public Noding(Byte data, int weight) {
        this.data = data;
        this.weight = weight;
    }


    public int getData() {
        return data;
    }

    public void setData(Byte data) {
        this.data = data;
    }

    public int getWeight() {
        return weight;
    }

    public void setWeight(int weight) {
        this.weight = weight;
    }


    @Override
    public int compareTo(@NotNull Noding o) {
        return this.weight - o.weight;
    }

    @Override
    public String toString() {
        return "data=" + data + ",  weight" + weight;
    }

    public void perOrder() {
        System.out.println(this);
        if (this.left != null) {
            this.left.perOrder();
        }
        if (this.right != null) {
            this.right.perOrder();
        }
    }
}

```

##### 树变编码

```java
    //生成霍夫曼树对应的编码表
    //1.把编码存放在Map<Byte,String>形式
    //生成霍夫曼表示，需要拼接路径，定义一个StringBuilder
    static Map<Byte,String> huffmanCodes =new HashMap<Byte, String>();
    static StringBuilder stringBuilder =new StringBuilder();
/**
 * 功能描述: <br>
 * 〈将传入的noding节点的所有叶子节点的Huffman编码得到，然后放入集合〉
 * @Param: [noding, code： 左子节点为0，右子节点为1, stringBuilder]
 * @Author: admin
 * @Date: 2020/3/14 11:04
 */
    private static void getCodes(Noding noding,String code,StringBuilder stringBuilder){
        StringBuilder stringBuilder1 = new StringBuilder(stringBuilder);//用于拼接
        stringBuilder1.append(code);
        if(noding !=null){
            if(noding.data ==null){
                //递归处理
                getCodes(noding.left,"0",stringBuilder1);
                getCodes(noding.right,"1",stringBuilder1);
            }
            else {
                //说明是叶子节点
                huffmanCodes.put(noding.data,stringBuilder1.toString());
            }
        }else {
            System.out.println("is empty");
        }
    }

```

### 二叉排序树

给一个数组，需要高效的排序、增添与删改

```java
package BST;

public class BinarySortTree {
    public static void main(String[] args) {
        int[] arr = {7, 4, 1, 2, 5, 8, 9};
        CreateBST createBST = new CreateBST();
        for (int val : arr) {
            createBST.add(new Node(val));
        }
        createBST.infixOrder();
        System.out.println();
        System.out.println(createBST.search(5));

        createBST.delNode(5);
        createBST.infixOrder();


    }
}

class CreateBST {
    private Node root;

    public void setRoot(Node root) {
        this.root = root;
    }

    public void add(Node node) {
        if (root == null) {
            root = node;
        } else {
            root.add(node);
        }
    }

    public void infixOrder() {
        if (root == null) {
            System.out.println("is empty");
        } else {
            root.infixOrder();
        }
    }

    public int delRightTreeMin(Node node) {
        Node target =node;
        while (target.getLeft()!=null){
            target =target.getLeft();
        }
        int temp =target.getNum();
        delNode(target.getNum());
        return temp;
    }

    public void delNode(int num) {
        if (root == null) {
            return;
        } else {
            Node temp = search(num);
            if (temp == null) {
                return;
            }
            if (root.getLeft() == null && root.getRight() == null) {
                root = null;
                return;
            }

            //找到temp的父节点

            Node parent = searchParent(num);

            if (temp.getLeft() == null && temp.getRight() == null) {
                if (parent.getLeft() != null && num == parent.getLeft().getNum()) {
                    parent.setLeft(null);
                } else if (parent.getRight() != null && num == parent.getRight().getNum()) {
                    parent.setRight(null);
                }
            } else if (temp.getLeft() != null && temp.getRight() != null) {//有左右子树
                int minVal = delRightTreeMin(temp.getRight());
                temp.setNum(minVal);
            } else {//删除只有一颗子树的节点
                //如果temp有左子节点
                if (temp.getLeft() != null) {
                    if (parent.getLeft().getNum() == num) {
                        parent.setLeft(temp.getLeft());
                    } else {
                        parent.setRight(temp.getRight());
                    }
                } else {
                    if (parent.getRight().getNum() == num) {
                        parent.setLeft(temp.getLeft());
                    } else {
                        parent.setRight(temp.getRight());
                    }
                }
            }
        }
    }

    public Node search(int num) {
        if (root == null) {
            return null;
        } else {
            return root.search(num);
        }
    }

    public Node searchParent(int num) {
        if (root == null) {
            return null;
        } else {
            return this.root.search(num);
        }
    }
}

class Node {
    private int num;
    private Node left;
    private Node right;

    public Node(int num) {
        this.num = num;
    }

    public int getNum() {
        return num;
    }

    public void setNum(int num) {
        this.num = num;
    }

    public Node getLeft() {
        return left;
    }

    public void setLeft(Node left) {
        this.left = left;
    }

    public Node getRight() {
        return right;
    }

    public void setRight(Node right) {
        this.right = right;
    }

    @Override
    public String toString() {
        return "num = " + num + " ";
    }

    public void add(Node node) {
        if (node == null) {
            return;
        }
        if (node.num < this.num) {
            if (this.left == null) {
                this.left = node;
            } else {
                this.left.add(node);
            }
        } else {
            if (this.right == null) {
                this.right = node;
            } else {
                this.right.add(node);
            }
        }

    }

    public void infixOrder() {
        if (this.left != null) {
            this.left.infixOrder();
        }
        System.out.println(this);
        if (this.right != null) {
            this.right.infixOrder();
        }
    }

    //查找此节点
    public Node search(int val) {
        Node temp;
        if (val == this.num) {
            return this;
        }
        if (val < this.num && this.left != null) {
            temp = this.left.search(val);
        } else if (val > this.num && this.right != null) {
            temp = this.right.search(val);
        } else {
            System.out.println("没找到");
            return null;
        }
        return temp;
    }

    //查找此节点的父节点
    public Node searchParent(int num) {
        //如果就是当前节点的父节点，就返回
        if ((this.left != null && this.left.num == num) ||
                (this.right != null && this.right.num == num)) {
            return this;
        } else {
            //如果查找的值小于当前节点的值。并且当前节点左子节点不为空
            if (num < this.num && this.left != null) {
                return this.left.searchParent(num);
            }
            //如果查找的值大于当前节点的值。并且当前节点右子节点不为空
            if (num >= this.num && this.right != null) {
                return this.right.searchParent(num);
            } else {
                return null;//没有父节点，比如root节点
            }
        }
    }

}
```

### 平衡二叉树

+ 要是一个数列是基本有序链表，建立一颗二叉排序树，可能会形成一条单链表的样子，这样查询速度会降低。所以构建平衡二叉树
+ 特点：
  + 是空树或者是左右子树的高度差不超过1，并且左右子树都是一颗平衡二叉树。
+ 常见平衡二叉树有AVL，红黑树，替罪羊树，伸展树

+ 需要平衡，所以需要知道子树的高度

```java
    //返回左子树的高度
    public int leftHeight(){
        if(left ==null){
            return 0;
        }else {
            return left.height();
        }
    }

    //返回右子树的高度
    public int rightHeight(){
        if(right ==null){
            return 0;
        }else {
            return right.height();
        }
    }

    //返回以这个节点为root的树的高度
    public int height(){
        return Math.max(left == null ? 0 : left.height(), 
                        right == null ? 0 :right.height()) + 1;//+1是因为本身就一个高度了
    }
```

#### 左旋转

1. Node newNode = new Node(num);//创建一个新的节点newNode，值等于当前根节点的值

2. newNode. left =this. left  //把新节点的左子树设置成为当前节点的左子树

3. newNode. right =right. left //把新节点的右子树设置成为当前节点右子树的左子树

4. this. value =right. value //把当前节点的值设置成为右子节点的值

5. this. right =right. right //把当前节点的右子树设置成为右子树的右子树

6. this. left =newNode//把当前节点的左子树设置为新节点

   ```java
       public void leftRotate() {
           Node newNode = new Node(num);
           newNode.left = this.left;
           newNode.right = this.right.left;
           this.setNum(right.getNum());
           this.right = this.right.right;
           this.left = newNode;
   
       }
   ```

   

#### 右旋转

1.  Node newNode = new Node(num);//创建一个新的节点newNode，值等于当前根节点的值

2.  newNode.right =this.right; //新节点的右子树指向当前节点的左子树

3.  newNode.left = this.left.right;//新节点的左子树设置成为当前节点的左子树的右子树

4.  this.setNum(left.getNum());//把当前节点的值设置成为左子节点的值

5.  this.left =left.left;//当前节点的左子树是左子树的左子树

6.  this.right=newNode;//当前节点的右子树设置成为新节点

   ```java
       public void rightRotate(){
           Node newNode = new Node(num);
           newNode.right =this.right;
           newNode.left = this.left.right;
           this.setNum(left.getNum());
           this.left =left.left;
           this.right=newNode;
       }
   
   ```

#### 双旋转

+ 有时候单旋转不能达成一个AVL树，就只能进行两次旋转调整。
+ 比如当左子树比右子树长2时，本来是需要右旋，但是当前节点的左节点的右子树比左子树要长，所以需要对当前节点的左节点进行左旋之后在操作
+ 同理另一种情况要先左旋再右旋

**完整代码**

```java
package AVL;

public class AVLTree {
    public static void main(String[] args) {
        int[] arr = {1,2,3,4,5,6,7,8,9,10,11,12,13,14,15};
        CreatAVLTree avlTree = new CreatAVLTree();
        for (int value : arr) {
            Node node = new Node(value);
            avlTree.add(node);
        }

        avlTree.infixOrder();
        System.out.println();
        System.out.println(avlTree.leftHeight(avlTree.getRoot()));
        System.out.println(avlTree.rightHeight(avlTree.getRoot()));
        System.out.println(avlTree.getRoot());

    }
}

class CreatAVLTree {
    private Node root;

    public void setRoot(Node root) {
        this.root = root;
    }

    public Node getRoot() {
        return root;
    }

    public void add(Node node) {
        if (root == null) {
            root = node;
        } else {
            root.add(node);
        }
    }

    public void infixOrder() {
        if (root == null) {
            System.out.println("is empty");
        } else {
            root.infixOrder();
        }
    }

    public int rightHeight(Node node) {
        if (node == null) {
            return 0;
        } else {
            return node.rightHeight();
        }
    }

    public int leftHeight(Node node) {
        if (node == null) {
            return 0;
        } else {
            return node.leftHeight();
        }
    }

    public int delRightTreeMin(Node node) {
        Node target = node;
        while (target.getLeft() != null) {
            target = target.getLeft();
        }
        int temp = target.getNum();
        delNode(target.getNum());
        return temp;
    }

    public void delNode(int num) {
        if (root == null) {
            return;
        } else {
            Node temp = search(num);
            if (temp == null) {
                return;
            }
            if (root.getLeft() == null && root.getRight() == null) {
                root = null;
                return;
            }

            //找到temp的父节点

            Node parent = searchParent(num);

            if (temp.getLeft() == null && temp.getRight() == null) {
                if (parent.getLeft() != null && num == parent.getLeft().getNum()) {
                    parent.setLeft(null);
                } else if (parent.getRight() != null && num == parent.getRight().getNum()) {
                    parent.setRight(null);
                }
            } else if (temp.getLeft() != null && temp.getRight() != null) {//有左右子树
                int minVal = delRightTreeMin(temp.getRight());
                temp.setNum(minVal);
            } else {//删除只有一颗子树的节点
                //如果temp有左子节点
                if (temp.getLeft() != null) {
                    if (parent.getLeft().getNum() == num) {
                        parent.setLeft(temp.getLeft());
                    } else {
                        parent.setRight(temp.getRight());
                    }
                } else {
                    if (parent.getRight().getNum() == num) {
                        parent.setLeft(temp.getLeft());
                    } else {
                        parent.setRight(temp.getRight());
                    }
                }
            }
        }
    }

    public Node search(int num) {
        if (root == null) {
            return null;
        } else {
            return root.search(num);
        }
    }

    public Node searchParent(int num) {
        if (root == null) {
            return null;
        } else {
            return this.root.search(num);
        }
    }

}


class Node {
    private int num;
    private Node left;
    private Node right;

    public Node(int num) {
        this.num = num;
    }

    public int getNum() {
        return num;
    }

    public void setNum(int num) {
        this.num = num;
    }

    public Node getLeft() {
        return left;
    }

    public void setLeft(Node left) {
        this.left = left;
    }

    public Node getRight() {
        return right;
    }

    public void setRight(Node right) {
        this.right = right;
    }

    @Override
    public String toString() {
        return "num = " + num + " ";
    }

    //返回左子树的高度
    public int leftHeight() {
        if (left == null) {
            return 0;
        } else {
            return left.height();
        }
    }

    //返回右子树的高度
    public int rightHeight() {
        if (right == null) {
            return 0;
        } else {
            return right.height();
        }
    }

    //返回以这个节点为root的树的高度
    public int height() {
        return Math.max(left == null ? 0 : left.height(), right == null ? 0 : right.height()) + 1;//+1是因为本身就一个高度了
    }

    //左旋
    public void leftRotate() {

        /**
         * 1. 创建一个新的节点newNode，值等于当前根节点的值
         * 2. newNode. left =this. left  //把新节点的左子树设置成为当前节点的左子树
         * 3. newNode. right =right. left //把新节点的右子树色成为当前节点右子树的左子树
         * 4. this. value =right. value //把当前节点的值设置成为右子节点的值
         * 5. this. right =right. right //把当前节点的右子树设置成为右子树的右子树
         * 6. this. left =newNode
         */
        //创建 一个新的节点
        Node newNode = new Node(num);
        newNode.left = this.left;
        newNode.right = this.right.left;
        this.setNum(right.getNum());
        this.right = this.right.right;
        this.left = newNode;

    }

    public void rightRotate(){
        Node newNode = new Node(num);

        newNode.right =this.right;
        newNode.left = this.left.right;
        this.setNum(left.getNum());
        this.left =left.left;
        this.right=newNode;
    }

    public void add(Node node) {
        if (node == null) {
            return;
        }
        if (node.num < this.num) {
            if (this.left == null) {
                this.left = node;
            } else {
                this.left.add(node);
            }
        } else {
            if (this.right == null) {
                this.right = node;
            } else {
                this.right.add(node);
            }
        }

        //添加完一个节点之后，rightheight-leftheight =1 ，左旋转
        if(rightHeight()-leftHeight()>1){
            if(right!=null && right.rightHeight()<right.leftHeight()){
                //先对右子树进行旋转
                right.rightRotate();
            }
            leftRotate();
        }else if(leftHeight()-rightHeight()>1){
            if(left!=null&&left.rightHeight()-left.leftHeight()>1){
                left.leftRotate();
            }
            rightRotate();
        }
    }

    public void infixOrder() {
        if (this.left != null) {
            this.left.infixOrder();
        }
        System.out.println(this);
        if (this.right != null) {
            this.right.infixOrder();
        }
    }

    //查找此节点
    public Node search(int val) {
        Node temp;
        if (val == this.num) {
            return this;
        }
        if (val < this.num && this.left != null) {
            temp = this.left.search(val);
        } else if (val > this.num && this.right != null) {
            temp = this.right.search(val);
        } else {
            System.out.println("没找到");
            return null;
        }
        return temp;
    }

    //查找此节点的父节点
    public Node searchParent(int num) {
        //如果就是当前节点的父节点，就返回
        if ((this.left != null && this.left.num == num) ||
                (this.right != null && this.right.num == num)) {
            return this;
        } else {
            //如果查找的值小于当前节点的值。并且当前节点左子节点不为空
            if (num < this.num && this.left != null) {
                return this.left.searchParent(num);
            }
            //如果查找的值大于当前节点的值。并且当前节点右子节点不为空
            if (num >= this.num && this.right != null) {
                return this.right.searchParent(num);
            } else {
                return null;//没有父节点，比如root节点
            }
        }
    }
}
```

### 多路查找树

节点的度：它下面有几个子节点，度就为几

树的度：树下所有节点的度的最大值就是树的度

#### 二三树

+ 最简单的B树

+ 所有叶子节点都在同一层（B树都满足）
+ 有两个子节点的叫二节点，要么没有子节点，要么两个子节点
+ 有三国子节点的叫三节点，要么没有子节点，要么三个子节点
+ 插入新节点时候，要是直接插入不满足前置条件，就需要拆分节点，先拆上一层，如果上层满了，再拆这一层

#### B树、B+树和B*树

1. 通过B树，降低了树的高度
2. 文件系统把一个节点的大小设置成为一页（4K）

## 并查集

1. 找2个元素是否在同一个集合中

2. 把2个集合合并

   ```java
    public void init(int[] p, int n){//n为集合的元素个数,p
           for (int i = 1; i <=n; i++) {
               p[i] = i;
           }
       }
   
       public int find(int[] p,int x){//返回x的祖宗节点+路径压缩
           //return x==p[x]?x:(p[x]=find(p,p[x]));
           if(p[x]!=x){
               p[x] =find(p, p[x]);
           }
           return p[x];
       }
      public boolean connected(int[] p,int a,int b){
           int ra = find(p,a);
           int rb = find(p,b);
           return ra==rb;
       }
       
       public void union(int[] p,int a,int b){
           int ra = find(p,a);
           int rb =find(p,b);
           if(ra==rb) return;
           p[ra] = rb;//b成为a的父节点
       }
   ```
```
   
若两元素在同一集合中**find(p,a) ==find(p,b)**
   
   把两个集合合并，就是把这一个集合的root成为另一个集合的儿子**p[find(a)] = find(b);**

## 图

+ 线性表局限余一个前驱和一个直接后继的关系
+ 树也只能有一个直接前驱，也就是父节点
+ 当需要表示多对多的关系时候就用到了图

### 图的概念

1. 顶点（vertex）
2. 边（edge)
3. 路径
4. 无向图
5. 有向图
6. 带权图（边有权值）

### 图的表示

二维数组——邻接矩阵

链表——邻接表

#### 图的创建，通过二维数组

​```java
package graph;

import java.lang.reflect.Array;
import java.util.ArrayList;
import java.util.Arrays;

public class Graph {

    private ArrayList<String> vertexList;//存顶点集合
    private int[][] edges;//图对应的邻接矩阵
    private int numOfEdge;//表示边的数目

    public static void main(String[] args) {
        int n=5;//定义节点的个数
        String[] VertexV ={
                "A","B","C","D","E"
        };
        Graph graph =new Graph(n);
        //添加顶点
        for(String val:VertexV){
            graph.insertV(val);
        }
        //添加边
        graph.insertE(0,1,1);
        graph.insertE(0,2,1);
        graph.insertE(2,1,1);
        graph.insertE(1,3,1);
        graph.insertE(4,1,1);

        graph.show();
    }

    //构造器
    public Graph(int n) {
        edges = new int[n][n];
        vertexList = new ArrayList<>(n);
        numOfEdge = 0;
    }

    //返回边的条数
    public int getNumOfEdge() {
        return numOfEdge;
    }

    //返回节点的个数
    public int getNumOfVertex() {
        return vertexList.size();
    }

    //返回节点i对应的数据
    public String getValByIndex(int i) {
        return vertexList.get(i);
    }

    //返回v1，v2的权值
    public int getWeight(int v1, int v2) {
        return edges[v1][v2];
    }

    //插入顶点
    public void insertV(String vertex) {
        vertexList.add(vertex);
    }

    /**
     * @Param: [v1:第一个点的下标, v2:第二个点的下标, weight矩阵的值（无权图为1）]
     */
    //添加边
    public void insertE(int v1, int v2, int weight) {
        edges[v1][v2] = weight;
        edges[v2][v1] = weight;
        numOfEdge++;
    }

    //显示矩阵
    public void show(){
        for(int[] i :edges){
            for(int val:i){
                System.out.printf("%d  ",val);
            }
            System.out.println();
        }

//        for(int[] link:edges){
//            System.out.println(Arrays.toString(link));
//        }
    }
}

```

### 图的深度优先遍历（DFS）

**图的遍历一般是对节点的访问。**

#### 遍历思想

1. 从初始访问节点出发，初始访问节点可能有夺多个邻接节点，深度优先遍历策略是首先访问第一个邻接节点，然后以这个节点作为初始节点，访问他的第一个邻接节点，可以理解为，每次访问完**当前节点**后就访问**当前节点的第一个邻接节点**
2. 可以看出，这样的策略是优先向纵向挖掘深入，而不是对所有邻接节点进行横向访问
3. 这是一个递归的过程

#### 算法步骤

1. 访问初始节点v，并标注v已经访问
2. 查找v的第一个邻接节点w
3. 若w存在将执行4，w不存在就回到第一步，从v的下一个节点继续
4. w未被访问，就进入w，对w进行dfs递归（对w进行123步），如访问了，就查找v的下一个邻接节点。
5. 查找w的下一个邻接节点，到步骤3

**非递归用stack实现**

```java

    //得到第一个邻接节点的下标
    public int getFirstNeighbor(int index) {
        for (int j = 0; j < vertexList.size(); j++) {
            if (edges[index][j] > 0) {
                return j;
            }
        }
        return -1;
    }

    //根据前一个邻接节点下标来获取下一个邻接节点
    public int getNextNeighbor(int v1, int v2) {
        for (int j = v2 + 1; j < vertexList.size(); j++) {
            if (edges[v1][j] > 0) {
                return j;
            }
        }
        return -1;
    }

    //DFS
    public void dfs(boolean[] isVisited, int i) {
        //首先访问此节点
        System.out.print(getValByIndex(i) + "->");
        //设置已经访问过
        isVisited[i] = true;
        //查找i的第一个邻接节点
        int w = getFirstNeighbor(i);
        //若w存在将执行4，
        while (w != -1) {
            //w未被访问，就进入w，对w进行dfs递归（对w进行123步），如访问了，就查找i的下一个邻接节点。
            if (!isVisited[w]) {
                dfs(isVisited, w);
            }
            //如果访问过了
            w = getNextNeighbor(i, w);//i是w的前一个结点，已经访问过了，就查找i的下一个邻接节点。
        }

    }

    //重载dfs
    public void dfs() {
        for (int i = 0; i < getNumOfVertex(); i++) {
            //w不存在就回到第一步，从i的下一个节点继续
            if (!isVisited[i]) {
                dfs(isVisited, i);
            }
        }
    }
```

### 图的广度优先遍历（BFS）

类似与一个分层搜索的过程，广度优先遍历需要使用一个queue了保持访问过的结点的 顺序，以便按这个顺序了分为这些节点的邻接节点

#### 算法步骤

1. 访问初始 节点v并标记v已经访问
2. 节点v入队列
3. 当队列非空时候，继续执行，否则算法结束
4. 出队列，取得头节点u
5. 查找节点u的第一个邻接节点w
6. 若节点u的邻接节点w不存在，就转到步骤3，否则循环执行一下三个步骤：
   + 若w尚未被访问，则访问节点w，并标记已经访问
   + 节点w入队列
   + 查找结点u的继w邻接节点后的下一个邻接节点，设为w，转到步骤6

**非递归用queue实现（这里是非递归）**

```java

    //广度优先遍历
    private void bfs(boolean[] isVisited, int i) {
        int u;//表示队列的头节点对应的下标
        int w;//邻接节点w
        //queue，记录节点的访问顺序
        LinkedList<Integer> queue = new LinkedList<Integer>();
        //输出节点信息
        System.out.print(getValByIndex(i) + "->");
        isVisited[i] = true;
        //节点加入队列
        queue.addLast(i);
        while (!queue.isEmpty()) {
            //取出节点下标
            u = queue.removeFirst();
            //得到第一个邻接节点的下标
            w = getFirstNeighbor(u);
            while (w != -1) {
                if (!isVisited[w]) {
                    System.out.print(getValByIndex(w) + "->");
                    isVisited[w] = true;
                    //入队列
                    queue.addLast(w);
                }
                //访问过了，以u为前驱（或是上一级前驱）找w下一个邻接节点
                w = getNextNeighbor(u, w);
            }
        }
    }

    //遍历所有的节点，对所有的节点进行广度优先
    public void bfs() {
        for (int i = 0; i < getNumOfVertex(); i++) {
            if (!isVisited[i]) {
                bfs(isVisited, i);
            }
        }
    }

```

## 十大算法

### 二分查找非递归

```java
public  static int binarySearch(int[] arr ,int target){
    int l =0;
    int r =arr.length-1;
    while (l<=r){
        int mid = l+(r-l)/2;
        if(arr[mid]==target){
            return mid;
        }else if(arr[mid]>target){
            r =mid-1;//需要向左
        } else {
            l =mid+1;
        }
    }
    return -1;
}
```

### 分治法

1. 分解：将原问题分解为若干个子问题，相互独立，与原问题的形式相同的子问题
2. 解决：子问题规模较小于是直接解决，否则递归的解决各个子问题
3. 合并：将各个子问题的解合并成为原问题的解

#### 例子：汉诺塔

```java
public static void hanoiTower( int num ,char a,char b, char c){
    //如果只有一个盘
    if(num ==1){
        System.out.println("第一个盘"+a+"->"+c);
    }else {
        //m>=2时候，看成两个盘子，最下面的一个，其余上面的合成一个
       // 上面所有A->B
        hanoiTower(num-1,a,c,b);
        //下面的A->C
        System.out.println(num+"盘" +a+"->"+c);
        hanoiTower(num-1,b,a,c);
    }
}
```

#### 例子：循环赛

设有n=2^k个选手参加循环赛，要求设计一个满足以下要求比赛日程表：

1. 每个选手必须与其它n-1个选手各赛一次；
2. 每个选手一天只能赛一次。

对于这个n = 2k 2^k2 
k支队伍，我们把他们一次分为2部分，`ans(n)=ans(n/2)+ans(n/2)`，依次进行到n=2的时候这个时候就可以2个任意打了。


```java
public class Dispatch {
    public static void main(String[] args) {
        int n =8;
        int[][] table =new int[n][n];
        Dispatch.sTable(table,n);

        for(int[] a:table){
            for (int b:a){
                System.out.print(b+" ");
            }
            System.out.println();
        }

    }

    public static void sTable(int[][] table ,int n){
        if(n==1){
            table[0][0]=1;
        }else {
            //填充左上
            int m =n/2;
            sTable(table,m);//m只球队进行循环赛
            //填充右上
            //根据已经填充的左上来确定右上
            for(int i=0;i<m;i++){
                for(int j=m;j<n;j++){
                    table[i][j]=table[i][j-m]+m;
                }
            }

            //根据右上填充左下
            for (int i=m;i<n;i++){
                for(int j=0;j<m;j++){
                    table[i][j]= table[j][i];
                }
            }

            //根据左上填充右下
            for(int i=m;i<n;i++){
                for(int j=m;j<n;j++){
                    table[i][j]=table[i-m][j-m];
                }
            }
        }
    }
}
```

### 动态规划

1. 动态规划的核心思想是：将大问题划分成小问题进行解决
2. 与分治法类似，其基本思想是先解决子问题然后从这些子问题得到原问题的解
3. 与分治不同的是，适合用动态规划解决的问题往往不是相互独立的（下一个字阶段的解是建立在上一个解的基础之上的
4. 动态规划可以通过填表的方式来逐步得解，以此得到最优解

#### 例子：背包问题

```伪代码
1. v[i][0]=v[0][j]=0;
2.if(w[i]>j):
	v[i][j] =v[i-1][j];
3.else if(w[i]<=j):
	v[i][j] = max(v[i-1][j],v[i]+v[i-1][j-w[i]]);
	//v[i-1]:上一个单元格装入的最大值
	//v[i]：表示当前商品的价值
	//v[i-1][j-w[i]]：装入i-1(上一行中)，找到到j-w[i]的最大值放进去
```

```java
import java.util.Arrays;

public class KnapsackProblem {
    public static void main(String[] args) {
        int[] w = {1, 4, 3};
        int[] val = {1500, 3000, 2000};
        int m = 4;//背包容量
        int n = val.length;
        int[][] table = new int[n + 1][m + 1];//0要占一行
        //为了记录商品的情况，定义一个二维数组
        int[][] bag = new int[n + 1][m + 1];//0要占一行

//        for(int i =0;i<table.length;i++){
//            for(int j =0;j<m+1;j++){
//                table[i][j]=1;
//            }
//        }

        for (int i = 0; i < m + 1; i++) {
            table[0][i] = 0;

        }
        for (int j = 0; j < n + 1; j++) {
            table[j][0] = 0;
        }

        //根据前面的公式了迭代处理
        for (int i = 1; i < table.length; i++) {//不处理0行
            for (int j = 1; j < table[0].length; j++) {
                if (w[i - 1] > j) {//从从1开始的，指向w的第一项要-1
                    table[i][j] = table[i - 1][j];
                } else {//从从1开始的，指向w的第一项要-1
                    // table[i][j] = Math.max(table[i - 1][j], val[i - 1] + table[i - 1][j - w[i - 1]]);
                    if (table[i - 1][j] > val[i - 1] + table[i - 1][j - w[i - 1]]) {
                        table[i][j] = table[i - 1][j];

                    } else {
                        table[i][j] = val[i - 1] + table[i - 1][j - w[i - 1]];
                        bag[i][j] = 1;
                    }
                }
            }
        }


        for (int[] a : table) {
            System.out.println(Arrays.toString(a));
        }

        int j = bag[0].length-1;
        for (int i = bag.length - 1; i > 0; i--) {

            if (bag[i][j] == 1) {
                System.out.printf("第%d个放入", i);
                j-=w[i-1];//从后往前，每加一个商品背包就减去相应的容量
            }
        }
    }
}
```

### KMP

#### 暴力破解

```java
//暴力匹配
public static int violenceMatch(String a, String b) {
    int j = 0;
    int i = 0;
    char[] content = a.toCharArray();
    char[] target = b.toCharArray();
    //    System.out.println(content[]);
    while (i < content.length && j < target.length) {
        if (target[j] == content[i]) {
            j++;
            i++;
        } else {
            i = i - (j - 1);
            j = 0;
        }

    }
    if (j == target.length ) {
        return i-j;
    }else return -1;
}
```

#### KMP字符串查找算法

KMP利用之前判断过的信息，通过一个next数组，保存模式串前后最长公共子序列长度，每次回溯时，通过next数组找到前面匹配过的位置，节省了大量的时间。

1. 先根据前后缀，找出匹配串的部分匹配表，建立next数组

2. 根据next数组移动子串的位置，母串指针是一直向下的，不像暴力算法需要回溯母串指针。

   ```java
   //kmp模版
   //求模式串的Next数组
   //在next[i] =j ，表示在模式串中，p[1,j] = p[i-j+1]
   //s[] 是长文本，p[]是模式串，n是s是长度，m是p的长度
   for(int i = 2,j=0;i<=m;i++){
       while(j>0&&p[i]!=p[j+1]){
           j = next[j];
       }
       if(p[i]==p[i+1]) j++;
       next[i]=j;
   }
   
   //匹配
   for(int i =1,j=0;i<=n;i++){
       while(j>0&&s[i]!=p[j+1]){
           j = next[j];
       }
           if(s[i] ==p[j+1]) j++;
           if(j==m){
               j = next[j];
               //匹配成功之后的逻辑
           }
       }
   }
   ```
   
   
   
   ```java
       //kmp搜索
       //return -1就是没有匹配到的，否则返回第一个位置
       public static int kmpS(String a,String b){
           int[] next = next(b);
           //遍历
           for(int i =0,j=0;i<a.length();i++){
               //不相等时候
               while (j>0&&a.charAt(i)!=b.charAt(j)){
                   j =next[j-1];
               }
   
               if(a.charAt(i)==b.charAt(j)){
                   j++;
               }
   
               if(j==b.length()){
                   return i-j+1;
               }
           }
           return -1;
       }
       //获取子串的部分匹配值
       public static int[] next(String dest){
           int[] next =new int[dest.length()];
           next[0]=0;//只有一个长度的字符串，部分匹配的值就是0
           for(int i = 1,j=0;i<dest.length();i++){
               //当dest.charAt(i)!=dest.charAt(j)时候，就需要从next[j-1]获取新的j
               //同时要满足有dest.charAt(i)==dest.charAt(j)是退出
               while (j>0 && dest.charAt(i)!=dest.charAt(j)){
                   j =next[j-1];
   
               }
               if(dest.charAt(i)==dest.charAt(j)){
                   j++;
               }
               next[i]=j;
           }
           return next;
       }
   }
   ```

### 贪心算法

```java
package Greedy;

import java.util.*;

public class Greedy {
    public static void main(String[] args) {
        Map<String, HashSet<String> > video = new HashMap<>();
        HashSet<String> strings1 = new HashSet<>();
        strings1.add("北京");
        strings1.add("上海");
        strings1.add("天津");

        HashSet<String> strings2 = new HashSet<>();
        strings2.add("广州");
        strings2.add("上海");
        strings2.add("深圳");

        HashSet<String> strings3 = new HashSet<>();
        strings3.add("成都");
        strings3.add("上海");
        strings3.add("杭州");

        HashSet<String> strings4 = new HashSet<>();
        strings4.add("上海");
        strings4.add("天津");

        HashSet<String> strings5 = new HashSet<>();
        strings5.add("杭州");
        strings5.add("大连");

        video.put("k1",strings1);
        video.put("k2",strings2);
        video.put("k3",strings3);
        video.put("k4",strings4);
        video.put("k5",strings5);

        Set<String> allAreas = new HashSet<>();
        allAreas.add("北京");
        allAreas.add("天津");
        allAreas.add("上海");
        allAreas.add("杭州");
        allAreas.add("大连");
        allAreas.add("成都");
        allAreas.add("深圳");
        allAreas.add("广州");

        //创建一个ArrayList，存放电台集合
        ArrayList<String> select = new ArrayList<>();

        //定义一个临时的集合，在遍历的过程在，存放遍历过程中的覆盖地区和没覆盖的交集
        HashSet<String> tempSet = new HashSet<>();

        //定义maxKey，保存有最大覆盖面积的一个电台，如果maxkey!=null，就把这个加入select集合
        String maxkey;
        while (allAreas.size()!=0){
            maxkey=null;
            //遍历video
            for(String key :video.keySet()){
                tempSet.clear();
                //这个可以可以覆盖的地区
                HashSet<String> areas = video.get(key);
                tempSet.addAll(areas);
                //求出tempset与allArea的交集,交集给tempset
                tempSet.retainAll(allAreas);
                //如果这个集合包含未覆盖的地区数量比maxkey指向的地区还多，就重置maxkey
                //tempSet.size()>video.get(maxkey).size()体现出贪心算法
                if(tempSet.size()>0&&
                        (maxkey==null||tempSet.size()>video.get(maxkey).size())){
                    maxkey =key;
                }
            }
            if(maxkey!=null){
                select.add(maxkey);
                //把maxkey指向的地区还从allArea去掉
                allAreas.removeAll(video.get(maxkey));
            }
        }
    }
}
```



## 最短路问题

### 单源最短路

#### dijkstraO（n^2)（稠密图中使用）

> s已经确定最短路程的点 ，t，不在是中的距离最近的点

1. dist[1]=0,dist[v] =+Max;

2. for v :0~n

   选择不在s中的，距离最近的点

   s <-t

   用t更新s集合中其他点到t的距离（dist[x]>dist[t]+w)

```java
import java.util.Arrays;
import java.util.Scanner;

/**
 * @author atom.hu
 * @version V1.0
 * @Package graph
 * @date 2020/9/15 9:38
 */
public class Dijkstra {
    /**
     * m,边的数量
     * n，点的数量
     * dist[] 源点到i点的距离
     * vis[] t点是否已经在集合中
     * g[][] 使用临街矩阵来存储稠密图
     */
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int m = sc.nextInt();
        int[][] g = new int[n + 10][n + 10];
        int[] dis = new int[n+10];
        boolean[] vis = new boolean[n+10];
        Arrays.fill(vis,false);
        Arrays.fill(dis, 10000);
        for (int[] gl : g
        ) {
            Arrays.fill(gl,10000);
        }
        for (int i = 0; i < m; i++) {
            int a = sc.nextInt();
            int b = sc.nextInt();
            int c = sc.nextInt();
            g[a][b] = Math.min(g[a][b],c);//去重边
        }
        int t = dijkstra(g,dis,vis,n,m);
        System.out.println(t);
    }

    private static int dijkstra(int[][] g, int[] dist, boolean[] vis, int n, int m){
        dist[1] =0;//第一个是最小值
        for (int i = 0; i < n; i++) {
            int t = -1;
            for (int j = 1; j <=n ; j++) {
                if((!vis[j])&&(t==-1||dist[t]>dist[j])){//选点，找出最小边
                    t =j;
                }
            }
            vis[t] = true;
            for (int j = 1; j <=n ; j++) {
                dist[j] =Math.min(dist[j],dist[t]+g[t][j]);//更新新的值
            }
        }
        if(dist[n]==10000) return -1;
        //System.out.println(Arrays.toString(dist));
        return dist[n];
    }

}
```

#### dijkstraO（n^2)（稀疏图中使用）

> 使用堆优化的算法，图的存储选择是邻接表

#### Bellman-fordO(nm)

> 可以求负环，例题：经过不超过k条边的最短路径

迭代n次，每一次循环所有边，a，b，w，（有一条a->b的方式，权值为w），dist[b] = min（dit[b] ,dis[a]+w])

 

#### SPFA O(m)

> 对dist[b] =min(dist[b],dist[a]+w)优化
>
> 每次先把起点放到队列中
>
> 当队列不为空时，：
>
> ​			取出队头 q.pop()
>
> ​			遍历t的所有出边 
>
> ​				t——》b
>
> ​				queue 《—— b
>
> ​			（更新过谁，我在拿来更新别人，只有这个点变小了，之后的点才会变小）
>
> ​				    

### 多源汇最短路

#### Floyd O(n^3)

```java
package graph;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Scanner;

/**
 * @author atom.hu
 * @version V1.0
 * @Package graph
 * @date 2020/9/16 9:53
 */
public class Floyd {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n =scanner.nextInt();
        int m =scanner.nextInt();
        int req = scanner.nextInt();
        int[][] graph = new int[n+10][n+10];
        for (int i = 1; i <=n; i++) {
            for (int j = 1; j <=n ; j++) {
                if(i==j){
                    graph[i][j]=0;
                }else {
                    graph[i][j] = 1000000;
                }
            }
        }
        for (int i = 1; i <=m; i++) {
            int x =scanner.nextInt();
            int y = scanner.nextInt();
            int z = scanner.nextInt();
            graph[x][y] = Math.min(graph[x][y],z);
        }
        //Floyd核心算法
        floyd(graph,n);
        ArrayList<Integer> res = new ArrayList<>();
        for (int i = 0; i < req; i++) {
            int x =scanner.nextInt();
            int y = scanner.nextInt();
            res.add(graph[x][y]);
        }
        for (int ans: res) {
            if(ans>=800000){
                System.out.println("impossible");
            }else {
                System.out.println(ans);
            }
        }
    }

    public static void  floyd(int[][] graph,int n){
        for (int k = 1; k <=n ; k++) {
            for (int i = 1; i <=n ; i++) {
                for (int j = 1; j <=n; j++) {
                    graph[i][j] = Math.min(graph[i][j],graph[i][k]+ graph[k][j]);
                }
            }
        }
    }

}
```



### Prim算法

> 先迭代n次，每次找出不在集合中距离集合中最小的点（第一个for），要是找不到，直接return max，然后更新最短路的长度，之后更新每个点到集合中的最短距离（第二个for，dist[j] = Math.min(dist[j],graph\[t\]\[j\]);）[dijkstral 这里是dist[j] = Math.min(dist[j],graph\[t\]\[j\])+dis[t];]

```java
package graph;

import java.util.Arrays;
import java.util.Scanner;

/**
 * @author atom.hu
 * @version V1.0
 * @Package graph
 * @date 2020/9/16 11:32
 */
public class Prim {
    public static void main(String[] args) {
        Scanner sc= new Scanner(System.in);
        int n = sc.nextInt();
        int m =sc.nextInt();
        int[][] graph = new int[n+10][n+10];
        int[] dis = new int[n+10];
        boolean[] vis = new boolean[n+10];
        Arrays.fill(vis,false);
        Arrays.fill(dis, 1000000);
        for(int[] l :graph){
            Arrays.fill(l,1000000);
        }
        for (int i = 0; i < m; i++) {
            int x =sc.nextInt();
            int y = sc.nextInt();
            int z = sc.nextInt();
            graph[x][y] = graph[y][x]=Math.min(graph[x][y],z);
        }
        int t = prim(graph,n,dis,vis);
        if(t==1000000){
            System.out.println("impossible");
        }else {
            System.out.println(t);
        }
    }

    private static int prim(int[][] graph,int n ,int[] dist ,boolean[] vis) {
        //dist[1] =0;
        int res = 0;//存的是所有边的最小生成树之和
        for (int i = 0; i < n; i++) {
            int t =-1;
            for (int j = 1; j <=n ; j++) {
                if(!vis[j]&&(t==-1 || dist[t]>dist[j])){
                    t = j;//结束时候，t就存的是当前最小的点了
                }
            }
            if(i>0&&dist[t]==1000000){//如果不是第一个点，并且所有点的最短路程是max的话，说明没有联通的的点
                return 1000000;
            }
            if(i>0){//先累计，再更新，防止负权自环
                res += dist[t];
            }
            for (int j = 1; j <=n; j++) {
                dist[j] = Math.min(dist[j],graph[t][j]);
            }
            vis[t] =true;
        }
        return res;
    }
}

```



```java
import java.util.Arrays;

public class Prim {
    public static void main(String[] args) {
        char[] data = new char[]{'a', 'b', 'c', 'd', 'e', 'f', 'g'};
        int v = data.length;
        MGraph graph = new MGraph(v);
        MinTree minTree = new MinTree();
        minTree.createGraph(graph, v, data);
        graph.insertE(0, 1, 5);
        graph.insertE(0, 2, 7);
        graph.insertE(0, 6, 2);
        graph.insertE(1, 3, 9);
        graph.insertE(1, 6, 3);
        graph.insertE(2, 4, 8);
        graph.insertE(4, 5, 5);
        graph.insertE(4, 6, 4);
        graph.insertE(5, 6, 6);
        graph.insertE(3, 5, 4);
        minTree.show(graph);

        minTree.prim(graph, 0);
    }

}


class MinTree {
    //图的邻接矩阵
    public void createGraph(MGraph graph, int verxs, char[] data) {
        for (int i = 0; i < verxs; i++) {
            graph.data[i] = data[i];
            for (int j = 0; j < verxs; j++) {
                graph.insertE(i, j, 10000);
            }
        }
    }

    public void show(MGraph graph) {
        for (int[] link : graph.weight) {
            System.out.println(Arrays.toString(link));
        }
    }

    public void prim(MGraph graph, int v) {
        //表示已经访问过
        int[] visited = new int[graph.verxs];
        visited[v] = 1;
        //h1,h2记录两个顶点的下标
        int h1 = -1;
        int h2 = -1;
        int count = 0;
        int minWeight = 10000;
        for (int k = 1; k < graph.verxs; k++) {//表示已经访问过的节点（已加入图）
            for (int i = 0; i < graph.verxs; i++) {//表示未被访问过的节点（未加入图）
                for (int j = 0; j < graph.verxs; j++) {
                    if (visited[i] == 1 && visited[j] == 0 && graph.weight[i][j] < minWeight) {
                        minWeight = graph.weight[i][j];
                        h1 = i;
                        h2 = j;
                    }
                }
            }
            System.out.println(graph.data[h1] + "," + graph.data[h2] + "权值：" + minWeight);
            visited[h2] = 1;//把找到的放入visited
            count += minWeight;
            minWeight = 10000;
        }
        System.out.println("count is:" + count);
    }

}


class MGraph {
    int verxs;//节点个数
    char[] data;//节点数据
    int[][] weight;//边

    public MGraph(int verxs) {
        this.verxs = verxs;
        data = new char[verxs];
        weight = new int[verxs][verxs];
    }

    public void insertE(int v1, int v2, int weights) {
        weight[v1][v2] = weights;
        weight[v2][v1] = weights;
    }
}
```

### Kruskal

给你一个points 数组，表示 2D 平面上的一些点，其中 points[i] = [xi, yi] 。

连接点 [xi, yi] 和点 [xj, yj] 的费用为它们之间的 曼哈顿距离 ：|xi - xj| + |yi - yj| ，其中 |val| 表示 val 的绝对值。

请你返回将所有点连接的最小总费用。只有任意两点之间 有且仅有 一条简单路径时，才认为所有点都已连接。

来源：力扣（LeetCode）

```java
import java.util.ArrayList;
import java.util.Collection;
import java.util.Collections;

/**
 * @author atom.hu
 * @version V1.0
 * @Package weekendExam
 * @date 2020/9/13 10:42
 */
public class Solution {
     int[] f ;

    public int find(int x) {
        return f[x] == x ? x :(f[x]= find(f[x]));//路径压缩
    }

    public int minCostConnectPoints(int[][] points) {
        int h = points.length;
        f=new int[h+1];
        for (int i = 0; i < f.length; i++) {
            f[i]=i;
        }
        ArrayList<Edge> edges = new ArrayList<>();
        for (int i = 0; i < h; i++) {
            for (int j = i + 1; j < h; j++) {
                int len = Math.abs(points[i][0] - points[j][0]) + Math.abs(points[i][1] - points[j][1]);
                if (len != 0) {
                    edges.add(new Edge(i, j, len));
                }
            }
        }
        int ans =0;
        Collections.sort(edges);
    
        for(Edge e:edges){
            int x=e.getX();
            int y =e.getY();
            int len = e.getLen();
            //System.out.println(x+" "+y+" "+len);
            if(find(x)==find(y)) {
                continue;
            }
            ans+=len;
            f[find(x)] = find(y);
        }
       // System.out.println(Arrays.toString(f));
        return ans;
    }
}

class Edge implements Comparable<Edge> {
    private int x;
    private int y;
    private int len;

    public int getX() {
        return x;
    }

    public void setX(int x) {
        this.x = x;
    }

    public Edge(int x, int y, int len) {
        this.x = x;
        this.y = y;
        this.len = len;
    }

    public int getY() {
        return y;
    }

    public void setY(int y) {
        this.y = y;
    }

    public int getLen() {
        return len;
    }

    public void setLen(int len) {
        this.len = len;
    }

    @Override
    public int compareTo(Edge o) {
        return Integer.compare(this.len, o.len);
    }
}
```

### Floyd

与Dij算法的区别是：

Dijkstra算法通过选定被访问的顶点，求出出发点与其他点的最短路径

Floyd中每一个顶点都是出发访问点，需要把每一个顶点看作被访问顶点。

每一轮循环中，把这个顶点作为中间顶点的情况都进行遍历，就会得到更新距离表和前驱关系

```java
import java.lang.reflect.Array;
import java.util.Arrays;

public class Floyd {
    public static void main(String[] args) {
        char[] data = new char[]{'a', 'b', 'c', 'd', 'e', 'f', 'g'};
//        int[][] matrix =new int[data.length][data.length];
        char[] vertex = new char[data.length];
        Graph graph = new Graph(data.length, data);
        Floyd floyd = new Floyd();
        floyd.createGraph(graph, data.length, data);
        graph.insertE(0, 1, 5);
        graph.insertE(0, 2, 7);
        graph.insertE(0, 6, 2);
        graph.insertE(1, 3, 9);
        graph.insertE(1, 6, 3);
        graph.insertE(2, 4, 8);
        graph.insertE(4, 5, 5);
        graph.insertE(4, 6, 4);
        graph.insertE(5, 6, 6);
        graph.insertE(3, 5, 4);
        graph.show();
        graph.floyd();
        graph.show();
    }

    public void createGraph(Graph graph, int verxs, char[] data) {
        for (int i = 0; i < verxs; i++) {
            graph.vertex[i] = data[i];
            for (int j = 0; j < verxs; j++) {
                graph.insertE(i, j, 1000);
                if (i == j) {
                    graph.insertE(i, j, 0);
                }
            }
        }
    }

}

class Graph {
    char[] vertex;
    int[][] dis;//保存从各个顶点出发到其他顶点的距离，最后的结果也在该数组
    int[][] pre;//保存到达目标节点的前驱节点

    public Graph(int length, char[] vertex) {
        this.vertex = vertex;
        this.pre = new int[length][length];
        //对pre初始化,主要是存放前驱节点的下标
        this.dis = new int[length][length];
        for (int i = 0; i < length; i++) {
            Arrays.fill(pre[i], i);
        }
    }

    public void show() {
        for (int[] a : dis) {
            for (int i : a) {
                System.out.printf("[\t%d]", i);
            }
            System.out.println();
        }

        for (int[] a : pre) {
            for (int i : a) {
                System.out.print(vertex[a[i]] + "    ");
            }
            System.out.println();
        }
        System.out.println();
    }

    public void insertE(int v1, int v2, int weights) {
        dis[v1][v2] = weights;
        dis[v2][v1] = weights;
    }

    //Floyd
    public void floyd() {
        int len;
        for (int k = 0; k < dis.length; k++) {//对中间顶点的遍历，k五顶点下标
            for (int i = 0; i < dis.length; i++) {//从i顶点开始
                for (int j = 0; j < dis.length; j++) {//到达k顶点
                    len = dis[i][k] + dis[k][j];
                    if (len < dis[i][j]) {//当这样小于直连距离
                        dis[i][j] = len;//更新距离
                        pre[i][j] = pre[k][j];//更新前驱节点
                    }
                }
            }
        }
    }
}
```

### 二分图

#### 判断是否为二分图 染色法O(n+m)

> **定义：可以把一个图划分到集合两边，集合内部没有边，**

> 一个图是二分图，当且仅当图中不含奇数环，

```java

import java.io.IOException;
import java.util.Arrays;
import java.util.Scanner;

/**
 * @author atom.hu
 * @version V1.0
 * @Package graph
 * @date 2020/9/16 17:22
 */
public class BC {
    static int n;
    static int m;
    static int N = 100010;
    static int M = 200010;
    static int[] h = new int[N];
    static int[] e = new int[M];
    static int[] ne = new int[M];
    static int idx = 0;
    static int[] color = new int[N];//共1和2两种不同的颜色
    static boolean[] st = new boolean[N];
    public static void add(int a,int b)
    {
        e[idx] = b;
        ne[idx] = h[a];
        h[a] = idx ++;
    }
    //dfs(u,c)表示把u号点染色成c颜色，并且判断从u号点开始染其他相连的点是否成功
    public static boolean dfs(int u,int c)
    {
        color[u] = c;
        for(int i = h[u];i != -1;i = ne[i])
        {
            int j = e[i];
            if(color[j] == 0)
            {
                if(!dfs(j,3 - c)) return false;
            }
            else if(color[j] == c) return false;//颜色重复
        }
        return true;
    }
    public static void main(String[] args) throws IOException {
        Scanner sc = new Scanner(System.in);

        n = sc.nextInt();
        m = sc.nextInt();
        Arrays.fill(h, -1);
        while(m -- > 0)
        {
            int a = sc.nextInt();
            int b = sc.nextInt();
            add(a,b);
            add(b,a);
        }
        boolean flag = true;//标记是否染色成功
        for(int i = 1;i <= n;i++)
        {
            //若未染色
            if(color[i] == 0)
            {
                if(!dfs(i,1))
                {
                    flag = false;
                    break;
                }
            }
        }
        if(flag) System.out.println("Yes");
        else System.out.println("No");
    }
}

```



#### 匈牙利算法 O(nm) 

> 返回一个二分图中，**匹配**（每2个节点只有一条边连接）边数量最多是多少

```java
package graph;

import java.util.Arrays;
import java.util.Scanner;

/**
 * @author atom.hu
 * @version V1.0
 * @Package graph
 * @date 2020/9/16 19:35
 */
public class BinaryCompare {
    static int n1, n2, m;
    static int N = 510, M = 100010, index = 0;
    static int[] h = new int[N];
    static int[] e = new int[M];
    static int[] next = new int[M];
    static int[] match = new int[N];
    static boolean[] st = new boolean[N];

    public static boolean find(int x) {
        for (int i = h[x]; i != -1; i = next[i]) {
            int j = e[i];
            if (!st[j]) {//这个点没有被匹配过
                st[j] = true;
                if (match[j] == 0 || find(match[j])) {
                    match[j] = x;
                    return true;
                }
            }
        }
        return false;
    }

    public static void add(int a, int b) {
        e[index] = b;
        next[index] = h[a];
        h[a] = index++;
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        n1 = sc.nextInt();
        n2 = sc.nextInt();
        m = sc.nextInt();
        Arrays.fill(h, -1);
        for (int i = 0; i < m; i++) {
            int a = sc.nextInt();
            int b = sc.nextInt();
            add(a, b);
        }
        int res = 0;
        for (int i = 1; i <= n1; i++) {
            Arrays.fill(st, false);
            if (find(i)) {
                res++;
            }
        }
        System.out.println(res);
    }
}

```
