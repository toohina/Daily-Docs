# 🧠 How to Approach Dynamic Programming Problems

## 🔑 Golden Rule of DP
**DP = Recursion + Overlapping Subproblems + Optimal Substructure**

If you can write recursion, DP is just optimizing it.

---

## 1️⃣ Step 1: Identify if it is a DP problem

Ask yourself these questions.

✔️ Signs it is DP
- “Maximum / Minimum”
- “Number of ways”
- “Can we reach…”
- “Best / Optimal”
- Repeated states (same parameters called again and again)

❌ Not DP
- Pure permutations with no reuse
- One-pass greedy problems
- One-pass / single-scan greedy problems without overlapping subproblems

---

## 2️⃣ Step 2: Decide the DP Pattern

Most problems fall into one of these patterns:

| Pattern | Examples |
|---|---|
| Take / Don’t Take | Knapsack, Subset Sum |
| Linear DP | Fibonacci, House Robber |
| 2D Grid DP | Unique Paths |
| Partition DP | Matrix Chain |
| String DP | LCS, Edit Distance |
| DP on Subsequences | LIS |

👉 First classify the problem.

---

## 3️⃣ Step 3: Define the State 🧩 (MOST IMPORTANT)

State = minimum info needed to solve the rest.

Ask: “What do I need to know to make the next decision?”

Examples:
- Knapsack → (index, capacity)
- Grid → (row, col)
- Subset sum → (index, currentSum)
- LCS → (i, j)

If state is wrong → DP fails.

---

## 4️⃣ Step 4: Write Pure Recursion First ✍️

Example: Subset Sum (pure recursion — don't think DP yet)
```java
boolean solve(int index, int sum) {
    if (sum == target) return true;
    if (index == n || sum > target) return false;

    // take
    if (solve(index + 1, sum + arr[index])) return true;

    // don't take
    return solve(index + 1, sum);
}
```

❗ Don’t think DP yet. Just recursion.

---

## 5️⃣ Step 5: Identify Overlapping Subproblems 🔁

Ask: “Are the same (index, sum) values called again?”

If yes → DP applicable

Example:
- `solve(2, 5)` ← called multiple times

---

## 6️⃣ Step 6: Add Memoization (Top-Down DP)

Create DP array:
```java
Boolean[][] dp = new Boolean[n][target + 1];
```

Modify recursion:
```java
boolean solve(int index, int sum) {
    if (sum == target) return true;
    if (index == n || sum > target) return false;

    if (dp[index][sum] != null)
        return dp[index][sum];

    boolean take = solve(index + 1, sum + arr[index]);
    boolean dontTake = solve(index + 1, sum);

    return dp[index][sum] = take || dontTake;
}
```

Now time complexity reduces drastically.

---

## 7️⃣ Step 7: Convert to Tabulation (Bottom-Up)

Define DP table meaning:
- `dp[i][s] = can we form sum s using first i elements`

Fill table:
```java
boolean[][] dp = new boolean[n + 1][target + 1];

dp[0][0] = true;

for (int i = 1; i <= n; i++) {
    for (int s = 0; s <= target; s++) {
        dp[i][s] = dp[i - 1][s];

        if (s >= arr[i - 1]) {
            dp[i][s] |= dp[i - 1][s - arr[i - 1]];
        }
    }
}
```

---

## 8️⃣ Step 8: Optimize Space (if possible)

If only previous row is needed:
```java
boolean[] dp = new boolean[target + 1];
dp[0] = true;

for (int num : arr) {
    for (int s = target; s >= num; s--) {
        dp[s] = dp[s] || dp[s - num];
    }
}
```

---

## 9️⃣ DP Thinking Template (Memorize This)

Whenever stuck, ask:
- What is my state?
- What are my choices?
- What is my base case?
- What repeats?
- Can I store it?

---

## 🔟 Common Interview DP Mistakes ❌

| Mistake | Fix |
|---|---|
| Jumping to DP directly | Write recursion first |
| Wrong state | Define minimal state |
| Forgetting base cases | Always write them |
| Using DP where greedy works | Check greedy first |

---

## 🔥 Final Mental Model

DP = Decision Tree + Pruning

You already understand take / don’t take — DP is just cutting duplicate branches.

---

# One-pass Greedy Problems — What are they?

Great question — this confusion is very common, and once it clicks, greedy vs DP becomes much clearer.

## 🧠 What is a One-Pass Greedy Problem?

A one-pass greedy problem is a problem where:
- ✅ You can traverse the input once
- ✅ At each step, you make the locally best decision
- ✅ That decision is never revisited or undone
- ✅ The final result is guaranteed to be optimal

No recursion. No backtracking. No DP table.

### 🔑 Core Property (Very Important)
Local optimum ⇒ Global optimum

If this property holds, the problem is greedy.

---

## 1️⃣ What does “one-pass” mean?
- You iterate left to right once.
- Constant or very small extra memory.
- Time complexity usually O(n).

---

## 2️⃣ Classic One-Pass Greedy Examples

Example 1: Maximum Subarray (Kadane’s Algorithm)
```java
int maxSubArray(int[] nums) {
    int curr = nums[0];
    int max = nums[0];

    for (int i = 1; i < nums.length; i++) {
        curr = Math.max(nums[i], curr + nums[i]);
        max = Math.max(max, curr);
    }
    return max;
}
```
✔ One pass — Greedy decision: “extend or restart”.

Example 2: Best Time to Buy and Sell Stock (Single Transaction)
```java
int maxProfit(int[] prices) {
    int minPrice = Integer.MAX_VALUE;
    int profit = 0;

    for (int price : prices) {
        minPrice = Math.min(minPrice, price);
        profit = Math.max(profit, price - minPrice);
    }
    return profit;
}
```
✔ One pass — Greedy decision: always buy at lowest so far.

Example 3: Jump Game I
```java
boolean canJump(int[] nums) {
    int maxReach = 0;

    for (int i = 0; i < nums.length; i++) {
        if (i > maxReach) return false;
        maxReach = Math.max(maxReach, i + nums[i]);
    }
    return true;
}
```
✔ One pass — Greedy: always extend maximum reach.

---

## 3️⃣ How One-Pass Greedy Differs from DP

| Greedy | DP |
|---:|---|
| One pass | Multiple states |
| No backtracking | Backtracking / memo |
| O(n) | O(n²), O(n×k) |
| Simple variables | DP table |
| No overlapping subproblems | Overlapping subproblems |

---

## 4️⃣ How to Identify a One-Pass Greedy Problem 🔍

Ask:
- Can I solve this by tracking just a few variables?
- Does each decision depend only on past info?
- Once I make a choice, do I never need to change it?
- Is the problem asking for max/min/reachability?

If yes to all → try greedy first.

---

## 5️⃣ Problems That Look Greedy But Are NOT One-Pass ❌

- Coin Change (minimum coins) — greedy can fail, needs DP.
  - Example: coins = [1, 3, 4], amount = 6 → greedy picks 4+1+1 but optimal is 3+3.
- 0/1 Knapsack — local best doesn’t guarantee global best → DP.

---

## 6️⃣ Interview Tip 💡

If unsure, say:
“I’ll try a greedy one-pass solution first; if local choices fail, I’ll move to DP.”

Interviewers like this thought process.

---

## 7️⃣ One-Line Definition (Memorize This)

A one-pass greedy problem is one where a single linear scan with locally optimal choices guarantees the globally optimal result.
