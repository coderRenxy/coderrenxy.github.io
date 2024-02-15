
---
title: "LeetCode 动态规划"
date: 2022-07-26T05:23:42+08:00
author: ["小任同学"]
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
lastmod: 2022-07-26T23:33:37+08:00	#更新文章的时候手动改一下时间就可以
description: 只包含 01背包、完全背包。
tags: 
- 算法
---

## 01背包

### 509. 斐波那契数

```Java
class Solution {
    public int fib(int n) {
        if(n<2) return n;
        int[] dp = new int [n+1];
        dp[0] = 0;
        dp[1] = 1;
        for(int i=2;i<=n;i++)
            dp[i]=dp[i-1]+dp[i-2];
        return dp[n];
    }
}
```

<hr>

### 70.爬楼梯

**思路**：走第i层时，从i-1到i只有一种方法，从i-2到i也只有一种方法，所以从0到i-1到i有dp[i-1]种方法，从0到i-2到i有dp[i-2]种方法。所以从0到dp[i]有dp[i-2]+dp[i-1]种方法，所以dp[i]=dp[i-1]+dp[i-2]。其实也能转换为完全背包来做，物品分别是1，2。都可以无限次用。

```Java
class Solution {
    public int climbStairs(int n) {
        if(n<=2) return n;
        int [] dp = new int[n+1];
        dp[1]=1;dp[2]=2;
        for(int i=3;i<=n;i++)
            dp[i]=dp[i-1]+dp[i-2];
        return dp[n];
    }
}
```

<hr>

### 746. 使用最小花费爬楼梯

**思路**：主要是dp数组如何构建。dp数组的含义是，从 i 跳走必须花 dp[i] 的钱，所以最后是只有三个数的话，要跳出这三个数：dp[2]=( dp[2-1] , dp[2-2] )+ cost[i] ；dp 数组初始化好直接返回就行。

```Java
class Solution {
    public int minCostClimbingStairs(int[] cost) {
        if(cost == null || cost.length == 0)   return 0;
        if(cost.length == 1)   return cost[0];
        int[] dp = new int[cost.length];
        dp[0] = cost[0];
        dp[1] = cost[1];
        for(int i = 2; i < cost.length; i++){
            dp[i] = Math.min(dp[i - 2], dp[i - 1]) + cost[i];
        }
        return Math.min(dp[cost.length - 1], dp[cost.length - 2]);
    }
}
```
<hr>


### 62.不同路径

**思路**：每个格子 i,j 都是从 i-1,j 或 i,j-1 走过来，所以 dp[ i ] [ j ] = dp[ i-1 ] [ j ] + dp[ i ] [ j - 1 ] ;上面、左边两行都要初始化为1，只能唯一，因为只有右移或者下移。

```Java
class Solution {
    public int uniquePaths(int m, int n) {
        if(m <= 0 || n <= 0) return 0;
        if(m == 1 || n == 1)    return 1;
        int[][] dp = new int[m][n];
        for(int i=0;i<n;i++)    dp[0][i]=1;
        for(int i=0;i<m;i++)    dp[i][0]=1;
        for(int i=1;i<m;i++){
            for(int j=1;j<n;j++)
                dp[i][j] = dp[i-1][j]+dp[i][j-1];
        }
        return dp[m-1][n-1];
    }
}
```
<hr>


### 63. 不同路径 II

这次只出了一个越界，其它全对，思路也是自己的，直接AC，兴奋。

**思路**：初始化要注意，只能向右👉，下👇走，所以在初始化第一行、第一列的时候如果遇见障碍物，该位置与后面所有直线位置dp[i][j] 都为0，注意初始化完成第一行、第一列，所以从 (1,1) 坐标开始初始化dp，否则就越界了。在其他位置遇见障碍物就直接 dp[i][j] = 0 。

```Java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length,n = obstacleGrid[0].length;
        int[][] dp = new int[m][n];
        int flag = 1;
        for(int i=0;i<m;i++){
            if(obstacleGrid[i][0] == 1)     flag = 0;
            dp[i][0] = flag;
        }  
        flag = 1;
        for(int i=0;i<n;i++){
            if(obstacleGrid[0][i] == 1)     flag = 0;
            dp[0][i] = flag;
        }
        for(int i=1;i<m;i++){
            for(int j=1;j<n;j++){
                if(obstacleGrid[i][j] == 1)     dp[i][j] = 0;
                else    dp[i][j] = dp[i-1][j]+dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    }
}
```

