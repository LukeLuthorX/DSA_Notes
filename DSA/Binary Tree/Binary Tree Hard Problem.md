##### Root to Node Path

![[Pasted image 20250713161042.png | 400]]
checking both right and left for each node and pushing the node value in path and if not found the value in that (NULL return) then remove and backtrack. This was done with DFS.
```cpp
bool solve(TreeNode* root, vector<int>&paths, int value){
	if(root == NULL) return false;
	paths.push_back(root->val);
	if(root->val == value) return true;
	if(solve(root->left,paths,value) || solve(root->right, paths, value)){
		return true;
	}
	paths.pop_back();
	return false;
}

vector<int>getPaths(TreeNode* root, int node){
	vector<int>paths;
	if(root == NULL)return paths;
	solve(root,paths,node);
	return paths;
}
```
---
##### Root to Leaf Paths
link - [Binary Tree Paths](https://leetcode.com/problems/binary-tree-paths/)
both left and right will have atleast one leaf if it exists, so we get one string from each, so no concept of backtracking here
```cpp
class Solution {
public:
    void dfs(TreeNode* root, string &path, vector<string>& ans) {
        path += to_string(root->val);
        if (root->left) {
            dfs(root->left, path + "->", ans);
        }
        if (root->right) {
            dfs(root->right, path + "->", ans);
        }
        if (root->left == NULL && root->right == NULL) {
            // leaf node
            ans.push_back(path);
        }
    }

    vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> ans;
        if (!root) return ans;
        dfs(root, "", ans);
        return ans;
    }
};
```
---

##### Lowest Common Ancestor
link - [Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

**Brute**
```cpp
class Solution {
public:
    bool solve(TreeNode* root, TreeNode* p, vector<TreeNode*>& path) {
        if (!root) return false;
        path.push_back(root);
        if (root == p) {
            return true;
        }
        if (solve(root->left, p, path) || solve(root->right, p, path)) {
            return true;
        }
        path.pop_back();
        return false;
    }

    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        // traverse from root to both node p and q and check simultaneously whether they had common ancestor or not, the moment the paths are different, the last same element in path is the answer
        vector<TreeNode*> path1, path2;
        solve(root, p, path1);
        solve(root, q, path2);
        int l1 = path1.size();
        int l2 = path2.size();
        TreeNode* ans;
        int m = min(path1.size(), path2.size());
		TreeNode* ans = nullptr;
		for (int i = 0; i < m; i++) {
		  if (path1[i] == path2[i]) {
		    ans = path1[i];
		  } else {
		    break;              //they will have same values till they diverge
		  }
		}
		return ans;
    }
};
```

**Optimized**
![[Pasted image 20250714183844.png|400]]
In single pass check for both the required nodes

> [!NOTE]- Explanation
> - **Question to each subtree**: “Hey left child, do you see either p or q down there? … Hey right child, same question.”
>     
> - **Collect their answers** (each answer is either:
>     
>     - a pointer to p,
>         
>     - a pointer to q,
>         
>     - or `NULL` if neither was found).
>         
> - **Combine those answers** at the current node:
>     
>     - If **both** children return non-`NULL`, that means one node was found on each side ⇒ **current node** is the LCA.
>         
>     - If **only one** side is non-`NULL**, bubble that pointer up unchanged.
>         
>     - If **neither** side found anything, return `NULL` up.

```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (root == NULL || root == p || root == q) {
            return root;
        }
        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);
        if (left == NULL) {
            return right;
        }
        if (right == NULL) {
            return left;
        } else {
            // both left and right are not null, we found our result
            return root;
        }
    }
};
```

---
##### Maximum Width of Binary Tree
link - [Maximum Width of Binary Tree](https://leetcode.com/problems/maximum-width-of-binary-tree/)

the imaginary nodes also contribute in the width of the tree. If tree nodes are indexed in order 1,2,3,..., the last nodes number in that level - first node number + 1 would give width
the core intuition is that if we can have indexes on each node in order, and even non-existent nodes absent could contribute
![[Pasted image 20250715131902.png | 400]]
edgecase -> in a skew tree, the values of nodes would keep getting doubled due to indexing formula of $2*i$ and would cause overflow, to fix that
instead of i -> $2*i+1$ and $2*i+2$ we make each $i = (i-min(no. on level))$ and find its children for the new i
```cpp
class Solution {
public:
    int widthOfBinaryTree(TreeNode* root) {
        if (!root) {
            return 0;
        }
        queue<pair<TreeNode*, int>> q; // {node, index}
        q.push({root, 0});
        int ans = 0;
        while (!q.empty()) {
            int size = q.size();
            int mmin = q.front().second; // the min index on that level (using this to remove overflow)
            // it will have the least index since first node of the level
            int first, last;
            for (int i = 0; i < size; i++) {
                int cur_id = q.front().second - mmin; // to remove overflow
                TreeNode* node = q.front().first;
                q.pop();
                if (i == 0) first = cur_id;
                if (i == size - 1) last = cur_id;
                if (node->left) {
                    q.push({node->left, (long long)2 * cur_id + 1});
                }
                if (node->right) {
                    q.push({node->right, (long long)2 * cur_id + 2});
                }
            }
            ans = max(ans, last - first + 1);
        }
        return ans;
    }
};
```

---
##### Children Sum Property
The arbitraty binary tree to be converted such that the node is its children's sum. Its allowed to increase the node value by +1 many times.

Since we dont have to find the minimal tree, we can have the inflated value of nodes as well, we traverse down and if sum of children's values is less than the parent's value, replace the children with parent's value, and if opposite, replace parent's values with su 
![[Pasted image 20250715144925.png | 400]]

```cpp
void changeTree(Node* root){
	if(root == NULL) return;
	int child = 0;
	if(root->left) child += root->left->data;
	if(root->right) child += root->right->data;
	if(child >= root->data){
		root->data = child;
		//if root is smaller, just assign the sum to its value
	}
	else{
		if(root->left) root->left->data = root->data;
		else if(root->right) root->right->data = root->data;
	}
	changeTree(root->left);
	changeTree(root->right);
	//while backtracking, replace the root value
	int tot = 0;
	if(root->left) tot+=root->left->data;
	if(root->right) tot+=root->right->data;
	if(root->left or root->right) root->data = tot;
}
```

---
##### All Nodes Distance K in Binary Tree 
link - [All Nodes Distance K in Binary Tree](https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/)

since we cant traverse backward, we maintain a parents map, and once mapped all nodes, we begin pushing radially outwards (left, right, up) nodes to that node and this is 1 distance apart and then subsequently those nodes are processed getting the nodes at every distance sequentially.
Basically converting a BT into undirected graph in a way.

```cpp
class Solution {
public:
    void markParent(TreeNode* root, unordered_map<TreeNode*, TreeNode*>& parent_track) {
        // doing this because in tree can't go backwards so maintaining parent for each node
        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()) {
            TreeNode* current = q.front();
            q.pop();
            if (current->left) {
                parent_track[current->left] = current;
                q.push(current->left);
            }
            if (current->right) {
                parent_track[current->right] = current;
                q.push(current->right);
            }
        }
    }

    vector<int> distanceK(TreeNode* root, TreeNode* target, int k) {
        unordered_map<TreeNode*, TreeNode*> parent_track; // Node->Parent
        markParent(root, parent_track);
        unordered_map<TreeNode*, bool> visited;
        queue<TreeNode*> q;
        q.push(target);
        visited[target] = true;
        int cur_level = 0;
        while (!q.empty()) {
            int size = q.size();
            if (cur_level++ == k) break;
            for (int i = 0; i < size; i++) {
                TreeNode* current = q.front();
                q.pop();
                // checking all the radially outwards node(left, right, parent)
                if (current->left && !visited[current->left]) {
                    q.push(current->left);
                    visited[current->left] = true;
                }
                if (current->right && !visited[current->right]) {
                    q.push(current->right);
                    visited[current->right] = true;
                }
                if (parent_track[current] && !visited[parent_track[current]]) {
                    q.push(parent_track[current]);
                    visited[parent_track[current]] = true;
                }
            }
        }
        vector<int> result;
        while (!q.empty()) {
            TreeNode* current = q.front();
            q.pop();
            result.push_back(current->val);
        }
        return result;
    }
};
```

---
##### Burning Tree
link - [Burning Tree](https://www.geeksforgeeks.org/problems/burning-tree/1)
```cpp
class Solution {
  public:
    int findMinTime(Node* targetNode, unordered_map<Node*, Node*>&parent){
        queue<Node*>q;
        unordered_map<Node*, int>visited;
        q.push(targetNode);
        visited[targetNode] = 1;
        int timer = 0;
        
        while(!q.empty()){
            int size = q.size();
            int flag = 0;
            for(int i = 0;i<size;i++){
                Node* node = q.front();
                q.pop();
                if(node->left && !visited[node->left]){
                    q.push(node->left);
                    visited[node->left] = 1;
                    flag = 1;
                }
                if(node->right && !visited[node->right]){
                    q.push(node->right);
                    visited[node->right] = 1;
                    flag = 1;
                }
                if(parent[node] && !visited[parent[node]]){
                    q.push(parent[node]);
                    visited[parent[node]] = 1;
                    flag = 1;
                }
            }
            if(flag)timer++;
        }
        return timer;
    }
    
