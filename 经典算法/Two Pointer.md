# Two Pointer

- 双指针分类

  1. 滑动窗口（同向双指针，例： 无重复字符最长子串）
  2. 快慢指针（例： 环形链表， 重排链表）
  3. 相向双指针（例：三数之和， 四数之和， 接雨水， 前后缀分解）
  4. 前后指针（例：链表删除， 去重）
- 双指针秒杀链表问题（快慢指针）

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

给你一个由 `n` 个整数组成的数组 `nums` ，和一个目标值 `target` 。请你找出并返回满足下述全部条件且**不重复**的四元组 `[nums[a], nums[b], nums[c], nums[d]]` （若两个四元组元素一一对应，则认为两个四元组重复）：- `0 <= a, b, c, d < n`

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

给定一个含有 `n`个正整数的数组和一个正整数 `target`。

找出该数组中满足其总和大于等于 `target` 的长度最小的 **连续子数组** `[nums<sub>l</sub>, nums<sub>l+1</sub>, ..., nums<sub>r-1</sub>, nums<sub>r</sub>]` ，并返回其长度。如果不存在符合条件的子数组，返回 `0` 。

思路：

滑动窗口方法，扩大窗口sum >= target，窗口右移保证此条件成立。记录所有长度取最小值。

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int n = nums.length;
        int left = 0, right = 0;
        if(n == 0) return 0;
        int sum = 0;
        int ans = n + 1;
        while(right < n){
            sum += nums[right++];
            while(sum >= target){
                ans = Math.min(ans, right - left);
                sum -= nums[left++];
            }
        }
        return ans == n + 1 ? 0 : ans;
  
    }
}
```

[713. 乘积小于 K 的子数组](https://leetcode.cn/problems/subarray-product-less-than-k/)

给你一个整数数组 `nums` 和一个整数 `k` ，请你返回子数组内所有元素的乘积严格*小于* `k` 的连续子数组的数目。

思路：

数组中元素均为正数，使用滑动窗口并记录当前窗口内的乘积和mul，当mul大于k，左指针右移。记录所有的乘积小于k的子数组。

每次添加一个新元素进去， 多了x种组合，x = right - left;(窗口采用左闭右开区间，滑动窗口初始区间[0，0））

```java
class Solution {
    public int numSubarrayProductLessThanK(int[] nums, int k) {
        int n = nums.length;
        int mul = 1;
        int left = 0, right = 0;
        int ans = 0;
        if(k <= 1) return 0;
        while(right < n){
            mul *= nums[right++];
            while(mul >= k){
                mul /= nums[left++];
            }
            ans += right - left;
        }
        return ans;
    }
}
```

[3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

给定一个字符串 `s` ，请你找出其中不含有重复字符的 *最长子串* 的长度。

思路：

利用hashmap存储滑动窗口中字符出现的最后位置。

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        //滑动窗口
        int ans = 0;
        int left = 0, right = 0;
        Map<Character, Integer> map = new HashMap<>(); //char index
        while(right < s.length()){
            if(map.containsKey(s.charAt(right))){
                left = Math.max(left, map.get(s.charAt(right)) + 1);//避免删除hashmap的key操作
            }
            map.put(s.charAt(right), right);
            ans = Math.max(ans, right - left + 1);
            right++;
        }
        return ans;
    }
}
```

* 快慢指针问题

  1. 876链表的中间结点
  2. 141环形链表
  3. 142环形链表II
  4. 143重排链表

[876. 链表的中间结点](https://leetcode.cn/problems/middle-of-the-linked-list/)

给你单链表的头结点 `head` ，请你找出并返回链表的中间结点。

如果有两个中间结点，则返回第二个中间结点。

思路：

快慢指针，快指针每次走两步，慢指针每次走一步。快指针走完，慢指针正好走一半。

```java
class Solution {
    public ListNode middleNode(ListNode head) {
        ListNode fast = head, slow = head;
        while(fast != null && fast.next != null){
            fast = fast.next.next;
            slow = slow.next;
        }
        return slow;
    }
}
```

[141. 环形链表](https://leetcode.cn/problems/linked-list-cycle/)

给你一个链表的头节点 `head` ，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。**注意：`pos` 不作为参数进行传递 ** 。仅仅是为了标识链表的实际情况。

*如果链表中存在环* ，则返回 `true` 。 否则，返回 `false` 。

思路：

快慢指针， 快指针走两步，慢指针走一步，如果有环，则快指针和慢指针一定会相遇。

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        ListNode slow = head, fast = head;
        while(fast != null && fast.next != null){
            fast = fast.next.next;
            slow = slow.next;
            if(slow == fast) return true;
        }
        return false;
    }
}
```

[142. 环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)

给定一个链表的头节点  `head` ，返回链表开始入环的第一个节点。 *如果链表无环，则返回 `null`。*

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（**索引从 0 开始** ）。如果 `pos` 是 `-1`，则在该链表中没有环。**注意：`pos` 不作为参数进行传递** ，仅仅是为了标识链表的实际情况。

**不允许修改 ** 链表。

思路：

先使用快慢指针找到相遇的结点，再将快指针指向头结点，使得快慢指针同时移动一步，当再次相遇时即为环入口。

因为第一次相遇时 fast 走了2k步，slow 走了 k 步， 多走了k步，也就是环的长度的整数倍。

假设相遇点距离入口 m 步， 则head到入口距离 k-m 步。 并且从相遇点继续前进 k-m 步也正好到达入口处。

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode slow = head, fast = head;
        while(fast != null && fast.next != null){
            fast = fast.next.next;
            slow = slow.next;
            if(slow == fast){
                //存在环
                fast = head;
                while(fast != slow){
                    fast = fast.next;
                    slow = slow.next;
                }
                return fast;
            }
        }
        return null;
    }
}
```

[143. 重排链表](https://leetcode.cn/problems/reorder-list/)


给定一个单链表 `L`的头节点 `head` ，单链表 `L` 表示为：

```
L0 → L1 → … → Ln - 1 → Ln
```

请将其重新排列后变为：

```
L0 → Ln → L1 → Ln - 1 → L2 → Ln - 2 → …
```

不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

思路：

先找到链表的中间节点，将后半段链表反转， 最后将两段链表合并。

```java
class Solution {
    public void reorderList(ListNode head) {
        //find middle node
        ListNode middle = findMiddle(head);
    
        ListNode head1 = head;
        ListNode head2 = middle.next;
        middle.next = null;
        //反转后半部分链表
        head2 = reverse(head2);
        //合并两个链表
        merge(head1, head2);
    }
    ListNode findMiddle(ListNode head){
        ListNode slow = head, fast = head;
        while(fast != null && fast.next != null){
            fast = fast.next.next;
            slow = slow.next;
        }
        return slow;
    }
    ListNode reverse(ListNode head){
        ListNode pre = null;
        ListNode cur = head;
        while(cur != null){
            ListNode tmp = cur.next;
            cur.next = pre;
            pre = cur;
            cur = tmp;
        }
        return pre;
    }
    void merge(ListNode head1, ListNode head2){
        ListNode l1, l2;
        while(head1 != null && head2 != null){
            l1 = head1.next;
            l2 = head2.next;

            head1.next = head2;
            head1 = l1;

            head2.next = head1;
            head2 = l2;
        }
    }
}
```
