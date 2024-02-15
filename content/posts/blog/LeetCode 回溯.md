
---
title: "LeetCode 回溯"
date: 2022-06-25T05:23:42+08:00
author: ["小任同学"]
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
lastmod: 2022-05-02T23:33:37+08:00	#更新文章的时候手动改一下时间就可以
description: 由此正式浸润回溯，其实之前也用到过回溯，现在系统刷一遍题目。
tags: 
- 算法
---

### 77. 组合

**思路**：分为终止条件、单层逻辑两层。

- 终止条件：当长度为 k 就 new  这个 List 并 add 进去并 return。
- 单层逻辑：for 嵌套 add、递归、remove。

**question**：

1. 为什么要 for：如果没有，找到第一个符合条件的组合就层层 remove 了。
2. for 的结束条件控制的是什么：控制当前层的可选个数。i < n - (k - path.size()) + 1 可以当作 i < n - k + 1看待，在第一层可选择的个数就是 n-k+1，因为考虑到后面要跟元素，所以要留 k-1 个位置，第二层要为下一层留位置，所以可选项反而多了，预留 k-2个位置就行，可选项为 n-(k-1)+1。

```Java
class Solution {
    List<List<Integer>> list = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    public List<List<Integer>> combine(int n, int k) {
        if(k > n)   return null;
        dfs(1,n,k);
        return list;
    }
    public void dfs(int start,int n,int k){// n 个里面取 k 个
        //终止条件
        if(k == path.size()){//层数/深度限定在此
            list.add(new ArrayList<>(path));
            return;
        }

        //单层逻辑
        for(; start <= n-(k-path.size())+1; start++){//循环才能出多个结果，不然直接找到就remove多层。
            path.add(start);
            dfs(start+1,n,k);
            path.remove(path.size()-1);
        }
        return;
    }
}
```

<hr>

### 216.组合总和III

**思路**：回溯做法，集合就是 1-9 九个数字里面取，递归终止条件是 size == k && sum == n

**注意**：一定要加一个 if（sum>n || path.size()>k）return;  不然会超时。

```Java
class Solution {
    List<List<Integer>> list = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    public List<List<Integer>> combinationSum3(int k, int n) {
        if(k>n) return list;
        dfs(1,k,n,0);
        return list;
    }
    public void dfs(int start,int k,int n,int sum){
        if(sum == n && k == path.size()){
            list.add(new ArrayList<>(path));
            return;
        }
        if(sum >= n || k <= path.size())    return;
        for(;start<=9-(k-path.size())+1;start++){
            sum += start;
            path.add(start);
            dfs(start+1,k,n,sum);
            sum -= start;
            path.remove(path.size()-1);
        }
        return;
    }
}
```

<hr>


### 17.电话号码的字母组合

思路：递归层数（深度）就是号码字符串长度，递归层度由递归终止条件控制，for 循环控制的次数是当前号码字符串中数字对应的字母映射。把一个数组的 index 和 content 作为 号码数字 和 字母的映射。用到 Stringbuilder、charAt、deleteCharAt、toString、append.

```Java
class Solution {
    List<String> list = new ArrayList<>();
    String nums[] = {"","","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"};
    StringBuilder sb = new StringBuilder();
    public List<String> letterCombinations(String digits) {
        if(digits.length() == 0 || digits == null)    return list;
        backTracking(digits,0);
        return list;
    }
    public void backTracking(String digits,int index){
        //深度由递归终止条件控制
        if(sb.length() == digits.length()){
            list.add(sb.toString());
            return;
        }

        for(int i=0;i<nums[digits.charAt(index)-'0'].length();i++){
            sb.append(nums[digits.charAt(index)-'0'].charAt(i));
            backTracking(digits,index+1);
            sb.deleteCharAt(sb.length()-1);
        }
        return;
    }
}
```

<hr>


### 39. 组合总和

莽夫**思路**：直接回溯。

**优化**思路：先排序，在 for 中 add 之前判断如果要 add的数 + sum > target，就 break，因为后面只会越来越大。

**注意**：循环条件赋初值给上次用完的数，这样就是把上次用的重复用了，如果赋初值为 0，那么会出现同一个组合的不同排列。