    Node* mapParent(Node* root, unordered_map<Node*, Node*>&parent, int target){
        //in this function, I need to map parents and find the target node from its value
        Node* res;
        queue<Node*> q;
        q.push(root);
        while(!q.empty()){
            Node* node = q.front();
            q.pop();
            if(node->data == target){
                res = node;
            }
            if(node->left){
                parent[node->left] = node;
                q.push(node->left);
            }
            if(node->right){
                parent[node->right] = node;
                q.push(node->right);
            }
        }
        return res;
    }
    int minTime(Node* root, int target) {
        unordered_map<Node*, Node*>parent;
        Node* targetNode = mapParent(root,parent,target);
        int ans = findMinTime(targetNode, parent);
        return ans;
    }
};
```

---
##### Count Complete Tree Nodes 
link - [Count Complete Tree Nodes](https://leetcode.com/problems/count-complete-tree-nodes/)

N nodes but we want to count in less than O(N) time, we know height of tree = h, $2^h - 1 =$ Number of nodes in a full binary tree, but in complete Binary Tree, the leaf row can have less nodes, but all shifted towards left.
we can check the left height and right height and if they are equal then its perfect subtree but if not then some nodes are missing and can't apply formula directly.

```cpp
class Solution {
public:
    int findLeftHeight(TreeNode* root) {
        int height = 0;
        while (root) {
            height++;
            root = root->left;
        }
        return height;
    }