<hr>



### 343. 整数拆分

**思路**：拆分成两个、两个或两个以上个、并更新以便取得最大值。初始化 dp[2]=1 .为什么？dp 数组是记录 i 值对应的最大《拆分积》。为什么内循环的条件是 j≤i-j ? 相当于剪枝操作， [ i-j , i ] 的数在 [0，i-j ] 已经乘过了，比如数字4， 1~4 是在1~2和2~4相乘，而不是两者相加=4。1~4和1~4会越界。

主要代码：

```Java
dp[i]=Math.max(dp[i],Math.max(j*(i-j),j*dp[i-j]));
```

为什么需要dp[i]、j*(i-j)、j*dp[i-j]取最大值，dp[i] 是为了取得最大值，因为dp[i]是多个比较的结果。而j*(i-j)是两个数的乘积，经过循环就是求得其分为两数的最大值，j*dp[i-j] 是拆分为**两个以上**，dp是i-j拆分为2+个数最大的积，那么是可能拆分为n个数的，会涵盖所有拆分可能，并*剩余值。

<hr>

### [96. 不同的二叉搜索树](https://leetcode.cn/problems/unique-binary-search-trees/)

**思路**：很抽象，先把0、1个节点只有一种可能确定好，再是从dp[2]、dp[3]来找递推公式。

dp[2]在1为头结点时，只能2挂在右边这种可能，2为头结点时，只能1挂在左边，dp[2]=1+1=2 。

dp[3]在1为头结点时，可以2、3依次深度排序，也可以1、3、2，所以两种可能。

dp[3]在2为头结点时，只能左右分别挂一个，所以只有一种可能。

dp[3]在3为头结点时，.类似1为头结点，两种可能。sum=2+1+2=5种可能。



dp[3] 递推公式：

1为头 sum+=dp[0]*dp[2]=2左孩子0个节点，右孩子2个节点。

2为头 sum+=dp[1]*dp[1]=1，左右孩子分别1个。

3为头 sum+=dp[2]*dp[0]=2，左孩子2个，右孩子一个。

```Java
class Solution {
    public int numTrees(int n) {
        int[] dp = new int[n+1];
        dp[0]=dp[1]=1;
        for(int i=2;i<=n;i++){
            for(int j=0;j<=i-1;j++)
                dp[i]+=dp[i-j-1]*dp[j];
        }
        return dp[n];
    }
}
```
<hr>


### 416. 分割等和子集

思路：以01背包背包的思路，dp[ i ][ j ] 的 i 代表从0到i选择，j代表背包容量为j，dp[i][j] 代表这样的前置条件下最大能装下的值。

主要还是数学逻辑：当背包容量为 sum/2 时，能在nums找到和为sum/2，也就是dp[ i ][ j ] =sum/2。就找到了，为什么？因为只要有和能为 sum 的一半，那么一定剩下的数字和一定也为 sum/2 。

```Java
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = 0,target;
        for(int i=0;i<nums.length;i++)
            sum += nums[i];
        if(sum % 2 == 1)    return false;
        target = sum/2;
        int[][] dp = new int[nums.length][target+1];
        for(int j = nums[0]; j <= target;j++)
            dp[0][j] = nums[0];
        for(int i = 1;i < nums.length;i++){
            for(int j = 1;j <= target;j++){
                if(j >= nums[i])   dp[i][j] = Math.max(dp[i-1][j],dp[i-1][j-nums[i]]+nums[i]);
                else    dp[i][j] = dp[i-1][j];
            }
        }
        return dp[nums.length-1][target] == target;
    }
}
```


<hr>

### 1049. 最后一块石头的重量 II

思路：还是跟上题一样，就是数学逻辑。用sum/2的容量来看最大能拿到多大，然后sum-max，也就是另一半的值，这两个值相减就出来了。

