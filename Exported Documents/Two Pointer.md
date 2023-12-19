# Two Pointer

- 双指针分类
      1. 滑动窗口（同向双指针，例： 无重复字符最长子串）
      2. 快慢指针（例： 环形链表， 重排链表）
      3. 相向双指针（例：三数之和， 四数之和， 接雨水， 前后缀分解）
      4. 前后指针（例：链表删除， 去重）
- 双指针秒杀链表问题

      1、合并两个有序链表

      2、链表的分解

      3、合并 `k` 个有序链表

      4、寻找单链表的倒数第 `k` 个节点

      5、寻找单链表的中点

      6、判断单链表是否包含环并找出环起点

      7、判断两个单链表是否相交并找出交点

- 子数组子串问题一般用到滑动窗口方法

[15. 三数之和](https://leetcode.cn/problems/3sum/)

给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请

你返回所有和为 `0` 且不重复的三元组。

**注意：** 答案中不可以包含重复的三元组。

思路：

   将数组排序， 遍历第一个数然后考虑两数之和问题（双指针逼近target）。

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> ans = new ArrayList<>();
        Arrays.sort(nums);
        int n = nums.length;
        for(int i = 0; i < n - 2; i++){
            //去重
            if(i > 0 && nums[i] == nums[i - 1]) continue;
            int j = i + 1, k = n - 1;
            while(j < k){
                if(nums[i] + nums[j] + nums[k] == 0){
                    ans.add(new ArrayList<Integer>(Arrays.asList(nums[i], nums[j], nums[k])));
                    //去重复  
                    while(j < k && nums[j] == nums[j + 1]){
                        j++;
                    }
                    while(j < k && nums[k] == nums[k - 1]){
                        k--;
                    }
                    j++;
                    k--;
                }else if(nums[i] + nums[j] + nums[k] > 0){
                    k--;
                }else{
                    j++;
                }
            }
        }
        return ans;
    }
}
```

[18. 四数之和](https://leetcode.cn/problems/4sum/)

给你一个由 `n` 个整数组成的数组 `nums` ，和一个目标值 `target` 。请你找出并返回满足下述全部条件且**不重复**的四元组 `[nums[a], nums[b], nums[c], nums[d]]` （若两个四元组元素一一对应，则认为两个四元组重复）：

- `0 <= a, b, c, d < n`
- `a`、`b`、`c` 和 `d` **互不相同**
- `nums[a] + nums[b] + nums[c] + nums[d] == target`

你可以按 **任意顺序** 返回答案 。

思路：

   和三数之和类似，增加一些剪枝操作。

```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        //参考三数之和 外层多一层遍历
        int n = nums.length;
        List<List<Integer>> res = new ArrayList<>();
        if(n < 4){
            return res;
        }
        Arrays.sort(nums);
        for(int a = 0; a < n - 3; a++){
            if(a > 0 && nums[a] == nums[a - 1]) continue;//去重复
            if((long)nums[a] + nums[a + 1] + nums[a + 2] + nums[a + 3] > target) break;//通过边界判断降低时间复杂度
            if((long)nums[a] + nums[n - 1] + nums[n - 2] + nums[n - 3] < target) continue;
            //三数之和
            for(int b = a + 1; b < n - 2; b++){
                if(b > a + 1 && nums[b] == nums[b - 1]) continue;
                if((long)nums[a] + nums[b] + nums[b + 1] + nums[b + 2] > target) break;
                if((long)nums[a] + nums[b] + nums[n -1] + nums[n - 2] < target) continue;
                int c = b + 1, d = n - 1;
                //两数之和
                while(c < d){
                    long sum = (long)nums[a] + nums[b] + nums[c] + nums[d];
                    if(sum == target){
                        res.add(new ArrayList<>(Arrays.asList(nums[a], nums[b], nums[c], nums[d])));
                        while(c < d && nums[c] == nums[c + 1]){
                            c++;
                        }
                        while(c < d && nums[d] == nums[d - 1]){
                            d--;
                        }
                        c++;
                        d--;
                    }else if(sum < target){
                        c++;
                    }else{
                        d--;
                    }
                }
            }
        }
        return res;

    }
}
```

[11. 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)

给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。

找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

**说明：** 你不能倾斜容器

思路：

   双指针移动柱子高度小的那个， 记录每次移动的最大容积。

```java
class Solution {
    public int maxArea(int[] height) {
        int res = 0;
        int left = 0, right = height.length - 1;
        while(left < right){
            int area = Math.min(height[left], height[right]) * (right - left);
            res = Math.max(res, area);
            if(height[right] > height[left]){
                left++;
            }else{
                right--;
            }
        }
        return res;
    }
}
```

[42. 接雨水](https://leetcode.cn/problems/trapping-rain-water/)

给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

![Image.png](https://res.craft.do/user/full/00fdd841-1e86-3ba3-5070-eea015553c54/doc/A33619DB-1A61-4316-A620-8E17B96CB991/87E3E2DE-45D5-4027-AD3F-04D76B3D9BF6_2/dax842z0Lr2NULd0QsxsJbFEA66dBu0TP2ZxU3yhlBUz/Image.png)

思路：

   1. 前后缀分解， 需要两个数组分别存储当前值前缀最大值和后缀最大值

```java
water[i] = min(
           # 左边最高的柱子
           max(height[0..i]),
           # 右边最高的柱子
           max(height[i..end])
        ) - height[i]
```

```java
class Solution {
    public int trap(int[] height) {
        int res = 0;
        int n = height.length;
        //water[i] = min(max(height[0...i]), max(height[i...end])) - height[i];
        int[] max_l = new int[n];
        int[] max_r = new int[n];
        max_l[0] = height[0];
        max_r[n - 1] = height[n - 1];
        for(int i = 1; i < n; i++){
            max_l[i] = Math.max(height[i], max_l[i - 1]);
        }
        for(int i = n - 2; i >= 0; i--){
            max_r[i] = Math.max(height[i], max_r[i + 1]);
        }
        for(int i = 1; i < n - 1; i++){
            res += Math.min(max_l[i], max_r[i]) - height[i];
        }
        return res;
    }
}
```

   2. 相向双指针， 不需要额外数组空间， 当前缀最大值小于后缀最大值时， 左边的雨水容积则确定了， 反之右边的雨水容积确定。继续向内扩展。

```java
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        int ans = 0;
        int left = 0, right = n - 1;
        int premax = 0, sufmax = 0;
        while(left < right){
            premax = Math.max(premax, height[left]);
            sufmax = Math.max(sufmax, height[right]);
            ans += premax < sufmax ? premax - height[left++] : sufmax - height[right--];
        }
        return ans;
    }
}
```

