[[Dynamic Programming]]
## **2D/3D DP and DP on Grids**

##### Geek's Training
link - [Geek's Training | Practice | GeeksforGeeks](https://www.geeksforgeeks.org/problems/geeks-training/1?utm_source=youtube&utm_medium=collab_striver_ytdescription&utm_campaign=geeks-training)

we maintain a state for index and one state for the operation done on activity on **last** day, last = $0,1,2,3$ , where 0,1,2 are the indices of the matrix or the three activities and $3$ is the initial value of last so that all the activities are picked on the first iteration on recurence relation
`dp[n-1][last]` = till n-1 days whats the max points you can get
`dp[3][last]` = days 0 - 3 => max points you can get

recursion -> think the function in terms of index, write base case, do stuff on index, return max, also think in terms of how final iteration would look like, the top, and subproblems in recursions would solve themselves, so top-down thinking

**memoization -** 
```cpp
class Solution {
  public:
    int solveUtil(int n,int last,vector<vector<int>>& arr,vector<vector<int>>& dp){
        if(n == 0){
            int maxi = 0;
            for(int i = 0;i<=2;i++){
                if(last!=i){
                    maxi = max(arr[0][i],maxi);
                }
            }
            return dp[n][last] = maxi;
        }
        if(dp[n][last]!=-1) return dp[n][last];
        int maxi = 0;
        for(int i = 0;i<=2;i++){
            if(i!=last){
                int points = arr[n][i] + solveUtil(n-1,i,arr,dp);
                maxi = max(points,maxi);
            }
        }
        return dp[n][last] = maxi;
        
    }
    int maximumPoints(vector<vector<int>>& arr) {
        int n = arr.size();
        vector<vector<int>>dp(n,vector<int>(4,-1));
        return solveUtil(n-1,3,arr,dp);
    }
};
```
***T.C = O($N \times 4 \times 3$)***   (each function is called for 4 states (technically 3, and 4 only for the first case and in each function a for loop runs for 3 i))
***S.C = O($N$) + O($N \times 4$) (stack space + array)***

**Tabulation**
```cpp
class Solution {
  public:
    int maximumPoints(vector<vector<int>>& arr) {
        int n = arr.size();
        vector<vector<int>>dp(n,vector<int>(4,0));
        
        dp[0][0] = max(arr[0][1],arr[0][2]);
        dp[0][1] = max(arr[0][0],arr[0][2]);
        dp[0][2] = max(arr[0][0],arr[0][1]);
        dp[0][3] = max({arr[0][1],arr[0][1],arr[0][2]});
        
        for(int day = 1;day<n;day++){
            for(int last = 0;last<4;last++){
                dp[day][last] = 0;
                for(int task = 0;task<3;task++){
                    if(task!=last){
                        int points = arr[day][task] + dp[day-1][task];
                        dp[day][last] = max(dp[day][last],points);
                    }
                }
            }
        }
        return dp[n-1][3];
    }
};
```
***T.C = O($N \times 4 \times 3$)*** 
***S.C = O($N \times 4$)**

**further space optimization** - only the previous state is required, so keeping array of size 4 to maintain state.

```cpp
class Solution {
  public:
    int maximumPoints(vector<vector<int>>& arr) {
        int n = arr.size();
        vector<int>dp(4,0);
        
        dp[0] = max(arr[0][1],arr[0][2]);
        dp[1] = max(arr[0][0],arr[0][2]);
        dp[2] = max(arr[0][0],arr[0][1]);
        dp[3] = max({arr[0][1],arr[0][1],arr[0][2]});
        
        for(int day = 1;day<n;day++){
            vector<int>temp(4,0); 
            for(int last = 0;last<4;last++){
                temp[last] = 0;
                for(int task = 0;task<3;task++){
                    if(task!=last){
                        int points = arr[day][task] + dp[task];
                        temp[last] = max(temp[last],points);
                    }
                }
            }
            dp = temp;
        }
        return dp[3];
    }
};
```
***T.C = O($N \times 4 \times 3$)*** 
***S.C = O($4$)***

#### DP on Grids
##### Unique Path
link - [Unique Paths - LeetCode](https://leetcode.com/problems/unique-paths/)

**memoization -** 
here we are using the top down approach and going from $(m-1,n-1)$ to $(0,0)$, and if we reach 0,0 then return 1 meaning the path is completed, otherwise return 0 (when it goes beyond the grid).
```cpp
class Solution {
public:
    int solveUtil(int i, int j,vector<vector<int>>&dp){
        if(i == 0 && j == 0) return 1;
        if(i<0 || j<0) return 0;
        if(dp[i][j] != -1) return dp[i][j];
        int up = solveUtil(i-1,j,dp);
        int left = solveUtil(i,j-1,dp);
        return dp[i][j] = (up + left);

    }
    int uniquePaths(int m, int n) {
        vector<vector<int>>dp(m,vector<int>(n,-1));
        return solveUtil(m-1,n-1,dp);
    }
};
```
***T.C = O($N \times M$)*** 
***S.C = O($(N-1)+(M-1)$)+($N \times M$)*** (number of paths(stack space) and dp space)

**Tabulation** - 
```cpp
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<vector<int>>dp(m,vector<int>(n,0));
        dp[0][0] = 1;
        for(int i = 0;i<m;i++){
            for(int j = 0;j<n;j++){
                if(i == 0 && j == 0) dp[i][j] = 1;
                else{
                    int up = 0;
                    int left = 0;
                    if(i>0)up = dp[i-1][j];
                    if(j>0)left = dp[i][j-1];
                    dp[i][j] = up+left;
                }
            }
        }
        return dp[m-1][n-1];
    }
};
```
***T.C = O($N \times M$)*** 
***S.C = O($N \times M$)***

**Further Space Optimization** -
only require one previous row. so initializing the row with 0 and updating as we keep traversing each row.
`dp[i-1][j]` is the previous row, so dp[j] and `dp[i][j-1]` is the same row just previous column stored in temp itself.
```cpp
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<int>dp(n,0);
        for(int i = 0;i<m;i++){
            vector<int>temp(n,0);
            for(int j = 0;j<n;j++){
                if(i == 0 && j == 0) temp[j] = 1;
                else{
                    int up = 0;
                    int left = 0;
                    if(i>0)up = dp[j];
                    if(j>0)left = temp[j-1];
                    temp[j] = (up + left);
                }
            }
            dp = temp;
        }
        return dp[n-1];
    }
};
```

##### Unique Path - 2
link - [Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)
1 means obstacle so add this condition at top and done.
`if(i>=0 && j>=0 && obstacleGrid[i][j]==1) return 0;`

##### Minimum Path Sum
link - [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)
almost the same as all unique paths of travelling from $(0,0)$ to $(m-1,n-1)$, just you have to take the minimum of up and left this time and return high value if out of bound since it isn't considered.

**memoization -** 
```cpp
class Solution {
public:
    int path(int i,int j,vector<vector<int>>& grid,vector<vector<int>>& dp){
        if(i == 0 && j == 0){
            return grid[i][j];
        }
        if(i<0 || j<0){
            return INT_MAX - 200;
        }
        if(dp[i][j] != -1) return dp[i][j];
        int up = grid[i][j] + path(i-1,j,grid,dp);
        int down = grid[i][j] + path(i,j-1,grid,dp);
        return dp[i][j] = min(up,down);
    }
    int minPathSum(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        vector<vector<int>>dp(m,vector<int>(n,-1));
        return path(m-1,n-1,grid,dp);
    }
};
```

**Tabulation -** 
```cpp
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        vector<vector<int>>dp(m,vector<int>(n,0));
        dp[0][0] = grid[0][0];
        for(int i = 0;i<m;i++){
            for(int j = 0;j<n;j++){
                if(i == 0 && j == 0) continue;
                int up;
                int left;
                if(i>0) up = grid[i][j] + dp[i-1][j];
                else up=INT_MAX - 200;
                if(j>0) left = grid[i][j] + dp[i][j-1];
                else left=INT_MAX - 200;
                dp[i][j] = min(up,left);
            }
        }
        return dp[m-1][n-1];
    }
};
```

##### Triangle Sum
link - [Triangle - LeetCode](https://leetcode.com/problems/triangle/)

**Memoization -** 
```cpp
class Solution {
public:
    int solver(int i, int j, vector<vector<int>>& triangle, vector<vector<int>>& dp){
        if(i == 0){
            return triangle[0][0];
        }
        if(dp[i][j] != -1) return dp[i][j];
        int pick1 = INT_MAX;
        int pick2 = INT_MAX;
        int sum = INT_MAX;
        if(j<i)pick1 = triangle[i][j]+solver(i-1,j,triangle,dp);
        if(j>0)pick2 = triangle[i][j]+solver(i-1,j-1,triangle,dp);
        sum = min({pick1,pick2,sum});
        return dp[i][j] = sum;
    }
    int minimumTotal(vector<vector<int>>& triangle) {
        int n = triangle.size();
        vector<vector<int>>dp(n,vector<int>(n,-1));
        int minSum = INT_MAX;
        for(int j = 0;j<n;j++){
            minSum = min(minSum, solver(n-1,j,triangle,dp));
        }
        return minSum;
    }
};
```

**Tabulation -** 
```cpp
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        int n = triangle.size();
        vector<vector<int>>dp(n,vector<int>(n,-1));
        dp[0][0] = triangle[0][0];
        for(int i = 1;i<n;i++){
            for(int j = 0;j<=i;j++){
                int pick1 = INT_MAX;
                int pick2 = INT_MAX;
                if(j<i)pick1 = triangle[i][j] + dp[i-1][j];
                if(j>0)pick2 = triangle[i][j] + dp[i-1][j-1];
                dp[i][j] = min(pick1, pick2);
            }
        }
        int ans = INT_MAX;
        for(int i = 0;i<n;i++){
            ans = min(dp[n-1][i],ans);
        }
        return ans;
    }
};
```

**Further Space Reduction**

```cpp
    int minimumTotal(vector<vector<int>>& triangle) {
        int n = triangle.size();
        vector<int>dp(n,0);
        dp[0] = triangle[0][0];
        for(int i = 1;i<n;i++){
            vector<int>temp(n,0);
            for(int j = 0;j<=i;j++){
                int pick1 = INT_MAX;
                int pick2 = INT_MAX;
                if(j<i)pick1 = triangle[i][j] + dp[j];
                if(j>0)pick2 = triangle[i][j] + dp[j-1];
                temp[j] = min(pick1, pick2);
            }
            dp = temp;
        }
        int ans = INT_MAX;
        for(int i = 0;i<n;i++){
            ans = min(dp[i],ans);
        }
        return ans;
    }
```

##### Maximum Falling Path Sum
link - [Minimum Falling Path Sum - LeetCode](https://leetcode.com/problems/minimum-falling-path-sum/description/)
solved it myself in 10 min using most spaced optimized approach :)

```cpp
int minFallingPathSum(vector<vector<int>>& matrix) {
	int n = matrix.size();
	vector<int>dp(n);
	for(int i = 0;i<n;i++){
		dp[i] = matrix[0][i];
	}
	for(int i = 1;i<n;i++){
		vector<int>temp(n);
		for(int j = 0;j<n;j++){
			int pick1 = INT_MAX, pick2 = INT_MAX, pick3 = INT_MAX;
			pick1 = matrix[i][j] + dp[j];
			if(j>0)pick2 = matrix[i][j] + dp[j-1];
			if(j<n-1)pick3 = matrix[i][j] + dp[j+1];
			temp[j] = min({pick1, pick2, pick3});
		}
		dp = temp;
	}
	int minSum = INT_MAX;
	for(int i = 0;i<n;i++){
		minSum = min(minSum, dp[i]);
	}
	return minSum;
}
```


##### Cherry Pickup
[Cherry Pickup II - LeetCode](https://leetcode.com/problems/cherry-pickup-ii/description/)
- here moving both the robots at once since if done separately, there would be many common cells counted in both of them.
- Here, *Starting point is fixed* and *Ending point is variable* so we write the **recursion from starting point.**
- we first take the indices for robots as ($i1,j1$) and ($i2,j2$). But one observation is that both of them simultaneously move to the next row, so no need to maintain two different i values.
- the possible paths are (i+1,j-1), (i+1, j), (i+1, j+1), so the pattern is -1,0,1 for j and row always increases by 1.

```cpp
class Solution {
public:
    int solveUtils(int i, int j1, int j2, int n, int m ,vector<vector<int>>& grid,vector<vector<vector<int>>>& dp){
        //out of bounds
        if(j1 < 0 || j1 >= m || j2 < 0 || j2 >= m){
            return -1e7;
        }
        //reached last row
        if(i == n-1){
            if(j1 == j2){
                return grid[i][j1];
            }
            else {
                return grid[i][j1] + grid[i][j2];
            }
        }
        if(dp[i][j1][j2]!=-1) return dp[i][j1][j2];
        int maxi = -1e7;
        for(int dj1 = -1; dj1<=1; dj1++){
            for(int dj2 = -1; dj2<=1; dj2++){
                int value = 0;
                if(j1 == j2) value = grid[i][j1];
                else value = grid[i][j1] + grid[i][j2];
                value += solveUtils(i+1,j1+dj1,j2+dj2,n,m,grid,dp);
                maxi = max(maxi, value);
            }
        }
        return dp[i][j1][j2] = maxi;
    }
    int cherryPickup(vector<vector<int>>& grid) {
        int m = grid[0].size();
        int n = grid.size();
        vector<vector<vector<int>>>dp(n,vector<vector<int>>(m,vector<int>(m,-1)));
        return solveUtils(0,0,m-1,n,m,grid,dp);
    }
};

```

***T.C = O($N \times M \times M$)$\times  9$*** 
***S.C = O($N \times M \times M$) + O($N$)***

```cpp
class Solution {
public:
    int cherryPickup(vector<vector<int>>& grid) {
        int m = grid[0].size();
        int n = grid.size();
        vector<vector<vector<int>>>dp(n,vector<vector<int>>(m,vector<int>(m,0)));
        //base case
        for(int j1 = 0;j1<m;j1++){
            for(int j2 = 0;j2<m;j2++){
                if(j1 == j2) dp[n-1][j1][j2] = grid[n-1][j1];
                else{
                    dp[n-1][j1][j2] = grid[n-1][j1] + grid[n-1][j2];
                }
            }
        }

        for(int i = n-2;i>=0;i--){
            for(int j1 = 0;j1<m;j1++){
                for(int j2 = 0; j2<m ;j2++){
                    int maxi = -1e7;
                    for(int dj1 = -1; dj1<=1; dj1++){
                        for(int dj2 = -1; dj2<=1; dj2++){
                            int value = 0;
                            if(j1 == j2) value = grid[i][j1];
                            else value = grid[i][j1] + grid[i][j2];
                            if(j1+dj1>=0 && j1+dj1<m && j2+dj2>=0 && j2+dj2<m)
                                value += dp[i+1][j1+dj1][j2+dj2];
                            else value+=-1e7;
                            maxi = max(maxi, value);
                        }
                    }
                    dp[i][j1][j2] = maxi;
                }
            }
        }
        return dp[0][0][m-1];
    }
};
```

*removed the extra auxilary space.*

Now, we can remove the state storing the previous row, since its already known.

```cpp
class Solution {
public:
    int cherryPickup(vector<vector<int>>& grid) {
        int m = grid[0].size();
        int n = grid.size();
        vector<vector<int>>front(m,vector<int>(m,0));
        vector<vector<int>>cur(m,vector<int>(m,0));
        //base case
        for(int j1 = 0;j1<m;j1++){
            for(int j2 = 0;j2<m;j2++){
                if(j1 == j2) front[j1][j2] = grid[n-1][j1];
                else{
                    front[j1][j2] = grid[n-1][j1] + grid[n-1][j2];
                }
            }
        }

        for(int i = n-2;i>=0;i--){
            for(int j1 = 0;j1<m;j1++){
                for(int j2 = 0; j2<m ;j2++){
                    int maxi = -1e7;
                    for(int dj1 = -1; dj1<=1; dj1++){
                        for(int dj2 = -1; dj2<=1; dj2++){
                            int value = 0;
                            if(j1 == j2) value = grid[i][j1];
                            else value = grid[i][j1] + grid[i][j2];
                            if(j1+dj1>=0 && j1+dj1<m && j2+dj2>=0 && j2+dj2<m)
                                value += front[j1+dj1][j2+dj2];
                            else value+=-1e7;
                            maxi = max(maxi, value);
                        }
                    }
                    cur[j1][j2] = maxi;
                }
            }
            front = cur;
        }
        return front[0][m-1];
    }
};
```
***T.C = O($N \times M \times M$)$\times  9$*** 
***S.C = O($M \times M$)***