    int findRightHeight(TreeNode* root) {
        int height = 0;
        while (root) {
            height++;
            root = root->right;
        }
        return height;
    }

    int countNodes(TreeNode* root) {
        if (root == NULL) return 0;
        int lh = findLeftHeight(root);
        int rh = findRightHeight(root);
        if (lh == rh) return (1 << lh) - 1; // 2 to the power lh - 1 : formula for perfect tree
        // not perfect subtree then add this node and then do the same process for its subtrees
        return 1 + countNodes(root->left) + countNodes(root->right);
    }
};
```

---
##### Construct Binary Tree from Preorder and Inorder Traversal
link - [Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
can you construct unique binary tree with following?
a) preorder & postorder
-> No
b) Inorder & preorder/postorder
-> Yes
without inorder given, its not possible to find unique tree

```cpp
class Solution {
public:
    TreeNode* solve(vector<int>& preorder, int preStart, int preEnd, vector<int>& inorder, int inStart, int inEnd, map<int, int>& inMap) {
        if (preStart > preEnd || inStart > inEnd) return NULL;
        TreeNode* root = new TreeNode(preorder[preStart]);
        int inRoot = inMap[root->val];
        int numsLeft = inRoot - inStart;
        root->left = solve(preorder, preStart + 1, preStart + numsLeft, inorder, inStart, inRoot - 1, inMap);
        root->right = solve(preorder, preStart + numsLeft + 1, preEnd, inorder, inRoot + 1, inEnd, inMap);
        return root;
    }

    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        map<int, int> inMap;
        for (int i = 0; i < inorder.size(); i++) {
            inMap[inorder[i]] = i;
        }
        TreeNode* root = solve(preorder, 0, preorder.size() - 1, inorder, 0, inorder.size() - 1, inMap);
        return root;
    }
};
```

---

##### Construct Binary Tree from Inorder and Postorder Traversal
link - [Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)
```cpp
class Solution {
public:
    TreeNode* solve(vector<int>& postorder, int postStart, int postEnd, vector<int>& inorder, int inStart, int inEnd, map<int, int>& inMap) {
        if (postStart > postEnd || inStart > inEnd) return NULL;
        TreeNode* root = new TreeNode(postorder[postEnd]);
        int inRoot = inMap[root->val];
        int numLeft = inEnd - inRoot; // right subtree
        root->right = solve(postorder, postEnd - numLeft, postEnd - 1, inorder, inRoot + 1, inEnd, inMap);
        root->left = solve(postorder, postStart, postEnd - numLeft - 1, inorder, inStart, inRoot - 1, inMap);
        return root;
    }

    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        map<int, int> inMap;
        for (int i = 0; i < inorder.size(); i++) 
            inMap[inorder[i]] = i;
        }
        TreeNode* root = solve(postorder, 0, postorder.size() - 1, inorder, 0, inorder.size() - 1, inMap);
        return root;
    }
};
```

---
##### Serialize and Deserialize Binary Tree
link - [Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)
```cpp
class Codec {
public:
    string serialize(TreeNode* root) {
        // encoding via bfs
        if (!root) return "";
        queue<TreeNode*> q;
        q.push(root);
        string s = "";
        while (!q.empty()) {
            TreeNode* node = q.front();
            q.pop();
            if (node == NULL) s.append("#,"); // using append since += may cause MLE
            else s.append(to_string(node->val) + ',');
            if (node) {
                q.push(node->left);
                q.push(node->right);
            }
        }
        return s;
    }