```Java
class Solution {
    List<List<Integer>> list = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    int sum = 0;
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        Arrays.sort(candidates);
        if(candidates == null || candidates.length == 0)    return list;
        getPaths(candidates,target,0);
        return list;
    }
    public void getPaths(int[] candidates, int target,int index){
        //终止条件
        if(sum == target){
            list.add(new ArrayList<>(path));
            return;
        }
        //单层逻辑
        for(int i=index;i<candidates.length;i++){
            if(sum + candidates[i] > target)    break;
            path.add(candidates[i]);
            sum += candidates[i];
            getPaths(candidates,target,i);
            path.remove(path.size()-1);
            sum -= candidates[i];
        }
        return;
    }
}
```

<hr>


### 40.组合总和II

**思路**：和39差的就是要去重，这里的去重指的是同一递归层的去重，绝不能和其他层关联，否则会少结果集。

**注意**：千万不要把去重的 if 里的 i>index 写作 i>0，这样只会保证不越界，但是每次都会把两层之间的数去重，而 i>index 则恰好不越界不影响其他层。还有传入递归的 index 位置的不是 index+1，而是 i+1.

```Java
class Solution {
    List<List<Integer>> list = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    int sum = 0;
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        if(candidates == null || candidates.length == 0)    return list;
        Arrays.sort(candidates);
        getPaths(candidates,target,0);
        return list;
    }
    public void getPaths(int[] candidates, int target,int index){
        //结束条件
        if(target == sum){
            list.add(new ArrayList<>(path));
            return;
        }
        //单层逻辑
        for(int i=index;i<candidates.length && sum + candidates[i] <= target;i++){
            if(i > index && candidates[i-1] == candidates[i])    continue;
            path.add(candidates[i]);
            sum += candidates[i];
            getPaths(candidates,target,i+1);
            path.remove(path.size()-1);
            sum -= candidates[i];
        }
        return;
    }
}
```

<hr>


### 131.分割回文串

**思路**：

1. 写出判断回文串的方法（把字符串，首尾切割位置传过去最优）。
2. 递归单层逻辑：外层 for 循环囊括所有可能第一切割位置、第n次切割位置，递归切下去，如果是回文串就 add 否则 continue以确保每个 add 的都是回文串。
3. 终止条件：当切割起始位置 index == 长度（末尾索引+1）

代码：

```Java
class Solution {
    List<List<String>> list = new ArrayList<>();
    List<String> listSon = new ArrayList<>();
    public List<List<String>> partition(String s) {
        backTracking(s,0);
        return list;
    }
    public void backTracking(String s,int begin){
        if(begin == s.length()){
            list.add(new ArrayList<>(listSon));
            return;
        }
        for(int i=begin;i<s.length();i++){
            if(isPalindrome(s,begin,i))
                listSon.add(s.substring(begin,i+1));
            else 
                continue;
            backTracking(s,i+1);
            listSon.remove(listSon.size()-1);
        }
        return;
    }
    public boolean isPalindrome(String s,int begin,int end){
        for(int i=begin,j=end;i<j;i++,j--)
            if(s.charAt(i)!=s.charAt(j)) return false; 
        return true;
    }
}
```


<hr>



### 93.复原IP地址

这题主要是"."、如何确保是四串，这两个问题可以合成一个，就是通过"."的数量来知道是否已经4串。

还有就是：怎么才是合法的串？0不能开头，除非它是一个独立的0，范围要在0-255，而且只能是数字。

