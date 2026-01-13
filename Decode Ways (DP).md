# Decode Ways

LeetCode: [Decode Ways](https://leetcode.com/problems/decode-ways/)

This file shows two approaches: a plain recursion and a memoized recursion (top-down DP) in Java.

## Problem
Given a string s containing only digits, return the number of ways to decode it where:
- 'A' -> "1", 'B' -> "2", ..., 'Z' -> "26".

## Approach 1 — Recursion (brute force)
Try to take one digit (if valid) or two digits (if valid) at each position and recurse.

```java
public int helper(int idx, String s) {
    if (idx == s.length()) return 1;

    // take one
    int tookone = 0;
    if (Integer.parseInt(s.substring(idx, idx + 1)) > 0) {
        tookone = helper(idx + 1, s);
    }

    // take two
    int tooktwo = 0;
    if (idx + 2 <= s.length()
        && Integer.parseInt(s.substring(idx, idx + 2)) > 9
        && Integer.parseInt(s.substring(idx, idx + 2)) < 27) {
        tooktwo = helper(idx + 2, s);
    }

    return tookone + tooktwo;
}
```

Notes:
- This is exponential in the worst case (overlapping subproblems).
- Works but will TLE for long inputs.

## Approach 2 — Memoization (Top-down DP)
Store results for each index to avoid recomputation.

```java
import java.util.Arrays;

class Solution {
    public int numDecodings(String s) {
        if (s == null || s.length() == 0) return 0;
        int[] dp = new int[s.length()];
        Arrays.fill(dp, -1);
        return helper(0, s, dp);
    }

    public int helper(int idx, String s, int[] dp) {
        if (idx == s.length()) return 1;

        if (dp[idx] != -1) return dp[idx];

        // take one
        int tookone = 0;
        if (Integer.parseInt(s.substring(idx, idx + 1)) > 0) {
            tookone = helper(idx + 1, s, dp);
        }

        // take two
        int tooktwo = 0;
        if (idx + 2 <= s.length()
            && Integer.parseInt(s.substring(idx, idx + 2)) > 9
            && Integer.parseInt(s.substring(idx, idx + 2)) < 27) {
            tooktwo = helper(idx + 2, s, dp);
        }

        return dp[idx] = tookone + tooktwo;
    }
}
```

Complexity:
- Time: O(n) — each index is computed once.
- Space: O(n) recursion + O(n) memo array.

