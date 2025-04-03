[[Dynamic Programming]]
## **1-D DP**
##### Fibonacci optimized with DP

**Using Memoization - Bottom Up**
```cpp
int fibonacci(int n, vector<int>&dp) {
	if (n <= 1) return n;
	if (dp[n] != -1) return dp[n];
	return dp[n] = fibonacci(n - 1, dp) + fibonacci(n - 2, dp);
}
int main() {
	int n;
	cin >> n;
	vector<int>dp(n + 1, -1);
	cout << fibonacci(n, dp);
}
```
***T.C = O($N$) (optimized from recursive O($2^N$))***
***S.C = O($N$) + O($N$) (stack space + array)***

**Using Tabulation - Bottom up**
```cpp
	vector<int>dp(n + 1, -1);
	dp[0] = 0;
	dp[1] = 1;
	for (int i = 2; i <= n; i++) {
		dp[i] = dp[i - 1] + dp[i - 2];
	}
	cout << dp[n]  << endl;
```
***Here, space complexity is O($N$) (stack space is saved so better approach)***

**Further Optimization**
```cpp
	int prev2 = 0;
	int prev = 1;
	for (int i = 2; i <= n; i++) {
		int cur = prev + prev2;
		prev2 = prev;
		prev = cur;
	}
	cout << prev  << endl;
```
***Don't actually need to store the values since only 2 previous values are needed, thus eliminate the space => S.C = O(1)***

>[!1-D DP Problems : types of dp problems]
>- count the number of ways (count or find best way)
>- min/max

##### Climbing Stairs
>[Climbing Stairs - LeetCode](https://leetcode.com/problems/climbing-stairs/description/)

```cpp
class Solution {
public:
    int steps(int n, vector<int>&dp){
        if(n <= 2) return n;
        if(dp[n]!=-1) return dp[n];
        return dp[n] = steps(n-1,dp) + steps(n-2,dp);
    }
    int climbStairs(int n) {
        vector<int>dp(n+1,-1);
        return steps(n,dp);
    }
};
```

##### Frog Jump
[Frog Jump | Practice | GeeksforGeeks](https://www.geeksforgeeks.org/problems/geek-jump/1?utm_source=youtube&utm_medium=collab_striver_ytdescription&utm_campaign=geek-jump)

my tabulation optimized solution - 
```cpp
    int minCost(vector<int>& height) {
        int prev2 = 0;
        int prev = abs(height[0] - height[1]);
        int n = height.size();
        for(int i = 2;i<n;i++){
            int cur; 
            if(abs(height[i] - height[i-1])+prev<=abs(height[i] - height[i-2])+prev2){
                cur = abs(height[i] - height[i-1])+prev;
            }
            else{
                cur = abs(height[i] - height[i-2])+prev2;
            }
            prev2 = prev;
            prev = cur;
        }
        if(n == 1) return prev2;
        return prev;
    }
```

##### Frog Jump - 2
[Minimal Cost | Practice | GeeksforGeeks](https://www.geeksforgeeks.org/problems/minimal-cost/1?utm_source=youtube&utm_medium=collab_striver_ytdescription&utm_campaign=minimal-cost)

**using memoization -**
```cpp
class Solution {
  public:
    int f(int n, vector<int>& arr, int k, vector<int>& dp){
        if(n == 0) return 0;
        if(dp[n]!=-1){
            return dp[n];
        }
        int mincost = INT_MAX;
        for(int j = 1;j<=k;j++){
            if(n-j>=0){
                int cost = abs(arr[n] - arr[n-j]) + f(n-j,arr,k,dp);
                mincost = min(cost,mincost);
            }
        }
        return dp[n] = mincost;
    }
    int minimizeCost(int k, vector<int>& arr) {
        int n = arr.size();
        vector<int>dp(n,-1);
        return f(n-1,arr,k,dp);
    }
};
```
***T.C = O($N$) x $k$*** 
***S.C = O($N$) + O($N$) (stack space + array)***

**Tabulation** - saves stack space
```cpp
int solveUtil(int n, vector<int>& height, vector<int>& dp, int k) {
    dp[0] = 0;
    for (int i = 1; i < n; i++) {
        int mincost = INT_MAX;
        for (int j = 1; j <= k; j++) {
            if (i - j >= 0) {
                int jump = dp[i - j] + abs(height[i] - height[i - j]);
                mincost = min(jump, mincost);
            }
        }
        dp[i] = mincost;
    }
    return dp[n - 1]; 
}
```
*can further optimize space by reducing the size of dp array to k instead of n and updating one value each iteration*

##### House Robber (max sum of non-adjacents)
Link to the problem - [House Robber - LeetCode](https://leetcode.com/problems/house-robber/description/)

**concept of pick or not pick**
memoization - 
```cpp
class Solution {
public:
    int solveUtil(int n,vector<int>& nums,vector<int>& dp){
        if(n<0)return 0;
        if(n == 0){
            return nums[0];
        }
        if(dp[n] != -1) return dp[n];
        return dp[n] = max(solveUtil(n-2,nums,dp) + nums[n], solveUtil(n-1,nums,dp));
    }
    int rob(vector<int>& nums) {
        vector<int>dp(nums.size()+1,-1);
        int cost = 0;
        return solveUtil(nums.size()-1,nums,dp);
        
    }
};
```
*solveUtil(n-2 ) + nums[n] is **pick***
*solveUtil(n-1 ) is **not pick***

Tabulation - better approach (saves space)
```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n,-1);
        dp[0] = nums[0];
        for(int i = 1; i<n;i++){
            int pick = nums[i];
            if(i-2>=0) pick += dp[i-2];
            int notpick = dp[i-1];
            dp[i] = max(pick, notpick);
        }
        return dp[n-1];
    }
};
```

Tabulation with 2 variables - best approach - ideal for pick not pick problems
```cpp
    int rob(vector<int>& nums) {
        int n = nums.size();
        int prev = nums[0];
        int prev2 = 0;
        for(int i = 1; i<n;i++){
            int cur = max(prev2+nums[i], prev);
            prev2 = prev;
            prev = cur;
        }
        return prev;
    }
```

###### House Robber - 2
here nums[0] and nums[n-1] arealso adjacent, so we find max for index 0 to n-1 and another for index 1 to n and take max of it.
*Also as per the solution - never tamper the actual array given and take copy of it to do the operations*
```cpp
class Solution {
public:
    int solveUtil(vector<int>& nums){
        int n = nums.size();
        int prev = nums[0];
        int prev2 = 0;
        for(int i = 1;i<n;i++){
            int cur = max(nums[i] + prev2, prev);
            prev2 = prev;
            prev = cur;
        }
        return prev;
    }
    int rob(vector<int>& nums) {
        int n = nums.size();
        if(n == 1) return nums[0];
        vector<int> temp1(n-1);
        vector<int> temp2(n-1);
        copy(nums.begin() + 1,nums.end(),temp1.begin());
        copy(nums.begin(),nums.end() - 1,temp2.begin());
        return max(solveUtil(temp1),solveUtil(temp2));
    }
};
```

