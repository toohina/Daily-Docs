# Minimum Jumps to Reach Home (DP)

## If the position exceeds x+b then no point going further. Why?
Since we cannot go backwards consecutively, if we go past x+b, there is no way we can reach back to x.

### First Approach (Plain Recursion):

```java
class Solution {
    public int minimumJumps(int[] forbidden, int a, int b, int x) {
        Set<Integer> forb=new HashSet<>();
        for(int num:forbidden)forb.add(num);
        int ans=helper(0,forb,a,b,x,false,0);
        return ans==Integer.MAX_VALUE?-1:ans;
    }
    public int helper(int pos,Set<Integer> forbidden,int a,int b,int x,boolean backtwice, int jumps){
        if(pos==x)return jumps;
        if(pos<0)return Integer.MAX_VALUE;
        if(pos>x+b)return Integer.MAX_VALUE;
        if(forbidden.contains(pos))return Integer.MAX_VALUE;
        int forward=helper(pos+a,forbidden,a,b,x,false,jumps+1);
        int backward=Integer.MAX_VALUE;
        if(backtwice==false)
        backward=helper(pos-b,forbidden,a,b,x,true,jumps+1);

        return Math.min(forward,backward);
    }
}
```
### Correctinig First Approach:
```java
class Solution {

    int INF = (int) 1e9;

    public int minimumJumps(int[] forbidden, int a, int b, int x) {

        Set<Integer> forb = new HashSet<>();
        for (int num : forbidden) forb.add(num);

        int[][] dp = new int[x + b + 1][2];
        for (int[] row : dp) Arrays.fill(row, -1);

        int ans = helper(0, forb, a, b, x, 0, dp);
        return ans >= INF ? -1 : ans;
    }

    // returns min jumps needed from this state to reach x
    public int helper(int pos, Set<Integer> forbidden,
                      int a, int b, int x,
                      int back, int[][] dp) {

        if (pos == x) return 0;
        if (pos < 0 || pos > x + b) return INF;
        if (forbidden.contains(pos)) return INF;

        if (dp[pos][back] != -1) return dp[pos][back];

        int forward = helper(pos + a, forbidden, a, b, x, 0, dp);

        int backward = INF;
        if (back == 0) {
            backward = helper(pos - b, forbidden, a, b, x, 1, dp);
        }

        int best = Math.min(forward, backward);
        if (best == INF) return dp[pos][back] = INF;

        return dp[pos][back] = 1 + best;
    }
}

```
