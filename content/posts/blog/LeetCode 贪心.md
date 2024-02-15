
---
title: "LeetCode 贪心"
date: 2022-07-26T05:23:42+08:00
author: ["小任同学"]
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
lastmod: 2022-07-26T23:33:37+08:00	#更新文章的时候手动改一下时间就可以
description: 贪心算法：局部最优推出全局最优。
tags: 
- 算法
---

### 455.分发饼干

```Java
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        int count=0;
        for(int i=0,j=0;i<s.length && j<g.length;i++){
            if(s[i] >= g[j]){
                count++;
                j++;
            }    
        }
        return count;
    }
}
```
<hr>

### 376. 摆动序列

我的思路：直接改变数组比如4，3，4，5.只保留，4，3，5而把第三个4去掉，因为保留最高或者最低的那个更容易出现转折，最后保留下来的数组的长度就是返回值。不过这样**坑多，可复用性差，下一次拿到题目抠细节抠到哭。**

```Java
class Solution {
    public int wiggleMaxLength(int[] nums) {
        int len=nums.length;
        boolean flag1=true,flag2=true;
            for(int j=0;j<len-1 && len>1;j++){
                int t = nums[j+1]-nums[j]; 
                if(t == 0){
                        cutArray(nums,j);
                        len--;
                        j--;
                        continue;
                }else if(j%2 == 1){
                            if(t>0)  flag1 = true;
                            if(t<0)  flag1 = false;
                        }else{
                            if(t>0)  flag2 = true;
                            if(t<0)  flag2 = false;
                        }
                    if(j == 0)  continue;
                    else if(flag1 == flag2){
                            cutArray(nums,j);
                            len--;
                            j--;
                        }
            }
        return len;
    }
    public void cutArray(int[] nums,int index){
        for(int i=index;i<nums.length-1;i++)
            nums[i] = nums[i+1];
    }
}
```

题解思路：

```Java
class Solution {
    public int wiggleMaxLength(int[] nums) {
        if(nums.length <= 1)    return nums.length;
        int cur=0,pre=0,count=1;
        for(int i=0;i<nums.length-1;i++){
            cur = nums[i+1]-nums[i];
            if(cur>0&&pre<=0 || cur<0&&pre>=0){//总之cur必须!=0，pre可能是初始化的0，因为所有的cur都!=0，所以后面赋值到的pre也!=0
            //这样比较就算持续3,7,4,5,6在最后比较的也是最后面那个偏差幅度最大的值6，最后有效是3，7，4，6.相比我之前的思想化繁为简。
                pre = cur;
                count++;
            }
        }
        return count;
    }
}
```

<hr>

### 53.最大子序和

如果多个数相加<0，那就是累赘，和后面任何数相加只会让sum更小。所以从这个数后面的第一个数就要从0重新计算sum 。

```Java
class Solution {
    public int maxSubArray(int[] nums) {
        if(nums.length==1) return nums[0];
        int sum = 0,max=nums[0];
        for(int i=0;i<nums.length;i++){
            sum+=nums[i];
            if(sum>max) max=sum;
            if(sum<=0)   sum=0;
        }
        return max;
    }
}
```

<hr>

### 122.买卖股票的最佳时机II

nums[j+1]>nums[j]时就可以买卖了，局部最优，否则就移到下一位置。

可能会有疑惑，5，3，7这样不就5，7也是盈利啊，但是3，7盈利更大呀，就算是3，5，7，在3买5卖，5买7卖其实结果也是一样。

```Java
class Solution {
    public int maxProfit(int[] prices) {
        int sum=0,t;
        for(int i=0;i<prices.length-1;i++){
            t=prices[i+1]-prices[i];
            if(t>0) sum+=t;
        }
        return sum;
    }
}
```
<hr>


### 55.跳跃游戏

局部最优这句话怎么利用？

覆盖范围，在第一个位置的覆盖范围内可到达的最远地方。

