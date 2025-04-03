[[Binary Tree]]
#### Height of a Binary Tree
Its just a bfs with not storing any element just incrementing level
```cpp
    int maxDepth(TreeNode* root) {

        if(root == NULL)return 0;

        queue<TreeNode*>st;

        st.push(root);

        int level = 0;

        while(!st.empty()){

            int size = st.size();

            for(int i = 0;i<size;i++){

                TreeNode* node = st.front();

                st.pop();

                if(node->left!=NULL)st.push(node->left);

                if(node->right!=NULL)st.push(node->right);

            }

            level++;

        }

        return level;

    }
```

A slightly better recursive approach that takes less space in general
```cpp
int maxDepth(TreeNode* root) {

        if(root == NULL)return 0;

        int lh = maxDepth(root->left);  

        int rh = maxDepth(root->right);

        return 1 + max(lh,rh);  

    }
```

#### Balanced Binary Tree
###### Brute force approach - O($N^2$)
finding max height of left and right subtree for all nodes. 
```cpp
    int height(TreeNode* root){

        if(root == NULL) return 0;

        int lh = height(root->left);

        int rh = height(root->right);

        return 1 + max(lh,rh);

    }

    bool isBalanced(TreeNode* root) {

        if(root == NULL)return true;

        int leftST = height(root->left);

        int rightST = height(root->right);

        if(abs(leftST-rightST)<=1 && isBalanced(root->right) && isBalanced(root->left))return true;

        return false;

    }
```

###### Optimized Approach - O(N)
goes to the end and start from there and once an imbalance is encountered, it just starts returning -1 from there and just goes back to the root and traversal stops from there.

```cpp
    int height(TreeNode* root){

        if(root == NULL) return 0;

        int lh = height(root->left);

        if(lh == -1 )return -1;

        int rh = height(root->right);

        if(rh == -1 )return -1;

        if(abs(lh - rh)>1) return - 1;        

        return 1 + max(lh,rh);

    }

    bool isBalanced(TreeNode* root) {

        if(height(root) != -1)return true;

        return false;

    }
```

###### Diameter of a Binary Tree
just some minor changes in the height code - O(N)

```cpp
    int diameter = 0;

    int height(TreeNode* root){

        if(root == NULL) return 0;

        int lh = height(root->left);

        int rh = height(root->right);

        diameter = max(lh + rh, diameter);

        return 1 + max(lh,rh);

    }

    int diameterOfBinaryTree(TreeNode* root) {

        height(root);

        return diameter;

    }
```
