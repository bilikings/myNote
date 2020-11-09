## TopK问题

你有一个数据榜单，你需要给用户显示rank排名前100的玩家。可以怎么样操作呢？

+ 直接排序，取top100
+ 堆排序，取top100
+ 快排的思想，使用划分函数取100

那么他们分别有什么缺陷呢？

+ 直接排序，时间复杂度O（nlogn），空间复杂度O（n），所以当用户过多的时候，一块内存空间读不进，直接OOM异常
+ 堆排序，构建小根堆，维护一个大小为K的堆，此时堆顶元素是这k个元素中最小值。所以新加入的值要是比堆顶大，就把这个堆顶pop出来，把这个新的值加入。由于维护一个堆的操作时间是O（logn）的，所以总空间复杂度是O（nlogn），空间复杂度是O（k），Java可以通过优先队列来实现，不需要自己写一个堆。
+ 使用划分函数进行快排，由于快排的特性，总能让flag左边的值大于于flag，flag右边的值小于flag（递减序列）。所以只需要判断flag的index在k的那一边就OK，index>k，就只需要排序[start，index-1]左半部分，反之排序[index+1,end]的部分，就很容易写出递归方案了，最好时间复杂度O（n），要是需要top1，那就是完全的进行一次快排时间复杂度：O（nlogn），由于需要修改数组，那自然空间复杂度为O（n）

#### 堆排序topK代码(使用优先队列PriorityQueue)

```java

public class PQTopK {
    public static int[] getTopK(int[] arr,int k){
        //（不指定Comparator时默认为最小堆），通过传入自定义的Comparator函数可以实现大顶堆。
        PriorityQueue<Integer> pq= new PriorityQueue<>();

        for (int i = 0; i < k; i++) {
            pq.add(arr[i]);
        }
        for (int i = k; i <arr.length ; i++) {
            if(!pq.isEmpty() && arr[i]>pq.peek()){
                pq.poll();
                pq.add(arr[i]);
            }
        }
        //现在堆中的数据就是前k个元素
        int[] ans = new int[pq.size()];
        for(int i=0;!pq.isEmpty()&&i<ans.length;i++){
            ans[i] = pq.poll();
        }
        //此时ans是正序的，第K大的是arr[0]
        return ans;
    }

    public static void main(String[] args) {
        int[] arr = {2,1,7,3,4,9,8,5,6};
        int k =5;
        final int[] topK = getTopK(arr, k );
        System.out.println(Arrays.toString(topK));
    }
}
```

#### 快排思想实现topK

```java
public class QsortTopK {
 /**
     * 取最小的k个数 
     */
    public static int[] getTopKByPartition(int[] arr, int k) {
        if (arr == null || arr.length <= 0 || k <= 1) {
            return null;
        }
        
        int size = arr.length;
        int target = k;
        
        int low = 0;
        int high = size - 1;
        
        int mid = getMid(arr, low, high);
        while (mid != target) {
            if (mid < target) {
                mid = getMid(arr, mid + 1, high);
            } else {
                mid = getMid(arr, low, mid - 1);
            }
        }
        int[] ret = new int[target];
        System.arraycopy(arr, 0, ret, 0, target);
        return ret;
    }
 
    /**
     * 快排思想-一趟排序（划分函数）
     */
    private static int getMid(int[] arr, int low, int high) {
        int base = arr[low];
        while (low < high) {
            // 判断条件必须加=场景，为<= 不能为<，否则数组中有相同数据时，会一直循环
            while (low < high && base >= arr[high]) {
                high--;
            }
            arr[low] = arr[high];
            
            // 判断条件必须加=场景，为>= 不能为>，否则数组中有相同数据时，会一直循环
            while (low < high && base <= arr[low]) {
                low++;
            }
            arr[high] = arr[low];
        }
        arr[low] = base;
        return low;
    }


    public static void main(String[] args) {
        int[] arr = {3, 9, 5, 2, 6};
        int k =3;
        System.out.println(Arrays.toString(getTopKByPartition(arr, k)));
        
    }
}
```



 **用堆来实现TopK 和 用快排来实现TopK 的效率对比：**

　　　　　　　　　　　　　　　　　　“小顶堆”　　　　|　　　　“快排”

　　　　数据量为100万+10时：　　　　11毫秒　　　　|　　　　124毫秒

　　　　数据量为1000万+10时：　　　　28毫秒　　　  |　　　　1438毫秒

#### 写在后面

要是是做游戏排行榜，大家的战力分布其实是符合正态分布的，所以找TopK时，可以根据数据分布，选择合适的西格玛值，就能大致的估计出第K个值，来优化base的取值，让其时间尽量靠近最佳时间。