```Java
class Solution {
    public boolean canJump(int[] nums) {
        if(nums.length==1)  return true;
        int range = nums[0];
        for(int i=0;i<=range;i++){
            if(nums[i]+i > range)    range=nums[i]+i;
            if(range >= nums.length-1)    return true;
        }
        return false;
    }
}
```

<hr>

### 45.跳跃游戏II

这道题我有个疑问就是，当前最远、下一步最远  如何保证下一步到最远不是到一个为0的数，到了为0是不是就动不了，其实不是的，这是《当前范围》《可能的下一步的所有最远范围》然后 curRange = nextRange ，这样nextRange又在更新，局部最优转换为了全局最优。

主要链路是不断比较计算总结出curRange可以到达的最远距离。coding时注意在curRange最后一位：curRange=nextRange；还有循环终止条件。

```Java
class Solution {
    public int jump(int[] nums) {
        if(nums.length==1)  return 0;
        int curRange=nums[0],nextRange=nums[0],count=1;
        for(int i=0;i<=curRange&&curRange<nums.length-1;i++){
            if(nums[i]+i>nextRange) nextRange=nums[i]+i;
            if(i==curRange) {
                curRange=nextRange;
                count++;
            }
        }
        return count;
    }
}
```

<hr>


### 1005.K次取反后最大化的数组和

1. **绝对值排序**
2. 0-len将负数取反，如果全是正书，不管一开始就是还是处理完才是，在绝对值最小位置反复取反。
3. 求和 

```Java
class Solution {
    public int largestSumAfterKNegations(int[] nums, int k) {
        int sum=0,temp;
        for(int i=0;i<nums.length;i++)//给绝对值排序
            for(int j=i+1;j<nums.length;j++)
                if(Math.abs(nums[j])>Math.abs(nums[i])){
                    temp=nums[j];
                    nums[j]=nums[i];
                    nums[i]=temp;
                }
        for(int i=0;k>0;i++){//处理k次
   //先判断是否到最末位置，再判断当前位置是否为负数。不然可能最末尾为负数，那就越界。 因为i没有-1. 
            if(i==nums.length-1){
                nums[i]=-nums[i];
                i--;
                k--;
            }  
            if(nums[i]<0){
                nums[i]=-nums[i];
                k--;
            }
        }    
        for(int i=0;i<nums.length;i++)//求和
            sum+=nums[i];
        return sum;
    }
}
```

<hr>

### 134.加油站

暴力解法（我的）：双重for，外层控制开始的位置，内层判断是否能成功走一圈。

注意：要在外循环内进行剪枝，不然会超时，但是效率尽管如此，双重循环效率依旧很低。

```Java
class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        for(int i=0;i<cost.length;i++){//起始位置全轮一遍
            int sum=gas[i],count=1;
            if(gas[i]==0)   continue;
            for(int j=i;cost[j]<=sum;j++){
                if(j==cost.length-1){
                    sum=sum+gas[0]-cost[j];//s=8+1-2=7,
                    count++;
                    j=-1;
                }else{
                    sum=sum+gas[j+1]-cost[j];
                    count++;
                }
                if(count>cost.length)   return i;
            }
        }
        return -1;
    }
}
```

<hr>

### 135.分发糖果

注意：评分更高的糖果多（>），如果相等就算<

两次循环，第一次从左到右，第二次从右到左。

第一次处理逻辑：从1开始，如果a[i]>a[i-1]，那么a[i]=a[i-1]+1，否则就a[i]=1

第二次处理逻辑：从length-2开始，如果a[i]>a[i+1]那就比较sweet[i]、sweet[i+1]+1，取大的赋值给a[i]，以此保证规则。

左—>右 ：

- 如果评分是1,2,3  糖果分别是1，2，3 
- 如果评分是2,1,3  糖果分别是1，1，2  （有问题）
- 如果评分是3,2,1  糖果分别是1，1，1 （有问题）
- 如果评分是3,1,2  糖果分别是1，1，3 （有问题）

右—>左：

