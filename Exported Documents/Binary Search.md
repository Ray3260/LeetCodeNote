# Binary Search

# Binary Search

- 二分搜索的经典写法（**闭区间**  ），需要注意三点：

  1. 循环退出条件， 注意low <= high。
  2. mid的取值， mid = low + （high - left） / 2
  3. low 和 high 的更新。 low = mid + 1， high = mid - 1；
- 二分的本质

  二分的本质是「二段性」而非「单调性」，比如[33. 搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)。「二段性」还能继续细分，不仅仅只有满足 01特性（满足/不满足）的「二段性」可以使用二分，满足 1?特性（一定满足/不一定满足）也可以二分，[162. 寻找峰值](https://leetcode.cn/problems/find-peak-element/)。
- 二分搜索常见问题：

  1. 查找第一个与target相等的元素(重复元素上界)，时间复杂度O（logn）
  2. 查找最后一个与target相等的元素（重复元素下界），时间复杂度O（logn）
  3. 查找第一个大于等于target的元素，时间复杂度O（logn）
  4. 查找最后一个小于等于target的元素，时间复杂度O（logn）

二分搜索常用于有序数组， 或者在值不重复数组中找到一个峰值（162. 寻找峰值, 1901. 寻找峰值II）

```java
class Solution{

  //以下均为闭区间写法 
  /**
    闭区间循环 跳出循环必定经过left = right
  */
    public int binarySearch(int[] nums, int target){
        //二分经典写法
        int left = 0, right = nums.length - 1; // 闭区间
        while(left <= right){
            int mid = left + (right - left) / 2;
            if(nums[mid] == target){
                return mid;
            }else if(nums[mid] < target){
                left = mid + 1;
            }else{
              right = mid - 1;
            }
        }
        return -1;
    }

    //二分查找第一个 >= target 的元素
    public int left_bound(int[] nums, int target){
        int left = 0, right = nums.length - 1;//闭区间
        while(left <= right){
            int mid = left + (right - left) / 2;
            if(nums[mid] <  target){
                //[mid + 1, right]
                left = mid + 1;
            }else{
                //[left, mid - 1]
                right = mid - 1;
            }
        }
        return left;
    }
    // 二分查找最后一个 <= target 的元素
    // left = right 跳出循环前一步
    // 如果nums[right] > target  right = mid - 1 < left 循环结束
    // 如果nums[right] < target  left = mid + 1 > right 循环结束
    public int right_bound(int[] nums, int target){
        int left = 0, right = nums.length - 1;
        while(left <= right){
            int mid = left + (right - left) / 2;
            if(nums[mid] > target){
                //[left, mid - 1]
                right = mid - 1;
            }else{
                //[mid + 1, right]
                left = mid + 1;
            }
        }
        return right;
    }
}
```

[50. Pow(x, n)](https://leetcode.cn/problems/powx-n/)

实现 [pow(*x*, *n*)](https://www.cplusplus.com/reference/valarray/pow/) ，即计算 `x` 的整数 `n` 次幂函数。

思路： 使用二分思想递归

```java
class Solution {
    public double myPow(double x, int n) {
        if(n == 0) return 1;
        if(n == 1) return x;
        if(n == -1) return 1 / x;
        double half = myPow(x, n / 2);
        double rest = myPow(x, n % 2);
        return rest * half * half;
    }
}
```

[69. x 的平方根](https://leetcode.cn/problems/sqrtx/)

给你一个非负整数 `x` ，计算并返回 `x` 的 **算术平方根** 。

由于返回类型是整数，结果只保留 **整数部分** ，小数部分将被 **舍去 。**

**注意：** 不允许使用任何内置指数函数和算符，例如 `pow(x, 0.5)` 或者 `x ** 0.5` 。

思路： 二分查找闭区间【1， x】 找到最后一个 <= target 的元素变种

```java
class Solution {
    public int mySqrt(int x) {
        int left = 1, right = x;
        while(left <= right){
            int mid = ((right - left) >> 1) + left;
            if(x / mid > mid){
                left = mid + 1;
            }else if(x / mid < mid){
                right = mid - 1;
            }else{
                return mid;
            }
        }
        return right;
    }
}
```

[33. 搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

整数数组 `nums` 按升序排列，数组中的值 **互不相同** 。

在传递给函数之前，`nums` 在预先未知的某个下标 `k`（`0 <= k < nums.length`）上进行了 **旋转**，使数组变为 `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]`（下标 **从 0 开始** 计数）。例如， `[0,1,2,4,5,6,7]` 在下标 `3` 处经旋转后可能变为 `[4,5,6,7,0,1,2]` 。

给你 **旋转后** 的数组 `nums` 和一个整数 `target` ，如果 `nums` 中存在这个目标值 `target` ，则返回它的下标，否则返回 `-1` 。

你必须设计一个时间复杂度为 `O(log n)` 的算法解决此问题。

思路：

以二分搜索为基础。

- nums[0]<=nums[mid], (0-mid 不包含旋转） 且nums[0]<=target<=nums[mid]时 right= mid - 1向前规约
- nums[mid] < nums[0]（0 - mid 包含旋转） 且target<=nums[mid]<nums[0]时，target在旋转位置到mid之间，向前规约
- nums[mid] < nums[0], nums[mid] < nums[0] <=target， target在旋转位置到0之间， 向前规约
- 其他情况向后规约

也就是说`nums[mid] < nums[0]`，`nums[0] > target`，`target > nums[mid]` 三项均为真或者只有一项为真时向后规约。使用异或操作简化判断。

```java
class Solution {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while(left <= right){
            int mid = ((right - left) >> 1) + left;
            if((nums[0] > target) ^ (target > nums[mid]) ^ (nums[mid] < nums[0])){
                left = mid + 1;//向后规约
            }else{
                right = mid - 1;//向前规约
            }
        }
        return left == nums.length || nums[left] != target ? -1 : left;
    }
}
```

[34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 `target`，返回 `[-1, -1]`。

你必须设计并实现时间复杂度为 `O(log n)` 的算法解决此问题。

思路：

使用二分查找元素的上界和下界。

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int start = left_bound(nums, target);
        if(start == nums.length || nums[start] != target){
            return new int[]{-1, -1};
        }
        int end = right_bound(nums, target);
        //因为数组都是整数所以等价于找到 <= target + 1的元素下标-1 
        //int end = left_bound(nums, target + 1) - 1;
        return new int[]{start, end};

    }
    int left_bound(int[] nums, int target){
        int left = 0, right = nums.length - 1;
        while(left <= right){
            int mid = left + (right - left) / 2;
            if(nums[mid] < target){
                left = mid + 1;
            }else{
                right = mid - 1;
            }
        }
        return left;
    }
    int right_boud(int[] nums, int target){
        int left = 0, right = nums.length - 1;
        while(left <= right){
            int mid = left + (right - left) / 2;
            if(nums[mid] > target){
                right = mid - 1;
            }else{
                left = mid + 1;
            }
        }
        return right;
    }
}
```

[35. 搜索插入位置](https://leetcode.cn/problems/search-insert-position/)

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

请必须使用时间复杂度为 `O(log n)` 的算法。

思路：

二分查找>=target的变种,left为要插入的位置

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while(left <= right){
            int mid = left + (right - left) / 2;
            if(nums[mid] > target){
                right = mid - 1;
            }else if(nums[mid] == target){
                return mid;
            }else if(nums[mid] < target){
                left = mid + 1;
            }
        }
        return left;
    }
}
```

[74. 搜索二维矩阵](https://leetcode.cn/problems/search-a-2d-matrix/)

给你一个满足下述两条属性的 `m x n` 整数矩阵：

- 每行中的整数从左到右按非严格递增顺序排列。
- 每行的第一个整数大于前一行的最后一个整数。

给你一个整数 `target` ，如果 `target` 在矩阵中，返回 `true` ；否则，返回 `false` 。

思路：

矩阵从左至右升序，从上至下升序。

1. 两次二分， 先对行进行二分在对列进行二分
2. 因为将二维矩阵的行尾和行首连接，也具有单调性，所以可以当成一维矩阵进行一次二分搜索
3. 可以从矩阵右上角开始往左下开始搜索， 当前值大于target向左， 小于target向下。（抽象bst）

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        //矩阵每行首尾相连 进行一次二分
        int m = matrix.length, n = matrix[0].length;
        int left = 0, right = m * n - 1;
        while(left <= right){
            int mid = left + (right - left) / 2;
            if(matrix[mid / n][mid % n] > target){
                right = mid - 1;
            }else if(matrix[mid / n][mid % n] < target){
                left = mid + 1;
            }else{
                return true;
            }
        }
        //抽象BST 从矩阵右上角开始搜索
        // int i = 0, j = n - 1;
        // while(i < m && j >= 0){
        //     if(matrix[i][j] < target){
        //         i++;//向下
        //     }else if(matrix[i][j] > target){
        //         j--;//向左
        //     }else{
        //         return true;
        //     }
        // }
        return false;
    }
}
```

[162. 寻找峰值](https://leetcode.cn/problems/find-peak-element/)

峰值元素是指其值严格大于左右相邻值的元素。

给你一个整数数组 `nums`，找到峰值元素并返回其索引。数组可能包含多个峰值，在这种情况下，返回 **任何一个峰值** 所在位置即可。

你可以假设 `nums[-1] = nums[n] = -∞` 。

你必须实现时间复杂度为 `O(log n)` 的算法来解决此问题。

思路：

**始终选择大于边界一端进行二分，可以确保选择的区间一定存在峰值，并随着二分过程不断逼近峰值位置。**

```java
class Solution {
    public int findPeakElement(int[] nums) {
        int n = nums.length;
        //闭区间[0, n - 2], 最后一个元素必定不为峰值， 为了方便判断当前元素和右边元素大小
        int left = 0, right = n - 2;
        while(left <= right){
            int mid = left + (right - left) / 2;
            if(nums[mid] > nums[mid + 1]){
                right = mid - 1;//向左规约， 左侧存在峰值
            }else{
                left = mid + 1;//向右规约， 右侧存在峰值
            }
        }
        return left;
    }
}
```

[1901. 寻找峰值 II](https://leetcode.cn/problems/find-a-peak-element-ii/)

一个 2D 网格中的 **峰值** 是指那些 **严格大于** 其相邻格子(上、下、左、右)的元素。

给你一个 **从 0 开始编号** 的 `m x n` 矩阵 `mat` ，其中任意两个相邻格子的值都 **不相同** 。找出 **任意一个 峰值** `mat[i][j]` 并 **返回其位置** `[i,j]` 。

你可以假设整个矩阵周边环绕着一圈值为 `-1` 的格子。

要求必须写出时间复杂度为 `O(m log(n))` 或 `O(n log(m))` 的算法

思路：

利用行最大值判断峰顶位置。 二分行号，如果当前行最大值比其相邻下方值大，则存在峰顶在小于等于当前行号中，如果当前行最大值比相邻下方值小， 则存在峰顶在大于等于当前行号中， 闭区间【0， m-2】，避免额外判断i+1越界

```java
class Solution {
    public int[] findPeakGrid(int[][] mat) {
        //二分查找一个峰值
        int m = mat.length, n = mat[0].length;
        int left = 0, right = m - 2;
        while(left <= right){
            int i = left + (right - left) / 2;
            int j = indexOfMax(mat[i]);
            if(mat[i][j] > mat[i + 1][j]){
                right = i - 1;
            }else{
                left = i + 1;
            }
        }
        return new int[]{left, indexOfMax(mat[left])};
    }
    //当前行最大值下标
    int indexOfMax(int[] a){
        int idx = 0;
        for(int i = 0; i < a.length; i++){
            if(a[i] > a[idx]){
                idx = i;
            }
        }
        return idx;
    }
}
```

[378. 有序矩阵中第 K 小的元素](https://leetcode.cn/problems/kth-smallest-element-in-a-sorted-matrix/)

给你一个 `n x n` 矩阵 `matrix` ，其中每行和每列元素均按升序排序，找到矩阵中第 `k` 小的元素。

请注意，它是 **排序后** 的第 `k` 小元素，而不是第 `k` 个 **不同** 的元素。

你必须找到一个内存复杂度优于 `O(n2)` 的解决方案。

思路：

1. 将矩阵每行看作一个有序数组， n个有序数组的第k小的元素，归并排序n个有序的数组，使用优先队列维护。
2. 二分方法， matrix[0][0]为最小值, matrix[n-1][n-1] 为最大值，对于mid，左上都是比mid小的， 右下都是比mid大的。锯齿线分割两部分。

```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int left = matrix[0][0], right = matrix[n - 1][n - 1];
        while(left <= right){
            int mid = left + (right - left) / 2;
            if(check(matrix, mid, k, n)){
                //数量超过k， 向左规约
                right = mid - 1;
            }else{
                left = mid + 1;
            }
        }
        return left;
    }
    //计算小于等于mid的个数
    boolean check(int[][] matrix, int mid, int k, int n){
        int i = n - 1;
        int j = 0;
        int num = 0;
        while(i >= 0 && j < n){
            if(matrix[i][j] <= mid){
                num += i + 1;
                j++;
            }else{
                i--;
            }
        }
        return num >= k;
    }
}
```

[1539. 第 k 个缺失的正整数](https://leetcode.cn/problems/kth-missing-positive-number/)

给你一个 严格升序排列 的正整数数组 arr 和一个整数 k 。

请你找到这个数组里第 k 个缺失的正整数。

思路：

由于数组严格升序， 当数组不缺失元素时，arr[i] = i + 1;

所以当arr[i] - i - 1 > 0 时缺失元素， 等于k时代表缺失k个元素。

```java
class Solution {
    public int findKthPositive(int[] arr, int k) {
        //  不缺失时arr[i] = i + 1,
        //  当arr[i] - i - 1 == k 时即是第k个缺失的
        int left = 0, right = arr.length - 1;
        while(left <= right){
            int mid = left + (right - left) / 2;
            if(arr[mid] - mid >= k + 1){
                right = mid - 1;
            }else{
                left = mid + 1;
            }
        }
        return k + left;
    }
}
```

[300. 最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)

给你一个整数数组 `nums` ，找到其中最长严格递增子序列的长度。

**子序列**  是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，`[3,6,2,7]` 是数组 `[0,3,1,6,2,2,7]` 的子序列。

思路：

1. 动态规划，dp[i]以当前元素为结尾的最长子序列。
2. 动态规划 + 二分查找，降低搜索dp数组的时间复杂度

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int n = nums.length;
        int[] dp = new int[n];
        int ans = 0;
        for(int num : nums){
            int left = 0, right = ans - 1;
            while(left <= right){
                int mid = left + (right - left) / 2;
                if(dp[mid] < num) left = mid + 1;
                else{
                    right = mid - 1;
                }
            }
            dp[left] = num;
            if(ans == right + 1) ans++;
        }
        return ans;
    }
}
```
