
---
title: "LeetCode 二叉树(二)"
date: 2022-09-01T09:29:42+08:00
author: ["小任同学"]
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
lastmod: 2022-09-01T23:58:37+08:00	#更新文章的时候手动改一下时间就可以
description: 二叉树模块二刷。
tags: 
- 算法
---
**前置知识填充篇**：

**满二叉树**：深度为 k 的满二叉树在深度为 k 层也都有左右子树。

**完全二叉树**：深度为 k 的完全二叉树除了第 k 层节点**可能**没填满外，其余每层节点数都达到最大值，并且第 k 层的节点都集中在该层最左边的若干位置。

**二叉搜索树**：有序树，左孩子不为空的情况下，结点的左孩子比结点数值小，右孩子不为空的情况下，右节点比节点数值大。

**平衡二叉树（AVL）**：是一棵空树 或者 它的左右子树高度值绝对值不超过1 且左右子树都是平衡树。

**二叉树的存储**：

二叉树可以链式存储，也可以顺序存储。

链式存储就是用指针串起来，内存不连续，顺序存储用数组，内存连续。

如果父节点的数组下标是 i，那么它的左孩子就是 i * 2 + 1，右孩子就是 i * 2 + 2。

**二叉树的遍历**：

1. 深度优先遍历：先往深处走，遇到叶子节点在往回走。分为《前中后序遍历》，这个前中后其实是中间结点的顺序，前序：中左右，中序：左中右，后序：左右中。
2. 广度优先遍历：一层一层的遍历。《层次遍历》（迭代法）

### 144.二叉树的前序遍历 & 145.二叉树的后序遍历 &  94.二叉树的中序遍历

递归做法：三个遍历一个方法代码挪一下位置。

迭代做法：先把图画出来标号顺序，再模拟过程。前后序差不多，中序 while 条件不一样。后序在得不到结果的时候想一下逆序再反转（Collection.reverse(result)）。

注意：

1. 迭代中序是复杂点，不断地往左推，到最左再依次弹出，弹出要注意父节点的 right 有没有，就这条思路。
2. 后序 不是 直接 前序 倒转，而是把 前序 的左右孩子入栈顺序倒转再 反转。
3. 前序：中左右。 后序：左右中。前序调转入栈顺序：中右左，反转：左右中=后序。

```Java
class Solution {//中序遍历
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        if(root == null)    return list;
        Stack<TreeNode> stack = new Stack<>();
        TreeNode cur = root;
        while(cur != null || !stack.isEmpty()){
            if(cur != null){
                stack.push(cur);
                cur = cur.left; 
            }else{
                cur = stack.pop();
                list.add(cur.val);
                cur = cur.right;
            }
        }
        return list;
    }
}
```

### 102.二叉树的层序遍历

**递归思路**：仍然是先深度到顶确定好 deep 把每一深度的 ArrayList new 出来，再往里依次添值。

是否创建 ArrayList 用 if 判断，进了递归的方法 deep 就要 +1，因为递归进递归会先走到 left 的底，再依次出递归 deep 只会回到原来的值。总之每次进递归的方法的初始参数都是当前参数中的结点、参数节点的上一深度，然后 deep++，就变成了当前节点、当前节点深度操作。

这题二刷肯定思路从0开始，大家都这样，不要放弃！！！

**二刷**思路：用 deep 和 list 的 size 来判断是否需要 new 新的 listSon。

```Java
class Solution {
    List<List<Integer>> list;
    public List<List<Integer>> levelOrder(TreeNode root) {
        list = new ArrayList<>();
        bfs(root,0);
        return list;
    }
    public void bfs(TreeNode root,int deep){
        if(root == null) return;
        deep++;
        if(list.size() < deep){
            List<Integer> item = new ArrayList<>();
            list.add(item);
        }
        list.get(deep-1).add(root.val);
        bfs(root.left,deep);
        bfs(root.right,deep);
    }
}
```

**迭代思路(推荐)**：利用队列的先进先出，起初队列只有一个 root ，然后构建一个循环（在每次循环都新建一个 ArrayList ，代表存每一个深度的所有元素值，该循环内嵌套一个循环拜访每个该深度的结点的值进 ArrayList 以及该节点的左右孩子并 offer 进队列。问题来了：如何判断每个深度有几个树节点？即如何确定内存循环的循环次数 ?在每次进外层循环时，都把当前的队列长度都定义到 len 变量上，该队列每次把一层的树节点长度记录下来，全都 poll 掉了剩下的就是下一深度的所有树节点。外循环应该好判断的吧 ? 当队列为空时，即整个深度 offer 不进去树节点时，就意味着全部遍历完了。

