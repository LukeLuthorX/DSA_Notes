[[Dynamic Programming]]

##### Matrix Chain Multiplication
link - [Matrix Chain Multiplication | Practice | GeeksforGeeks](https://www.geeksforgeeks.org/problems/matrix-chain-multiplication0303/1?utm_source=youtube&utm_medium=collab_striver_ytdescription&utm_campaign=matrix-chain-multiplication)

Matrix multiplication -  $A_{5 \times 30}  \times  B_{30 \times 20}$ 
since 30 match in both, resultant martix is 5 x 30. The number of calculations made is 5 x 30 x 20 (take the common part once)

partioning the matrix multiplication
![[DPonMCM.png|400]]

> [!NOTE]- ###### What does "splitting at `k`" MEAN?
> 
> It means you are deciding to do it in these steps:
> 
> 1. **First:** Multiply all the matrices from index `i` up to index `k`. Let's call the result of this multiplication `LEFT_RESULT`. This `LEFT_RESULT` will be a single matrix.
>     
> 2. **Second:** Multiply all the matrices from index `k+1` up to index `j`. Let's call the result of this `RIGHT_RESULT`. This `RIGHT_RESULT` will also be a single matrix.
>     
> 3. **Finally:** Multiply `LEFT_RESULT` and `RIGHT_RESULT`. This gives you the final product of the whole chain from `i` to `j`.
>     
> 
> **`arr[i-1] * arr[k] * arr[j]` is ONLY about the COST of Step 3 (the FINAL multiplication).**
> 
> It's the cost to multiply the `LEFT_RESULT` matrix and the `RIGHT_RESULT` matrix together.
> Cost of getting LEFT_RESULT (from recursion) + Cost of getting RIGHT_RESULT (from recursion) + Cost of multiplying LEFT_RESULT and RIGHT_RESULT (which is arr[i-1] * arr[k] * arr[j])

```cpp
class Solution {
  public:
    int solveUtil(int i, int j, vector<int> &arr, vector<vector<int>> &dp){
        if(i == j) return 0; //single matrix -> no calculation in multiply
        if(dp[i][j] != -1) return dp[i][j];
        int mini = 1e9;
        for(int k = i;k<j;k++){
            int steps = arr[i-1] * arr[k] * arr[j] + solveUtil(i,k,arr,dp) + solveUtil(k+1,j,arr,dp);
            mini = min(mini,steps);
        }
        return dp[i][j] = mini;
    }
    int matrixMultiplication(vector<int> &arr) {
        int n = arr.size();
        vector<vector<int>>dp(n,vector<int>(n,-1));
        return solveUtil(1,n-1,arr,dp);
    }
};
```

**Tabulation**
```cpp
class Solution {
  public:
    int matrixMultiplication(vector<int> &arr) {
        int n = arr.size();
        vector<vector<int>>dp(n,vector<int>(n,0));
        
        for(int i = 0;i<n;i++){
            dp[i][i] = 0;
        }
        
        for(int i = n-1;i>=1;i--){
            for(int j = i+1;j<n;j++){
                int mini = 1e9;
                for(int k = i;k<j;k++){
                    int steps = arr[i-1] * arr[k] * arr[j] + dp[i][k] + dp[k+1][j]; 
                    //left*common*right from spliting
                    mini = min(mini, steps);
                }
                dp[i][j] = mini;
            }
        }
        return dp[1][n-1];
    }
};
```

---
##### Minimum Cost to Cut a Stick
link - [Minimum Cost to Cut a Stick - LeetCode](https://leetcode.com/problems/minimum-cost-to-cut-a-stick/)

**Memoization**
```cpp
class Solution {
public:
    int solveUtil(int i, int j, vector<int>& cuts, vector<vector<int>>&dp) {
        if (i > j) return 0; // All cuts over
        if(dp[i][j] != -1) return dp[i][j];
        int mini = INT_MAX;
        for (int ind = i; ind <= j; ind++) {
            int cost = cuts[j + 1] - cuts[i - 1] + solveUtil(i, ind - 1, cuts,dp) + solveUtil(ind + 1, j, cuts,dp);
            mini = min(mini, cost);
        }
        return dp[i][j] = mini;
    }
    int minCost(int n, vector<int>& cuts) {
        cuts.push_back(n);
        cuts.insert(cuts.begin(),0);
        sort(cuts.begin(), cuts.end());
        int m = cuts.size() - 2; 
        vector<vector<int>>dp(m+1,vector<int>(m+1,-1));
        return solveUtil(1,m, cuts,dp);
    }
};
```

**Tabulation**
```cpp
class Solution {
public:
    int minCost(int n, vector<int>& cuts) {
        cuts.push_back(n);
        cuts.insert(cuts.begin(),0);
        sort(cuts.begin(), cuts.end());
        int m = cuts.size() - 2; 
        vector<vector<int>>dp(m+2,vector<int>(m+2,0));
        
        for(int i = m; i>=1 ; i--){
            for(int j = 1; j<=m;j++){
                if(i>j) continue;
                int mini = INT_MAX;
                for (int ind = i; ind <= j; ind++) {
                    int cost = cuts[j + 1] - cuts[i - 1] + dp[i][ind-1] + dp[ind+1][j];
                    mini = min(mini, cost);
                }
                dp[i][j] = mini;
            }
        }
        return dp[1][m];
    }
};
```

---
##### Burst Balloons
link - [Burst Balloons - LeetCode](https://leetcode.com/problems/burst-balloons/)

**Memoization**
```cpp
class Solution {
public:
    int solveUtil(int i, int j, vector<int>& nums, vector<vector<int>>&dp){
        if(i>j) return 0; //no more subproblems
        if(dp[i][j] != -1) return dp[i][j];
        int maxi = INT_MIN;
        for(int ind = i;ind<=j;ind++){
            int coins = nums[i-1] * nums[ind] * nums[j+1] + solveUtil(i,ind-1,nums,dp) + solveUtil(ind+1,j,nums,dp);
            maxi = max(maxi,coins);
        }
        return dp[i][j] = maxi;
    }
    int maxCoins(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>>dp(n+1,vector<int>(n+1,-1));
        nums.push_back(1);
        nums.insert(nums.begin(),1);
        return solveUtil(1,n,nums,dp);
    }
};
```

> [!NOTE]- why bottom up
> In the Burst Balloons problem, a bottom-up approach is employed because processing balloons from the beginning creates interdependencies due to neighbor alterations after each burst. By contrast, a bottom-up strategy, which effectively considers which balloon is burst _last_, resolves these dependencies. This reverse perspective allows the problem to be decomposed into independent subproblems, making it solvable with dynamic programming.

**Tabulation**
```cpp
class Solution {
public:
    int solveUtil(int i, int j, vector<int>& nums, vector<vector<int>>&dp){
        if(i>j) return 0; //no more subproblems
        if(dp[i][j] != -1) return dp[i][j];
        int maxi = INT_MIN;
        for(int ind = i;ind<=j;ind++){
            int coins = nums[i-1] * nums[ind] * nums[j+1] + solveUtil(i,ind-1,nums,dp) + solveUtil(ind+1,j,nums,dp);
            maxi = max(maxi,coins);
        }
        return dp[i][j] = maxi;
    }
    int maxCoins(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>>dp(n+2,vector<int>(n+2,0));
        nums.push_back(1);
        nums.insert(nums.begin(),1);
        
        for(int i = n; i>=1;i--){
            for(int j = 1;j<=n;j++){
                if(i>j) continue;
                int maxi = INT_MIN;
                for(int ind = i; ind <= j; ind++){
                    int coins = nums[i-1]*nums[ind]*nums[j+1] + dp[i][ind-1] + dp[ind+1][j];
                    maxi = max(maxi, coins);
                }
                dp[i][j] = maxi;
            }
        }
        return dp[1][n];
    }
};
```
---
##### Evaluate Boolean Expression to True
link - [Boolean Evaluation - Naukri Code 360](https://www.naukri.com/code360/problems/problem-name-boolean-evaluation_1214650?source=youtube&campaign=striver_dp_videos&utm_source=youtube&utm_medium=affiliate&utm_campaign=striver_dp_videos&leftPanelTabValue=SUBMISSION)
(if parenthesis given as in leetcode problem, then its a basic stack problem not dp)

```cpp
int mod = 1000000007;

#define ll long long

ll solveUtil(int i, int j, int isTrue, string &exp, vector<vector<vector<ll>>> &dp) {
    if (i > j) return 0;
    if (i == j) {
        if (isTrue) {
            return exp[i] == 'T'; // returns 1 if true, 0 if false
        } else {
            return exp[i] == 'F';
        }
    }
    if (dp[i][j][isTrue] != -1) return dp[i][j][isTrue];
    ll ways = 0;
    for (int ind = i + 1; ind <= j - 1; ind += 2) {
        // left & right partition, either we need true or false to get no of ways to
        // get true from expression at ind
        ll lT = solveUtil(i, ind - 1, 1, exp, dp);
        ll lF = solveUtil(i, ind - 1, 0, exp, dp);
        ll rT = solveUtil(ind + 1, j, 1, exp, dp);
        ll rF = solveUtil(ind + 1, j, 0, exp, dp);

        if (exp[ind] == '&') {
            if (isTrue) {
                ways = (ways + (rT * lT) % mod) % mod;
            } else {
                ways = (ways + (rT * lF) % mod + (rF * lT) % mod + (rF * lF) % mod) % mod;
            }
        } else if (exp[ind] == '|') {
            if (isTrue) {
                ways = (ways + (rT * lT) % mod + (rF * lT) % mod + (rT * lF) % mod) % mod;
            } else {
                ways = (ways + (rF * lF) % mod) % mod;
            }
        } else {
            // xor
            if (isTrue) {
                ways = (ways + (rT * lF) % mod + (rF * lT) % mod) % mod;
            } else {
                ways = (ways + (rT * lT) % mod + (rF * lF) % mod) % mod;
            }
        }
    }
    return dp[i][j][isTrue] = ways;
}

int evaluateExp(string &exp) {
    int n = exp.size();
    vector<vector<vector<ll>>> dp(n, vector<vector<ll>>(n, vector<ll>(2, -1)));
    return solveUtil(0, n - 1, 1, exp, dp);
}
```
---
##### Palindrome Partitioning
link - [Palindrome Partitioning II - LeetCode](https://leetcode.com/problems/palindrome-partitioning-ii/description/)

forward partitioning concept
![[DPonPartitionP.png|400]]
```cpp
class Solution {
public:
    bool isPalindrome(int i, int j, string &s){
        while(i < j){
            if(s[i] != s[j]) return false;
            i++;
            j--;
        }
        return true;
    }

    int solveUtil(int i, int n, string &s, vector<int>& dp){
        if(i == n) return 0;
        if(dp[i] != -1) return dp[i];
        int minCost = INT_MAX;
        for(int j = i; j < n; j++){
            if(isPalindrome(i, j, s)){
                int cost = 1 + solveUtil(j + 1, n, s, dp);
                minCost = min(minCost, cost);
            }
        }
        return dp[i] = minCost;
    }

    int minCut(string s) {
        int n = s.size();
        if(n == 0) return 0;
        vector<int> dp(n, -1);
        return solveUtil(0, n, s, dp)-1;
    }
};

```
---
##### Partition Array for Maximum Sum
link - [Partition Array for Maximum Sum - LeetCode](https://leetcode.com/problems/partition-array-for-maximum-sum/description/)

forward partition concept

```cpp
class Solution {
public:
    int solveUtil(int ind,int k,vector<int>& arr,int n,vector<int>&dp){
        if(ind == n) return 0;
        int len = 0;
        int maxi = INT_MIN;
        int maxAns = INT_MIN;
        if(dp[ind] != -1) return dp[ind];
        for(int i = ind; i<min(n,ind+k);i++){
            len++;
            maxi = max(maxi, arr[i]);
            int sum = len*maxi + solveUtil(i+1,k,arr,n,dp);
            maxAns = max(sum,maxAns);
        }
        return dp[ind] = maxAns;
    }
    int maxSumAfterPartitioning(vector<int>& arr, int k) {
        int n = arr.size();
        vector<int>dp(n,-1);
        return solveUtil(0,k,arr,n,dp);
    }
};
```

