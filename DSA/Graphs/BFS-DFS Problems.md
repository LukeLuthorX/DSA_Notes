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
