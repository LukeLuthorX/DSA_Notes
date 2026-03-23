[[Graphs]]
##### Number of Provinces
link - [Number of Provinces - LeetCode](https://leetcode.com/problems/number-of-provinces/)

we are just applying dfs on each node, and if it is already marked visited as it was connected to some other then we dont count it. simple.
```cpp
class Solution {
public:
    void dfs(int node, int n, vector<int>&visited, vector<vector<int>>& isConnected){
        visited[node] = 1;
        for(int i = 0;i<n;i++){
            if(!visited[i] && isConnected[node][i]==1){
                dfs(i,n,visited,isConnected);
            }
        }
    }
    int findCircleNum(vector<vector<int>>& isConnected) {
        int n = isConnected.size();
        vector<int>visited(n,0);
        int cnt = 0;
        for(int i = 0;i<n;i++){
            if(!visited[i]){
                cnt++;
                dfs(i,n,visited,isConnected);
            }
        }
        return cnt;
    }
};
```

---

##### Number of Islands
link - [Number of Islands - LeetCode](https://leetcode.com/problems/number-of-islands/)

>we consider each value in the matrix as a node. And a visited 2D array of the same size is made to keep track. Each node is visited, bfs performed on a node, and its neighbours are the one node above, blow, left and right. This seems unconventional but since the nodes are just embedded in the matrix itself, that's how we do things. Normally, diagonals are also considered then we run nested for loop for nrow and ncol from -1 to 1 as did in 2D DP problems, but here it is explicitly mentioned no diagonals.

```cpp
class Solution {
public:
    void bfs(int row, int col, vector<vector<char>>& grid, vector<vector<int>>&visited){
        visited[row][col] = 1;
        queue<pair<int,int>>q;
        q.push({row,col});
        int n =grid.size();
        int m = grid[0].size();
        while(!q.empty()){
            int row = q.front().first;
            int col = q.front().second;
            q.pop();

            int nrow;
            int ncol;

            int del[2] = {-1,1};
            for(int i=0;i<2;i++){
                nrow = row+del[i];
                ncol = col;
                if(nrow>=0 && nrow<n && ncol>=0 && ncol<m &&!visited[nrow][ncol] && grid[nrow][ncol] == '1'){
                            visited[nrow][ncol] = 1;
                            q.push({nrow,ncol});
                }
            }
            for(int i=0;i<2;i++){
                nrow = row;
                ncol = col+del[i];
                if(nrow>=0 && nrow<n && ncol>=0 && ncol<m &&!visited[nrow][ncol] && grid[nrow][ncol] == '1'){
                            visited[nrow][ncol] = 1;
                            q.push({nrow,ncol});
                }
            }
        }
    }

    int numIslands(vector<vector<char>>& grid) {
        int n =grid.size();
        int m = grid[0].size();
        vector<vector<int>>visited(n,vector<int>(m,0));
        int cnt = 0;
        for(int row = 0;row<n;row++){
            for(int col = 0;col<m;col++){
                if(!visited[row][col] && grid[row][col] == '1'){
                    cnt++;
                    bfs(row,col,grid,visited);
                }
            }
        }
        return cnt;
    }
};
```

---
##### Rotten Oranges
link - [Rotting Oranges - LeetCode](https://leetcode.com/problems/rotting-oranges/description/)

```cpp
class Solution {
public:
    int orangesRotting(vector<vector<int>>& grid) {
        int n = grid.size();
        int m = grid[0].size();

        
        queue<pair<pair<int,int>,int>>q;
        vector<vector<int>>visited(n,vector<int>(m,0));
        //putting all the rotten oranges in the queue first with time = 0;
        for(int row = 0;row<n;row++){
            for(int col = 0;col<m;col++){
                if(grid[row][col] == 2){
                    q.push({{row,col},0});
                    visited[row][col] = 2;
                }
            }
        }

        //tm used to store highest time
        //each neigbouring node to the rotten one is explored and rotten
        //subsequently 
        int tm = 0;
        int delr[] = {-1,0,1,0};
        int delc[] = {0,1,0,-1};
        while(!q.empty()){
            int r = q.front().first.first;
            int c = q.front().first.second;
            int t = q.front().second;
            q.pop();
            tm = max(tm,t);

            for(int i = 0;i<4;i++){
                int nrow = r + delr[i];
                int ncol = c + delc[i];
                
                if(nrow>=0 && nrow<n && ncol>=0 && ncol < m && visited[nrow][ncol] == 0 && grid[nrow][ncol] == 1){
                    q.push({{nrow,ncol},t + 1});
                    visited[nrow][ncol] = 2;
                }
            }
        }

        //checking if some remaining fresh orange is remaining or not
        for(int i = 0;i<n;i++){
            for(int j = 0;j<m;j++){
                if(visited[i][j] != 2 && grid[i][j] == 1){
                    return -1;
                }
            }
        }
        return tm;
    }
};
```

---
##### Flood Fill
link - [Flood Fill - LeetCode](https://leetcode.com/problems/flood-fill/)

```cpp
class Solution {
public:
    vector<vector<int>> floodFill(vector<vector<int>>& image, int sr, int sc, int color) {
        int m = image.size();
        int n = image[0].size();
        vector<vector<int>>finalImage;

        finalImage = image;
        finalImage[sr][sc] = color;
        queue<pair<int,int>>q;
        q.push({sr,sc});

        int delr[] = {-1,0,1,0};
        int delc[] = {0,1,0,-1};
        vector<vector<int>>visited(m,vector<int>(n,0));
        while(!q.empty()){
            int r = q.front().first;
            int c = q.front().second;
            q.pop();
            finalImage[r][c] = color;
            for(int i = 0;i<4;i++){
                int nrow = r + delr[i];
                int ncol = c + delc[i];
                if(nrow>=0 && nrow<m && ncol>=0 && ncol<n && !visited[nrow][ncol] && image[nrow][ncol] == image[sr][sc]){
                    q.push({nrow,ncol});
                    visited[nrow][ncol] = 1;
                }
            }
        }
        return finalImage;
    }
};
```

---

##### Undirected Graph Cycle (BFS)
problem link - [Undirected Graph Cycle (BFS)](https://www.geeksforgeeks.org/problems/detect-cycle-in-an-undirected-graph/1)

```cpp
class Solution {
  public:
    bool detectCycle(vector<vector<int>>& adj, vector<int>&visited, int i) {
        queue<pair<int,int>>q; //storing the node and the source it came 
        q.push({i,-1}); //initially it is 0 (and in other iterations could 
        //be any other node of connected components and it has no source)
        visited[i] = 1; //from (parent)
        while(!q.empty()){
            int node = q.front().first;
            int parent = q.front().second;
            q.pop();
            for(auto adjacentNode: adj[node]){
                if(visited[adjacentNode] == 0){
                    q.push({adjacentNode,node});
                    visited[adjacentNode] = 1;
                }
                else if(parent != adjacentNode){
                    return true;
                    //if its visited already and the its source
                    //is not same as the one we are at now, 
                    //meaning this node is not visited node from 
                    //the same guy and hence from somewhere else,
                    //meaning there is cycle
                }
            }
        }
        return false;        
    }
    bool isCycle(int V, vector<vector<int>>& edges){
        //making adjacent list from edges
        vector<vector<int>>adjList(V);
        for(auto itr:edges){
            adjList[itr[1]].push_back(itr[0]);
            adjList[itr[0]].push_back(itr[1]);
        }
        
        //for connected components we run this loop
        vector<int>vis(V,0);
        for(int i = 0;i<V;i++){
            if(!vis[i])
            if(detectCycle(adjList,vis,i)) return true;
        }
        return false;
    }
};
```

---
##### Undirected Graph Cycle (DFS)
problem link - [Undirected Graph Cycle (DFS)](https://www.geeksforgeeks.org/problems/detect-cycle-in-an-undirected-graph/1)

```cpp
class Solution {
  public:
    bool DFS(int node, int parent, vector<int>& visited, vector<vector<int>>& adjList){
        visited[node]=1;
        for(auto adjacentNode: adjList[node]){
            if(!visited[adjacentNode]){
                if(DFS(adjacentNode, node, visited, adjList) == true){ //true means cycle
                    return true; //if at any node cycle is found,
                    //then its parent recursion call will return
                    //true as well since it has cycle as well
                }
            }
            else if(parent != adjacentNode){
                    return true;
                    //if its visited already, we check if the node
                    //is visiting its parent (the usual case), but
                    //if not parent then there is a cycle
                }
        }
        return false;
    }
    bool isCycle(int V, vector<vector<int>>& edges) {
        vector<vector<int>>adjList(V);
        for(int i = 0;i<edges.size();i++){
            adjList[edges[i][0]].push_back(edges[i][1]);
            adjList[edges[i][1]].push_back(edges[i][0]);
        }
        vector<int>visited(V,0);
        
        for(int i = 0;i<V;i++){
            if(!visited[i]){
                if(DFS(i,-1,visited,adjList) == true) return true;
            }
        }
        return false;
        
    }
};
```

---
##### 01 Matrix
link - [01 Matrix](https://leetcode.com/problems/01-matrix/)

```cpp
class Solution {
public:
    vector<vector<int>> updateMatrix(vector<vector<int>>& mat) {
        int n = mat.size();
        int m = mat[0].size();
        queue<pair<pair<int,int>,int>>q;
        vector<vector<int>>visited(n,vector<int>(m,0));
        vector<vector<int>>distance(n,vector<int>(m,0));

        //first put all the targets in queue and smallest distance is 0
        //now from there iterate level wise and put in queue nodes which
        //are not visited and valid, since its level wise with roots 0's
        //so they have the smallest distance only, now keep iterating and fill
        //distance matrix 
        for(int i = 0;i<n;i++){
            for(int j = 0;j<m;j++){
                if(mat[i][j] == 0){
                    q.push({{i,j},0});
                    visited[i][j] = 1;
                }
            }
        }
        while(!q.empty()){
            int row = q.front().first.first;
            int col = q.front().first.second;
            int steps = q.front().second;
            q.pop();
            distance[row][col] =  steps;
            int diffrow[] = {0,0,1,-1};
            int diffcol[] = {1,-1,0,0};
            for(int i=0;i<4;i++){
                int nrow = row + diffrow[i];
                int ncol = col + diffcol[i];
                if(nrow>=0 && nrow<n && ncol>=0 && ncol<m && !visited[nrow][ncol]){
                    q.push({{nrow,ncol}, steps + 1});
                    visited[nrow][ncol] = 1;
                }
            }
        }
        return distance;
    }
};
```

---

##### Surrounded Region
link - [Surrounded  Region](https://leetcode.com/problems/surrounded-regions/description/)

```cpp
class Solution {
public:
    void DFS(int row, int col, vector<vector<int>>&visited, vector<vector<char>>& board){
        int n = board.size();
        int m = board[0].size();
        visited[row][col] = 1;
        int diffrow[] = {0,0,1,-1};
        int diffcol[] = {1,-1,0,0};
        for(int i=0;i<4;i++){
            int nrow = row + diffrow[i];
            int ncol = col + diffcol[i];
            if(nrow>=0 && nrow<n && ncol>=0 && ncol<m && !visited[nrow][ncol] && board[nrow][ncol] == 'O'){
                DFS(nrow,ncol,visited,board);   
            }
    }
    }
    void solve(vector<vector<char>>& board) {
        //if an O is in either in first/last row/col then it cannot be converted
        //and any O connected to it cannot be either, so we apply dfs from those 
        //nodes and mark nodes visited which are connected, rest can be converted
        int n = board.size();
        int m = board[0].size();

        vector<vector<int>> visited(n, vector<int>(m, 0));
        for(int i = 0; i<n;i++){
            if(board[i][0] == 'O'){ //first column
                DFS(i,0,visited,board);
            }
            if(board[i][m-1] == 'O'){ //last column
                DFS(i,m-1,visited,board);
            }
        }
        for(int i = 0; i<m;i++){
            if(board[0][i] == 'O'){ //first row
                DFS(0,i,visited,board);
            }
            if(board[n-1][i] == 'O'){ //last row
                DFS(n-1,i,visited,board);
            }
        }

        for(int i = 0;i<n;i++){
            for(int j = 0;j<m;j++){
                if(!visited[i][j] and board[i][j] == 'O'){
                    board[i][j] = 'X';
                }
            }
        }
    }
};
```

---
##### Number of Enclaves
link - [Number of Enclaves](https://leetcode.com/problems/number-of-enclaves/)

```cpp
class Solution {
public:
    int numEnclaves(vector<vector<int>>& grid) {
        int n = grid.size();
        int m = grid[0].size();
        vector<vector<int>>visited(n,vector<int>(m,0));
        queue<pair<int,int>>queue;
        for(int i = 0;i<n;i++){
            for(int j = 0;j<m;j++){
                //first,last row/col
                if(i == 0|| j == 0 || i == n-1 || j == m-1){
                    if(grid[i][j] == 1){
                        queue.push({i,j});
                        visited[i][j] = 1;
                    }
                }
            }
        } 
        while(!queue.empty()){
            int row = queue.front().first;
            int col = queue.front().second;
            queue.pop();
            int delrow[] = {0,1,-1,0}; 
            int delcol[] = {1,0,0,-1}; 
            for(int i = 0;i<4;i++){
                int newrow = row + delrow[i];
                int newcol = col + delcol[i];
                if(newrow>=0 && newrow < n && newcol>=0 && newcol<m && !visited[newrow][newcol] && grid[newrow][newcol] == 1){
                    queue.push({newrow,newcol});
                    visited[newrow][newcol] = 1;
                }
            }
        }
        int count = 0;
        for(int i = 0;i<n;i++){
            for(int j = 0;j<m;j++){
                if(visited[i][j] == 0 && grid[i][j] == 1){
                    count++;
                }
            }
        }
        return count;
            
    }
};
```

---

##### Word Ladder
link - [Word Ladder](https://leetcode.com/problems/word-ladder/)

![[Pasted image 20250618210907.png|400]]
```cpp
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        //store the words in a set to know whether a word exist while on iteration
        unordered_set<string> st(wordList.begin(),wordList.end());
        queue<pair<string,int>>queue; //queue stores the word and the level(bfs) at which it was recieved
        queue.push({beginWord,1});
        st.erase(beginWord); 
        //in a sense, deleting from set works like a visited array
        while(!queue.empty()){
            string word = queue.front().first;
            int steps = queue.front().second;
            queue.pop();
            if(word == endWord) return steps;//the first time we get endWord is the shortest
            //try changing each letter of word one by one in each iteration
            for(int i = 0;i<word.size();i++){
                char original = word[i];
                for(char c = 'a';c<='z';c++){
                    word[i] = c;
                    //if word exists in set
                    if(st.find(word) != st.end()){
                        st.erase(word);//we do not want to take
//case of a word found again in later step, we dont need it since we already have that word in a lesser step so why need it with larger step which would make the shortest path longer
                        queue.push({word,steps+1});
                    }
                }
                word[i] = original;
            }

        }
        return 0;
    }
};
```

##### Word Ladder - II
link - 

![[Pasted image 20250619001601.png|400]]
we store the entire sequences in queue and maintain levels, and remove the last word in vector inside queue only if the entire level is complete, meaning level is increased, the vector has grown in size by 1. We start with the beginWord and append the words that are present in set after changing one letter. Since we start from level 0 (0 elements in ans vector) so the moment we reach the endWord, we are already the smallest level possible.

```cpp
class Solution {
public:
    vector<vector<string>> findLadders(string beginWord, string endWord, vector<string>& wordList) {
        unordered_set<string> st(wordList.begin(), wordList.end());
        queue<vector<string>>q; //no need of maintaining level since its size gives it already
        q.push({beginWord});
        vector<string>usedOnLevel;
        usedOnLevel.push_back(beginWord);
        vector<vector<string>> ans;
        int level = 0;

        while(!q.empty()){
            vector<string> vec = q.front();
            q.pop();

            //erase all words used in previous levels to transform
            if(vec.size() > level){
                level++;
                for(auto it: usedOnLevel){//since we mark out words(from set) only when the level is over
                    st.erase(it);
                }
            }

            string word = vec.back();
            if(word == endWord){
                //first sequence to reach end
                if(ans.size() == 0){
                    ans.push_back(vec);
                }
                else if(ans[0].size() == vec.size()){
                    ans.push_back(vec);
                }
            }

            for(int i = 0;i<word.size();i++){
                char original = word[i];
                for(char c = 'a';c<='z';c++){
                    word[i] = c;
                    if(st.count(word) > 0){//if that mutation is present in set
                        vec.push_back(word);//push it to the vector
                        q.push(vec);//this new level or same level diff iteration vector to be pushed in queue
                        //mark all visited on level
                        usedOnLevel.push_back(word);
                        vec.pop_back(); //we don't want this new word to mess the
                        //remaining iteration of this level, so pop_back.
                    }
                }
                word[i] = original;
            }
        }
        return ans;
    }
};

```

---
##### Bipartite Graph
link - [Bipartite Graph](https://leetcode.com/problems/is-graph-bipartite/description/)

_a graph that can be colored with 2 colors such that no 2 adjacent nodes have the same color_
>graph with odd length cycle cannot be bipartite. Even length cycle and linear graphs are bipartite

→ instead of visited array in the dfs, here we can take array color to mark visited as well as the color with which its marked (0/1), the adjacent one assigns color to its adjacent if not the same color.

```cpp
class Solution {
public:
    bool DFS(int node, int col, vector<int>&color, vector<vector<int>>&graph){
        color[node] = col;
        for(auto it: graph[node]){
            if(color[it] == -1){
                if(DFS(it,!col,color,graph) == false) return false;
            }
            else if(color[it] == col){
                return false;
            }
        }
        return true;
    }
    bool isBipartite(vector<vector<int>>& graph) {
        int n = graph.size();
        vector<int> color(n,-1);
        //for connected components
        for(int i = 0;i<n;i++){
            if(DFS(i,0,color,graph) == false) return false;
        }
        return true;
    }
};

```

---
##### Cycle Detection in Directed Graph (DFS)
link - [gfg](https://www.geeksforgeeks.org/problems/detect-cycle-in-a-directed-graph/1)

→ if a node is visited twice does not mean it forms cycle since it has to come back from same path it took before for a cycle formation in directed edges, so we take a vector visited and another vector pathVisited, we mark pathVisited 1 when we traverse a node, and when DFS is done and it backtracks, it is marked 0 again. If an adjacent node is visited but not from same path then no need to go down that path again, nothing there but if its from same path then cycle.

```cpp
class Solution {
  public:
    bool DFS(int node, vector<int>&visited, vector<int>&pathVisited,vector<vector<int>> &edges){
        visited[node] = 1;
        pathVisited[node] = 1;
        
        for(auto it: edges[node]){
            if(!visited[it]){
                if(DFS(it,visited, pathVisited, edges) == true) return true;//if later on it gets true
            }
            //if the node has been visited and visited on the same path
            else if(pathVisited[it]){
                return true;
            }
        }
        
        pathVisited[node] = 0; //when backtracking, revert its state
        return false;
    }
    
    bool isCyclic(int V, vector<vector<int>> &edges) {
        vector<vector<int>>adj(V);
        for(int i = 0;i<edges.size();i++){
            adj[edges[i][0]].push_back(edges[i][1]);
        }
        vector<int>visited(V,0);
        vector<int>pathVisited(V,0);
        
        for(int i = 0;i<V;i++){
            if(!visited[i])
                if(DFS(i,visited,pathVisited,adj) == true) return true;
        }
        return false;
        
    }
};
```