**二刷**思路：把 root 放进去队列，poll 出来 add(val)，然后放入左右孩子，poll 出来 add(val)。一层孩子 new 一个 list，而结束条件是 !queue.isEmpty() ，控制层层分离的则是 最初操作的 queue.size() 。

```Java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> list = new ArrayList<>();
        if(root == null)    return list;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        TreeNode temp = null;
        while(!queue.isEmpty()){
            List<Integer> listSon = new ArrayList<>();
            int len = queue.size();
            while(len > 0){
                temp = queue.poll();
                listSon.add(temp.val);
                if(temp.left != null)   queue.offer(temp.left);
                if(temp.right != null)  queue.offer(temp.right);
                len--;
            }
            list.add(listSon);
        }
        return list;
        }
}
```

### 107.二叉树的层次遍历II

就是用上题的迭代再反转，反转就把装有ArrayList<Integer>的 list 从尾到头的 add 到另一个 result 就行。

### 199.二叉树的右视图

利用层序迭代遍历留住每层的最后一个元素值。

### 637.二叉树的层平均值

还是层序遍历。

### 429.N叉树的层序遍历

主要注意定义的 Node ，有属性名为 children 的 List ，里面全是当前节点的孩子，再正常层序遍历就好了。

### 515.在每个树行中找最大值

类似层序遍历留住最后一个元素。

### 116.填充每个节点的下一个右侧节点指针

思路：很简单，自己用层序遍历套娃。

注意：老是忘记 offer root 结点进入队列，进入了就容易在 len 上判断失误：

//需要判断 len 是否等于 1 ，如果队列为空了，peek 出来就是 null 。但是队列在下一层有元素时，并不会为 null ，所以需要 len 来判断当前值。

### 117.填充每个节点的下一个右侧节点指针II

和116的差距就是这里不是完全二叉树，逻辑代码都一样。

### 104.二叉树的最大深度

做了前面的还不会请自行gg.....

### 111.二叉树的最小深度

有坑，不是说 root.left 节点为空最小深度就是1，是从根节点到最近叶子节点。只有一个结点的 left、right 都为 null ，才是叶子节点。

**注意**：一定要在第一步判空:     if(root == null)   ..............

### 226.翻转二叉树

**递归**：确定参数类型、返回值 —> 确定终止条件 —> 确定单层逻辑。先 coding 出交换代码的单层逻辑，再左右子树递归。

层序遍历在内层循环也能处理。

### 101. 对称二叉树

不管是递归还是迭代，都要判断比较的两个节点都为 null、一个为 null、两个都不为 null是否相等。

**递归**很好做，但是不能很好地理解题意。单层逻辑就是判断以上条件并 return 两两结点再进该方法。

**迭代**：利用队列，推入二层的两个节点，所有判空都在循环中进行。然后推四个孩子。

其实还是迭代更具普适性。

### 100.相同的树

**递归**：同101，不过在递归的参数 node1 和 node2 都为 null 容易不理解（101就迷糊了的问题），为什么返回 null，返回 null 并不会结束整个程序，只会把那一层递归的程序结束，最后 return 的是两个判断的 &&。

**迭代**：同101。

### 572.另一个树的子树

据题意，该 t 树要么是等于 s 树，要么是其左子树 or 右子树。定义一个判断是否相等树的方法，再 || 到两个 isSubtree 方法。

**注意**：在 isSubtree 方法一定要判空 s 结点，不然就算在 isSametree 方法中能判空，再进到 isSubtree 也还是空，再 isSubtree.left 就空指针了。就算刚开始传入的 s 不为 null，但是 s 一直在变化，总会有出现 null 的情况出现。任何一个递归一定是有一个结束条件来收敛！

### 222.完全二叉树的节点个数

迭代：一个循环粗糙层序遍历。

递归：return 1+左递归+右递归。

**注意**：用 offer 要判空“值”，poll 不需要判空“队列”。

### 110.平衡二叉树

**思路**：没什么东西，bfs、dfs都行，两个相减再用两个孩子递归。

**注意**：在处理deep时要抠清楚，正常相减 deep 起始值无所谓，但是，如果节点为 null 那返回的一定是 0，deep 初始值只与这里有关。

**拓展**：bfs 万精油， dfs 前序求深度，后序求高度。

**二刷**：实在没想到思路，竟然是这样。递归一个求深度方法；

