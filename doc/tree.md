#### 二叉树
###### 二叉树，是由很多个 TreeNode 构成的这种树形的数据结构
        class TreeNode {
            int value;
            TreeNode left;
            TreeNode right;
        }
###### 就像链表中的 ListNode。二叉树并不一定非得是“二叉”的，而是说每个节点最多有两个孩子，叫 left 和 right，但也可以没有。当每个节点都只有一个孩子的时候，就退化成了链表。所以链表就是一棵特殊的树，而树是一个特殊的图<br><img src='/img/20200826155827.jpg' width="50%" hight="50%">