```Java
class Solution {
    List<String> result = new ArrayList<>();
    public List<String> restoreIpAddresses(String s) {
        if(s.length() > 12)   return result;
        backTrack(s,0,0);
        return result;
    }
    public void backTrack(String s,int startIndex,int pointNum){
        if(pointNum == 3){ //.的个数
                if(isValid(s,startIndex,s.length()-1)) {result.add(s);}
                return;
        }
        for(int i=startIndex;i<s.length()-3+pointNum;i++){
            if(isValid(s,startIndex,i)){
                s = s.substring(0,i+1)+"."+s.substring(i+1);
                pointNum++;
                backTrack(s,i+2,pointNum);
                s = s.substring(0,i+1)+s.substring(i+2);
                pointNum--; 
            }
            else {break;}
        }
    }
    public Boolean isValid(String s,int begin,int end){
        if(begin > end) return false;
        int num = 0;
        if(s.charAt(begin) == '0' && begin != end)  return false;//0不能开头，可当一个数字单独存在；
        for(int i=begin;i<=end;i++){
            if(s.charAt(i)>'9' || s.charAt(i)<'0') return false;
            num = (s.charAt(i)-'0')+num*10;
            if(num > 255)   return false;
        }
        return true;
    }
}
```


<hr>



### 78.子集

主要是每一次进回溯都new一个List，反正集合没有重复元素，正常回溯足够解题。

```Java
class Solution {
        List<List<Integer>> list = new ArrayList<>();
        List<Integer> listSon = new ArrayList<>();
    public List<List<Integer>> subsets(int[] nums) {
        backTrack(nums,0);
        return list;
    }
    public void backTrack(int nums[],int begin){
        list.add(new ArrayList<>(listSon));
        for(int i=begin;i<nums.length;i++){
            listSon.add(nums[i]);
            backTrack(nums,i+1);
            listSon.remove(listSon.size()-1);
        }
        return;
    }
}
```


<hr>



### 90.子集II

跟上一个子集差别就是List<List<Integer>>去重以及还有一个在用nums前先sort，因为乱序可能导致[1,4] 和 [4,1] 无法去重。比如nums={4，1，4}，就会出现这种情况。

```Java
class Solution {
    Set<List<Integer>> set = new HashSet<>();
    List<Integer> listSon = new ArrayList<>();
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        backTrack(nums,0);
        List<List<Integer>> list = new ArrayList<>();
        for(List i:set)
            list.add(i);
        return list;
    }
    public void backTrack(int[] nums,int begin){
        set.add(new ArrayList<>(listSon));
        for(int i=begin;i<nums.length;i++){
            listSon.add(nums[i]);
            backTrack(nums,i+1);
            listSon.remove(listSon.size()-1);
        }
        return;
    }
}
```


<hr>



### 46.全排列

主要是每次从0开始，用LinkedList作子list方便使用contains方法，当子list包含该元素时就continue，否则就正常回溯。或者用used数组来标识是否已经存在当前元素。

```Java
class Solution {
    List<List<Integer>> list = new ArrayList<>();
    LinkedList<Integer> listSon = new LinkedList<>();
    public List<List<Integer>> permute(int[] nums) {
        backTrack(nums);
        return list;
    }
    public void backTrack(int[] nums){
        if(nums.length == listSon.size()){
                list.add(new ArrayList<>(listSon));
                return;
        }
        for(int i=0;i<nums.length;i++){
            if(listSon.contains(nums[i]))   continue;
            listSon.add(nums[i]);
            backTrack(nums);
            listSon.remove(listSon.size()-1);
        }
    }
}
```


<hr>



### 47.全排列 II

和 全排列I 一样的处理逻辑，但是用used数组做，因为有重复元素，数组只看下标是否被使用，而contains看包不包含这个元素值。

```Java
class Solution {
    Set<List<Integer>> set = new HashSet<>();
    List<Integer> list = new ArrayList<>();
    boolean[] used;
    public List<List<Integer>> permuteUnique(int[] nums) {
        if(nums.length == 0) return null;
        used = new boolean[nums.length];
        backTrack(nums);
        return new ArrayList<>(set);
    }
    public void backTrack(int[] nums){
        if(list.size() == nums.length){
            set.add(new ArrayList<>(list));
            return;
        }
        for(int i=0;i<nums.length;i++){
            if(used[i]) continue;
            used[i] = true;
            list.add(nums[i]);
            backTrack(nums);
            list.remove(list.size()-1);
            used[i] = false;
        }
    }
}
```


<hr>



### 图论 hard

332.重新安排行程  &&  37. 解数独  &&  第51题. N皇后

不太清楚图，不大理解题意。卒......


<hr>

持续更新.......  
如有错误，敬请斧正！！！




