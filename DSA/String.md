
> [!NOTE]- # Easy Problems
> ##### Remove Outermost Parentheses
> link - [Remove Outermost Parentheses](https://leetcode.com/problems/remove-outermost-parentheses/)
> ```cpp
> class Solution {
> public:
>     string removeOuterParentheses(string s) {
>         int cnt = 0;
>         string res = "";
>         for (int i = 0; i < s.size(); i++) {
>             if (s[i] == ')') cnt--;
>             if (cnt != 0) {
>                 res.append(1, s[i]);
>             }
>             if (s[i] == '(') cnt++;
>         }
>         return res;
>     }
> };
> ```
> 
> ---
> ##### Reverse Words in a String
> link - [Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/)
> ```cpp
> class Solution {
> public:
>     string reverseWords(string s) {
>         stringstream ss(s);
>         string word;
>         vector<string> words;
> 
>         //remove whitespaces
>         while(ss >> word){
>             words.push_back(word);
>         }
> 
>         string res;
>         for(int i = words.size() - 1; i>=0; i--){
>             res += words[i];
>             if(i != 0){
>                 res += " ";
>             }
>         }
>         return res;
>     }
> };
> ```
> 
> ---
> ##### Largest Odd Number in String
> link - [Largest Odd Number in String](https://leetcode.com/problems/largest-odd-number-in-string/)
> ```cpp
> class Solution {
> public:
>     string largestOddNumber(string num) {
>         int index = -1;
>         for(int i = num.size() - 1; i>=0; i--){
>             if((num[i] - '0') % 2 == 1) {
>                 index = i;
>                 break;
>             }
>         }
>         if(index == -1)return "";
>         return num.substr(0,index+1);
>     }
> };
> ```
> 
> ---
> ##### Longest Common Prefix
> link - [Longest Common Prefix](https://leetcode.com/problems/longest-common-prefix/)
> ```cpp
> class Solution {
> public:
>     string longestCommonPrefix(vector<string>& strs) {
>         string res = "";
>         int minLength = INT_MAX;
>         for(auto s:strs){
>             int size = s.size();
>             minLength = min(minLength,size);
>         }
>         if(strs.size() == 1) return strs[0];
>         string baseline = strs[0];
>         int index = -1;
>         for(int i = 0;i<minLength;i++){
>             int flag = 0;
>             for(auto s:strs){
>                 if(s[i] != baseline[i]){
>                     flag = 1;
>                 }
>             }
>             if(flag) break;
>             index = i;
>         }
>         if(index == -1)return "";
>         return baseline.substr(0,index+1);
>     }
> };
> ```
> 
> ---
> #####  Isomorphic Strings
> link - [Isomorphic Strings](https://leetcode.com/problems/isomorphic-strings/)
> ```cpp
> class Solution {
> public:
>     bool isIsomorphic(string s, string t) {
>         unordered_map<char, char> mpp1;
>         unordered_map<char, char> mpp2;
>         for(int i = 0; i < s.size(); i++) {
>             if(mpp1.find(s[i]) != mpp1.end()) {
>                 if(mpp1[s[i]] != t[i]) return false;
>             }
>             if(mpp2.find(t[i]) != mpp2.end()) {
>                 if(mpp2[t[i]] != s[i]) return false;
>             }
>             else {
>                 mpp1[s[i]] = t[i];
>                 mpp2[t[i]] = s[i];
>             }
>         }
>         return true;
>     }
> };
> ```
> 
> ---
> #####  Rotate String
> link - [Rotate String](https://leetcode.com/problems/rotate-string/)
> ```cpp
> class Solution {
> public:
>     bool rotateString(string s, string goal) {
>         for(int i = 0; i < s.size(); i++) {
>             if(s == goal) return true;
>             char firstL = s[0];
>             s = s.substr(1, s.size()-1);
>             s.push_back(firstL);
>         }
>         return false;
>     }
> };
> ```
> 
> ---
> #####  Valid Anagram
> link - [Valid Anagram](https://leetcode.com/problems/valid-anagram/)
> ```cpp
> class Solution {
> public:
>     bool isAnagram(string s, string t) {
>         vector<int> letters(26, 0);
>         for(char c : s) {
>             letters[c - 'a']++;
>         }
>         for(char c : t) {
>             if(letters[c - 'a'] == 0) return false;
>             letters[c - 'a']--;
>         }
>         for(int i = 0; i < 26; i++) {
>             if(letters[i]) return false;
>         }
>         return true;
>     }
> };
> ```

---
