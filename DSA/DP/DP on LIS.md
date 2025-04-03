[[Dynamic Programming]]

##### Longest Common Subsequence
link - [Longest Increasing Subsequence - LeetCode](https://leetcode.com/problems/longest-increasing-subsequence/description/)

coordinate shift in dp to adjust for negative value.

**Memoization**
```cpp
class Solution {
public:
    int solveUtil(int ind, int prev_ind,int n, vector<int>& nums, vector<vector<int>>& dp){
        if(ind == n){
            return 0;
        }
        if(dp[ind][prev_ind+1] != -1) return dp[ind][prev_ind+1];
        int notpick = 0 + solveUtil(ind+1, prev_ind,n, nums, dp);
        int pick = 0;
        if(prev_ind == -1 || nums[ind]>nums[prev_ind]) pick = 1 + solveUtil(ind+1, ind,n, nums, dp);
        return dp[ind][prev_ind+1] = max(pick,notpick);
    }
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>>dp(n,vector<int>(n+1,-1));
        return solveUtil(0,-1,n,nums,dp);
    }
};
```

**Tabulation**
```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>>dp(n+1,vector<int>(n+1,0));
        //when we are comparing indices, we use the original prev_ind,
        //only when dp array is used, coordinate shifted prev_ind is used.
        for(int ind = n-1; ind>=0; ind--){
            for(int prev_ind = ind - 1; prev_ind >= -1; prev_ind--){
                int notpick = dp[ind+1][prev_ind+1];
                int pick = 0;
                if(prev_ind == -1 || nums[ind] > nums[prev_ind]){
                    pick = 1 + dp[ind+1][ind+1];
                }
                dp[ind][prev_ind+1] = max(pick, notpick);
            }
        }
        return dp[0][0]; // should be 0,-1 but since coordinate shift -1 + 1 = 0
    }
};
```

**Further Space Optimization**
```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int>after(n+1,0), cur(n+1,0);
        //when we are comparing indices, we use the original prev_ind,
        //only when dp array is used, coordinate shifted prev_ind is used.
        for(int ind = n-1; ind>=0; ind--){
            for(int prev_ind = ind - 1; prev_ind >= -1; prev_ind--){
                int notpick = after[prev_ind+1];
                int pick = 0;
                if(prev_ind == -1 || nums[ind] > nums[prev_ind]){
                    pick = 1 + after[ind+1];
                }
                cur[prev_ind+1] = max(pick, notpick);
            }
            after = cur;
        }
        return after[0]; // should be 0,-1 but since coordinate shift -1 + 1 = 0
    }
};
```


**The Conventional OG Tabulation Approach for this Problem**
```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int>dp(n,1);
        //the minimum ans is obviously 1 so initializing dp with it

        int maxi = 1;
        //the dp array stores the maximum lis till that index, for that all previous
        //elements are checked with index i if taking that increases size or not (if you
        //pick that element up, its previous lis also becomes part of i index element)
        //the answer is maximum of dp array
        for(int i = 0;i<n;i++){
            for(int prev = 0;prev<i;prev++){
                if(nums[prev] < nums[i]){
                    dp[i] = max(dp[i], 1 + dp[prev]);
                }
            }
            maxi = max(maxi, dp[i]);
        }
        return maxi;
    }
};
```

**Binary Search NlogN solution**
```cpp
class Solution {
  public:
    int lis(vector<int>& arr) {
        int n = arr.size();
        vector<int>temp;
//the temp array doesnt store LIS, its maintained just for length
//we keep replacing values to save the space for creating multiple
//arrays of Increasing sequence possibilities
        temp.push_back(arr[0]);
        int len = 1;
        for(int i = 1;i<n;i++){
            if(arr[i]>temp.back()){
                temp.push_back(arr[i]);
                len++;
            }
            else{
            //lowerbound returns an iterator pointing to the first element 
            //that is not less than a specified value in a sorted range.
                auto ind = lower_bound(temp.begin(), temp.end(), arr[i]) - temp.begin();    
                temp[ind] = arr[i]; 
            }
        }
        return len;
    }
};
```

> [!NOTE]- ##### Explanation on this by Gemini
> **Intuitive Approach with Binary Search**
> 
> 1. **`temp` Array:** The core idea is to maintain a `temp` array. This array does **not** store the actual LIS, but it helps us determine the length of the LIS. `temp` will store the smallest ending value of all increasing subsequences of a certain length.
>     
> 2. **Iterating through the input array:** We iterate through the input array (`arr`) element by element.
>     
> 3. **Case 1: Extending the LIS:** If the current element `arr[i]` is greater than the last element of the `temp` array, it means we can extend the current longest increasing subsequence. We simply append `arr[i]` to `temp`. This increases the length of the LIS we've found so far.
>     
> 4. **Case 2: Optimizing for a shorter ending value:** If the current element `arr[i]` is **not** greater than the last element of `temp`, it means `arr[i]` cannot directly extend the current LIS. However, `arr[i]` might be able to replace an element in `temp` to potentially create an LIS with the same length but with a smaller ending value. A smaller ending value is beneficial because it gives us a better chance to extend the subsequence later with larger elements from the input array.
>     
>     - **Binary Search (Lower Bound):** In this case, we use binary search (specifically, `lower_bound`) on the `temp` array to find the smallest element in `temp` that is greater than or equal to `arr[i]`.
>     - **Replacement:** We replace that element in `temp` with `arr[i]`. This is the crucial step. We are not necessarily increasing the length of our LIS at this point, but we are potentially making it "better" by having a smaller ending value for a subsequence of the same length.
> 5. **Length of LIS:** After iterating through the entire input array, the length of the `temp` array will give you the length of the Longest Increasing Subsequence. Remember, `temp` itself is not necessarily the LIS, but its length is.
>     
> 
> **Example Walkthrough (`arr = [10, 9, 2, 5, 3, 7, 101, 18]`)**
> ![[DPonLIS_Gemini.png|600]]

---
##### Printing LIS
link - [Print Longest Increasing Subsequence | Practice | GeeksforGeeks](https://www.geeksforgeeks.org/problems/printing-longest-increasing-subsequence/1?utm_source=youtube&utm_medium=collab_striver_ytdescription&utm_campaign=printing-longest-increasing-subsequence)

![[DPonLIS.png|300]]
![[DPonLISv2.png|300]]
```cpp
class Solution {
  public:
    vector<int> longestIncreasingSubsequence(int n, vector<int>& arr) {
        vector<int>dp(n,1), hash(n);
        for (int i = 0; i < n; ++i) { 
	        hash[i] = i; 
        }
        int maxi = 1;
        int lastIndex = 0;
        for(int i = 0;i<n;i++){
            for(int prev = 0;prev<i;prev++){
                if(arr[prev] < arr[i] && dp[i]<1+dp[prev]){
                    dp[i] = 1 + dp[prev];
                    hash[i] = prev;
                }
            }
            if(dp[i] > maxi){
                maxi = dp[i];
                lastIndex = i;
            }
        }
        //backtracking
        vector<int>temp;
        temp.push_back(arr[lastIndex]);
        while(hash[lastIndex] != lastIndex){
            lastIndex = hash[lastIndex];
            temp.push_back(arr[lastIndex]);
        }
        reverse(temp.begin(),temp.end());
        return temp;
    }
};
```

---
##### Largest Divisible Subset
link - [Largest Divisible Subset - LeetCode](https://leetcode.com/problems/largest-divisible-subset/description/)
```cpp
class Solution {
public:
    vector<int> largestDivisibleSubset(vector<int>& nums) {
        // Since we have to find subset, we can sort the array
        // You pick an element if it is divisible by the last element,
        // and if it is, then it is also divisible by the previous all elements
        sort(nums.begin(), nums.end());
        int n = nums.size();
        vector<int> dp(n, 1), hash(n);

        // Initialize hash with the indices themselves
        for (int i = 0; i < n; ++i) {
            hash[i] = i;
        }

        int maxi = 1, lastIndex = 0;
        for (int ind = 0; ind < n; ind++) {
            for (int prev = 0; prev < ind; prev++) {
                if (dp[ind] < 1 + dp[prev] && nums[ind] % nums[prev] == 0) {
                    dp[ind] = 1 + dp[prev];
                    hash[ind] = prev;
                }
            }
            if (dp[ind] > maxi) {
                maxi = dp[ind];
                lastIndex = ind;
            }
        }

        vector<int> temp;
        temp.push_back(nums[lastIndex]);
        while (hash[lastIndex] != lastIndex) {
            lastIndex = hash[lastIndex];
            temp.push_back(nums[lastIndex]);
        }
        reverse(temp.begin(), temp.end());
        return temp;
    }
};

```

> [!NOTE]- ##### Step-by-Step Explanation:
> 
> 1. **Sorting the Array**:
>     
>     - The array `nums` is sorted in ascending order. This helps in ensuring that if `nums[j]` is divisible by `nums[i]` (where `i < j`), then `nums[j]` is also divisible by all elements in the subset ending at `nums[i]`.
> 2. **Initialization**:
>     
>     - `dp` is a vector where `dp[i]` represents the size of the largest divisible subset ending with `nums[i]`.
>     - `hash` is a vector where `hash[i]` stores the index of the previous element in the subset ending with `nums[i]`.
>     - `maxi` keeps track of the maximum size of any divisible subset found so far.
>     - `lastIndex` stores the index of the last element of the largest divisible subset.
> 3. **Dynamic Programming (DP) Table Construction**:
>     
>     - For each element `nums[ind]`, we check all previous elements `nums[prev]` (where `prev < ind`).
>     - If `nums[ind]` is divisible by `nums[prev]` and including `nums[ind]` in the subset ending at `nums[prev]` results in a larger subset, we update `dp[ind]` and set `hash[ind]` to `prev`.
> 4. **Finding the Largest Subset**:
>     
>     - After constructing the DP table, we find the index `lastIndex` of the element that ends the largest divisible subset.
> 5. **Tracing Back the Subset**:
>     
>     - Starting from `lastIndex`, we trace back using the `hash` vector to construct the largest divisible subset.
>     - We continue this process until we reach the start of the subset (when `hash[lastIndex] == lastIndex`).
> 6. **Reversing the Subset**:
>     
>     - Since we constructed the subset by tracing back, it is in reverse order. We reverse it to get the correct order.
> 
> ### Example Walkthrough:
> 
> Let's walk through the example `nums = [3, 4, 16, 8]`:
> 
> 1. **Sorting**:
>     
>     - Sorted array: `[3, 4, 8, 16]`
> 2. **Initialization**:
>     
>     - `dp = [1, 1, 1, 1]`
>     - `hash = [0, 1, 2, 3]`
>     - `maxi = 1`
>     - `lastIndex = 0`
> 3. **DP Table Construction**:
>     
>     - For `ind = 1` (`nums[1] = 4`):
>         - `prev = 0`: `4 % 3 != 0`, so skip.
>     - For `ind = 2` (`nums[2] = 8`):
>         - `prev = 0`: `8 % 3 != 0`, so skip.
>         - `prev = 1`: `8 % 4 == 0`, so `dp[2] = 2`, `hash[2] = 1`.
>     - For `ind = 3` (`nums[3] = 16`):
>         - `prev = 0`: `16 % 3 != 0`, so skip.
>         - `prev = 1`: `16 % 4 == 0`, so `dp[3] = 2`, `hash[3] = 1`.
>         - `prev = 2`: `16 % 8 == 0`, so `dp[3] = 3`, `hash[3] = 2`.
> 4. **Finding the Largest Subset**:
>     
>     - `maxi = 3`
>     - `lastIndex = 3`
> 5. **Tracing Back the Subset**:
>     
>     - Start from `lastIndex = 3` (`nums[3] = 16`).
>     - `hash[3] = 2` (`nums[2] = 8`).
>     - `hash[2] = 1` (`nums[1] = 4`).
>     - `hash[1] = 1` (stop condition).
> 6. **Reversing the Subset**:
>     
>     - Subset: `[16, 8, 4]`
>     - Reversed subset: `[4, 8, 16]`
> 
> ### Final Output:
> 
> - The largest divisible subset is `[4, 8, 16]`.

---
##### Longest String Chain
link - [Longest String Chain - LeetCode](https://leetcode.com/problems/longest-string-chain/description/)
```cpp
class Solution {
public:
    bool checkPossible(string &s1, string &s2){
        if(s1.size() != s2.size() + 1) return false;
        int first = 0, second = 0;
        //second < s2.size() to deal with the case bcda and bcd
        while(first < s1.size()){
            if(second < s2.size() && s1[first] == s2[second]){
                first++;
                second++;
            }
            else{
                first++;
            }
        }
        if(first == s1.size() && second == s2.size()) return true;
        return false;
    } 
    static bool comp(const string &s1, const string &s2) {
        return s1.size() < s2.size();
    }
    int longestStrChain(vector<string>& words) {
        int n = words.size();
        vector<int>dp(n,1);
        //this originally works for subsequences, however you can even pick words
        //in different order, so to convert this to work for subset, just sort in the 
        //order of size
        sort(words.begin(),words.end(),comp);
        int maxi = 1;
        for(int ind = 0;ind<n;ind++){
            for(int prev = 0;prev<ind;prev++){
                if(checkPossible(words[ind],words[prev]) && dp[ind]<1+dp[prev]){
                    dp[ind] = 1 + dp[prev];
                }
            }
            maxi = max(maxi,dp[ind]);
        }
    return maxi;
    }
};
```

---
##### Longest Bitonic Subsequence
link - [Longest Bitonic subsequence | Practice | GeeksforGeeks](https://www.geeksforgeeks.org/problems/longest-bitonic-subsequence0824/1)

strictly increasing then decreasing, however LIS is also a bitonic sequence

```cpp
class Solution {
  public:
    int LongestBitonicSequence(int n, vector<int> &nums) {
        //Longest Increasing Subsequence and the same from reverse would
        //give Longest Decreasing Subsequence, for an element, its taken
        //for both LIS and LDS, so we subtract 1
        vector<int>dp(n,1);
        for(int ind = 0;ind<n;ind++){
            for(int prev = 0;prev<ind;prev++){
                if(nums[ind] > nums[prev] && dp[ind] < 1 + dp[prev]){
                    dp[ind] = 1 + dp[prev];
                }
            }
        }
        
        vector<int>dp2(n,1);
        for(int ind = n-1;ind>=0;ind--){
            for(int prev = n-1;prev>ind;prev--){
                if(nums[ind] > nums[prev] && dp2[ind] < 1 + dp2[prev]){
                    dp2[ind] = 1 + dp2[prev];
                }
            }

        }
        
        int maxi = 0;
        for (int i = 0; i < n; i++)
        {
            // it may happen that we get sorted array either in increasing / decreasing order, hence in that case if we don't add this condition, we might get issue
            // so in this case, either dp1[i] = large value and dp[i] would be 1 OR dp2[i] = large value and dp1[i] = 1
            // so a biotonic sequence - must have increasing an increasing sequence from left as well as right, hence it's necessary that dp1[i] > 1 && dp2[i] > 1
            // dp1[i] > 1 && dp2[i] > 1, ensures both of the LIS are valid
            if(dp[i] > 1 && dp2[i] > 1){
               maxi = max(maxi,dp[i]+dp2[i]-1);
               //normally just this works, but incase of a complete LIS (sorted array)
               //we need to not subtract.
            }
        }
        
        return maxi;
    }
};
```

##### Number of Longest  Increasing Subsequences
link - [Number of Longest Increasing Subsequence - LeetCode](https://leetcode.com/problems/number-of-longest-increasing-subsequence/description/)
![[DPonLISLast.png| 400]]

```cpp
class Solution {
public:
    int findNumberOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int>dp(n,1), cnt(n,1);
        int maxi = 1;
        for(int ind = 0;ind<n;ind++){
            for(int prev = 0;prev<ind;prev++){
                if(nums[prev] < nums[ind] && dp[prev] + 1 > dp[ind]){
                    dp[ind] = 1 + dp[prev];
                    //inherit the count
                    cnt[ind] = cnt[prev];
                }
                else if (nums[prev] < nums[ind] && dp[prev] + 1 == dp[ind]){
                    //increase the count
                    cnt[ind] += cnt[prev];
                }
            }
            maxi = max(maxi, dp[ind]);
        }
        int nos = 0;
        for(int i = 0;i<n;i++){
            if(dp[i] == maxi) nos+=cnt[i];
        }
        return nos;
    }
};
```
