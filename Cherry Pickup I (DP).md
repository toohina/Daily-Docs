Question Link: https://leetcode.com/problems/cherry-pickup/

Why Greedy does not Work?

The first approach that comes to our mind is ofcourse being greedy and choosing max value of cherries picked at every step and using 2 helper functions for the same: One from (0,0) to (n-1,n-1)
and another for (n-1,n-1) to (0,0). Then adding up the max values from both the functions.
This DOES NOT Work because, we may go through the same cell again and again while selecting the max cherry path but we need to account for the cherry that is picked up as well so that we cannot 
pick up the same cherry again as the cherry can only be picked once.

So What is the Work Around?
1. In this case, the best thing to do is, let 2 pointers tread through the grid at the same time: 
2. One pointer accounting for the path to reach (n-1,n-1) and the other pointer indicating the path used  to reach back (0,0)
3. The two pointers will be moving together, that means they will be taking single steps each time together. For instance, if p1 moves 2 places, p2 would also have moved 2 places. Keep this in mind as this fact is important to make the DP memoization optimized.
4. We will be having 4 combinations of movements for p1(r1,c1) and p2(r2,c2):
maxCherry(r1+1,c1,r2+1,c2)
maxCherry(r1,c1+1,r2,c2+1)
maxCherry(r1,c1+1,r2+1,c2)
maxCherry(r1+1,c1,r2,c2+1)
5. DONT FORGET TO STORE THE VALUE OF THE BOX AS WE GOTTO BACKTRACK AND PUT IN THE SAME VALUE AFTER CALLING OUR RECURSIVE FUNCS. For this however I got an error and chatGPT said that I dont need to do that in DP as DP depends on previous complete state. But in the video I followed said something else: https://www.youtube.com/watch?v=BFhUAqbRzgg
6. Remember, that when p1 and p2 land on the same cell, the cherry will only be picked once. That is:
if(r1==r2 && c1==c2) then pick cherry once
else pick cherry twice or once or 0 depending on the condition.
7. What we will return is the max of the 4 combinations adding the cherries collected in that step.
8. We can use 4D DP table to store the max cherries: DP[r1][c1][r2][c2]
9. However, we can convert this into a 3D Table by removing r1 as r1 is nothing but r2+c2-c1 according to the 3rd point.

```java
class Solution {
    public int cherryPickup(int[][] grid) {
        int n = grid.length;
        int[][][] dp = new int[n][n][n];
        for (int[][] a : dp)
            for (int[] b : a)
                Arrays.fill(b, -1);

        int ans = maxCherry(grid, n, 0, 0, 0, dp);
        return Math.max(0, ans);
    }

    int maxCherry(int[][] grid, int n, int c1, int r2, int c2, int[][][] dp) {
        int r1 = r2 + c2 - c1;

        if (r1 < 0 || r2 < 0 || c1 < 0 || c2 < 0 ||
            r1 >= n || r2 >= n || c1 >= n || c2 >= n ||
            grid[r1][c1] == -1 || grid[r2][c2] == -1)
            return Integer.MIN_VALUE;

        if (r1 == n - 1 && c1 == n - 1)
            return grid[r1][c1];

        if (dp[c1][r2][c2] != -1)
            return dp[c1][r2][c2];

        int cherries = grid[r1][c1];
        if (r1 != r2 || c1 != c2)
            cherries += grid[r2][c2];

        int maxFuture = Math.max(
            Math.max(
                maxCherry(grid, n, c1,   r2+1, c2,   dp), //d,d
                maxCherry(grid, n, c1+1, r2+1,   c2, dp) //r,d
            ),
            Math.max(
                maxCherry(grid, n, c1+1,   r2, c2+1, dp),//r,r
                maxCherry(grid, n, c1, r2, c2+1,   dp)//d,r
            )
        );

        if (maxFuture == Integer.MIN_VALUE)
            return dp[c1][r2][c2] = Integer.MIN_VALUE;

        return dp[c1][r2][c2] = cherries + maxFuture;
    }
}

```
