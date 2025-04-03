[[Dynamic Programming]]
Concept based on Next Greater and Next Smaller Element of Topic **Stacks**
pre-requisite -  [Largest Rectangle in Histogram](https://www.youtube.com/watch?v=X0X6G-eWgQ8&ab_channel=takeUforward)

##### Maximum Rectangle Area with all 1's
link - [Maximal Rectangle - LeetCode](https://leetcode.com/problems/maximal-rectangle/description/)
![[DPonSqare.png|400]]
here, for each row, histogram is considered like the previous problem, of height top to that row, and max is considered. 
remembering the past heights to increase the length as we go and find max area, thus dp, but mainly stack problem.

```cpp
class Solution {
public:
    int largestRectangleArea(vector < int > & heights) {
      int n = heights.size();
      stack < int > st;
      vector<int> leftsmall(n), rightsmall(n);
      for (int i = 0; i < n; i++) {
        while (!st.empty() && heights[st.top()] >= heights[i]) {
          st.pop();
        }
        if (st.empty())
          leftsmall[i] = 0;
        else
          leftsmall[i] = st.top() + 1;
        st.push(i);
      }
      // clear the stack to be re-used
      while (!st.empty())
        st.pop();

      for (int i = n - 1; i >= 0; i--) {
        while (!st.empty() && heights[st.top()] >= heights[i])
          st.pop();

        if (st.empty())
          rightsmall[i] = n - 1;
        else
          rightsmall[i] = st.top() - 1;

        st.push(i);
      }
      int maxA = 0;
      for (int i = 0; i < n; i++) {
        maxA = max(maxA, heights[i] * (rightsmall[i] - leftsmall[i] + 1));
      }
      return maxA;
    }
    int maximalRectangle(vector<vector<char>>& matrix) {
        //now progressively finding height of histogram row by row
        int n = matrix.size();
        int m = matrix[0].size();
        int maxAns = INT_MIN;
        vector<int>height(m,0);
        for(int i = 0;i<n;i++){
            for(int j = 0;j<m;j++){
                if(matrix[i][j] == '1') height[j]++;
                else height[j] = 0;
            }
            int area = largestRectangleArea(height);
            maxAns = max(area, maxAns);
        }
        return maxAns;
    }
};
```
---
##### Cound Square Submatrices with All Ones
link -[Count Square Submatrices with All Ones - LeetCode](https://leetcode.com/problems/count-square-submatrices-with-all-ones/description/) 

![[DPonSquarev3.png|400]]

![[DPonSquarev2.png|400]]
see how diagonal, up and left value is taken as reference to find squares

```cpp
class Solution {
public:
    int countSquares(vector<vector<int>>& matrix) {
        int n = matrix.size();
        int m = matrix[0].size();
        vector<vector<int>>dp(n,vector<int>(m,0));
        for(int i = 0;i<n;i++){
            dp[i][0] = matrix[i][0];
        }
        for(int j = 0;j<m;j++){
            dp[0][j] = matrix[0][j];
        }

        for(int i = 1;i<n;i++){
            for(int j = 1;j<m;j++){
                if(matrix[i][j] == 0)dp[i][j] = 0;
                else{
                    dp[i][j] = 1 + min(dp[i-1][j], min(dp[i-1][j-1],dp[i][j-1]));
                }
            }
        }
        int sum = 0;
        for(int i = 0;i<n;i++){
            for(int j = 0;j<m;j++){
                sum+=dp[i][j];
            }
        }
        return sum;
    }
};
```