### 257. 二叉树的所有路径

**思路**：跟深度相关，首选 dfs的前序遍历，然后肯定是要递归的，在此之上还要回溯：进到多少层，结束了再出来，出一层删最后一个元素，出n层删n个元素。两个list都放做递归方法的参数，因为两个都要保持不变得用，所有操作都要手动做！递归的结束条件是当前节点的左右孩子为空。

**二刷**：主要就是用左右孩子为空当作依据来操作，先去 List<Integer> 出来 path ，再去操作字符串，不然append(root.left.val).append(”→”) 这样很难回溯。

### 404.左叶子之和

**思路**：不同条件进不同的处理，当前结点（左节点）为不为叶子结点要判断，当前节点的右节点为不为空也要判断。

**注意**：一定要判断是否为叶子结点，是求**左叶子**结点和，不是左节点和。

### 513.找树左下角的值

**思路**：层序遍历只保存最左边的值，很简单，有足够的时间优雅一点！

### 112. 路径总和

**思路**：主要就是递归+回溯，和257差不多。

注意：return 是结束当前递归层，如果要影响到其他层，则需要具有传递性的语句。

### 113. 路径总和 ll

**思路**：和 day 19 的 112 差不多思路（递归+回溯），不同之处在于判空处理。因为是要存入路径，所以参数多了两个 List：一个用于存多条路径，一个用于增删单条路径。

**注意**：这条增删单条路径的，在判空捕捉到 add 该条路径时，必须 new 一个新的 List ，不然还是操作的那条是始终在变的。

### 106.从中序与后序遍历序列构造二叉树

**key1**：中后序的第一个元素一定是左下角那个，因为中序是：左中右，后序是：左右中。都是先递归左。、后序的最后一个元素一定是 root，左右中，最后才吃到 root。

**key2**：后序遍历最后一个节点是根节点，中序遍历根节点左边是左子树的节点，右边是右子树的结点。一个边界是某数组只有一个元素，另一个是数组为空，why？因为在递归传递参数时有+1、-1的操作，所以当有一个节点、无结点单独拎出来。然后分别找到了左右子树的根节点，加到当前的根节点的左右孩子位置，然后递归左右子树。

**key3：**

1. ****后序中结点分布应该是：[左子树结点，右子树结点，根结点]；
2. 中序中结点分布应该是：[左子树结点，根结点，右子树结点]；
3. 左右子树的节点分别去左右子树匹配；

**key4**：前序的左右数组 与 中序的左右数组一定是分别一样长度。在切割时利用。

**注意**：

1. 开闭区间，[] 的话传入的参数应该是0、leng-1、0、leng-1，如果是 ”左闭右开“ 的话传入的参数应该是0、leng、0、leng。切割处理也不同。
2. 后序要抠掉最后一个结点，因为这个结点 new 出来了。

### 105.从前序与中序遍历序列构造二叉树

同 106 所述，就是抠的是左边界。

### 二刷构建树

前序：[root,root.left,root.right]

中序：[root.left,root,root.right]

后序：[root.left,root.right,root]

前序遍历的第一个，后序遍历的最后一个元素就是树的根节点，根据这根节点可以在 **中序遍历** 中找到 val 相同的节点，也就是这棵树的根节点，这个节点的左边就是左子树的所有节点，右边就是右子树的所有结点。这样我们知道了左右子树各自的节点个数。根据前序数组，root,左子树所有节点，右子树所有节点，可以拿到左子树的所有节点的前序遍历、右子树所有节点的后序遍历，继续递归。后序遍历同理。结束条件是中序 left > right,left == right 时 new这个节点。

### 654.最大二叉树

**错误**思路：先要知道，它不像前中后序那样一直分左右数组，而是第一次就分好，后面无需再分左右数组，只要不断的操作这个数组，detail 就是一直移除一个元素，什么最好，显然可以 remove 的 ArrayList better，so，还要明确第一次是放在左孩子位置，后面是递归放在左子树的右孩子，另一子树与之相反，亦复如是。如何控制这个次数，显然加一个参数 deep 最好，if ... else 把出现次数多的放在 if 中。错在题意理解上。

**正确**思路：就是递归不断将左右两边去找最大那个返回，并将其左右数组递归。判空条件和105、106一样。left > right 时没有元素了，left == right 时只有一个元素直接 return new TreeNode();

注意：在初始化 index 时，不要随意初始化，初始化为 left，只要在 [left，right] 都行。

**二刷**：类似建树，比建树简单多了。

### 617.合并二叉树