```Java
class Solution {
    public int lastStoneWeightII(int[] stones) {
        int sum=0,target;
        for(int i:stones)
            sum += i;
        target=sum/2;
        int[][] dp = new int[stones.length][target+1];
        for(int i=stones[0];i<target+1;i++)
            dp[0][i] = stones[0];
        for(int i=1;i<stones.length;i++)
            for(int j=1;j<target+1;j++)
                if(stones[i] > j)   dp[i][j] = dp[i-1][j];
                else    dp[i][j] = Math.max(dp[i-1][j],dp[i-1][j-stones[i]]+stones[i]);
        return sum-dp[stones.length-1][target]-dp[stones.length-1][target];
    }
}
```



一维数组（滑动数组）：相当于二维 dp[ i ] [ j ] = dp[ i-1 ] [ j ] 直接省去这一步骤，而是直接把上一层拷贝下来。

```Java
dp[i][j] = Math.max(dp[i-1][j],dp[i-1][j-stones[i]]+stones[i]);
//变化
dp~~[i]~~[j] = Math.max(dp~~[i]~~[j],dp~~[i]~~[j-stones[i]]+stones[i]);把多行放在一行里面滑动操作了。
//简化
dp[j] = Math.max(dp[j],dp[j-stones[i]]+stones[i]);

```

代码变为：

```Java
class Solution {
    public int lastStoneWeightII(int[] stones) {
        int sum = 0;
        for (int i : stones) {
            sum += i;
        }
        int target = sum >> 1;
        //初始化dp数组
        int[] dp = new int[target + 1];
        for (int i = 0; i < stones.length; i++) {
            //采用倒序
            for (int j = target; j >= stones[i]; j--) {
                //两种情况，要么放，要么不放
                dp[j] = Math.max(dp[j], dp[j - stones[i]] + stones[i]);
            }
        }
        return sum - 2 * dp[target];
    }
}
```

在内循环直接条件限制为 j ≥ stones 其实是像第一层初始化这样理解：装不下就不装，就不变，保持等于原来的dp[ j ]，也就是现在的dp[ j-1 ]。直接把二维数组时候的 if-else 省略了。

<hr>

### 494. 目标和

思路：将这个数组分为两个子集：left、right，left+right=sum，left-right=target。两个方程相加2 * left=（sum+target）—> left=（sum+target）/ 2 。确定了左边就确定了右边，所以找到left 为总和的组合数为 n，整体组合数就是n。

区别：与以前不同在于，求可能可能性的总数，前几道题是最大价值。都是01背包问题。主要区别在于循环体、初始化。

- 特殊情况1：容量为nums[0]：不放、放入nums[0]一共两种情况对吧?  错了，只能直接nums[0]-->nums[0],在0-0这个区间只有 nums[i] 能放进去，那么只有恰好nums[0]=size时候这种放法。

- 特殊情况2：size为0时（初始化），此时就有“一种方法”就是“不放入”。

```Java
class Solution {
    public int findTargetSumWays(int[] nums, int target) {
        int sum = 0, leftBagSize;
        for(int i:nums) sum += i;
        if( (sum + target) % 2 == 1)    return 0;
        leftBagSize = Math.abs( (sum + target) / 2 );  //考虑负数
        int[] dp = new int[leftBagSize+1];//这里不是最大价值，是组合个数。

        //初始化
        dp[0] = 1;

        for(int i = 0; i < nums.length; i++){
            for(int j = leftBagSize; j >= nums[i]; j--){
            //求的是总和（所有可能性）要把不+nums[i]的也算上
                dp[j] += dp[j-nums[i]];
            }
        }

        return dp[leftBagSize];
    }
}

```

<hr>

### 474.一和零

思路：理解题意！找出并返回 `strs` 的**最大子集**的长度，最大子集指的是最长的子集长度，即最多可以包含几个字符串作为子集。那么dp[i][j] 也就是个三维的01背包，进一个字符串先判断几个 0 几个 1  。然后逆序遍历来更新值，从m，n开始，i≥one，是为了至少有这个容量来装，防止 max 里面 index = -1 越界，Math.max里面更新很巧妙，如果是去掉当前串的 01 个数，就是看此时（去当前串01）容量是否已经初始化过，即是否装过了，装过了还能装就是1+1=2次。如果没装过就是0+1，相当于初始化了。

```Java
class Solution {
    public int findMaxForm(String[] strs, int m, int n) {
        int[][] dp = new int [m+1][n+1];
        for(String str:strs){
            int zero = 0, one = 0;
            for(char ch:str.toCharArray()){
                if(ch == '0')
                    zero++;
                else
                    one++;
            }
            for(int i = m; i >= zero;i--)
                for(int j = n; j >= one; j--)
                    dp[i][j] = Math.max(dp[i][j], dp[i-zero][j-one]+1);
        }
        return dp[m][n];
    }
}
```

