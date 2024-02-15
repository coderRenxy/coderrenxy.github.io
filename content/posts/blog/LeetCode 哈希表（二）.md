
---
title: "LeetCode 哈希表(二)"
date: 2022-09-01T05:23:42+08:00
author: ["小任同学"]
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
lastmod: 2022-09-01T23:33:37+08:00	#更新文章的时候手动改一下时间就可以
description: 哈希表模块二刷。
tags: 
- 算法
---
### **242.有效的字母异位词**

暴力哈希：map1.put(ch,++map1.get(ch))会报错，+1就不会。

```Java
class Solution {
    public boolean isAnagram(String s, String t) {
        Map<Character,Integer> map1 = new HashMap<>();
        Map<Character,Integer> map2 = new HashMap<>();
        for(Character ch : s.toCharArray()){
            if(map1.containsKey(ch))    map1.put(ch,map1.get(ch)+1);
            else    map1.put(ch,1);
        }
        for(Character ch : t.toCharArray()){
            if(map1.containsKey(ch))    map1.put(ch,map1.get(ch)-1);
            else    return false;
        }
        for(Character ch : s.toCharArray())
            if(map1.get(ch) != 0)   return false;
        return true;
    }
}
```

巧用数组：

```Java
class Solution {
    public boolean isAnagram(String s, String t) {
        int[] nums = new int[26];
        for(char ch : s.toCharArray())
            nums[ch-'a']++;
        for(char ch : t.toCharArray())
            nums[ch-'a']--;
        for(int num : nums)
            if(num != 0)    return false;
        return true;
    }
}
```

### [**349. 两个数组的交集**](https://leetcode-cn.com/problems/intersection-of-two-arrays/)

注意：set.add()、set.contains()、map.put()、map.get()、map.containsKey()。结果用 set 来放就不用来回倒腾。剪枝优化一下 length==0 || nums==null.

```Java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set1 = new HashSet<>();
        Set<Integer> set2 = new HashSet<>();
        for(int i : nums1)
            set1.add(i);
        for(int i : nums2)
            if(set1.contains(i))   set2.add(i);
        return set2.stream().mapToInt(x->x).toArray();
    }
}
```

### [**202. 快乐数**](https://leetcode-cn.com/problems/happy-number/)

不是快乐数的数称为不快乐数（unhappy number），所有不快乐数的数位平方和计算，最後都会进入 4 → 16 → 37 → 58 → 89 → 145 → 42 → 20 → 4 的循环中。

所以进主函数的循环条件为 ≠1 && !set.contains(?); 只要能进这个循环就进去拿到 nextNumber ，进到该方法里再进行拿到 各数上的平方和。

注意：res += n % 10 * n % 10; 一定要写成 res += (n % 10) * (n % 10); 因为四则运算的优先级会先  n%10 再 *n 。

```Java
class Solution {
    public boolean isHappy(int n) {
        int sum = n;
        Set<Integer> set = new HashSet<>();
        while(!set.contains(n)){
            set.add(n);
            if(n == 1)  return true;
            sum = 0;
            while(n != 0){
                sum += (n%10) * (n%10);
                n /= 10;
            }
            n = sum;
        }
        return false;
    }
}
```

### 1. 两数之和

暴力冒泡只要注意初始化： j=i+1即可。

**进阶**版本：主要熟悉几个常用 API：`map.containsKey(key)、map.get(key)`。

```Java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer,Integer> map = new HashMap<>();
        int[] array = new int[2];
        for(int i = 0; i <= nums.length; i++){
            if(map.containsKey(target-nums[i])){
                array[0] = map.get(target-nums[i]);
                array[1] = i;
                return array;
            }else{
                map.put(nums[i],i);
            }
        }
        return array;
    }
}
```

### [**454. 四数相加 II**](https://leetcode-cn.com/problems/4sum-ii/)

**思路**：先把前两个数组之和（key），该和出现的次数value 存入map，再遍历后两个数组拿到 0-（c+d）去查map是否存在，存在 count+=value。