**思路**：两个结点都为 null 或其中一个为 null 为终止条件，都不为 null 就合并再 return 。

**注意**：当有一个节点为 null 但是另一个节点存在时，应该 return 该节点而不是把该结点的值赋给 new 出来的新节点，这样就不会丢失它的左右孩子了，如果某深度为 null 了。那更深处必然都为 null，要合并时将该节点直接移过去左右孩子就不用考虑了。

### 700.二叉搜索树中的搜索

略......

### 98.验证二叉搜索树

中序遍历然后把 root 值作为目标值，在目标值左边都要小于 root.val ，在目标值右边都要大于 root.val 。然后在 return 中递归左右子树。

### 530.二叉搜索树的最小绝对差

暴力解法：直接遍历出 list，再递归左右子树去遍历差值。

优雅解法：用这个解法首先把二叉搜索树的特点：左小右大 结合进来了，左子树的最右后代（左孩子的右孩子的右孩子的右孩子.......）、右子树的最左后代是值最接近根节点的结点。

**二刷**：左子树最右子节点的递归方法，右子树最左子节点的递归方法。

### 501.二叉搜索树中的众数

**暴力**解法：遍历整棵树并在过程中 put 进去结点的 值（key）、出现频率（value）。再拿到 map 中出现频率（value）最大的值（key）。

注意：要不断更新存储最大频率 key 的 List。如果有新的最大频率就清空（clear） List 再 add，出现同样频率就 add 。 

**迭代**法：利用二叉搜索树的特点，类中序遍历，记录当前、上一结点。

### 236. 二叉树的最近公共祖先

**暴力**解法：分别记录找到 p 、q 的路径（递归+回溯）再双层 for 找最末位置的匹配项。

```Java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root == null)    return null;
        TreeNode node = root;
        List<TreeNode> list1 = new ArrayList<>();
        List<TreeNode> list2 = new ArrayList<>();
        getPath(list1, root, p);
        getPath(list2, root, q);
        for(int i=0; i<list1.size(); i++){
            for(int j=0; j<list2.size(); j++){
                if(list1.get(i).val == list2.get(j).val)
                    node = list1.get(i);
            }
        }
        return node;
    }
    public boolean getPath(List<TreeNode> list, TreeNode root, TreeNode target){
        if(root.val == target.val){
            list.add(target);
            return true; 
        }
        if(root.left != null){
            list.add(root);
            if(getPath(list, root.left, target)) return true;
            list.remove(root);
        }
        if(root.right != null){
            list.add(root);
            if(getPath(list, root.right, target)) return true;
            list.remove(root);
        }
        return false;
    }
}
```

最优解法**思路**：后序遍历，从下往上找，找到目标 p、q 就存下来，这个 p、q 必然是某棵子数的左右孩子，找到了左右孩子分别不断往上传递，到了最小祖宗深度之后直接不断返回 root。

```Java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {//就算p、q有一个是根节点，也会在这里将其当作最小祖宗结点返回。
        if(root == null || root == p || root == q)    return root;//不管这个结点是叶子结点、p、q都会返回来，是叶子节点就返回一个null（到递归层）。否则返回一个p、q（到递归层）
        TreeNode left = lowestCommonAncestor(root.left, p, q);//左子树去遍历寻找p、q，递归层（遍历过程）找到了p、q就会返回回来并保存下来，如果到了叶子节点返回的为null就存不到结点
        TreeNode right = lowestCommonAncestor(root.right, p, q);//右子树去遍历寻找p、q
        //具体的判断找没找到那个p、q，找到就不断往上次递归层传递。上层递归再判断是否p、q齐全，以上两行递归完便找到了left、right，都找到才会回到deep=最小祖宗这一行返回root，更深处都是不会返回root，然后以上的deep层层跳出递归都是走return root这行。
        if(left != null && right != null)   return root;
        if(left != null && right == null)   return left;
        if(left == null && right != null)   return right;
        return null;
    }
}
```

**二刷**：从root向下找如果找到了p||q就return，否则就向下遍历，递归到了最深处还没找到返回值就是null，把左边找到的值用 left 传递，没找到就是 null 返回，然后做判断，左右孩子都是null就返回null，找到 p、q 任意一个都返回，也就是 left、right 会层层传递，左右孩子都不为空就返回 root。

### 235. 二叉搜索树的最近公共祖先

**key**：第一个出现在 (p.val,q.val) 的结点就是搜索二叉树的最近公共祖先。

