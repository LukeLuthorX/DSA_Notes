[[Dynamic Programming]]
##### Best Time to Buy and Sell Stocks
link - [Best Time to Buy and Sell Stock - LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
            int mini=INT_MAX;
            int profit=0;
            //if selling on the ith day, buying must happen in 1 - i-1 day with minimum price
            for(int i=0;i<prices.size();i++){
                mini=min(mini,prices[i]);
                profit=max(profit,prices[i]-mini);
            }
            //here remembering the minimum value is the essence of DP, although this problem
            //is just constructive algorithm
            return profit;
        }
};
```


##### Best Time to Buy and Sell Stocks II
link - [Best Time to Buy and Sell Stock II - LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)

**Memoization**
```cpp
class Solution {
public:
    int solveUtil(int ind, int buy,int n, vector<int>& prices, vector<vector<int>>& dp){
        if(ind == n){
            return 0;
            //if all days are passed, neither can he sell nor buy
        }

        if(dp[ind][buy] != -1) return dp[ind][buy];

        int profit = 0;
        if(buy){
        //we have a state buy, if we buy a stock, the amount is deducted so -ve
        //and we move to next day with buy state 0 meaning cant buy unless sold

        //one case, you bought on the day and other case you didn't
            profit = max(-prices[ind] + solveUtil(ind+1,0,n,prices,dp), 0 + solveUtil(ind+1,1,n,prices,dp));
        }
        else{
            //you sold on the day, or you didn't
            profit = max(prices[ind] + solveUtil(ind+1,1,n,prices,dp), 0 + solveUtil(ind+1,0,n,prices,dp));
        }
        return dp[ind][buy] = profit;
    }
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        vector<vector<int>>dp(n,vector<int>(2,-1));
        return solveUtil(0,1,n,prices,dp);
    }
};
```

**Tabulation**

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        vector<vector<int>>dp(n+1,vector<int>(2,0));
        
        dp[n][0] = dp[n][1] = 0;

        for(int ind = n-1;ind>=0;ind--){
            for(int buy = 0; buy<=1; buy++){
                int profit = 0;
                if(buy){
                    profit = max(-prices[ind] + dp[ind+1][0] , dp[ind+1][1]);
                }
                else{
                    profit = max(prices[ind] + dp[ind+1][1], dp[ind+1][0]);
                }
                dp[ind][buy] = profit;  
            }   
        }
        return dp[0][1];
    }
};
```

**Further Optimization**

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        vector<int>ahead(2,0), cur(2,0);
        
        ahead[0] = ahead[1] = 0;

        for(int ind = n-1;ind>=0;ind--){
            for(int buy = 0; buy<=1; buy++){
                int profit = 0;
                if(buy){
                    profit = max(-prices[ind] + ahead[0] , ahead[1]);
                }
                else{
                    profit = max(prices[ind] + ahead[1], ahead[0]);
                }
                cur[buy] = profit;  
            }   
            ahead = cur;
        }
        return ahead[1];
    }
};
```

---
##### Best Time to Buy and Sell Stock III
link - [Best Time to Buy and Sell Stock III - LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/)

**Memoization**
```cpp
class Solution {
public:
    int solveUtil(int ind, int buy, int cap,int n, vector<int>& prices,vector<vector<vector<int>>>&dp){
        //this problem has only limitation on the dp on stock part-2 that it can
        //be done only twice, for that a state capacity is used
        if(cap == 0) return 0; 
        if(ind == n){
            return 0;
        }
        if(dp[ind][buy][cap]!=-1) return dp[ind][buy][cap];
        int profit = 0;
        if(buy){
            //buying doesnt mean a transaction is complete, so cap remains as it is, only
            //selling means transaction completed then cap is decremented
            profit = max(-prices[ind] + solveUtil(ind+1,0,cap,n,prices,dp), 0 + solveUtil(ind+1,1,cap,n,prices,dp));

        }
        else{
            profit = max(prices[ind] + solveUtil(ind+1,1,cap-1,n,prices,dp),0 + solveUtil(ind+1,0,cap,n,prices,dp));
        }
        return dp[ind][buy][cap] = profit;
    }
    int maxProfit(vector<int>& prices) {
       int n = prices.size();
       vector<vector<vector<int>>>dp(n,vector<vector<int>>(2,vector<int>(3,-1)));
       return solveUtil(0,1,2,n,prices,dp); 
    }
};
```

**Tabulation**
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
       int n = prices.size();
       vector<vector<vector<int>>>dp(n+1,vector<vector<int>>(2,vector<int>(3,0)));
       
       //base case, ind and buy can be anything but if cap = 0, its 0
       for(int ind = 0;ind<=n;ind++){
        for(int buy = 0;buy<=1;buy++){
            dp[ind][buy][0] = 0;
        }
       }
        //base case -> ind = n, buy and case anything, its 0
       for(int buy = 0;buy<=1;buy++){
        for(int cap = 0;cap<=2;cap++){
            dp[n][buy][cap] = 0;
        }
       }
        //skip cap = 0 in the mimicing of recursion in the loop
        for(int ind = n-1;ind>=0;ind--){
            for(int buy = 0; buy<=1;buy++){
                for(int cap = 1;cap<=2;cap++){
                    int profit = 0;
                    if(buy){
                        profit = max(-prices[ind] + dp[ind+1][0][cap],  dp[ind+1][1][cap]);
                    }
                    else{
                        profit = max(prices[ind] + dp[ind+1][1][cap-1], dp[ind+1][0][cap]);
                    }
                    dp[ind][buy][cap] = profit;
                }
            }
        }
        return dp[0][1][2];
    }
};
```

