
---
title: "LeetCode 栈、队列(二)"
date: 2022-09-01T09:23:42+08:00
author: ["小任同学"]
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
lastmod: 2022-09-01T23:54:37+08:00	#更新文章的时候手动改一下时间就可以
description: 栈、队列模块二刷。
tags: 
- 算法
---
### 232.用栈实现队列

**思路**：一个出栈的栈，一个入栈的栈实现一个队列，运用栈的四个 API ，在 pop 、peek 是要判断出栈的栈是否为空，为空就把入栈的栈内元素全 pop 到出栈的栈中。

注意：想清楚为什么 pop、peek stackIn要判空 Out 栈？因为什么？自己手动模拟。

```Java
class MyQueue {
    Stack<Integer> stackIn;
    Stack<Integer> stackOut;
    public MyQueue() {
        stackIn = new Stack<>();
        stackOut = new Stack<>();
    }
    
    public void push(int x) {
        stackIn.push(x);
    }
    
    public int pop() {
        outputIsNull();
        return stackOut.pop();
    }
    
    public int peek() {
        outputIsNull();
        return stackOut.peek();
    }
    
    public boolean empty() {
        return stackIn.isEmpty() && stackOut.isEmpty();
    }
    public void outputIsNull(){
        if(!stackOut.isEmpty()) return;
        while(!stackIn.isEmpty())   stackOut.push(stackIn.pop());
    }
}
```

### 225. 用队列实现栈

**思路**：挪到其中一个队列只剩一个元素再操作。

**注意**：API ：poll 相当于 pop、offer 相当于 push、其他不变。

```Java
class MyStack {
    Queue<Integer> queueIn;
    Queue<Integer> queueOut;

    public MyStack() {
        queueIn = new LinkedList<>();
        queueOut = new LinkedList<>();
    }
    
    public void push(int x) {
        queueIn.offer(x);
    }
    
    public int pop() {
        while(queueIn.size() > 1)
            queueOut.offer(queueIn.poll());
        Queue<Integer> temp = queueIn;
        queueIn = queueOut;
        queueOut = temp;
        return queueOut.poll();
    }
    
    public int top() {
        while(queueIn.size() > 1)
            queueOut.offer(queueIn.poll());
        int num =  queueIn.peek();
        queueOut.offer(queueIn.poll());
        Queue<Integer> temp = queueIn;
        queueIn = queueOut;
        queueOut = temp;
        return num;
    }
    
    public boolean empty() {
        return queueIn.isEmpty() && queueOut.isEmpty();
    }
}
```

### 20. 有效的括号

**思路**：遇见左括号就 push 右括号，否则就看栈是否提前空了、栈顶元素匹配当前括号，否则就 pop。

栈提前空了会导致后面就算有没匹配上的括号，最后 return 的依然是 true 。

```Java
class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        for(char ch : s.toCharArray()){
            if(ch == '(')   stack.push(')');
            else if(ch == '{')  stack.push('}');
            else if(ch == '[')  stack.push(']');
            else if(stack.isEmpty() || ch != stack.pop())  return false;//判空是为了防止pop异常，也可以说是为了最后数量不匹配。
        }
        if(!stack.isEmpty())    return false;
        return true;
    }
}
```

### 1047. 删除字符串中的所有相邻重复项

**思路**：不用栈也行，直接 sb 的最后一个字符去匹配。

Tips：StringBuilder的删除是 deleteCharAt(int index)

```Java
class Solution {
    public String removeDuplicates(String s) {
        StringBuilder sb = new StringBuilder();
        Stack<Character> stack = new Stack<>();
        for(int i=0;i<s.length();i++){
            if(!stack.isEmpty() && stack.peek()==s.charAt(i)){
                sb.deleteCharAt(sb.length()-1);
                stack.pop();
            }
            else{
                sb.append(s.charAt(i));
                stack.push(s.charAt(i));
            }
        }
        return new String(sb);
    }
}
```

### 150. 逆波兰表达式求值

