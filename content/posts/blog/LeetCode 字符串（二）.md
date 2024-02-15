
---
title: "LeetCode 字符串(二)"
date: 2022-09-01T07:23:42+08:00
author: ["小任同学"]
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
lastmod: 2022-09-01T23:34:37+08:00	#更新文章的时候手动改一下时间就可以
description: 字符串模块二刷。
tags: 
- 算法
---
### **344. 反转字符串**

```Java
class Solution {
    public void reverseString(char[] s) {
        char temp;
        for(int i=0;i<s.length/2;i++){
            temp = s[i];
            s[i] = s[s.length-1-i];
            s[s.length-1-i] = temp;
        }
    }
}
```

### **541. 反转字符串 II**

字符串切割从第 i 位置到 k + i - 1 才是切下 k 个字符 .

判断条件没有很复杂,就是拿到的个数 > k 就操作反转前 k 个, < k 直接全反转.

就是 ≥2k 和 2k ≥ len ≥ k  ,两个区间都是用一操作,融合了. 

```Java
class Solution {
    public String reverseStr(String s, int k) {
        char[] ch = s.toCharArray();
        for(int i = 0; i < ch.length; i+=2*k){
            char temp ;
            int begin = i, end = Math.min(i+k-1,ch.length-1);
            while(begin < end){
                temp = ch[begin];
                ch[begin] = ch[end];
                ch[end] = temp;
                begin++;
                end--;
            }
        }
        return new String(ch);
    }
}
```

### 剑指Offer 05.替换空格

**注意** : 只有 String 转换为了 StringBuffer or StringBuilder 才能用 append && toString 方法.

```Java
class Solution {
    public String replaceSpace(String s) {
        int count = 0,i = 0;
        for(char c : s.toCharArray())
            if(c == ' ')    count++;
        char[] ch = new char[s.length()+2*count];
        for(char c : s.toCharArray()){
            if(c == ' '){
                 ch[i++] = '%';
                 ch[i++] = '2';
                 ch[i++] = '0';
            }   
            else
                ch[i++] = c;
        }
        return new String(ch);
    }
}
```

### **151. 翻转字符串里的单词**

**思路**：先全部反转、再依次反转各个 word。

记住几个 API ： charAt、setChatAt，操作 String 先转换成 StringBuilder or StringBuffer 再操作。

注意要移除开头结尾的 space、word 间连续的 space。

其中去除连续的 space 注意判断条件 当前 char != space || sb最后一个 char 不为 space（上一个 char 不为 space），两个判断条件反了会短路。

```Java
class Solution {
    public String reverseWords(String s) {
        //删除开头结尾中间的空格
        StringBuilder sb = removeSpace(s);
        reverseString(sb,0,sb.length()-1);
        reverseEachWord(sb);
        return sb.toString();
    }
    public StringBuilder removeSpace(String s){
        int left = 0, right = s.length()-1;
        StringBuilder sb = new StringBuilder();
        while(s.charAt(left) == ' ') left++;
        while(s.charAt(right) == ' ') right--;
        while(right >= left){
            if(s.charAt(left) != ' ' || sb.charAt(sb.length()-1) != ' ')  sb.append(s.charAt(left));
            left++;
        }
        return sb;
    }
    public void reverseString(StringBuilder sb,int left,int right){
        while(left < right){
            char temp = sb.charAt(left);
            sb.setCharAt(left,sb.charAt(right));
            sb.setCharAt(right,temp);
            left++;
            right--;
        }
    }
    public void reverseEachWord(StringBuilder sb){
        int left = 0,right;
        while(left < sb.length()-1){
            right = left+1;
            while(right != sb.length() && sb.charAt(right) != ' ')//为什么不是len-1？ 因为最后是right-1当右边界操作。并且&&先后顺序乱了就会越界。
                right++;
            reverseString(sb,left,right-1);
            left = right+1;
        }
    }
}
```

### **剑指Offer58-II.左旋转字符串**

**思路**：把 n 后面的先 append 到 new 的 sb 上，再把 n 之前的 append 到 sb 。

```Java
class Solution {
    public String reverseLeftWords(String s, int n) {
        StringBuilder sb = new StringBuilder();
        for(int i=n;i<s.length();i++)
            sb.append(s.charAt(i));
        for(int i=0;i<n;i++)
            sb.append(s.charAt(i));
        return sb.toString();
    }
}
```

### **28. 实现 strStr()**

KMP

### 459. 重复的子字符串

**KMP**

持续更新.......  
如有错误，敬请斧正！！！




