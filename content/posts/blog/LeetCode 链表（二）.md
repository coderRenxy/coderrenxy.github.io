
---
title: "LeetCode 链表（二）"
date: 2022-09-01T05:23:42+08:00
author: ["小任同学"]
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
lastmod: 2022-09-01T23:33:37+08:00	#更新文章的时候手动改一下时间就可以
description: 二刷链表。
tags: 
- 算法
---

### [**203. 移除链表元素**](https://leetcode-cn.com/problems/remove-linked-list-elements/)

**思路**：先处理头结点 null 或者 val ，再去处理移除节点。

**注意**：返回值返回 head。

```Java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        //处理头结点为 val 或为 null
        while(head != null && head.val == val)
            head = head.next;
        if(head == null)  return null;
        ListNode p = head;
        ListNode q = p.next;
        while(q != null){
            if(q.val == val){
                p.next = q.next;
                q = p.next;
            }else{
                p = q;
                q = q.next;
            }
        }
        return head;
    }
}
```

### [707. 设计链表](https://leetcode-cn.com/problems/design-linked-list/)

**思路**：先定义好 class ，然后在已有的 class 定义一个 size 和 头结点 head，然后写出 addAtIndex，其它都可以调用或者抄代码 ；

**注意**：size 要变化。

```Java
class ListNode{
    int val;
    ListNode next;
    ListNode(){}
    ListNode(int val){this.val = val;}
}
class MyLinkedList {
    ListNode head;
    int size;

    public MyLinkedList() {//初始化链表
        int size = 0;
        head = new ListNode(0);
    }
    
    public int get(int index) {
        if(index >= size || index < 0)   return -1;
        ListNode p = head;
        for(int i = 0; i <= index; i++)
            p = p.next;
        return p.val;
    }
    
    public void addAtHead(int val) {
        addAtIndex(0,val);
    }
    
    public void addAtTail(int val) {
        addAtIndex(size, val);
    }
    
    public void addAtIndex(int index, int val) {
        if(index > size)    return;
        if(index < 0)
            index = 0;
        size++;
        ListNode Node = new ListNode(val);
        ListNode p;
        p = head;
        for(int i = 0; i < index; i++)
            p = p.next;
        Node.next = p.next;
        p.next = Node;
    }
    
    public void deleteAtIndex(int index) {
        if(index >= size || index < 0)    return;
        ListNode p;
        p = head;
        for(int i = 0; i < index; i++)
            p = p.next;
        p.next = p.next.next;
        size--;
    }
}
```

### 206. 反转链表

