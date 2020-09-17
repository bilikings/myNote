# 动态规划

状态表示（集合 属性（Max,Min,Count））

状态计算：不从不漏，化整为零

划分依据：“寻找最后一个不同点”

选择（背包模型）

序列（子序列模型）

状态压缩模型

树形dp模型

区间dp模型

单调对列优化模型

斜率优化模型

#### 01背包

```java
dp[i][j] = Math.max(dp[i-1][j],dp[i-1][j-W[i]]+v[i]);
```

#### 完全背包

```java
dp[i][j] = Math.max(dp[i-1][j],dp[i][j-w[i]]+v[i]);
```

#### 最长公共子序列

![image-20200820224913354](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200820224913354.png)

## 背包九讲