Tips：与本题无关：数字（int）转字符（char）要强转( char ) ( 0 + ’ 0 ’ )，字符转数字隐式就可以了不用声明出来   ‘ 2 ’ - ’ 0 ’ ，但是为了书写统一，还是都强转一下。

API：Integer.valueOf( str );

```Java
class Solution {
    public int evalRPN(String[] tokens) {
        Stack<Integer> stack = new Stack<>();
        for(int i = 0; i < tokens.length; i++){
            if(tokens[i].equals("+")) {
                int a = stack.pop(), b = stack.pop();
                stack.push(b+a);
            }else if(tokens[i].equals("-")) {
                int a = stack.pop(), b = stack.pop();
                stack.push(b-a);
            }else if(tokens[i].equals("*")) {
                int a = stack.pop(), b = stack.pop();
                stack.push(b*a);
            }else if(tokens[i].equals("/")) {
                int a = stack.pop(), b = stack.pop();
                stack.push(b/a);
            }else stack.push(Integer.valueOf(tokens[i]));
        }
        return stack.pop();
    }
}
```

### 239. 滑动窗口最大值

**思路**：自己用双端队列实现一个队列，add 方法来保证单调（队头始终为最大值），poll 方法保证窗口的滑动。每次取队头给记录窗口最大值的数组赋值就行了。如何设计 add 、poll ？

add：一直比较队尾，如果小于要 add 的 val，就杀掉这个队尾，直到上一个元素大于 val，此时 add  这个 val。

poll：拿到 ”窗口最后一个值” 与 队头（最大值）去比较，如果相等，就杀死队头，因为这个本该从滑动窗口走出的元素影响最大值的判断了。否则就不操作。

**注意**：

Deque 双端队列才可以取队尾（getLast（ ）、removeLast（ ）），Queue 单端队列不可以。

Deque 双端队列中，add（）才是在队尾添加元素。

peek、poll 必须保证队列不为空。

```Java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if(nums ==null || nums.length == 0 || nums.length == 1 || k == 1)
            return nums;
        int q = 0, max;
        int [] res = new int[nums.length-k+1];
        MyQueue deque = new MyQueue();
        //填充前k个
        for(int i = 0; i < k; i++)
            deque.add(nums[i]);
        res[q++] = deque.peek();
        for(int i = k; i < nums.length; i++){
            deque.pop(nums[i-k]);
            deque.add(nums[i]);
            res[q++] = deque.peek();
        }
        return res;
    }  
}
class MyQueue{
    Deque<Integer> deque = new LinkedList<>();
    int peek(){
        return deque.peek();
    } 
    void pop(int val){
        if(!deque.isEmpty() && val == deque.peek())     deque.pop();
    }
    void add(int val){
        while(!deque.isEmpty() && val > deque.getLast())
            deque.removeLast();
        deque.add(val);
    }
}
```

### **347.前 K 个高频元素**

暴力解法：留意语法错误、API。

```Java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        int p = 0, q = 0;
        int[] res = new int[k];
        Map<Integer,Integer> map = new HashMap<>();
        for(int i = 0; i < nums.length; i++)
            if(map.containsKey(nums[i]))   map.put(nums[i],map.get(nums[i])+1);
            else map.put(nums[i],1);
        int[] temp1 = new int[nums.length];
        int[] temp2 = new int[nums.length];
        for(int i : map.keySet())
            temp1[p++] = i;
        for(int i : map.keySet())
            temp2[q++] = map.get(i);
        for(int i=0;i<nums.length-1;i++){
            for(int j=i+1;j<nums.length;j++)
                if(temp2[i] < temp2[j]){
                    int temp = temp2[i];
                    temp2[i] = temp2[j];
                    temp2[j] = temp;
                    temp = temp1[i];
                    temp1[i] = temp1[j];
                    temp1[j] = temp;
                }
        }
        p = 0;
        for(int i = 0; i < k; i++)
            res[p++] = temp1[i];
        return res;
    }
}
```

持续更新.......  
如有错误，敬请斧正！！！




