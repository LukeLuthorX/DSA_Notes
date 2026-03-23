[[Binary Tree]]
### Some Definitions

- **Full Binary Tree** - 0 or 2 nodes
- **Complete Binary Tree** => all levels are filled and if the last level has some missing, the remaining ones should be on the left side.
- **Perfect Binary Tree** - all leaf nodes are at the same level
- **Balanced Binary Tree** - height of tree at max is $log(n)$
- **Degenarate Tree** - skewed tree


## Traversal

#### Preorder Traversal
```cpp
    void preorder(TreeNode* root, vector<int>&ans){
        if(root == NULL) return;
        ans.push_back(root->val);
        preorder(root->left,ans);
        preorder(root->right,ans);
    }
```
Time complexity is O(N) because there are total N nodes to visit, and Space complexity is O(N), this is auxilliary space used in recurrsion and this is for the worst case if its a skewed tree.
==*Postorder and Inorder Traversal are also similar.*==

#### Level Order Traversal
storing nodes in a queue, and poping them and simultaneously pushing its children into queue, for loop is not need if only printing is to be done, here vector of arrays of each layer is maintained, so at each time, queue has one level.
```cpp
    vector<vector<int>> levelOrder(TreeNode* root) {
        if(root == NULL)return {};
        queue<TreeNode*> q;
        q.push(root);
        vector<vector<int>>ans;
        while(!q.empty()){
            vector<int> level;
            int size = q.size(); 
            for(int i = 0; i<size;i++){
                TreeNode* node = q.front();
                q.pop();
                level.push_back(node->val);
                if(node->left != NULL){
                    q.push(node->left);
                }
                if(node->right != NULL){
                    q.push(node->right);
                }
            }
            ans.push_back(level);
        }
        return ans;
    }
```

#### Iterative Preorder Traversal

Since generally we use recursive approach for it and recursion uses stack space, we use stack with for loop. we pop top and push its right child first then left child as opposed to queue.

```cpp
    vector<int> preorderTraversal(TreeNode* root) {

        vector<int> ans;

        if(root==NULL)return ans;

        stack<TreeNode*> st;

        st.push(root);

        while(!st.empty()){

            TreeNode* node = st.top();

            st.pop();

            ans.push_back(node->val);

            if(node->right!=NULL)st.push(node->right);

            if(node->left!=NULL)st.push(node->left);

        }

        return ans;

    }
```

#### Iterative Inorder Traversal
```cpp
    vector<int> inorderTraversal(TreeNode* root) {

        vector<int> ans;

        stack<TreeNode*> st;

        TreeNode* node = root;

        while(true){

            if(node!=NULL){

                st.push(node);

                node = node->left;

            }

            else{

                if(st.empty() == true)break;

                node = st.top();

                st.pop();

                ans.push_back(node->val);

                node = node->right;

            }

        }

        return ans;

    }
```

#### Iterative Postorder Traversal Using 2 Stacks

The first stack stores the nodes, first root is pushed then poped then this is pushed to second stack and then its left child then right child is pushed to first stack and this contiunes till all nodes are traversed and in second stack we find the postorder in reverse.
```cpp
    vector<int> postorderTraversal(TreeNode* root) {

        vector<int> ans;

        if(root == NULL)return ans;

        stack<TreeNode*>st;

        stack<TreeNode*>st2;

        TreeNode* node = root;

        st.push(node);

  

        while(!st.empty()){

            node = st.top();

            st.pop();

            st2.push(node);

            if(node->left!=NULL)st.push(node->left);

            if(node->right!=NULL)st.push(node->right);

        }

        while(!st2.empty()){

            node = st2.top();

            ans.push_back(node->val);

            st2.pop();

        }

        return ans;

    }
```

#### Iterative Postorder Using One Stack
every iterative approach has O(2N) time complexity

```cpp
    vector<int> postorderTraversal(TreeNode* root) {

        vector<int> ans;

        TreeNode* cur = root;

        TreeNode* temp;

        stack<TreeNode*> st;

        while(cur!=NULL || !st.empty()){

            if(cur!=NULL){

                st.push(cur);

                cur = cur->left;

            }

            else{

                temp = st.top()->right;

                if(temp == NULL){

                    temp = st.top();

                    st.pop();

                    ans.push_back(temp->val);

                    while(!st.empty() && temp == st.top()->right){

                        temp = st.top();

                        st.pop();

                        ans.push_back(temp->val);

                    }

                }

                else{

                    cur = temp;

                }

            }

        }

        return ans;

    }
```

#### Preorder Inorder Postorder Traversals in One Traversal
stack stores a node and a number for state management
if state = 1 => Preorder++ , push left child if present
if state = 2 => Inorder++, push right child if present
if state = 3 => Postorder++ =>erase
*T.C = O(3N)
S.C = O(4N)*
```cpp
    vector<int> postorderTraversal(TreeNode* root) {

        stack<pair<TreeNode*,int>> st;

        st.push({root,1});

        vector<int>pre;

        vector<int>in;

        vector<int>post;

        if(root == NULL)return post;

        while(!st.empty()){

            auto node = st.top();

            st.pop();

  

            if(node.second == 1){

                pre.push_back(node.first->val);

                node.second++;

                st.push(node);

                if(node.first->left!=NULL)st.push({node.first->left,1});

            }

            else if(node.second == 2){

                in.push_back(node.first->val);

                node.second++;

                st.push(node);

                if(node.first->right!=NULL)st.push({node.first->right,1});

            }

            else{

                post.push_back(node.first->val);

            }

        }

        return post;

    }
```


