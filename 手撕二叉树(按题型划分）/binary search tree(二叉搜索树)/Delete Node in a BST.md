# 1. 删除二叉搜索树中的节点

[力扣题目链接](https://leetcode.cn/problems/delete-node-in-a-bst/)

## 方法一:递归
```cpp
   class Solution {  
public:  
    // 定义一个函数，用于删除二叉搜索树中指定键值的节点  
    TreeNode* deleteNode(TreeNode* root, int key) {  
        // 如果根节点为空，直接返回空  
        if (root == nullptr) {  
            return nullptr;  
        }  
  
        // 如果要删除的键值大于当前节点的键值，则在左子树中查找并删除  
        if (root->val > key) {  
            root->left = deleteNode(root->left, key);  
            return root;  
        }  
  
        // 如果要删除的键值小于当前节点的键值，则在右子树中查找并删除  
        if (root->val < key) {  
            root->right = deleteNode(root->right, key);  
            return root;  
        }  
  
        // 如果要删除的键值等于当前节点的键值，则需要分三种情况处理  
        if (root->val == key) {  
            // 如果当前节点没有左右子节点，直接删除当前节点，返回空  
            if (!root->left && !root->right) {  
                return nullptr;  
            }  
  
            // 如果当前节点只有右子节点，直接将右子节点提升到当前节点的位置  
            if (!root->right) {  
                return root->left;  
            }  
  
            // 如果当前节点只有左子节点，直接将左子节点提升到当前节点的位置  
            if (!root->left) {  
                return root->right;  
            }  
  
            // 如果当前节点既有左子节点又有右子节点，找到右子树的最左节点（即右子树的最小值），用它替换当前节点的位置，并调整左右子节点的指针指向  
            TreeNode *successor = root->right;  
            while (successor->left) {  
                successor = successor->left;  
            }  
            root->right = deleteNode(root->right, successor->val);  
            successor->right = root->right;  
            successor->left = root->left;  
            return successor;  
        }  
  
        // 如果以上情况都不满足，则直接返回当前节点，无需进行任何操作  
        return root;  
    }  
};
```

#### 解题思路关键
* 找到要删除的节点
我们应该这样思考：既然要删除节点，那么我们首先肯定要找到删除的节点的位置呀。</br>
如何找到要删除节点的位置呢?<br>
对!根据二叉搜索树的性质递归寻炸从根节点到要删除节点的路径。
* 要删除的节点左右都有节点时,如何处理这种情况
```cpp
            TreeNode *successor = root->right;
            while (successor->left) {
                successor = successor->left;
            }
            root->right = deleteNode(root->right, successor->val);
            successor->right = root->right;
            successor->left = root->left;
            return successor;
```
或者我们也可以这样写
```cpp
           TreeNode *successor = root->right;
            while (successor->left) {
                successor = successor->left;
            }
            successor->left=root->left;

            return root->right;
```
## 方法二:二级指针解法
```cpp
      class Solution {
public:
    TreeNode* mergeTree(TreeNode* lt, TreeNode* rt) {
        if (!lt) return rt;
        auto it = lt;
        while (it->right)
            it = it->right;
        it->right = rt;
        return lt;
    }

    TreeNode* deleteNode(TreeNode* root, int key) {
        TreeNode** it = &root;
        while (*it && (*it)->val != key) {
            if (key < (*it)->val)
                it = &(*it)->left;
            else
                it = &(*it)->right;
        }
        if (*it) {
            const auto to_delete = *it;
            *it = mergeTree((*it)->left, (*it)->right);
            delete to_delete;
        }
        return root;
    }
};
```
也可以这样写
```cpp
class Solution {
public:
    TreeNode* deleteNode(TreeNode* root, int key) {
        TreeNode **p = &root; //指向待删除节点的指针
        while(*p && (*p)->val != key) 
            p = (*p)->val < key ? &(*p)->right : &(*p)->left;
        if(!*p) return root;
        TreeNode **t = &(*p)->right; //指向右树的最左空指针的指针（这里把左树合到右树，反之同理即可）
        while(*t) t = &(*t)->left;
        *t = (*p)->left;
        *p = (*p)->right;
        return root;
    }
};
```
#### 解题思路关键
* 二级指针的优点
二级指针就能做到不改变节点内部的的值，即使是空指针也是会有自己的地址，直接通过更换指向子树根节点的指针的地址做到子树的移动<br>