思路：先写出循环体再思考细枝末节，例如最后一个节点要单独写并返回一个新的头结点，再比如在循环之前要判空 head ，防止 next = [cur.next](http://cur.next) 报异常。

```Java
class Solution {
    public ListNode reverseList(ListNode head) {
        if(head == null)    return null;
        ListNode pre = null;
        ListNode cur = head;
        ListNode next = cur.next;
        while(next != null){
            cur.next = pre;
            pre = cur;
            cur = next;
            next = cur.next;
        }
        cur.next = pre;
        return cur;

    }
}
```

### 24. 两两交换链表中的结点

**思路**：创建一个虚拟头结点再用 pre 指向它，最后返回的是 [虚拟头结点.next](http://xn--2ssv8t8rltwnh1i.next) 这个新的头结点，额外加一个 temp [节点缓存 pre.next.next](http://xn--pre-369er75g38r0pf.next.next) 其实也就相当于 next ，head 节点已经可以自由使用了，因为返回的头结点与 head 无关，head 用来缓存 pre.next 相当于 cur ，三个节点都缓存了，这三个节点相互操作就很容易，先把三个节点外的节点操作完成。然后去操作里面，再在循环中移动这几个节点。

```Java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head == null)    return null;
        ListNode pre,temp;
        ListNode dummyNode = new ListNode(0);
        dummyNode.next = head;
        pre = dummyNode;
        
        while(pre.next != null && pre.next.next != null){
            temp = head.next;
            head.next = temp.next;
            pre.next = temp;
            temp.next = head;
            pre = head;
            head = head.next;
        }
        return dummyNode.next;
    }
}
```

### **19.删除链表的倒数第N个节点**

**暴力解法**：注意考虑传入的值的合法性

```Java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        if(head == null)    return null;
        ListNode cur;
        cur = head;
        int i = 0;
        while(cur != null){//计算长度
            cur = cur.next;
            i++;
        }
        if(i == n)  //删除的是头结点（单独讨论），也避免只有一个节点时 cur.next.next 为 null
            return head.next;
        i = i-n;
        cur = head;
        while(i > 1){
            cur = cur.next;
            i--;
        }
        cur.next = cur.next.next;
        return head;
    }
}
```

**进阶版本**：首先一边扫描肯定无从下手，想想，倒数第 n 个结点，是正着数的第几个 ? 

显而易见：size-n 个，那如何拼凑出 size ？那是肯定要扫描一遍完整的链表结点的。

如果扫描 size++ 出长度那必然要再操作一遍，落入了扫描多次的圈套，so what？

定义一个 fast 指针、一个 slow 指针，fast 扫描全文，而 slow 指向要被删除的结点的上一结点。如何凑出 **size -n**？先让 fast 走 n 次，再让 fast 和 slow 一起走剩下的 **size-n** 次，此时 fast 指向了最后一个元素，slow 指向了删除的结点的上一结点。用 **虚拟头结点** 会减少很多麻烦。

```Java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        if(head == null || head.next == null)    return null;
        ListNode fast,slow,pre;
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        pre = slow = fast = dummy;
        int i = 1;
        while(i < n){
            fast = fast.next;
            i++;
        }
        while(fast.next != null){
            pre = slow;
            fast = fast.next;
            slow = slow.next;
        }
        pre.next = slow.next;
        return dummy.next;
    }
}
```

### **面试题 02.07. 链表相交**

**暴力**：两层循环遍历，注意有的在 A 链表的位置不一定对应 B 链表的位置。

```Java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if(headA == null || headB == null)  return null;
        ListNode node1 = headA;
        ListNode node2 = headB;
        while(node1 != null){
            node2 = headB;
            while(node2 != null){
                if(node2 == node1)  return node2;
                else    node2 = node2.next;
            }
            node1 = node1.next;
        }
        return null;
    }
}
```

**巧解**：AB一定在某一点会相遇并且之后的链表重合，后面重合有个特点，节点数相等。也就是说要在某点相遇必定要链表后半段节点数相等，也就是前面可以有一个长度差不去扫描，从 “剩余共同长度“ 的位置开始，也就是跳过前一小段。两条链表一起扫描，避免了双层扫描。

```Java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if(headA == null || headB == null)  return null;
        ListNode node1 = headA, temp;
        ListNode node2 = headB;
    int lenA = 0, lenB = 0, gap = 0; 
        while(node1 != null){
      lenA++;
            node1 = node1.next;
        }
        while(node2 != null){
      lenB++;
            node2 = node2.next;
        }
        node1 = headA;
        node2 = headB;
        if(lenA < lenB){
            node1 = headB;
            node2 = headA;
        } 
        gap = Math.abs(lenA-lenB);
        while(gap-- > 0)  node1 = node1.next;
        while(node1 != null && node1 != node2){
            node1 = node1.next;
            node2 = node2.next;
        }
        return node1;
    }
}
```

### **142.环形链表II**

两个**重要点**

1. 从**头结点**出发一个指针，从**相遇节点** 也出发一个指针，这两个指针每次只走一个节点， 那么当这两个指针相遇的时候就是 环形入口的节点。
2. **快指针**永远走的是**慢指针**的两倍长度。

**解释**一下：

先判断有没有环，while 下 快指针走 2 步，慢指针走 1 步，如果两个结点 位置一致，即为有环，否则无环则一定会 fast.next  || [fast.next.next](http://fast.next.next) ==null。

从**头节点**到环**入口结点**距离为 A，环**入口节点**到**相遇结点正向**距离为 B，**相遇节点正向**到入口节点距离为 C。一定是在环内相遇，且 A == C。

~~为什么 (x + y) * 2 = x + y + n (y + z)，其中没必要纠结 y ≥ b+c 即圈长与否，也就是说走了多少圈的 y 都可以，因为 y+z 一定是整圈，因为 y 要么是 ~~**~~环入口到相遇点的距离~~** 要么是 ~~ n*(y+z)+y ，总之会多余一个 y ，再加上 c 一定是整圈。~~

fast在slow前面一步，那么下一步追上。

fast在slow前面两步，那么下一步变成只差一步。

slow 进 环入口走不到一圈一定会被追上，这种走一圈的极端条件 fast 走了两圈，而即使第一圈擦肩而过，第二圈也一定会相遇。

```Java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if(head == null)    return null;
        ListNode fast = head ,slow = head;
        while(fast.next != null && fast.next.next != null){
            fast = fast.next.next;
            slow = slow.next;
            if(slow == fast){//必定有环
                fast = head;
                while(slow != fast){
                    slow = slow.next;
                    fast = fast.next;
                }
                return slow;
            }
        }
        return null;
    }
}
```