- 如果评分是1,2,3  糖果分别调整为1，2，3 
- 如果评分是2,1,3  糖果分别调整为2，1，2  
- 如果评分是3,2,1  糖果分别调整为3，2，1 
- 如果评分是3,1,2  糖果分别调整为2，1，2 

整体过程分别从左到右、从右到左保证评分的规则性，第一遍可能使得做边不规则（a[len-1]肯定规则），第二遍可能使得右边不规则（a[0]肯定规则）。一个是从1开始，和i-1比较，一个是len-1开始，和i+1，分别向左和向右比较。

```Java
class Solution {
    public int candy(int[] ratings) {
        if(ratings.length<=1)   return ratings.length;
        int count=0;
        int[] sweet = new int[ratings.length];
        sweet[0]=1;
        for(int i=1;i<ratings.length;i++){
            if(ratings[i]>ratings[i-1])
                sweet[i]=sweet[i-1]+1;
            else
                sweet[i]=1;
        }
        for(int i=ratings.length-2;i>=0;i--){
            if(ratings[i]>ratings[i+1])
                sweet[i]=Math.max(sweet[i],sweet[i+1]+1);
        }
        for(int i:sweet)
            count+=i;
        return count;
    }
}
```

<hr>

### 406.根据身高重建队列

先按照身高来排（高—>低），再按照前面比他高的人数（少—>多），遵循这两个规律来排序，再依次按照“前面有几个人”来当索引插入到LinkedList。

lamada表达式排序的写法：Arrays.sort(数组名,(a,b)→{return >0的值就交换，反之则不})；

```Java
class Solution {
    public int[][] reconstructQueue(int[][] people) {
        Arrays.sort(people,(a,b)->{
            if(a[0]==b[0])   return a[1]-b[1];
            return b[0]-a[0];
        });
        LinkedList<int[]> linkedList = new LinkedList<>();
        for(int[] p:people)
            linkedList.add(p[1],p);
        return linkedList.toArray(new int[people.length][]);
    }
}
```

<hr>

### 452.用最少数量的箭引爆气球

主题思路：先按左/右**排序**好，然后在循环里判断：

- 如果**第二个**数组的**左**边界 ≤ **第一个**数组的**右**边界，则这两个有重合，就不需要++一支箭，而且这里一个关键就是：把此时的第二个数组的右边界换成两数组**交集的右边界**，不然下一次判断如果是和上一个数组重合，那么还需判断是否和上上个数组重合。否则就是count++；

注意：**排序**过程不能用Arrays.sort( points,(a,b)→{return a-b;} ); 因为最近先加的测试用例差值太大。所以用Arrays.sort( points,(a,b) → Integer.compare(a,b) );

```Java
class Solution {
    public int findMinArrowShots(int[][] points) {
        if(points.length==0)    return 0;
        Arrays.sort(points, (o1,o2) -> Integer.compare(o1[0],o2[0]));
        int count=1;
        for(int i=1;i<points.length;i++){
            if(points[i][0]<=points[i-1][1]){
                points[i][1]=Math.min(points[i][1],points[i-1][1]);
            }else
                count++;
        }
        return count;
    }
}
```

<hr>

### 435.无重叠区间

跟上一题类似，都是更新边界值。

```Java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        Arrays.sort(intervals,(a,b)->{
            if(a[0]==b[0])  return a[1]-b[1];  
                return a[0]-b[0];    
            });
        int count=0;
        for(int i=1;i<intervals.length;i++){
            if(intervals[i][0]<intervals[i-1][1]){
                count++;
                intervals[i][1]=Math.min(intervals[i][1],intervals[i-1][1]);//更新右边界，避免反复比较死去的区间（右补集）
            }      
        }
        return count;
    }
}
```

<hr>

### 763.划分字母区间

思路：把每个字符最后出现的索引位置放在一个数组，如果在某个字符区间出现一个字符的右区间超过该字符的右区间，则刷新右区间，如果当前索引达到了右区间，就add，再把计数器归0；

