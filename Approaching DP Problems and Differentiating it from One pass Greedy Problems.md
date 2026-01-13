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
### Subset Sum (0/1) — Bottom-Up DP

#### 🧠 Goal of the Problem
Given:
```java
int[] arr = {2, 3, 7};
int target = 5;
```
Question: Can we form sum = 5 using some of the elements?  
Each element can be used at most once (take / don’t take).

---

#### 1️⃣ What does dp[i][s] mean?
This is the MOST IMPORTANT PART.

dp[i][s] = true if we can form sum = s using the first i elements

Clarify “first i elements”:
- i = 0 → no elements
- i = 1 → {2}
- i = 2 → {2, 3}
- i = 3 → {2, 3, 7}

Example:
- dp[2][5] → Can we make sum 5 using {2, 3}?

---

#### 2️⃣ DP Table Size
```java
boolean[][] dp = new boolean[n + 1][target + 1];
```
Why n + 1? We need row 0 → “using 0 elements”.  
Columns run from 0..target (sums).

---

#### 3️⃣ Base Case Initialization
```java
dp[0][0] = true;
```
Why? Using 0 elements we can make sum 0 (empty subset).  
All other entries in row 0 are false:
- dp[0][1] = false, dp[0][2] = false, ...

Also, for every i: dp[i][0] = true (we can always form sum 0 by choosing nothing).

---

#### 4️⃣ Filling the Table (Core Logic)
We iterate over items (i) and all possible sums (s):
```java
for (int i = 1; i <= n; i++) {
    for (int s = 0; s <= target; s++) {
        // fill dp[i][s]
    }
}
```
Meaning: For each prefix of the array and for each target sum, decide if sum s is formable.

---

#### 5️⃣ DON’T TAKE Case
```java
dp[i][s] = dp[i - 1][s];
```
Meaning: If we don't take the current element, can we already make sum s using previous elements? Copy from the previous row.

Example: dp[2][5] = dp[1][5]

---

#### 6️⃣ TAKE Case
```java
if (s >= arr[i - 1]) {
    dp[i][s] |= dp[i - 1][s - arr[i - 1]];
}
```
Why arr[i - 1]? Because array indices are 0-based while i is 1-based in the DP table:
- i = 1 → element index 0
- i = 2 → element index 1

Meaning: If we take current element (value v = arr[i-1]), we need the remaining sum (s - v) to be formable by previous elements. So check dp[i-1][s-v].

Example: arr[i - 1] = 3, s = 5 → remaining = 2 → dp[2][5] |= dp[1][2]

---

#### 7️⃣ Why OR (|=)?
```java
dp[i][s] = dontTake OR take
```
If either not taking or taking yields true, dp[i][s] is true.

---

#### 8️⃣ Full Code (With Meaning)
```java
boolean[][] dp = new boolean[n + 1][target + 1];

// base cases
dp[0][0] = true;
for (int i = 1; i <= n; i++) dp[i][0] = true; // optional but clear

for (int i = 1; i <= n; i++) {
    for (int s = 0; s <= target; s++) {

        // don't take
        dp[i][s] = dp[i - 1][s];

        // take (if current element can fit)
        if (s >= arr[i - 1]) {
            dp[i][s] |= dp[i - 1][s - arr[i - 1]];
        }
    }
}

// Answer: dp[n][target]
```

- dp[n][target] tells us if we can form `target` using all n elements.

---

#### 9️⃣ Visual Table Example
For arr = {2, 3}, target = 5:

i \ s | 0 | 1 | 2 | 3 | 4 | 5
---|---:|---:|---:|---:|---:|---:
0 | T | F | F | F | F | F
1 (2) | T | F | T | F | F | F
2 (3) | T | F | T | T | F | T

- dp[2][5] = true → subset {2, 3}

---

#### 🔑 Mental Model (Memorize This)
“Each DP row answers: what sums can I form up to this element?”

For each element, choose take or don't take and build answers from smaller subproblems.

---

#### Complexity
- Time: O(n * target) — we evaluate every (i, s).
- Space: O(n * target). Can be optimized to O(target) using a single boolean[] and iterating s from target down to arr[i-1].

---

#### 🔥 Interview Gold Line
“For each element, I decide take or don’t take, and build answers from smaller subproblems — dp[i][s] means whether sum s is achievable using the first i elements.”
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
### Subset Sum (0/1) — 1D Optimization (Space Optimized)

#### 🧠 What we are optimizing

Earlier we had:
```java
boolean[][] dp = new boolean[n + 1][target + 1];
```

But notice something important: to compute dp[i][*], we only use values from dp[i - 1][*].  
So we don’t need the full 2D table.

#### 1️⃣ Key Observation (This unlocks optimization)

From tabulation:
dp[i][s] = dp[i - 1][s] 
        OR dp[i - 1][s - arr[i - 1]]

👉 Only the previous row is needed — we can reuse the same array.  
So instead of O(n × target) space, we can use O(target).

---

#### 2️⃣ What does 1D `dp[s]` mean?

`dp[s] =` can we form sum `s` using elements processed so far?

As we iterate elements one by one, `dp` keeps updating to reflect sums achievable with the processed prefix.

---

#### 3️⃣ The Optimized Code
```java
boolean[] dp = new boolean[target + 1];
dp[0] = true;   // base case

for (int num : arr) {
    for (int s = target; s >= num; s--) {
        dp[s] = dp[s] || dp[s - num];
    }
}

// Answer: dp[target]
```

---

#### 4️⃣ Base Case
`dp[0] = true;`  
Why? Sum 0 is always possible (empty subset). This replaces `dp[0][0] = true` from 2D DP.

---

#### 5️⃣ Outer Loop — Processing Elements
`for (int num : arr)`

Meaning: “I am now deciding whether to take or not take this number.” Each number is processed once → 0/1 behavior guaranteed.

---

#### 6️⃣ Inner Loop — WHY BACKWARD? ⚠️⚠️⚠️
`for (int s = target; s >= num; s--)`

This is the most important concept.

❓ Why not forward?

If we go forward:
```java
for (int s = num; s <= target; s++)
```
❌ That would allow reusing the same element multiple times — it becomes UNBOUNDED KNAPSACK.

🔥 Backward loop prevents reuse: when iterating right → left, `dp[s - num]` still reflects the state before processing the current `num`, so `num` is used only once. ✔ Correct for 0/1 knapsack / subset sum.

---

#### 7️⃣ Example Walkthrough (VERY IMPORTANT)

Input:
```
arr = {2, 3}
target = 5
```

Initial:
```
dp = [true, false, false, false, false, false]
```

Processing `num = 2` (s = 5 → 2):
- `dp[2] = dp[2] || dp[0]` → true

Result:
```
[true, false, true, false, false, false]
```

Processing `num = 3` (s = 5 → 3):
- `dp[5] = dp[5] || dp[2]` → true
- `dp[3] = dp[3] || dp[0]` → true

Result:
```
[true, false, true, true, false, true]
```

✔ Sum 5 possible → {2,3}

---

#### 8️⃣ What if we looped FORWARD (WRONG)?

Forward loop would allow reusing `3` multiple times → `{3,3}` which violates the 0/1 constraint.

---

#### 9️⃣ When to Use Backward vs Forward

- 0/1 Knapsack / Subset Sum: 🔙 Backward
- Coin Change (unbounded) / Unlimited usage: 🔜 Forward

🔑 One-Line Rule (MEMORIZE THIS)  
Backward loop = use element once. Forward loop = reuse element multiple times.

---

#### 🧠 Final Mental Model

2D DP → clear logic.  
1D DP → same logic, reused space.  
Backward loop → preserves correctness.
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