**Further Space Optimization**
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
       int n = prices.size();

       vector<vector<int>>after(2,vector<int>(3,0));
       vector<vector<int>>cur(2,vector<int>(3,0));
       
       //base case, ind and buy can be anything but if cap = 0, its 0
       for(int ind = 0;ind<=n;ind++){
        for(int buy = 0;buy<=1;buy++){
            after[buy][0] = 0;
        }
       }
        //base case -> ind = n, buy and case anything, its 0
       for(int buy = 0;buy<=1;buy++){
        for(int cap = 0;cap<=2;cap++){
            after[buy][cap] = 0;
        }
       }
        //skip cap = 0 in the mimicing of recursion in the loop
        for(int ind = n-1;ind>=0;ind--){
            for(int buy = 0; buy<=1;buy++){
                for(int cap = 1;cap<=2;cap++){
                    int profit = 0;
                    if(buy){
                        profit = max(-prices[ind] + after[0][cap],  after[1][cap]);
                    }
                    else{
                        profit = max(prices[ind] + after[1][cap-1], after[0][cap]);
                    }
                    cur[buy][cap] = profit;
                }
            }
            after = cur;
        }
        return after[1][2];
    }
};
```

---
##### Best Time to Buy and Sell Stock IV
link - [Best Time to Buy and Sell Stock IV - LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/description/)

```cpp
class Solution {
public:
    int solveUtil(int ind, int buy, int cap,int n, int k, vector<int>& prices,vector<vector<vector<int>>>&dp){
        if(cap == 0) return 0; 
        if(ind == n){
            return 0;
        }
        if(dp[ind][buy][cap]!=-1) return dp[ind][buy][cap];
        int profit = 0;
        if(buy){
            profit = max(-prices[ind] + solveUtil(ind+1,0,cap,n,k,prices,dp), 0 + solveUtil(ind+1,1,cap,n,k,prices,dp));
        }
        else{
            profit = max(prices[ind] + solveUtil(ind+1,1,cap-1,n,k,prices,dp),0 + solveUtil(ind+1,0,cap,n,k,prices,dp));
        }
        return dp[ind][buy][cap] = profit;
    }
    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
        vector<vector<vector<int>>>dp(n,vector<vector<int>>(2,vector<int>(k+1,-1)));
        return solveUtil(0,1,k,n,k,prices,dp); 
    }
};
```

>another way is to take a single array for buy sell and cap, here if transaction is even its sell, and if odd its buy and that number is capacity. and here transaction is equal to 2 x k

----
##### Best Time to Buy and Sell Stock with Cooldown
link - [Best Time to Buy and Sell Stock with Cooldown - LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/)

```cpp
class Solution {
public:
    int solveUtil(int ind, int buy,int n, vector<int>& prices, vector<vector<int>>&dp){
        if(ind >= n) return 0;
        int profit = 0;
        if(dp[ind][buy] != -1) return dp[ind][buy];
        if(buy){
            profit = max(-prices[ind] + solveUtil(ind+1,0,n,prices,dp), solveUtil(ind+1,1,n,prices,dp));
        }
        else{
            profit = max(prices[ind] + solveUtil(ind+2,1,n,prices,dp), solveUtil(ind+1,0,n,prices,dp));
        }
        return dp[ind][buy] = profit;
    }
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        vector<vector<int>>dp(n,vector<int>(2,-1));
        return solveUtil(0,1,n,prices,dp);
    }
};
```

---
##### Best Time to Buy and Sell Stock with Transaction Fee
link - [Best Time to Buy and Sell Stock with Transaction Fee - LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/)

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int n = prices.size();
        vector<int>after(2,0), cur(2,0);

        after[0] = after[1] = 0;

        for(int ind = n-1;ind>=0;ind--){
            for(int buy = 0; buy<=1; buy++){
                int profit = 0;
                if(buy) profit = max(-prices[ind] + after[0], after[1]);
                else profit = max(prices[ind] + after[1] - fee, after[0]);
                cur[buy] = profit;
            }
            after = cur;
        }
        return after[1];
    }
};
```