```Java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root.val > p.val && root.val > q.val)    return lowestCommonAncestor(root.left , p, q);
        if(root.val < p.val && root.val < q.val)    return lowestCommonAncestor(root.right, p, q);
        return root;
    }
}
```

### 701.二叉搜索树中的插入操作

**key**：如果 root.val > target 递归左子树找最接近的点，如果遇到 null 就 return new target，比到直到 new 了一个结点，也就是搞定了，会一路 return 回去，至于为什么一路都是 return 呢？因为 > \ < 只会走进一个 if ，进了出来只会去 return，return 是当前这个点，而我们 insert 结点是在最深一层遍历，插入的位置也是一个结点的 左/右 孩子，然后一路返回的都是之前存在的结点。

```Java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if(root == null)    return new TreeNode(val);
        if(val > root.val)  root.right = insertIntoBST(root.right, val);
        if(val < root.val)  root.left  = insertIntoBST(root.left , val);
        return root;
    }
}
```

### 450.删除二叉搜索树中的节点

当找到了删除节点时：

**key1**：当删除节点的左右节点都为 null，return null；

**key2**：当删除结点左右孩子都不为空，找被删 root 的右节点的最左祖孙 并将被删 root 的 left 作为 root右节点最左祖孙的 left 孩子。

**key3**：当 root.left or root.right 为 null，return 不为空的结点。

否则就递归并将返回为左右孩子。

**code**：

```Java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if(root == null || (root.val == key && root.left == null && root.right ==null))   
            return null; 
        return deleteNode1(root, key);
    }
    public TreeNode deleteNode1(TreeNode root, int key) {
        if(root == null)    return null;
        if(root.val > key)  root.left  = deleteNode1(root.left , key);
        if(root.val < key)  root.right = deleteNode1(root.right, key);
        if(root.val == key){ 
            if(root.right != null && root.left != null){
                //无论该处是否为null，都将root.right传进来取root.right最左祖孙。
                TreeNode temp = root.right;//6
                while(temp.left != null)//找到最左祖孙。
                    temp = temp.left;
                temp.left = root.left;
                return root.right;
            }else if(root.right != null && root.left == null){
                return root.right; 
            }else if(root.right == null && root.left != null){
                return root.left;
            }else{
                return null;
            }
        }
        return root;
    }
}
```

**二刷**：root.right 节点当作新的 root，root.left 变成 root.right 的最左节点的孩子

注意：用 root.left  = 递归(root.left , key) 的方式，来传递，这样才能把 key 对应的 node 删除并且 让 node 的父节点去链接 noder.right 节点。找右孩子的最左祖孙时可以 while 循环来找。

### 669. 修剪二叉搜索树

**思路**：分别处理不正常结点，正常的节点。

```Java
class Solution {
    public TreeNode trimBST(TreeNode root, int low, int high) {
        if(root == null)    return null;
        //处理不正常的节点
        if(root.val < low)   return trimBST(root.right, low, high);
        if(root.val > high)  return trimBST(root.left , low, high);
        //处理正常节点
        root.left  = trimBST(root.left , low, high);
        root.right = trimBST(root.right, low, high);
        return root;
    }
}
```

### 108.将有序数组转换为二叉搜索树

**思路**：分别递归左右区间

**注意**：

1. 左右开闭一定要统一起来，我都是都闭着。
2. 左右区间去递归一个方法。

```Java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        return method1(nums, 0, nums.length-1);
    }
    public TreeNode method1(int[] nums,int left,int right){
        if(left > right)    return null;                   
        if(left == right)   return new TreeNode(nums[left]);
        int mid = left + (right-left)/2;
        TreeNode root = new TreeNode(nums[mid]);
        root.left  = method1(nums, left, mid-1);
        root.right = method1(nums, mid+1, right); 
        return root;
    }
}
```

### 538.把二叉搜索树转换为累加树

**思路**：这道题最难的就是看懂题，如果换成数组看就比较好看出来，从右下角的 right 到 root 到 left。相当于就是一个反转了的中序 dfs。

**二刷**：最难的是题意，注意，变化后左子树也变成了左孩子大于右孩子，已经不是二叉搜索树了。

```Java
class Solution {
    int sum = 0;
    public TreeNode convertBST(TreeNode root) {
        dfs(root);
        return root;
    }
    public void dfs(TreeNode root){
        if(root == null)    return ;
        dfs(root.right);
        sum+=root.val;
        root.val = sum;
        dfs(root.left);
    }
}
```



持续更新.......  
如有错误，敬请斧正！！！




