[[Dynamic Programming]]
##### Longest Common Subsequence
link -  [Longest Common Subsequence - LeetCode](https://leetcode.com/problems/longest-common-subsequence/description/)

**Memoization**

```cpp
class Solution {
public:
    int solveUtil(int ind1, int ind2, string &text1, string &text2, vector<vector<int>> &dp){
        if(ind1<0 || ind2<0) return 0;

        if(dp[ind1][ind2]!=-1) return dp[ind1][ind2];
        //if the character matches, move both the pointers behind and 
        //common sequence length increases by 1
        if(text1[ind1] == text2[ind2]) return 1 + solveUtil(ind1-1,ind2-1,text1,text2,dp);

        //if not common character, there are 2 possible cases, move pointer to the first string
        //back and keep the one in second string stationary and vice versa
        //and we take the maximum of both.
        return dp[ind1][ind2] = 0 + max(solveUtil(ind1-1,ind2,text1,text2,dp), solveUtil(ind1,ind2-1,text1, text2,dp));
    }

    int longestCommonSubsequence(string text1, string text2) {
        int n = text1.size();
        int m = text2.size();
        vector<vector<int>>dp(n,vector<int>(m,-1));
        return solveUtil(n-1,m-1,text1,text2,dp);
    }
};
```

T.C = O(N x M)
S.C = O(N x M) + O(N + M)  (auxilliary stack space)

**Tabulation**
```cpp
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int n = text1.size();
        int m = text2.size();
        vector<vector<int>>dp(n+1,vector<int>(m+1,-1));

        //since the base case if index reaching -1, and we cant do this with dp array
        //we shift the index by 1 such that now the base case is 0

        for(int i = 0;i<=n;i++) dp[i][0] = 0;
        for(int j = 0;j<=m;j++) dp[0][j] = 0;

        for(int i = 1; i<=n; i++){
            for(int j = 1; j<=m; j++){
                if(text1[i-1] == text2[j-1]) dp[i][j] = 1 + dp[i-1][j-1];
                else dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
            }
        }
        return dp[n][m];
    }
};
```

**Further Optimization**

```cpp
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int n = text1.size();
        int m = text2.size();
        vector<int>prev(m+1, 0), cur(m+1, 0);

        for(int j = 0;j<=m;j++) prev[j] = 0;

        for(int i = 1; i<=n; i++){
            for(int j = 1; j<=m; j++){
                if(text1[i-1] == text2[j-1]) cur[j] = 1 + prev[j-1];
                else cur[j] = max(prev[j], cur[j-1]);
            }
            prev = cur;
        }
        return prev[m];
    }
};
```

---

##### Print LCS
![[DP_string1.png]]
```cpp
class Solution {
public:
    string longestCommonSubsequence(string text1, string text2) {
        int n = text1.size();
        int m = text2.size();
        vector<vector<int>>dp(n+1,vector<int>(m+1,-1));

        for(int i = 0;i<=n;i++) dp[i][0] = 0;
        for(int j = 0;j<=m;j++) dp[0][j] = 0;

        for(int i = 1; i<=n; i++){
            for(int j = 1; j<=m; j++){
                if(text1[i-1] == text2[j-1]) dp[i][j] = 1 + dp[i-1][j-1];
                else dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
            }
        }

        int length = dp[n][m];
        string ans = "";
        for(int i = 0; i<length;i++) ans+='$'; // take a dummy array of size of lcs

        int index = length-1;
        int i = n, j = m;

        while(i>0 && j>0){
            //if you observe the tabulation dp table, you'll see that when the elements
            //are equal, in the recursion it always go to i-1 and j-1 (both indices are decreased)
            //Now, if both char are equal we take that char in our ans
            if(text1[i-1] == text2[j-1]) {
                ans[index] = text1[i-1];
                index--;
                i--;
                j--;
            }
            //now if both char are not equal, there are 2 cases from where the longest subseq.
            //would have come. Looking at the table or recursion, you know it.
            else if(dp[i-1][j] > dp[i][j-1]){
                i--;
            }
            else{
                j--;
            }
        }
        return ans;
    }
};
```

>if there are more than one lcs which have to be printed, then only approach is brute force.

---
##### Longest Common Substring
link - [Longest Common Substring | Practice | GeeksforGeeks](https://www.geeksforgeeks.org/problems/longest-common-substring1452/1)
![[dp-substring2.png|300]]

>small modification from the previous lcs code. Now if chars match, add 1 and go to previous state to get the value, but if they dont, make it zero.

```cpp
class Solution {
  public:
    int longestCommonSubstr(string& s1, string& s2) {
        int n = s1.size();
        int m = s2.size();
        vector<vector<int>>dp(n+1,vector<int>(m+1,0));
        for(int i = 0;i<=n;i++) dp[i][0] = 0;
        for(int j = 0;j<=m;j++) dp[0][j] = 0;
        
        int ans = 0;
        for(int i = 1; i<=n;i++){
            for(int j = 1; j<=m; j++){
                if(s1[i-1] == s2[j-1]){
                    dp[i][j] = 1 + dp[i-1][j-1];
                    ans = max(ans, dp[i][j]);
                }
                else{
                    dp[i][j] = 0;
                }
            }
        }
        return ans;
        
    }
};
```

**Space Optimization**

```cpp
class Solution {
  public:
    int longestCommonSubstr(string& s1, string& s2) {
        int n = s1.size();
        int m = s2.size();
        vector<int>prev(m+1,0), cur(m+1,0);

        for(int j = 0;j<=m;j++) prev[j] = 0;
        
        int ans = 0;
        for(int i = 1; i<=n;i++){
            for(int j = 1; j<=m; j++){
                if(s1[i-1] == s2[j-1]){
                    cur[j] = 1 + prev[j-1];
                    ans = max(ans, cur[j]);
                }
                else{
                    cur[j] = 0;
                }
            }
            prev = cur;
        }
        return ans;
        
    }
};
```

---

##### Longest Palindromic Subsequence
link - [Longest Palindromic Subsequence - LeetCode](https://leetcode.com/problems/longest-palindromic-subsequence/)

```cpp
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int n = text1.size();
        int m = text2.size();
        vector<vector<int>>dp(n+1,vector<int>(m+1,-1));

        for(int i = 0;i<=n;i++) dp[i][0] = 0;
        for(int j = 0;j<=m;j++) dp[0][j] = 0;

        for(int i = 1; i<=n; i++){
            for(int j = 1; j<=m; j++){
                if(text1[i-1] == text2[j-1]) dp[i][j] = 1 + dp[i-1][j-1];
                else dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
            }
        }
        return dp[n][m];
    }
    int longestPalindromeSubseq(string s) {
        //if you observe, just take reverse of the original string, and see that
        //the longest common subsequence in them is a palindrome
        //so just find lcs for s and reversed s.
        string s2 = s;
        reverse(s2.begin(),s2.end());
        return longestCommonSubsequence(s,s2);
    }
};
```

---

##### Minimum Insertions to Make Palindrome
link - [Minimum Insertion Steps to Make a String Palindrome - LeetCode](https://leetcode.com/problems/minimum-insertion-steps-to-make-a-string-palindrome/description/)

>if we observe, just keeping the longest palindrome subsequence intact, and then the remaining characters can be reversed and appened at right and left of the intact sequence.

![[dp-strings3.png|300]]

```cpp
class Solution {
public:
    int minInsertions(string s) {
        int n = s.size();
        string s2 = s;
        reverse(s2.begin(),s2.end());
        vector<vector<int>>dp(n+1,vector<int>(n+1,0));

        for(int i = 0;i<=n;i++) dp[0][i] = 0;
        for(int j = 0;j<=n;j++) dp[j][0] = 0;

        for(int i = 1;i<=n;i++){
            for(int j = 1;j<=n;j++){
                if(s[i-1] == s2[j-1]) dp[i][j] = 1 + dp[i-1][j-1];
                else dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
            }
        }
        return n - dp[n][n];
    }
};
```

---
##### Delete Operation for Two Strings
link -[Delete Operation for Two Strings - LeetCode](https://leetcode.com/problems/delete-operation-for-two-strings/description/)

just some tweaks to the lcs code and done
```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        int n = word1.size();
        int m = word2.size();
        vector<vector<int>>dp(n+1, vector<int>(m+1,0));

        for(int i = 0;i<=n;i++) dp[i][0] = 0;
        for(int j = 0;j<=m;j++) dp[0][j] = 0;

        for(int i = 1;i<=n;i++){
            for(int j = 1;j<=m;j++){
                if(word1[i-1] == word2[j-1]) dp[i][j] = 1 + dp[i-1][j-1];
                else{
                    dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
                }
            }
        }
        return (n+m) - (2*dp[n][m]);
    }
};
```

---
##### Shortest Common Supersequence
link - [Shortest Common Supersequence - LeetCode](https://leetcode.com/problems/shortest-common-supersequence/)

![[DPonStrings-3.png| 300]]
if value is same then we only take it once, we move to i-1, j-1, otherwise we either go to i-1,j or i,j-1 and pick that up (we didn't pick this in printing LCS)-> we get the string in reverse order. Also, here if you reach the end of one string, some part of other string can be remaining so need to deal with that as well.

```cpp
class Solution {
public:
    string shortestCommonSupersequence(string str1, string str2) {
        //to minimize the length of the supersequence, the common parts of both
        //strings should be taken only once. -> LCS
        //length of supersequence = n+m - len(LCS)
        //now to print, we are going to use DP Table like printing LCS problem

        int n = str1.size();
        int m = str2.size();
        vector<vector<int>>dp(n+1, vector<int>(m+1,0));

        for(int i = 0; i<=n;i++) dp[i][0] = 0;
        for(int j = 0; j<=m; j++) dp[0][j] = 0;

        for(int i = 1; i<=n;i++){
            for(int j = 1; j<=m; j++){
                if(str1[i-1] == str2[j-1]) dp[i][j] = 1 + dp[i-1][j-1];
                else{
                    dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
                }
            }
        }

        string ans = "";
        int i = n, j = m;
        while(i>0 && j>0){
            if(str1[i-1] == str2[j-1]){
                ans += str1[i-1];
                i--;
                j--;
            }
            else if(dp[i-1][j] > dp[i][j-1]){
                ans += str1[i-1];
                i--;
            }
            else{
                ans += str2[j-1];
                j--;
            }
        }

        while(i>0){
            ans += str1[i-1];
            i--;
        }

        while(j>0){
            ans += str2[j-1];
            j--;
        }
        reverse(ans.begin(),ans.end());
        return ans;

    }
};
```

---
##### Distinct Subsequences
link - [Distinct Subsequences - LeetCode](https://leetcode.com/problems/distinct-subsequences/description/)

```cpp
class Solution {
public:
    int solveUtil(int i, int j, string &s, string &t, vector<vector<int>> &dp){
        //base case
        if(j<0){
            return 1; 
            //if j is -1, means string t is exhausted, found.
        }
        if(i<0){
            return 0; 
            //if i is -1, t is not in s
        }
        if(dp[i][j] != -1) return dp[i][j];
        
        //if char matches, try taking it once, and one case without taking it
        if(s[i] == t[j]){
            return dp[i][j] = solveUtil(i-1,j-1,s,t,dp) + solveUtil(i-1,j,s,t,dp);
        }
        
        //pov - you didn't find the char of t in s, so you gotta move
        //only i cuz can't move until you match j
        else{
            return dp[i][j] = solveUtil(i-1,j,s,t,dp);
        }
    }
    int numDistinct(string s, string t) {
        int n = s.size();
        int m = t.size();
        vector<vector<int>>dp(n,vector<int>(m,-1));
        return solveUtil(n-1,m-1,s,t,dp);
    }
};
```

T.C = O(N x M)
S.C = O(N x M) + O(N + M)

**Tabulation**

```cpp
class Solution {
public:
    int numDistinct(string s, string t) {
        int n = s.size();
        int m = t.size();
        vector<vector<double>>dp(n+1,vector<double>(m+1,0));
        
        for(int i = 0;i<=n;i++) dp[i][0] = 1; //t is exhausted
        
        //for(int j = 1;j<=m;j++) dp[0][j] = 0; //s is exhausted
        //make sure j starts from 1, since dp[0][0] = 1 from prev loop & would get
        //overwritten otherwise, or rather remove it altogether since dp is initialized
        // as 0 only.

        for(int i = 1;i<=n;i++){
            for(int j = 1;j<=m;j++){
                if(s[i-1] == t[j-1]){
                   dp[i][j] = dp[i-1][j-1] + dp[i-1][j];
                }
                else{
                    dp[i][j] = dp[i-1][j];
                }
            }
        }
        return (int)dp[n][m];
    }
};
```

**Further Space Optimization**

```cpp
class Solution {
public:
    int numDistinct(string s, string t) {
        int n = s.size();
        int m = t.size();
        vector<double>prev(m+1,0), cur(m+1,0);
        
        prev[0] = cur[0] = 1;

        for(int i = 1;i<=n;i++){
            for(int j = 1;j<=m;j++){
                if(s[i-1] == t[j-1]){
                   cur[j] = prev[j-1] + prev[j];
                }
                else{
                    cur[j] = prev[j];
                }
            }
            prev = cur;
        }
        return (int)prev[m];
    }
};
```

**Further God level Optimization**
![[DPonStrings4.png| 300]]
```cpp
class Solution {
public:
    int numDistinct(string s, string t) {
        int n = s.size();
        int m = t.size();
        vector<double>prev(m+1,0);
        
        prev[0] = 1;

        for(int i = 1;i<=n;i++){
            for(int j = m;j>=1;j--){
                if(s[i-1] == t[j-1]){
                   prev[j] = prev[j-1] + prev[j];
                }
            }
        }
        return (int)prev[m];
    }
};
```

---
##### Edit Distance
link - [Edit Distance - LeetCode](https://leetcode.com/problems/edit-distance/)

**Memoization**

```cpp
class Solution {
public:
    int solveUtil(int i, int j, string &word1, string &word2, vector<vector<int>> &dp){
        //base case
        if(i<0){
            return j+1; 
        //for eg., s1 = horse, s2 = ros, and horse is exhausted but s2 is at index 1,
        //so it needs 0 to j index values to be inserted in s2
        }

        if(j<0){
            return i+1;
        //will have to delete the remaining values of s1
        }

        if(dp[i][j] != -1) return dp[i][j];

        //if char matches, just go to previous index for both
        if(word1[i] == word2[j]) return 0 + solveUtil(i-1,j-1,word1,word2,dp);
        
        //one case where you insert the value of s2[j] into s1 at the right
        //so only j moves back and since the matched value is added to the right
        //i remains there itself
        int ins = 1 + solveUtil(i,j-1,word1,word2,dp);

        //if deletion from s1 happens, just value of i is decreased since it was 
        //not matched so j remains intact
        int del = 1 + solveUtil(i-1,j,word1,word2,dp);

        //if values dont match and s1 value gets replaced, then match happens,
        //both the indices move back
        int replace = 1 + solveUtil(i-1,j-1,word1, word2, dp);

        return dp[i][j] = min(ins, min(del, replace));

    }
    int minDistance(string word1, string word2) {
        int n = word1.size();
        int m = word2.size();
        vector<vector<int>>dp(n,vector<int>(m,-1));
        return solveUtil(n-1,m-1,word1,word2,dp);
    }
};
```

**Tabulation**

```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        int n = word1.size();
        int m = word2.size();
        vector<vector<int>>dp(n+1,vector<int>(m+1,0));
        
        for(int i = 0;i<=n;i++) dp[i][0] = i;
        for(int j = 0;j<=m;j++) dp[0][j] = j;

        for(int i = 1;i<=n;i++){
            for(int j = 1;j<=m;j++){
                if(word1[i-1] == word2[j-1]) dp[i][j] = dp[i-1][j-1];
                else{
                    int ins = 1 + dp[i][j-1];
                    int del = 1 + dp[i-1][j];
                    int replace = 1 + dp[i-1][j-1];
                    dp[i][j] = min(ins, min(del, replace));
                }
            }
        }
        return dp[n][m];
    }
};
```

---
##### Wildcard matching
link - [Wildcard Matching - LeetCode](https://leetcode.com/problems/wildcard-matching/)

**Memoization** (got MLE)

```cpp
class Solution {
public:
    int solveUtil(int i, int j, string &s, string &p, vector<vector<int>>dp){
        if(i<0 && j<0) return true;
        if(i<0 && j>=0) return false;
        if(j<0 && i>=0){
            for(int ii = 0;ii<=i;ii++){
                if(p[ii] != '*') return false;
            }
            return true;    
        }
        if(dp[i][j] != -1) return dp[i][j];
        if(p[i] == s[j] || p[i] == '?'){
        //if they match or ?, move back both pointers
            return dp[i][j] = solveUtil(i-1,j-1,s,p,dp);
        }

        if(p[i] == '*'){
        //if * means it can be either 0 or any sequence
        //so one case consider 0, so i moves back and j remains as it is
        //in other case, we keep i at * and move back j, saying it matched
        //one value, and in recursive iteration, it will follow a pattern such that,
        //it will be checked from picking 1 to all remaining elements of j and all cases
            return dp[i][j] = solveUtil(i-1,j,s,p,dp) | solveUtil(i,j-1,s,p,dp);
        }
        
        return dp[i][j] = false;
    }
    bool isMatch(string s, string p) {
        int n = p.size();
        int m = s.size();
        vector<vector<int>>dp(n,vector<int>(m,-1));
        return solveUtil(n-1,m-1,s,p,dp);

    }
};
```

**Tabulation**

```cpp
class Solution {
public:

    
    bool isMatch(string s, string p) {
        int n = p.size();
        int m = s.size();
        vector<vector<bool>>dp(n+1,vector<bool>(m+1,0));
        //one based indexing  

        //base case  
        dp[0][0] = true;
        for(int j = 1; j<=m;j++){
            dp[0][j] = false;
        }

        for(int i = 1; i<=n;i++){
            bool flag = true;
            for(int ii = 1;ii<=i;ii++){
                if(p[ii-1] != '*'){
                    flag = false;
                    break;
                }
            }
            dp[i][0] = flag;
        }

        //mimic recursion
        for(int i = 1; i<=n; i++){
            for(int j = 1; j<=m; j++){
                if(p[i-1] == s[j-1] || p[i-1] == '?'){
                    dp[i][j] = dp[i-1][j-1];
                }
                else if(p[i-1] == '*'){
                    dp[i][j] = dp[i-1][j] | dp[i][j-1];
                }
                else dp[i][j] = false;
            }
        }
        return dp[n][m];
    }
};
```

