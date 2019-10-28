# LeetCode练习笔记

### 100、Same tree

Given two binary trees, write a function to check if they are the same or not.

Two binary trees are considered the same if they are structurally identical and the nodes have the same value.

```java
public boolean isSameTree(TreeNode p, TreeNode q) {
    if ((p==null)&&(q==null)){
        return true;
    }else if(p==null||q==null){
        return false;
    }

    if (p.val == q.val){
        return isSameTree(p.left,q.left)&&isSameTree(p.right,q.right);
    }else {
        return false;
    }
}
```