<hr>

## 完全背包

指的是物品可以无限次被使用，此时就是正序遍历，先遍历“物品”还是“背包”要看是 **排列** 还是 **组合** ，**组合 先** 遍历 **物品**，因为这种遍历方式物品有先后顺序，而排列没有，则会打乱顺序还有结果。


### 518. 零钱兑换 II

思路：**完全背包**问题（组合：无序）每个物品可以用无限次，**正向遍历**。容量为0的时候不放入也是一种方法。这样在以后j=coins[i]时可以dp[j]+=dp[j-coins[i]] 即dp[0] 即1；



```Java
class Solution {
    public int change(int amount, int[] coins) {
        int[] dp = new int[amount+1];
        dp[0] = 1;
        for(int i = 0; i < coins.length; i++){
            for(int j = coins[i]; j <= amount; j++){
                dp[j] += dp[j-coins[i]];
            }
        }
        return dp[amount];
    }
}
```

<hr>

### 377. 组合总和 Ⅳ

思路：求的是排列（强调顺序，不同顺序为不同排列），排列先遍历背包，再遍历物品。

```Java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target+1];
        dp[0] = 1;
        for(int i = 0; i <= target; i++)
            for(int j = 0; j < nums.length; j++)
                if(i >= nums[j])   dp[i] += dp[i-nums[j]];
        return dp[target];
    }
}
```

<hr>

### 322. 零钱兑换

思路：满足条件的，最小硬币个数。无限使用 == 完全背包，求的是组合（不强调顺序）所以是先遍历物品。

```Java
class Solution {
    public int coinChange(int[] coins, int amount) {
        if(amount == 0) return 0;
        int max = Integer.MAX_VALUE;
        int[] dp = new int[amount+1];
        dp[0] = 0;
        for(int i=1; i<=amount; i++)//初始化
            dp[i] = max;
        for(int i = 0; i < coins.length; i++)
            for(int j = coins[i]; j <= amount; j++)
                if(dp[j-coins[i]] != max)    dp[j] = Math.min(dp[j-coins[i]]+1,dp[j]); 
        return dp[amount] == max ? -1 : dp[amount];
    }
}
```

<hr>

### 279.完全平方数

思路：完全背包，自己构建一个长度为 (int) Math.sqrt(n) 的物品数组，为什么？因为target最多被 sqrt(target) 平方出 target，而自己也要构建物品数组，i对应i*i ;

```Java
class Solution {
    public int numSquares(int n) {
        int max = Integer.MAX_VALUE;
        int len = (int)Math.sqrt(n);
        int[] dp = new int[n+1];
        dp[0] = 0;
        for(int i = 1; i <= n; i++)
            dp[i] = max;
        for(int i = 1; i <= len; i++)
            for(int j = i*i; j <= n; j++)
                if(dp[j-i*i] != max)  dp[j] = Math.min(dp[j],dp[j-i*i]+1);
        return dp[n];
    }
}
// 0 1 2 3 4 5 6 7 8 9 10 11 12
// 0 1 2 3 4 5 6 7 8 9 10 11 12
// 0 1 2 3 1 2 3 4 2 3 4  5  3
// 0 1 2 3 1 2 3 4 2 1 2  3  3
```

<hr>

### 139.单词拆分

思路：用字符串截取、List的contains方法来判断是否存在该截取的**子串**，存在且 d[j] 为 true 就 dp[i] = true .这样做的好处是每个位置每个子串都处理到了，而如果只移动i，等存在 并且 dp[i] = true 时，才移动j，那么在["aaa","aaaa"] , s="aaaaaaa" 这个测试用例就过不去，因为走的是两个aaa，剩下aa就走不动了。

```Java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        boolean[] dp = new boolean[s.length()+1];
        dp[0] = true;
        for(int i = 1; i <= s.length(); i++)
            for(int j = 0; j < i; j++)
                if(wordDict.contains(s.substring(j,i)) && dp[j]) dp[i] = true;
        return dp[s.length()];
    }
}
```


<hr>

持续更新.......  
如有错误，敬请斧正！！！




