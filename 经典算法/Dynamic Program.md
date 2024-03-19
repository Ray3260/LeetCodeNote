## 动态规划入门：从记忆化搜索到递推

三步走：思考回溯怎么写（） -> 记忆化搜索 -> 1:1翻译成递推
进阶： -> 空间优化（滚动数组）

[198. 打家劫舍](https://leetcode.cn/problems/house-robber/)
你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额
1、回溯写法 选还是不选

```java
class Solution{
    public int rob(int[] nums) {
        return dp(nums, 0);
    }
    int dp(int[] nums, int i){
        if(i >= nums.length){
            return 0;
        }
        int res = Math.max(dp(nums, i + 1), dp(nums, i + 2) + nums[i]);
        return res;
    }
}
```

2、 添加memo记忆化搜索避免重复计算

```java
class Solution{
    int[] memo;
    public int rob(int[] nums) {
        memo = new int[nums.length];
        Arrays.fill(memo, -1);
        return dp(nums, 0);
    }
    int dp(int[] nums, int i){
        if(i >= nums.length){
            return 0;
        }
        if(memo[i] != -1) return memo[i];
        int res = Math.max(dp(nums, i + 1), dp(nums, i + 2) + nums[i]);
        memo[i] = res;
        return res;
    }
}
```

3、1:1翻译成递推，将递归变成循环，将dp函数变为数组, 递归边界为数组初始值

```java
class Solution{
    public int rob(int[] nums){
        int n = nums.length;
        int[] dp = new int[n + 2];//初始值为0
        for(int i = 0; i < n; i++){
            dp[i + 2] = Math.max(dp[i] + nums[i], dp[i + 1]);
        }
        return dp[n + 1];
    }
}
```

4、由于状态转移只用到了前两个状态，可以用滚动数组优化空间

```java
class Solution{
    public int rob(int[] nums){
        int n = nums.length;
        int dp_1 = 0, dp_2 = 0;
        for(int i = 0; i < n; i++){
            int temp = Math.max(dp_1 + nums[i], dp_2);
            dp_1 = dp_2;
            dp_2 = temp;
        }
        return dp_2;
    }
}
```

## 0-1背包问 完全背包

1、0-1背包，有n个物品，第i个物品的体积为w[i],价值为v[i]，每个物品至多选一个，求体积不超过capacity的最大价值和

```java
//0-1背包 回溯写法
//dfs(i, c) = max(dfs(i - 1, c), dfs(i - 1, c - w[i])+v[i])
class Solution{
    public int pack(int capacity, int[] w, int[] v){
        return backtrace(capacity, w, v, 0, capacity);;
    }
    int backtrace(int capacity, int[] w, int[] v, int i){
        if(i == w.length || capacity == 0) return 0;
        if(capacity < w[i]) return backtrace(capacity, w, v, i +1);
        return Math.max(backtrace(capacity - w[i], w, v, i + 1) + v[i], backtrace(capacity, w, v, i +1));
    }
}
//0-1背包变体：
//至多装capacity， 恰好装capacity，至少装capacity；
```

[494. 目标和](https://leetcode.cn/problems/target-sum/)
    给你一个非负整数数组 nums 和一个整数 target 。
    向数组中的每个整数前添加 '+' 或 '-' ，然后串联起所有整数，可以构造一个 表达式 ：
    例如，nums = [2, 1] ，可以在 2 之前添加 '+' ，在 1 之前添加 '-' ，然后串联起来得到表达式 "+2-1" 。
    返回可以通过上述方法构造的、运算结果等于 target 的不同 表达式 的数目。
思路：
1、直接回溯,记忆化搜索全部情况
```java
class Solution {
    Map<String, Integer> memo = new HashMap<>();
    public int findTargetSumWays(int[] nums, int target) {
        return backtrace(nums, target, 0);
    }
    public int backtrace(int[] nums, int c, int i){
        if(i == nums.length) return c == 0 ? 1 : 0;
        String key = i + "," + c;
        if(memo.containsKey(key)) return memo.get(key);
        int res = backtrace(nums, c - nums[i],  i + 1) + backtrace(nums, c + nums[i], i + 1);
        memo.put(key, res);
        return res;
    }
}
```
2、转换成0-1背包问题，假设添加正号的数的和是p， 数组整体和为s， 剩余的负数和为-（s - p） 则target = p-（s - p）= 2p - s
即 p = （s + t） / 2， 所以问题变为 0-1背包 恰好等于p的最大值
```java
class Solution {
    //记忆化搜索方式
    int[][] memo;
    public int findTargetSumWays(int[] nums, int target) {
        //设p = 添加正号的和， s = nums的和， t = target
        //t = p - (s - p), p = (t + s) / 2
        //0-1背包问题， 恰好等于p的个数
        int n = nums.length;
        for(int num : nums) target += num;
        if(target < 0 || target % 2 != 0) return 0;
        target /= 2;
        
        memo = new int[n][target + 1];
        for(int i = 0; i < n; i++){
            Arrays.fill(memo[i], -1);
        }
        return backtrace(n - 1, target, nums);
    }
    int backtrace(int i, int t, int[] nums){
        if(i < 0) return t == 0 ? 1 : 0;
        if(memo[i][t] != -1) return memo[i][t];
        if(t < nums[i]) return backtrace(i - 1, t, nums);
        int res = backtrace(i - 1, t, nums) + backtrace(i - 1, t - nums[i], nums);
        memo[i][t] = res;
        return res;
    }
}
```

```java
class Solution {
    //1:1 翻译成递推
    public int findTargetSumWays(int[] nums, int target) {
        for (int x : nums) target += x;
        if (target < 0 || target % 2 == 1) return 0;
        target /= 2;

        int n = nums.length;
        int[][] f = new int[n + 1][target + 1];
        f[0][0] = 1;
        for (int i = 0; i < n; ++i)
            for (int c = 0; c <= target; ++c)
                if (c < nums[i]) f[i + 1][c] = f[i][c];
                else f[i + 1][c] = f[i][c] + f[i][c - nums[i]];
        return f[n][target];
    }
}
```
