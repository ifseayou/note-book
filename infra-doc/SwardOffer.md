# sward_offer66

### 26

~~~java
package com.isea.dw.sword_offer;

/**
 * @author isea_you
 * @date 2019/8/28
 * @time 15:51
 * @target: 输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。
 */
public class Tree2LinkedList {
    static class TreeNode{
        private int value;
        private TreeNode left;
        private TreeNode right;

        public TreeNode() {}

        public TreeNode(int value) {
            this.value = value;
            this.left = null;
            this.right = null;
        }
    }

    public static TreeNode Convert(TreeNode root) {
        if (root == null){
            return null;
        }
        if (root.left == null && root.right ==  null){
            return root;
        }
        // 将左子树构建成双链表，并返回头节点
        TreeNode left = Convert(root.left);
        // 定位左子树双链表的最后一个节点
        TreeNode cur = left;
        while(cur != null && cur.right != null){
            cur = cur.right;
        }

        // 如果左子树链表不为为空的话，将当前root追加到左子树链表
        if (left != null){
            cur.right = root;
            root.left = cur;
        }

        // 将右子树转为双链表，并返回头节点
        TreeNode right = Convert(root.right);
        // 如果右子树链表不为空，追加到root
        if (right != null){
            right.left = root;
            root.right = right;
        }
        return left != null? left:root;
    }

    public static void main(String[] args) {
        TreeNode head = new TreeNode(4);
        head.left = new TreeNode(2);
        head.right = new TreeNode(6);

        head.left.left = new TreeNode(1);
        head.left.right = new TreeNode(3);
        head.right.left = new TreeNode(5);
        head.right.right = new TreeNode(7);

        TreeNode node = Convert(head);
        TreeNode cur = node;
        while(cur != null){
            System.out.print(cur.value);
            cur = cur.right;
        }
    }
}
~~~



