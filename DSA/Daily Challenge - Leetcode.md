##### Find the Maximum Length of Valid Subsequence I
link - [Find the Maximum Length of Valid Subsequence I](https://leetcode.com/problems/find-the-maximum-length-of-valid-subsequence-i/)
```cpp
class Solution {
public:
    int maximumLength(vector<int>& nums) {
        // 1. take both even
        // 2. take both odd
        // 3. take one even one odd (alternating)
        // return max of all 4 cases
        int n = nums.size();
        int allEven = 0;
        int allOdd = 0;
        int alternateEvenOdd = 0; // alternate sequence ending with odd
        int alternateOddEven = 0; // alternate sequence ending with even
        for (int i = 0; i < n; i++) {
            if (nums[i] % 2 == 0) {
                allEven++;
                alternateOddEven = alternateEvenOdd + 1;
            } else if (nums[i] % 2 != 0) {
                allOdd++;
                alternateEvenOdd = alternateOddEven + 1;
            }
        }
        return max(max(allEven, allOdd), max(alternateEvenOdd, alternateOddEven));
    }
};
```
alternateEvenOdd -> has best sequence ending with odd, in a sense maintaining previous state (memory) thus DP (2 variable)

---
##### Maximum Length of Valid Subsequence II
link - [Find the Maximum Length of Valid Subsequence II](https://leetcode.com/problems/find-the-maximum-length-of-valid-subsequence-ii/)
the observation is that (s[0] + s[1])%k == (s[1] + s[2])%k == ...
since they are in addition and one term is common, s[0] % k == s[2] % k
so values at indices 0,2,4,6,... mod k would be same and indices 1,3,5,7,... would be same.
so the subsequence is guaranteed to have a pattern like [a,b,a,b,a,b..], alternating values, given a and b are remainder when mod the value with k.
with this observation, we can say that if we pick this value, then the longest subsequence would be ending with [b,a] and its previous sequence was ending with [a,b], and adding this number will increase the length by 1.
we know any number % k will be in the range [0,k-1].
`dp[i][j]` is the subsequence with current number picked value, i and j are the last 2 numbers of the sequence. 
```cpp
class Solution {
public:
    int maximumLength(vector<int>& nums, int k) {
        int n = nums.size();
        vector<vector<int>> dp(k, vector<int>(k, 0));
        int res = 0;
        for (auto num : nums) {
            int curr_rem = num % k;
            for (int prev_rem = 0; prev_rem < k; prev_rem++) {
                dp[prev_rem][curr_rem] = dp[curr_rem][prev_rem] + 1;
                res = max(res, dp[prev_rem][curr_rem]);
            }
        }
        return res;
    }
};
```

---
##### Remove Sub-Folders from the Filesystem
link - [Remove Sub-Folders from the Filesystem](https://leetcode.com/problems/remove-sub-folders-from-the-filesystem/)
```cpp
class Solution {
public:
    vector<string> removeSubfolders(vector<string>& folder) {
        sort(folder.begin(), folder.end());
        // sorting lexicographically since parent would come before the subfolder
        vector<string> res;

        // root is definitely not a subfolder
        res.push_back(folder[0]);
        string last_folder = folder[0];
        for (int i = 1; i < folder.size(); i++) {
            if (last_folder.size() < folder[i].size() && folder[i].substr(0, last_folder.size()) == last_folder && folder[i][last_folder.size()] == '/') {
                continue;
                // "/a" and "/a/b", so /a should be smaller than /a/b, /a should be part of /a/b and the edge case /a is part "/ab" but that's wrong so check if there is a "/" at the end.
            } else {
                res.push_back(folder[i]);
                last_folder = folder[i];
            }
        }
        return res;
    }
};
```

---
##### Maximum Erasure Value
link - [Maximum Erasure Value](https://leetcode.com/problems/maximum-erasure-value/)
```cpp
class Solution {
public:
    int maximumUniqueSubarray(vector<int>& nums) {
        // tried the O(n^2) subarray (even around O(N^3)) method of trying out all subarrays and checked if it already exists or not, got TLE
        // now sliding window, right window moves with flow, when we encounter a duplicate, left is moved to shrink the window till the duplicate is not part of subarray - effective time complexity O(n)
        int n = nums.size();
        int currentSum = 0;
        int maxSum = 0;
        int left = 0;
        unordered_set<int> seen;
        for (int right = 0; right < n; right++) {
            while (seen.find(nums[right]) != seen.end()) {
                currentSum -= nums[left];
                seen.erase(nums[left]);
                left++;
            }
            currentSum += nums[right];
            seen.insert(nums[right]);
            maxSum = max(maxSum, currentSum);
        }
        return maxSum;
    }
};
```

---
##### Maximum Score From Removing Substrings
link - [Maximum Score From Removing Substrings](https://leetcode.com/problems/maximum-score-from-removing-substrings/)

```cpp
class Solution {
public:
    // we greedily remove the term which gets us more points, 
    //since removing "ab" or "ba" might cause another pair being adjacent, 
    //so I was thinking do I need to run the loop again and again from
    // start once a single change happens, but turns out we can just use stack!
    int maximumGain(string s, int x, int y) {
        char higher_char;
        char lower_char;
        int higher_score;
        int lower_score;
        if (x > y) {
            higher_char = 'a';
            lower_char = 'b';
            higher_score = x;
            lower_score = y;
        } else {
            higher_char = 'b';
            lower_char = 'a';
            higher_score = y;
            lower_score = x;
        }
        stack<char> st;
        int score = 0;
        // removing the higher score first
        for (char c : s) {
            if (!st.empty() && st.top() == higher_char && c == lower_char) {
                st.pop();
                score += higher_score;
            } else st.push(c);
        }
        string afterHigh = "";
        int stSize = st.size(); // I was making this mistake
        for (int i = 0; i < stSize; i++) {
            afterHigh.append(1, st.top());
            st.pop();
        }
        reverse(afterHigh.begin(), afterHigh.end());
        stack<char> st2;
        // removing the lower score now
        for (char c : afterHigh) {
            if (!st2.empty() && st2.top() == lower_char && c == higher_char) {
                st2.pop();
                score += lower_score;
            } else st2.push(c);
        }
        return score;
    }
};
```

---
##### Smallest Subarrays With Maximum Bitwise OR
link - [Smallest Subarrays With Maximum Bitwise OR](https://leetcode.com/problems/smallest-subarrays-with-maximum-bitwise-or/)

```cpp
class Solution {
public:
    // Got TLE for an O(N^2) solution. Now the optimal approach is
    // to start from the last number, and since it's OR, the maxOr can only
    // increase or stay constant, not decrease ever. We find the
    // latest position of the bits; if that bit can be turned on earliest,
    // we get a shorter subarray.
    // Keep track of all the bits' earliest positions and we know
    // to turn that bit on, we must reach at least the location which
    // turns that bit on.
    vector<int> smallestSubarrays(vector<int>& nums) {
        int n = nums.size();
        vector<int> lastSeen(32, 0), ans(n, 1);
        for (int i = n - 1; i >= 0; i--) {
            for (int bit = 0; bit < 32; bit++) {
                if (nums[i] & (1 << bit)) lastSeen[bit] = i;
                ans[i] = max(ans[i], lastSeen[bit] - i + 1);
            }
        }
        return ans;
    }
};
```

---
