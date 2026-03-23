[[Binary Tree]]
##### Height of a Binary Tree
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

##### Balanced Binary Tree
 **Brute force approach - O($N^2$)**
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

**Optimized Approach - O(N)**
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

**Diameter of a Binary Tree**
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

---
##### Binary Tree Maximum Path Sum
link - [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)

```cpp
class Solution {
public:
    int maxPathSum(TreeNode* root) {
        int maxi = INT_MIN;
        maxPath(root, maxi);
        return maxi;
    }

    int maxPath(TreeNode* node, int& maxi) {
        if (node == NULL) return 0;
        int left = max(0, maxPath(node->left, maxi));
        // to avoid negative nodes we take max
        int right = max(0, maxPath(node->right, maxi));

        maxi = max(maxi, left + right + node->val);
        // checking the inverted U path's max, not taking that path
        // so we can continue next steps but we compare its value with coming ones
        return max(left, right) + node->val;
        // on returning, you take the child with higher
        // value (left or right) and the node itself so that we can probe further for better path
    }
};
```

---

##### Same Tree
link -[Same Tree](https://leetcode.com/problems/same-tree/)
```cpp
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        // Just do traversal and if at any point they differ, it's not the same
        if (p == NULL || q == NULL) {
            return (p == q); // True if same, and if not, then not the same tree
        }

        return (p->val == q->val) && isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
    }
};
```

---
##### Zig-Zag Traversal
link - [Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)
```cpp
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        if(root == NULL) return {};
        queue<TreeNode*>q;
        q.push(root);
        vector<vector<int>>ans;
        bool levels = true; //maintain which level we are at, root would be L->R
        while(!q.empty()){
            vector<int>level;
            int size = q.size();
            for(int i = 0;i<size;i++){
                TreeNode* node = q.front();
                q.pop();
                level.push_back(node->val);
                if(node->left != NULL) q.push(node->left);
                if(node->right != NULL) q.push(node->right);
            }
            if(levels == true){
                ans.push_back(level);
            }
            else{
                reverse(level.begin(), level.end());
                ans.push_back(level);
            }
            levels = !levels;
        }
        return ans;
    }
};
```

---
##### Boundary Traversal
link - [Boundary Traversal](https://www.geeksforgeeks.org/problems/boundary-traversal-of-binary-tree/1)

```cpp
class Solution {
  public:
    bool isleaf(Node* root){
        if(!root->left && !root->right) return true;
        return false;
    }
    
    void addLeftBoundary(Node* root, vector<int>&res){
        Node* cur = root->left;
        while(cur){
            if(!isleaf(cur))res.push_back(cur->data);
            if(cur->left) cur = cur->left;
            else cur = cur->right;
        }
    //if not leaf, traverse left node, if no left, go to its right 
    //(that would still be the left boundary)
    }
    
    void addLeaves(Node* root, vector<int>&res){
        //just traversal with condition
        if(isleaf(root)){
            res.push_back(root->data);
            return;
        }
        if(root->left) addLeaves(root->left, res);
        if(root->right) addLeaves(root->right, res);
    }
    
    void addRightBoundary(Node* root, vector<int>&res){
        Node* cur = root->right;
        vector<int>temp;
        while(cur){
            if(!isleaf(cur)) temp.push_back(cur->data);
            if(cur->right) cur = cur->right;
            else cur = cur->left;
        }
        
        for(int i = temp.size()-1;i>=0;i--){
            //reverse of right boundary
            res.push_back(temp[i]);
        }
    }
    
    vector<int> boundaryTraversal(Node *root) {
        vector<int>res;
        if(!root) return res;
        if(!isleaf(root)) res.push_back(root->data);
        addLeftBoundary(root,res);
        addLeaves(root,res);
        addRightBoundary(root,res);
        return res;
    }
};
```

---
##### Vertical Order Traversal of a Binary Tree
link - [Vertical Order Traversal of a Binary Tree](https://leetcode.com/problems/vertical-order-traversal-of-a-binary-tree/)
*solved myself with some hint on getting stuck*

```cpp
class Solution {
public:
    vector<vector<int>> verticalTraversal(TreeNode* root) {
        queue<pair<TreeNode*, pair<int, int>>> q;
        q.push({root, {0, 0}}); // node,{row,col}
        map<int, vector<pair<int, int>>> mp;
        while (!q.empty()) {
            int qsize = q.size();
            for (int i = 0; i < qsize; i++) {
                TreeNode* node = q.front().first;
                int row = q.front().second.first;
                int col = q.front().second.second;
                mp[col].push_back({row, node->val});
                q.pop();
                if (node->left) q.push({node->left, {row + 1, col - 1}});
                if (node->right) q.push({node->right, {row + 1, col + 1}});
            }
        }
        vector<vector<int>> ans;
        for (auto it = mp.begin(); it != mp.end(); it++) {
            vector<pair<int, int>> sameColumn = it->second;
            sort(sameColumn.begin(), sameColumn.end());

            vector<int> value;
            for (auto itr : sameColumn) {
                value.push_back(itr.second);
            }
            ans.push_back(value);
        }
        return ans;
    }
};
```

---
##### Top-View of Binary Tree
link - [Top View](https://www.geeksforgeeks.org/problems/top-view-of-binary-tree/1)
![[Pasted image 20250713132253.png|400]]
the first node in the vertical line would be visible

```cpp
class Solution {
  public:
    vector<int> topView(Node *root) {
        vector<int>ans;
        queue<pair<Node*, int>>q; //storing Node and its vertical position
        map<int,int>mp; //storing the level and its node
        
        if(root == NULL) return ans;
        
        q.push({root,0});
        while(!q.empty()){
            Node* node = q.front().first;
            int level = q.front().second;
            q.pop();
            
            if(mp.find(level) == mp.end()){
                //this level was not on map before
                //since we only want first value for each level
                //we only push first values for each levels
                mp[level] = node->data;
            }
            
            if(node->left){
                q.push({node->left,level-1});
            }
            if(node->right){
                q.push({node->right,level+1});
            }
        }
        //now map contains sorted (level wise) nodes for
        //the first node value of each level
        for(auto it:mp){
            ans.push_back(it.second);
        }
        return ans;
    }
};
```

---
##### Bottom View of Binary Tree
link - [Bottom View](https://www.geeksforgeeks.org/problems/bottom-view-of-binary-tree/1)

```cpp
class Solution {
  public:
    vector<int> bottomView(Node *root) {
        vector<int>ans;
        if(root == NULL)return ans;
        
        queue<pair<Node*, int>>q;
        q.push({root,0});
        
        map<int,int>mpp;
        while(!q.empty()){
            auto it = q.front();
            q.pop();
            Node* node = it.first;
            int level = it.second;
            
            //latest data will overwrite prev one
            mpp[level] = node->data;
            
            if(node->left) q.push({node->left,level-1});
            if(node->right) q.push({node->right,level+1});
        }
        
        for(auto it: mpp){
            ans.push_back(it.second);
        }
        return ans;
    }
};
```

---
##### Binary Tree Right Side View
link - [Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)

```cpp
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        vector<int> ans;
        if (root == NULL) return ans;
        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()) {
            int qsize = q.size();
            for (int i = 0; i < qsize; i++) {
                TreeNode* node = q.front();
                q.pop();
                if (i == qsize - 1) ans.push_back(node->val);
                if (node->left) q.push(node->left);
                if (node->right) q.push(node->right);
            }
        }
        return ans;
    }
};
```

---
##### Symmetric Tree (Mirror)
link - [Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)
```cpp
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if (root == NULL) return true;
        return solve(root->left, root->right);
    }

    bool solve(TreeNode* left, TreeNode* right) {
        if (left == NULL || right == NULL) return left == right;
        if (left->val != right->val) return false;
        return solve(left->left, right->right) && solve(left->right, right->left);
    }
};
```

