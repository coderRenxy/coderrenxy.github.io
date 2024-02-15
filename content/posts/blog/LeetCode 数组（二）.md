
---
title: "LeetCode 数组(二)"
date: 2022-07-27T05:23:42+08:00
author: ["小任同学"]
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
lastmod: 2022-07-27T23:33:37+08:00	#更新文章的时候手动改一下时间就可以
description: 数组模块二刷。
tags: 
- 算法
---

### 704. 二分查找

**思路**：首先把 left 、mid 、 right 都定义出来。mid 的赋值，左右指针的跳转都在循环内完成，注意要 left=mid+1，right = mid-1，不然有的用例会超时，循环条件则是 right ≥ left 不然藏在边界的 target 就查不到了。

```java
class Solution {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length-1, mid;
        while(right >= left ){
            mid = (right-left)/2+left;
            if(target == nums[mid]) return mid;
            else if(target > nums[mid]) left = mid+1;
                else    right = mid-1;
        }
        return -1;
    }
}
```
<hr>

### 27.  移除元素

**暴力解法**：需要注意，i--；是因为既然当前值为  val 的点的右边所有点一切都左移了一位，所以现在的 i 指向的是已删除（被覆盖）的位置，该位置补了后面那位没被扫描的数字，而该次循环结束就要 i++了，所以会指向这个没扫描的下一个进行扫描，就漏掉一个了。还有一个就是无限循环容易发生是因为 for 循环条件不能是 < length。而应该是 < len 这个自定义的，会随着更新而更新。

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int len = nums.length;
        for(int i = 0; i < len; i++)
            if(nums[i] == val){
                for(int j = i; j < len-1; j++)
                    nums[j] = nums[j+1];
                len--;
                i--;
            }
            return len;
    }
}
```

**快慢指针**法：fast 位置是 val 值，就 fast++，不是 val 值就 nums[slow] = nums[fast]，slow 后移一位，为了不影响 非val 的值，并且slow可以计数（非 val 个数）。

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int slow = 0, fast = 0;
        for(; fast < nums.length; fast++)
            if(nums[fast] != val){
                nums[slow] = nums[fast];
                slow++;
            }    
        return slow;
    }
}
```
<hr>

### ****[977. 有序数组的平方](https://leetcode-cn.com/problems/squares-of-a-sorted-array/)****

**思路**：暴力就是先平方再 arrays.sort( ); 但是时间复杂度为 O(n)+nlogn; 所以这么解不行，用双指针的方法要注意，一定要从两头找**最大**的放入新数组的**最右**侧，因为两头只能找最大，最小的有可能类似：{-3，0，3}，最小在中间。注意循环条件是 left ≤ right，而不是 left < right，因为有边界要处理。

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int left = 0, right = nums.length-1,i=nums.length-1;
        int[] array = new int[nums.length];
        while(left <= right)
            if(nums[left]*nums[left] > nums[right]*nums[right])
                array[i--] = nums[left]*nums[left++];
            else
                array[i--] = nums[right]*nums[right--];
        return array;
    }
}
```

<hr>

### ****[209. 长度最小的子数组](https://leetcode-cn.com/problems/minimum-size-subarray-sum/)****

思路：滑动窗口，这样写法虽然时间复杂度是 O(n)，但是效率比另一种 O(n^2) 要低很多，另一种在 for 循环嵌套一个 while 循环，让达到 ≥ target 的 sum 一直减去 nums[left] ，而 sum 不需要反复初始化为 0. 注意循环自增 end。

```java
//双指针
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int begin = 0, end = 0, sum = 0, min = Integer.MAX_VALUE;
        for(; end < nums.length; end++){
            sum += nums[end];
            if(sum >= target){
                min = Math.min(min,end-begin+1);
                sum = 0;
                end = begin;
                begin++;
            }
        }
        return min == Integer.MAX_VALUE ? 0 : min;
    }
}
```

```java
//滑动窗口
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int begin = 0, end = 0, sum = 0, min = Integer.MAX_VALUE;
        for(; end < nums.length; end++){
            sum += nums[end];
            while(sum >= target){
                min = Math.min(min,end-begin+1);
                sum -= nums[begin++];
            }       
        }
        return min == Integer.MAX_VALUE ? 0 : min;
    }
}
```

<hr>

### ****[59. 螺旋矩阵 II](https://leetcode-cn.com/problems/spiral-matrix-ii/)****

思路：

1. 先模拟整个一圈的流程，再去写外层循环，循环次数可以由 n/2 来确定，但此时有个问题，n 为奇数则中间 array[n/2][n/2] 需要单独赋值，而 n 为偶数不需要。
2. 还有在判断的时候很容易搞混什么适合走的是 x ，什么时候走的是 y。
3. 在往内圈走的时候很容易碰到一个问题就是：n 需不需要 n-- ？其实设置一个偏移量 offset 就行了，因为如果 n— 那么在第三、四个循环解决不了少走一个格子的问题。
4. 设置 startX、startY 是为了走下一圈的时候能顺利切换。

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int[][] array = new int[n][n]; 
        int x, y, startX = 0, startY = 0, num = 1, circle = n / 2, offset = 0;
        while(circle-- > 0){
            x = startX;
            y = startY;
            for(; y + offset < n-1; y++){
                array[x][y] = num++;
            }
            for(; x + offset < n-1; x++){
                array[x][y] = num++;
            }
            for(; y - offset > 0; y--){
                array[x][y] = num++;
            }
            for(; x - offset > 0; x--){
                array[x][y] = num++;
            }
            startX++;
            startY++;
            offset++;
        }
        if(n % 2 == 1)array[n/2][n/2] = num;
        return array;
    }
}
```
<hr>

持续更新.......  
如有错误，敬请斧正！！！




