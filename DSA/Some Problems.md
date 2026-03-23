##### N-Queens
link - [N-Queens](https://leetcode.com/problems/n-queens/)
Recursion Backtracking
![[Pasted image 20250711113612.png | 500]]
Every row must have 1 Queen, and every column should have 1 Queen and no queen should attack each other.
So we place it in a column and then test if another queen fits in, if not we remove that new queen and backtracks to previous step and that's how we try all cases we want.
you start with placing in the first column and go there by till last and if last is successful, then we managed to get an answer, since rows and cols are mutually exclusive for all queens, we dont need to check if each row has a queen or not.
And since you start with 0 till n, while you place, you don't need to check the placement of queen on right side because there would exist none, so just check the left side, so left diagnal (upper and lower) and left direction only.

In spirit, my only intention is to have the ability to place **This** queen there, I am not concerned about the  future queens, but if I can place this queen, I am done and will move to future queens in next steps.

```cpp
class Solution {
public:
    bool canPlace(int row, int col, vector<string>& board, int n) {
        int duprow = row;
        int dupcol = col;
        while (row >= 0 && col >= 0) {
            // checking if left upper diagonal is safe or not
            if (board[row][col] == 'Q') return false;
            row--;
            col--;
        }
        col = dupcol;
        row = duprow;
        // checking if left lower diagonal is safe or not
        while (row < n && col >= 0) {
            if (board[row][col] == 'Q') return false;
            row++;
            col--;
        }
        col = dupcol;
        row = duprow;
        // checking the left position (in the same row)
        while (col >= 0) {
            if (board[row][col] == 'Q') return false;
            col--;
        }
        // if none return false, we can fit the queen there
        return true;
    }

    void solve(int col, vector<string>& board, vector<vector<string>>& ans, int n) {
        if (col == n) {
            ans.push_back(board);
            return;
            // means we were able to put all queens in all columns
            // and our work is done, and this configuration is
            // one of the answers
        }
        // like col 0 -> all rows (essentially trying out every place in each column)
        for (int row = 0; row < n; row++) {
            if (canPlace(row, col, board, n)) {
                // if no other queen becoming problem, just place the
                // queen there
                board[row][col] = 'Q';
                // placed queen, now try out the next column
                solve(col + 1, board, ans, n);
                // if the next columns are successful, we have the board in ans
                // and null return which terminates before this, but if not
                // we need to clear the queens placed in these iteration before
                // we try out the other rows
                board[row][col] = '.';
            }
        }
    }

    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> ans;
        vector<string> board;
        string s(n, '.');
        // initialize board with empty cells
        for (int i = 0; i < n; i++) {
            board.push_back(s);
        }
        solve(0, board, ans, n);
        return ans;
    }
};
```

**Optimized - Hashing**
![[Pasted image 20250711200930.png | 400]]
for rows, have an array and if any queen is placed in that row, just mark it in hash array and no need to retrace it in next iteration since going left row takes N time and all 3 takes 3N time, and for diagonals (lower), if you add row + col, the number is same for the diagonals, so hash it as well.
upper diagonal,
![[Pasted image 20250711201250.png | 400]]
```cpp
class Solution {
public:
    void solve(int col, vector<string>& board, vector<vector<string>>& ans, int n, vector<int>& leftRow, vector<int>& upperDiagonal, vector<int>& lowerDiagonal) {
        if (col == n) {
            ans.push_back(board);
            return;
        }
        for (int row = 0; row < n; row++) {
            if (leftRow[row] == 0 && lowerDiagonal[row + col] == 0 && upperDiagonal[n - 1 + col - row] == 0) {
                board[row][col] = 'Q';
                leftRow[row] = 1;
                lowerDiagonal[row + col] = 1;
                upperDiagonal[n - 1 + col - row] = 1;
                solve(col + 1, board, ans, n, leftRow, upperDiagonal, lowerDiagonal);
                board[row][col] = '.';
                leftRow[row] = 0;
                lowerDiagonal[row + col] = 0;
                upperDiagonal[n - 1 + col - row] = 0;
            }
        }
    }

    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> ans;
        vector<string> board;
        string s(n, '.');
        for (int i = 0; i < n; i++) {
            board.push_back(s);
        }
        vector<int> leftRow(n, 0), upperDiagonal(2 * n - 1, 0), lowerDiagonal(2 * n - 1);
        solve(0, board, ans, n, leftRow, upperDiagonal, lowerDiagonal);
        return ans;
    }
};
```
Time Complexity = $O(N!)$

---
##### Bitmask Technique for Generating All Subsequence
```cpp
for (int mask = 1; mask < (1 << n); mask++) {
    // mask goes: 1,2,3,4,5,6,7 (for n=3)
    // (1<<n) = 8, so mask < 8
    
    string sub = "";
    for (int i = 0; i < n; i++) {
        // Check if bit at position i is 1
        if (mask & (1 << i)) {
            sub += s1[i];  // include this char
        }
    }
    allSubseqs.push_back(sub);
}
```

