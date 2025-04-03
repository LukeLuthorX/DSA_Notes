[[Dynamic Programming]]
- Subsequence **→** contigous or non contigous, however must be in the same order
- subset can be not in order

##### Subset Sum Problem
link - [Subset Sum Problem | Practice | GeeksforGeeks](https://www.geeksforgeeks.org/problems/subset-sum-problem-1611555638/1)

**Memoization** - 
```cpp
class Solution {
  public:
    bool solveUtil(int index, int target, vector<int>& arr, vector<vector<int>>&dp){
        if(target == 0){
            return true;
        }
        if(index == 0){
            return (target == arr[0]);
        }
        if(dp[index][target]!=-1)return dp[index][target];
        
        bool notpick = solveUtil(index-1,target, arr,dp);
        bool pick = false;
        if(target>=arr[index]){
            pick = solveUtil(index-1,target-arr[index], arr,dp);
        }
        
        return dp[index][target] = (pick | notpick);
    }
    bool isSubsetSum(vector<int>& arr, int target) {
        int n = arr.size();
        vector<vector<int>>dp(n, vector<int>(target+1,-1));
        return solveUtil(n-1,target,arr,dp);
    }
};
```

T.C = O(N \* target)
S.C = O(N \* target) + O(N)

**Tabulation**

```cpp
class Solution {
  public:
    bool isSubsetSum(vector<int>& arr, int target) {
        int n = arr.size();
        vector<vector<bool>>dp(n, vector<bool>(target+1,0));
        
        for(int i = 0;i<n;i++){
            dp[i][0] = true; //base case -> if target is 0, which is 
            //applicable for all indices
        }
        dp[0][arr[0]] = true; // again base case that if index is 0, and 
        // the target value is arr[0], then its true.
        
        //now mimic recursion
        for(int i = 1;i<n;i++){
            for(int k = 1; k<=target;k++){
                bool notpick = dp[i-1][k];
                bool pick = false;
                if(k >= arr[i]){
                    pick = dp[i-1][k-arr[i]];
                }
                dp[i][k] = pick | notpick;
            }
        }
        
        return dp[n-1][target];
    }
};
```

**Further Optimization**

```cpp
class Solution {
  public:
    bool isSubsetSum(vector<int>& arr, int target) {
        int n = arr.size();
        vector<bool>prev(target+1,0), cur(target+1,0);
        prev[0] = cur[0] = true; //base case -> if target is 0
        if(arr[0]<target)prev[arr[0]] = true;
        //just the previous state is needed, so no need for 2D dp

        //now mimic recursion
        for(int i = 1;i<n;i++){
            for(int k = 1; k<=target;k++){
                bool notpick = prev[k];
                bool pick = false;
                if(k >= arr[i]){
                    pick = prev[k-arr[i]];
                }
                cur[k] = pick | notpick;
            }
            prev = cur;
        }
        
        return prev[target];
    }
};
```

---

##### Partition Equal Subset Sum
link - [Partition Equal Subset Sum - LeetCode](https://leetcode.com/problems/partition-equal-subset-sum/)

if an array can be divided into 2 parts of equal sum then each part's sum will be S/2 where S is the sum of all elements of the array. If one part is found which means other remaining ones form the other part.

```cpp
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = accumulate(nums.begin(), nums.end(), 0);
        if(sum%2 != 0){
            return false;
        }
        else{
            int target = sum/2;
            //now boils down to subset sum problem
            vector<bool>prev(target+1,0),cur(target+1,0);
            prev[0] = cur[0] = true;
            if(nums[0]<target) prev[nums[0]] = true;
            int n = nums.size();
            for(int i = 1;i<n;i++){
                for(int k = 1;k<=target;k++){
                    bool notpick = prev[k];
                    bool pick = false;
                    if(k>=nums[i]) pick = prev[k-nums[i]];
                    cur[k] = pick | notpick;
                }
                prev = cur;
            }
            return prev[target];
        }
    }
};
```

---
##### Minimum sum partition
link - [Minimum sum partition | Practice | GeeksforGeeks](https://www.geeksforgeeks.org/problems/minimum-sum-partition3317/1?itm_source=geeksforgeeks)

> [!NOTE]- Explanation
> - if you draw tabulation table for the subset sum problem -
> ![[dp_subsequence1.png]]
> 
> - you can see how all values of target are reached till the actual target value has been reached through many iterations.
> 
> >- we  can derive if every possible target between 1 and k can be achieved or not.
> 
> - now you have all possible values of subset sums of different target value (from 1 to sum) and you can get the sum of other subset by subtracting the value with total sum and take absolute difference as asked in the problem.
> - also, if you see the pattern of getting the values in tabulation, you will see they repeat, so we can instead find it for sum/2.

```cpp
I was solving this problem: Given an array arr[] containing non-negative integers, the task is to divide it into two sets set1 and set2 such that the absolute difference between their sums is minimum and find the minimum difference. made this solution, not sure where am I failing:

public: int minDifference(vector<int>& arr) { int sum = accumulate(arr.begin(), arr.end(), 0);

    //finding all possible target values exist or not till sum
    //this is naturally obtained using tabulation approach
    int n = arr.size();
    vector<vector<bool>>dp(n,vector<bool>(sum+1,0));
    
    for(int i = 0;i<n;i++){
        dp[i][0] = true;
    }
    dp[0][arr[0]] = true;
    
    for(int i = 1;i<n;i++){
        for(int k = 1;k<=sum;k++){
            bool pick = dp[i-1][k];
            bool notpick = false;
            if(k>=arr[i]) pick = dp[i-1][k-arr[i]];
            dp[i][k] = pick|notpick;
        }
    }
    
    //now I know all possible values of sum of subsets I can split to
    int mini = 1e9;
    for(int i = 0;i<=sum;i++){
        if(dp[n-1][i] == true){ //this value of target exists
            mini = min(mini, abs(i - (sum - i)));
        }
    }
    return mini;
}
```

---
##### Perfect Sum Problem
link - [Perfect Sum Problem | Practice | GeeksforGeeks](https://www.geeksforgeeks.org/problems/perfect-sum-problem5633/1?utm_source=youtube&utm_medium=collab_striver_ytdescription&utm_campaign=perfect-sum-problem)

similarly can do with tabulation. The only issue with this code is that it doesnt handle cases with 0's. Only valid for positive number solution.
```cpp
class Solution {
  public:
    int solveUtil(int index, int target, vector<int>& arr, vector<vector<int>>& dp){
        if(target == 0)return 1;
        if(index == 0){
            return (arr[index] == target);
        }
        if(dp[index][target] != -1)return dp[index][target];
        int notpick = solveUtil(index-1,target,arr,dp);
        int pick = 0;
        if(target >= arr[index]) pick = solveUtil(index-1, target-arr[index], arr, dp);
        return dp[index][target] =  pick+notpick;
    }
    
    int perfectSum(vector<int>& arr, int target) {
       int n = arr.size();
       vector<vector<int>>dp(n,vector<int>(target+1,-1));
       return solveUtil(n-1,target, arr, dp);
    }
};
```

**Solution with 0's included in the problem**

```cpp
class Solution {
  public:
    int solveUtil(int index, int target, vector<int>& arr, vector<vector<int>>& dp){
        if(index == 0){
            if(target == 0 && arr[0] == 0) return 2;
            if(target == 0 || arr[0] == target) return 1;
            return 0;
        }
        if(dp[index][target] != -1)return dp[index][target];
        int notpick = solveUtil(index-1,target,arr,dp);
        int pick = 0;
        if(target >= arr[index]) pick = solveUtil(index-1, target-arr[index], arr, dp);
        return dp[index][target] =  pick+notpick;
    }
    
    int perfectSum(vector<int>& arr, int target) {
       int n = arr.size();
       vector<vector<int>>dp(n,vector<int>(target+1,-1));
       return solveUtil(n-1,target, arr, dp);
    }
};
```


---

##### Partition with Given Difference
link - [Partitions with Given Difference | Practice | GeeksforGeeks](https://www.geeksforgeeks.org/problems/partitions-with-given-difference/1?utm_source=youtube&utm_medium=collab_striver_ytdescription&utm_campaign=partitions-with-given-difference)

```cpp
class Solution {
  public:
    int countPartitions(vector<int>& arr, int d) {
        int sum = accumulate(arr.begin(),arr.end(),0);
        int n = arr.size();
        
        if (d > sum) return 0;
        
        //sum1 + sum2 = sum
        //sum1 - sum2 = d
        //sum1 = (sum+d)/2
        
        if((sum+d) %2 != 0) return 0;
        
        int target = (sum+d)/2;
        vector<vector<int>>dp(n,vector<int>(target+1,0));
        
        //initializing dp - base case
        if(arr[0] == 0) dp[0][0] = 2; //if the first index is 0, this
        //can be used twice, one with pick and another with notpick
        else dp[0][0] = 1;
        
        if(arr[0] != 0 && arr[0] <= target) dp[0][arr[0]] = 1;
        
        for(int i = 1;i<n;i++){
            for(int k = 0;k<=target;k++){
                int notpick = dp[i-1][k];
                int pick = 0;
                if(k>=arr[i]) pick = dp[i-1][k - arr[i]];
                dp[i][k] = pick + notpick;
            }
        }
        
        return dp[n-1][target];
    }
};
```

---

##### Assign Cookies
No idea why this greedy easy problem is in this section
link - [Assign Cookies - LeetCode](https://leetcode.com/problems/assign-cookies/description/)

```cpp
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        int n = g.size();
        int m = s.size();

        sort(g.begin(),g.end());
        sort(s.begin(),s.end());

        int l = 0, r = 0; //r on greed, l on cookies
        //traverse l regardless, if that cookie can be taken by that child at r, then
        //give it to him and move to next child
        while(l<m && r<n){
            if(g[r]<=s[l]) r++;
            l++;
        }
        return r;
    }
};
```

---
##### 0-1 Knapsack & Unbounded Knapsack

**Memoization**
```cpp
class Solution {
  public:
    
    int solveUtil(int index, int capacity, vector<int> &val, vector<int> &wt, vector<vector<int>> &dp){
        //base case
        if(index == 0){
            if(capacity>=wt[0]) return val[0];
            else return 0;
        }
        if(dp[index][capacity] != -1) return dp[index][capacity];
        int notpick = solveUtil(index - 1, capacity, val, wt, dp);
        
        int pick = 0;
        if(wt[index]<=capacity) pick = val[index] + solveUtil(index-1,capacity-wt[index],val,wt,dp);
        
        return dp[index][capacity] = max(pick,notpick);
    }
    
    
    int knapSack(int capacity, vector<int> &val, vector<int> &wt) {
        int n = val.size();
        vector<vector<int>>dp(n,vector<int>(capacity+1,-1));
        return solveUtil(n-1,capacity,val,wt,dp);
    }
};
```

**Tabulation**

```cpp
class Solution {
  public:
    int knapSack(int capacity, vector<int> &val, vector<int> &wt) {
        int n = val.size();
        vector<vector<int>>dp(n,vector<int>(capacity+1,0));
        
        //base case
        for(int i = 0;i<=capacity;i++){
            if(i>=wt[0]) dp[0][i] = val[0];
            else dp[0][i] = 0;
        }
        
        //mimic recursion
        for(int i = 1;i<n;i++){
            for(int k = 0; k<=capacity;k++){
                int notpick = dp[i-1][k];
                int pick = 0;
                if(k>=wt[i]) pick = val[i] + dp[i-1][k-wt[i]];
                dp[i][k] = max(pick,notpick);
            }
        }
        return dp[n-1][capacity];
        
    }
};
```

**Unbounded Knapsack**
```cpp
class Solution {
  public:
    int solveUtil(int index, int capacity, vector<int>& val, vector<int>& wt, vector<vector<int>>&dp ){
        //base case
        if(index == 0){
            return ((int)(capacity/wt[0])) * val[0];
        }
        if(dp[index][capacity] != -1) return dp[index][capacity];
        int notpick = 0 + solveUtil(index-1,capacity,val,wt,dp);
        int pick = 0;
        if(capacity>=wt[index]) pick = val[index] + solveUtil(index,capacity-wt[index],val,wt,dp);;
        
        return dp[index][capacity] = max(pick,notpick);
        
    }
    int knapSack(vector<int>& val, vector<int>& wt, int capacity) {
        int n = val.size();
        vector<vector<int>>dp(n,vector<int>(capacity+1,-1));
        return solveUtil(n-1,capacity,val,wt,dp);
    }
};
```



---
##### Coin Change
link - [Coin Change - LeetCode](https://leetcode.com/problems/coin-change/)

why greedy fails here? due to the lack of uniformity in the testcases. => all possible ways.

**Memoization**
```cpp
class Solution {
public:
    int solveUtil(int index, int target,vector<int>& coins,vector<vector<int>>& dp ){
        if(index == 0){
            if(target % coins[0] == 0){
                return target/coins[0];
            }
            else return 1e9;
        }
        if(dp[index][target] != -1) return dp[index][target];

        int notpick = 0 + solveUtil(index-1, target, coins, dp); //simply move to previous index

        int pick = INT_MAX;
        if(target>=coins[index]) pick = 1 + solveUtil(index, target - coins[index], coins, dp);
        //since there is an infinite supply of coins, you may choose many denominations of the same
        //coin, so you dont move to previous index unless you are unable to take more, and for each
        //iteration, you get all possible cases

        return dp[index][target] = min(pick,notpick);
    }
    int coinChange(vector<int>& coins, int amount) {
        int n = coins.size();
        vector<vector<int>>dp(n, vector<int>(amount+1,-1));
        int ans = solveUtil(n-1,amount,coins,dp);
        if(ans>=1e9) return -1;
        return ans;
    }
};
```


**Tabulation**

```cpp
    int coinChange(vector<int>& coins, int amount) {
        int n = coins.size();
        vector<vector<int>>dp(n, vector<int>(amount+1,-1));
        
        //base case
        for(int t = 0;t<=amount;t++){
            if(t%coins[0] == 0) dp[0][t] = t/coins[0];
            else dp[0][t] = 1e9;
        }

        for(int i = 1;i<n;i++){
            for(int target = 0; target<=amount;target++){
                int notpick = 0 + dp[i-1][target];
                int pick = INT_MAX;
                if(target>=coins[i]) pick = 1 + dp[i][target - coins[i]];
                dp[i][target] = min(pick, notpick);
            }
        }

        int ans = dp[n-1][amount];
        if(ans>=1e9) return -1;
        return ans;
    }
```

---

##### Target Sum
link - [Target Sum - LeetCode](https://leetcode.com/problems/target-sum/description/)

> [!NOTE]+ Important 
> **when dp indices can be negative, use map**

```cpp
class Solution {
public:
    int solveUtil(int index, int target, vector<int>& nums,  map<pair<int, int>, int>& dp){
        //base case
        if(index == 0){  
            if(target - nums[0] == 0 && target + nums[0] == 0) return 2; // Both +0 and -0 are valid  
            if(target - nums[0] == 0 || target + nums[0] == 0) return 1; // Only one is valid  
            return 0;  
        } 
        if(dp.find({index, target}) != dp.end()) return dp[{index, target}];
        int pickNegative = solveUtil(index-1,target+nums[index],nums,dp);
        int pickPositive = solveUtil(index-1,target-nums[index],nums,dp);

        return dp[{index, target}] = (pickNegative + pickPositive);
    }
    
    int findTargetSumWays(vector<int>& nums, int target) {
        int n = nums.size();
        map<pair<int, int>, int> dp; // Use map to handle negative indices
        return solveUtil(n-1,target,nums,dp);
    }
};
```

>Another approach what Striver told was that you can see this problem as count subset partition with target problem, one subset is taken as positive, another as negative and you have to find which all combination's sum will result in the target.  (S1 - S2 = d)

---

##### Coin Change II
link - [Coin Change II - LeetCode](https://leetcode.com/problems/coin-change-ii/)

```cpp
class Solution {
public:
    int solveUtil(int index,int amount,vector<int>& coins, vector<vector<int>>& dp ){
        //base case
        if(index == 0){
            if(amount % coins[0] == 0){
                return 1;
            }
            else return 0;
        }
        if(dp[index][amount] != -1) return dp[index][amount];
        int notpick = solveUtil(index-1,amount,coins,dp);
        int pick = 0;
        if(amount>=coins[index]) pick = solveUtil(index, amount - coins[index], coins, dp);
        return dp[index][amount] = (pick + notpick);
    }
    int change(int amount, vector<int>& coins) {
        int coinSize = coins.size();
        vector<vector<int>>dp(coinSize, vector<int>(amount+1,-1));
        return solveUtil(coinSize - 1, amount,  coins, dp);
    }
};
```

---
##### Rod Length
link - [Rod Cutting | Practice | GeeksforGeeks](https://www.geeksforgeeks.org/problems/rod-cutting0840/1)

**Memoization**

```cpp
class Solution {
  public:
    int solveUtil(int index, int N, vector<int> &price, vector<vector<int>>&dp){
        //think of this problem like this -> if n = 5, you can have rods
        // of length 1,2,3,4,5.
        //Now from that array, you have to collect rod lengths to make N
        //while maximizing the price => similar to knapsack
        //infinite supply of rods - duplicate allowed if possible
        
        //base case
        if(index == 0){
            return N*price[0];
        }
        
        if(dp[index][N] != -1) return dp[index][N];
        
        int notPick = 0 + solveUtil(index - 1, N, price,dp);
        int pick = INT_MIN;
        int rodlength = index + 1;
        if(rodlength <= N) pick = price[index] + solveUtil(index, N - rodlength, price,dp);
        return dp[index][N] = max(pick, notPick);
        
    }
    
    int cutRod(vector<int> &price) {
        int n = price.size();
        vector<vector<int>>dp(n,vector<int>(n+1, -1));
        return solveUtil(n-1, n , price,dp);
    }
};
```