    TreeNode* deserialize(string data) {
        if (data.size() == 0) return NULL;
        stringstream s(data); // makes string iterate like object
        // default whitespace, can put any delimiter
        string str;
        getline(s, str, ','); // every time this runs, one object from stream after delimiter is
        // dumped on str, and it moves like a pointer
        TreeNode* root = new TreeNode(stoi(str));
        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()) {
            TreeNode* node = q.front();
            q.pop();
            getline(s, str, ',');
            if (str == "#") {
                node->left = NULL;
            } else {
                TreeNode* leftNode = new TreeNode(stoi(str));
                node->left = leftNode;
                q.push(leftNode);
            }
            getline(s, str, ',');
            if (str == "#") node->right = NULL;
            else {
                TreeNode* rightNode = new TreeNode(stoi(str));
                node->right = rightNode;
                q.push(rightNode);
            }
        }
        return root;
    }
};
```

---
##### Morris Traversal
TIme Complexity = $O(N)$
Space Complexity = $O(1)$ *as compared to $O(N)$ auxiliary space in other traversals*

from concept of threaded binary tree.
In-order Morris Traversal:  
1st case: if left is null, print current node and go right 
2nd case: before going left, make right most node on left subtree connected to current node, then go left 
3rd case: if thread is already pointed to current node, then remove the thread

inorder follows the left root right, and if left is null, we know then this should be root, thus the first case. once we go forward, cant return so before traversing forward if there is a left, we go to the right most node on left subtree and connect current node and then continue because that is the last node for that subtree and once it finishes we can directly go to its root and then traverse right subtree

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> inorder;
        TreeNode* cur = root;
        while (cur != NULL) {
            if (cur->left == NULL) {
                inorder.push_back(cur->val);
                cur = cur->right;
            } else {
                // go to left subtree's right most node
                TreeNode* prev = cur->left;
                while (prev->right && prev->right != cur) {
                    prev = prev->right;
                }
                // if the right most node's right points to null (which it should by default)
                // make it point to its root and then continue with traversing left subtree
                if (prev->right == NULL) {
                    prev->right = cur;
                    cur = cur->left;
                }
                // if it is already threaded to root, remove it and left subtree is over
                // and back to root and left done so at root right now, then explore right subtree
                else {
                    prev->right = NULL; // remove thread if it was already there
                    inorder.push_back(cur->val);
                    cur = cur->right;
                }
            }
        }
        return inorder;
    }
};
```

**Morris Traversal For Preorder (one line change)**
```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> inorder;
        TreeNode* cur = root;
        while (cur != NULL) {
            if (cur->left == NULL) {
                inorder.push_back(cur->val);
                cur = cur->right;
            } else {
                TreeNode* prev = cur->left;
                while (prev->right && prev->right != cur) {
                    prev = prev->right;
                }
                if (prev->right == NULL) {
                    prev->right = cur;
	                inorder.push_back(cur->val); //root left right
                    cur = cur->left;
                }
                else {
                    prev->right = NULL; 
                    cur = cur->right;
                }
            }
        }
        return inorder;
    }
};
```

---
##### Flatten Binary Tree to Linked List
link - [Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)

Brute Approach what I did, first I was creating new nodes, but then instead used the already existing ones, still 2 full iterations and storing nodes
```cpp
class Solution {
public:
    void preorder(TreeNode* root, vector<TreeNode*>& pre) {
        if (root == NULL) return;
        pre.push_back(root);
        preorder(root->left, pre);
        preorder(root->right, pre);
    }

    void flatten(TreeNode* root) {
        if (!root) return;
        vector<TreeNode*> pre;
        preorder(root, pre);
        TreeNode* prev = root;
        int index = 1;
        while (index < pre.size()) {
            prev->right = pre[index];
            prev->left = NULL;
            prev = prev->right;
            index++;
        }
    }
};
```

**Another Recursive Approach** -> $O(N)$ Time and Space Complexity.
The approach is to visit nodes in reverse preorder and stitch them together in backtracking.
```cpp
class Solution {
public:
    TreeNode* prev = NULL;

    void flatten(TreeNode* root) {
        if (root == NULL) return;
        flatten(root->right);
        flatten(root->left);
        root->right = prev;
        root->left = NULL;
        prev = root;
    }
};
```

**Morris Traversal Version of Optimized Solution** 
the preorder will have root then left subtree and then right subtree, so once left subtree is over it will point to the right subtree, root comes at top as usual

```cpp
class Solution {
public:
    void flatten(TreeNode* root) {
        TreeNode* cur = root;
        while (cur != NULL) {
            if (cur->left != NULL) {
                TreeNode* prev = cur->left;
                while (prev->right) prev = prev->right; // rightmost of the left subtree
                prev->right = cur->right; // pointing last value of left subtree to right subtree
                cur->right = cur->left;
                cur->left = NULL;
            }
            cur = cur->right;
        }
    }
};
```

----