**注意**：可能有数组传入为 null，计算值的时候默认为 0 就行，如果两个普通的 for循环嵌套，有空数组会被判断条件拦截下来，但如果用的是 foreach 就没问题。

```Java
class Solution {
    public int fourSumCount(int[] nums1, int[] nums2, int[] nums3, int[] nums4) {
        Map<Integer,Integer> map = new HashMap<>();
        int count = 0;
        for(int i : nums1){
            for(int j : nums2){
                if(map.containsKey(i+j))    map.put(i+j,map.get(i+j)+1);
                else map.put(i+j,1);
            }
        }
        for(int i : nums3){
            for(int j : nums4){
                if(map.containsKey(-i-j))    count += map.get(-i-j);
            }
        }
        return count;
    }
}
```

### **383.赎金信**

**思路**：长度为26的数组。

```Java
class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        int[] nums = new int[26];
        for(char ch : magazine.toCharArray())
            nums[ch-'a']++;
        for(char ch : ransomNote.toCharArray())
            if(nums[ch-'a'] == 0)   return false;
            else    nums[ch-'a']--;
        return true;
    }
}
```

### 15.三数之和

先记住两个 API：`Arrays.sort(nums)、``Arrays.asList(nums[i], nums[left], nums[right])`

**思路**：先排个序，方便很多，测试用例里也是排序号的！再用双指针 left、right 来定位。SUM>0 就right—，反之 left++。

**注意**：

- 如果多个相同数值，直接跳过该段！在哪个判断条件的循环就要注意哪个的“**雷同跳转**”。
- if 要匹配 else，否则进多个 if 就会导致上一条注意失效。

```Java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> list = new ArrayList<>();
        for(int i=0;i<nums.length;i++){
            if(nums[i] > 0) return list;//剪枝
            if(i>0 && nums[i]==nums[i-1])   continue;
            int left = i+1,right = nums.length-1;
            while(left < right){
                if(nums[i]+nums[left]+nums[right] < 0)  left++;
                else    if(nums[i]+nums[left]+nums[right] > 0)  right--;
                else    if(nums[i]+nums[left]+nums[right] == 0){
                            list.add(Arrays.asList(nums[i],nums[left],nums[right]));
                            while(right > left && nums[right] == nums[right-1]) right--;
                            while(right > left && nums[left] == nums[left+1]) left++;
                            right--;
                            left++;
                        }
            }
        }
        return list;
    }
}
```

### 18.四数之和

**思路** : 就是**三数之和**多了一个 for 循环。

```Java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        Arrays.sort(nums);
        List<List<Integer>> list = new ArrayList<>();
        for(int i = 0; i < nums.length-3 ; i++){
            if(nums[i]>0 && nums[i] > target) return list;//也可能负的越来越多，第一个是正数才成立。
            if(i > 0 && nums[i] == nums[i-1])    continue;
            for(int j = i+1; j < nums.length-2; j++){
                if(j > i+1 && nums[j] == nums[j-1])    continue;//因为i和第一个j一定是可以用的，而i和其第二个j（与j-1值相同）就导致元组重复。
                int left = j+1,right = nums.length-1;
                while(left < right){
                    long sum = (long) nums[i]+nums[j]+nums[left]+nums[right];
                    if(sum > target) right--;
                    else if(sum < target)    left++;
                    else{
                        list.add(Arrays.asList(nums[i],nums[j],nums[left],nums[right]));
                        while(left < right && nums[left] == nums[left+1])   left++;
                        while(left < right && nums[right] == nums[right-1])   right--;
                        left++;
                        right--;
                    }
                }
            } 
        }
        return list;
    }
}
```

**注意:**

```Java
if(j > i + 1  &&  nums[j] == nums[j-1]){continue;}
```

1. 这个条件是 j > i+1 ,而不是 j > 0  .
2. 还有就是 > target < target == target 绝对 if...else 而不是单独的多个 if .

持续更新.......  
如有错误，敬请斧正！！！




