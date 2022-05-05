
---
title: "LeetCode 一周小记 No.4"
date: 2022-03-31T05:13:32+08:00
draft: false
---
> description：前序+中序 / 中序+后序返回树想破脑袋，然后以后要每天花 monring 来复习考试内容，一日之际在于晨，每天题量直接 cut off。接下来是篇关于树的文章，可以好好了解一下。树里我踩过的坑。

## Monday
### ****106.从中序与后序遍历序列构造二叉树****

**key1**：中后序的第一个元素一定是左下角那个，因为中序是：左中右，后序是：左右中。都是先递归左。、后序的最后一个元素一定是 root，左右中，最后才吃到 root。

**key2**：后序遍历最后一个节点是根节点，中序遍历根节点左边是左子树的节点，右边是右子树的结点。一个边界是某数组只有一个元素，另一个是数组为空，why？因为在递归传递参数时有+1、-1的操作，所以当有一个节点、无结点单独拎出来。然后分别找到了左右子树的根节点，加到当前的根节点的左右孩子位置，然后递归左右子树。

**key3：**

1.  ****后序中结点分布应该是：[左子树结点，右子树结点，根结点]；
2.  中序中结点分布应该是：[左子树结点，根结点，右子树结点]；
3.  左右子树的节点分别去左右子树匹配；

**key4**：前序的左右数组 与 中序的左右数组一定是分别一样长度。在切割时利用。

**注意**：

1. 开闭区间，[] 的话传入的参数应该是0、leng-1、0、leng-1，如果是 [) 的话传入的参数应该是0、leng、0、leng。切割处理也不同。
2. 后序要抠掉最后一个结点，因为这个结点 new 出来了。

### ****105.从前序与中序遍历序列构造二叉树****

同 106 所述，就是抠的是左边界。

<hr>

## Tuesday
### ****654.最大二叉树****

**错误**思路：先要知道，它不像前中后序那样一直分左右数组，而是第一次就分好，后面无需再分左右数组，只要不断的操作这个数组，detail 就是一直移除一个元素，什么最好，显然可以 remove 的 ArrayList better，so，还要明确第一次是放在左孩子位置，后面是递归放在左子树的右孩子，另一子树与之相反，亦复如是。如何控制这个次数，显然加一个参数 deep 最好，if ... else 把出现次数多的放在 if 中。错在题意理解上。

**正确**思路：就是递归不断将左右两边去找最大那个返回，并将其左右数组递归。判空条件和105、106一样。left > right 时没有元素了，left == right 时只有一个元素直接 return new TreeNode();

注意：在初始化 index 时，不要随意初始化，初始化为 left，只要在 [left，right] 都行。<hr>

## Wednesday

### ****617.合并二叉树****

**思路**：两个结点都为 null 或其中一个为 null 为终止条件，都不为 null 就合并再 return 。

**注意**：当有一个节点为 null 但是另一个节点存在时，应该 return 该节点而不是把该结点的值赋给 new 出来的新节点，这样就不会丢失它的左右孩子了，如果某深度为 null 了。那更深处必然都为 null，要合并时将该节点直接移过去左右孩子就不用考虑了。

### ****700.二叉搜索树中的搜索****

略......

### ****98.验证二叉搜索树****

中序遍历然后把 root 值作为目标值，在目标值左边都要小于 root.val ，在目标值右边都要大于 root.val 。然后在 return 中递归左右子树。

### ****530.二叉搜索树的最小绝对差****

暴力解法：直接遍历出 list，再递归左右子树去遍历差值。

优雅解法：用这个解法首先把二叉搜索树的特点：左小右大 结合进来了，左子树的最右后代（左孩子的右孩子的右孩子的右孩子.......）、右子树的最左后代是值最接近根节点的结点。

<hr>

## Thursday
### ****501.二叉搜索树中的众数****

**暴力**解法：遍历整棵树并在过程中 put 进去结点的 值（key）、出现频率（value）。再拿到 map 中出现频率（value）最大的值（key）。

注意：要不断更新存储最大频率 key 的 List。如果有新的最大频率就清空（clear） List 再 add，出现同样频率就 add 。

**迭代**法：利用二叉搜索树的特点，类中序遍历，记录当前、上一结点。

### ****236. 二叉树的最近公共祖先****

**暴力**解法：分别记录找到 p 、q 的路径（递归+回溯）再双层 for 找最末位置的匹配项。  
```java
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

最优解法思路：后序遍历，从下往上找，找到目标 p、q 就存下来，这个 p、q 必然是某棵子数的左右孩子，找到了左右孩子分别不断往上传递，到了最小祖宗深度之后直接不断返回 root。
```java
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
 <hr>

## Friday

### ****235. 二叉搜索树的最近公共祖先****

**key**：第一个出现在 (p.val,q.val) 的结点就是搜索二叉树的最近公共祖先。
```java
class Solution {
    TreeNode node = null;
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root.val > p.val && root.val > q.val)    return lowestCommonAncestor(root.left , p, q);
        if(root.val < p.val && root.val < q.val)    return lowestCommonAncestor(root.right, p, q);
        return root;
    }
}
```
### ****701.二叉搜索树中的插入操作****

**key**：如果 root.val > target 递归左子树找最接近的点，如果遇到 null 就 return new target，比到直到 new 了一个结点，也就是搞定了，会一路 return 回去，至于为什么一路都是 return 呢？因为 > \ < 只会走进一个 if ，进了出来只会去 return，return 是当前这个点，而我们 insert 结点是在最深一层遍历，插入的位置也是一个结点的 左/右 孩子，然后一路返回的都是之前存在的结点。
```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if(root == null)    return new TreeNode(val);
        if(val > root.val)  root.right = insertIntoBST(root.right, val);
        if(val < root.val)  root.left  = insertIntoBST(root.left , val);
        return root;
    }
}
```
### ****450.删除二叉搜索树中的节点****

当找到了删除节点时：

**key1**：当删除节点的左右节点都为 null，return null；

**key2**：当删除结点都不为空，找被删 root 的右节点的最左祖孙 并将被删 root 的 left 作为 root右节点最左祖孙的 left 孩子。

**key3**：当 root.left or root.right 为 null，return 不为空的结点。

否则就递归并将返回为左右孩子。

**code**：
```java
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


<hr>

持续更新.......  
如有错误，敬请斧正！！！