```Java
class Solution {
    public List<Integer> partitionLabels(String s) {
        List<Integer> list = new ArrayList<>();
        char[]ch=s.toCharArray();
        int[] count= new int[26];
        for(int i=0;i<s.length();i++){
            count[ch[i]-'a']=i;
        }
        int first=0,sum=1;
        //为什么额外定一个sum？因为first可能碰到这个情况：
        //一个字符区间内的一个字符超出这个区间，那么 first 就变了，随之 last-first+1 的数值也变了。sum来计数是一个补救措施
        for(int i=0;i<s.length();i++){
            if(count[ch[first]-'a']==i){
                list.add(sum);
                first=i+1;
                sum=0;//初始化是1，这里是0因为后面也会++；
            }else if(count[ch[i]-'a']>count[ch[first]-'a']){
                first=i;
            }
            sum++;
        }
        return list;
    }
}
```

<hr>

### 56.合并区间

**思路**：先按边界排序，把其add到linkedlist中，然后每次都判断最后一个元素和当前循环到的节点是否有交集，如果有就把他们的并集顶替掉list中的最后那个元素，否则就add。

```Java
class Solution {
    public int[][] merge(int[][] intervals) {
        if(intervals.length<=1) return intervals;
        LinkedList<int[]> list = new LinkedList<>();
        Arrays.sort(intervals,(a,b)->{
           // if(a[0]==b[0])  return a[1]-b[1];
            return a[0]-b[0];
        });
        list.add(intervals[0]);
        int[] temp=new int[2];
        for(int i=1;i<intervals.length;i++){
            if(intervals[i][0]<=list.getLast()[1]){
                temp=list.removeLast();
                list.add(new int[]{Math.min(intervals[i][0],temp[0]),Math.max(temp[1],intervals[i][1])});
            }else{
                list.add(intervals[i]);  
            }  
        }
        return list.toArray(new int[list.size()][2]);
    }
}
```

<hr>

### 738.单调递增的数字

**思路**：先将其转换为字符数组，不要按数字来遍历，而是利用字符，如果4512，那么4499就是最大，规律就是，当前位置如果<上一位，即这两个位置**非递增**，那左边的应该-1，其右边**全**要变成9，因为全部要变9，所以先记录并更新最前变9的位置，最后一次性改变。但是必须将**非递增的左边**位置一直-1，不然比如332，会变成329，因为332中33两位置相等，右边的3并未更新为2，更新完了就可以了。

**注意**：另外避免标识的last未使用误用导致错误（定义在【0，len-1】），或者太小(-1)导致越界，所以应将last定义为 ≥len 。

String str = String.valueOf(任何类型);   ：将任何类型转换为字符串类型；

Integer.parseInt(字符串) ：字符串类型—>基本数据类型int

类似的：Double.parseDouble(字符串)

```Java
class Solution {
    public int monotoneIncreasingDigits(int n) {
        String str = String.valueOf(n);
        char[]ch=str.toCharArray();
        int last=ch.length;
        for(int i=ch.length-2;i>=0;i--){
            if(ch[i+1]<ch[i]){
                ch[i]--;
                last=i+1;
            } 
        }
        for(int i=ch.length-1;i>=last;i--)
            ch[i]='9';
        return Integer.parseInt(String.valueOf(ch));
    }
}
```
<hr>

### 714.买卖股票的最佳时机含手续费

主体思路都在注释。

```Java
class Solution {
    public int maxProfit(int[] prices, int fee) {
        int sum=0,buy=prices[0]+fee;
        for(int p:prices){
            if(p>buy){
                sum += p-buy;//交易状态中有fee会减掉（得到纯利润）。交易完成状态：是把上一次交易提高利润的情况下就不减掉fee；
                buy = p;//交易状态：留出这次交易卖出价格以便下次可以在交易完成状态提高利润。
            }
            if(p+fee<buy){//交易状态：如果买入比上一次买入便宜，就重新买入。 交易完成状态：
                buy=p+fee;
            }
        }
        return sum;
    }
}
```
<hr>


### 968.监控二叉树

摆烂卒......

二刷见



