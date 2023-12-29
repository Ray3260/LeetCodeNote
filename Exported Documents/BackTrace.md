* 回溯算法和DFS类似，本质上是一种暴力穷举算法。回溯算法是在遍历【树枝】，DFS是在遍历【节点】。
  解决一个回溯问题，实际上就是遍历一棵决策树，树的每个节点存放一个合法答案，将整棵树遍历一遍，将叶子结点的答案收集起来就能得到所有合法答案。
* 站在回溯树的一个节点上，需要思考3个问题：

  1. 路径： 也就是已经作出的选择
  2. 选择列表：也就是当前可以做的选择
  3. 结束条件：也就是到达决策树的底层，无法再做选择的条件
* 全排列 和 N皇后 问题是经典回溯问题。

  ```
  # 回溯算法模板
  result = []
  def backtrack(路径, 选择列表):
      if 满足结束条件:
          result.add(路径)
          return

      for 选择 in 选择列表:
          做选择
          backtrack(路径, 选择列表)
          撤销选择


  ```

## 从多重循环到回溯

* 17. [17. 电话号码的字母组合](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)
      给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。答案可以按 任意顺序 返回。
      给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。
      思路：
      多层循环无法统一表达能力有局限，使用回溯算法。
      首先将数字和字母对应起来（map数组），然后枚举每个数字。

  ```java
      class Solution {
          private static final String[] mapping = new String[]{"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
          private final List<String> ans = new ArrayList<>();
          private char[] digits, path;

          public List<String> letterCombinations(String digits) {
              int n = digits.length();
              if(n == 0) return List.of();
              this.digits = digits.toCharArray();
              path = new char[n];
              backtrace(0);
              return ans;
          }
          private void backtrace(int i){
              if(i == digits.length){
                  ans.add(new String(path));
                  return;
              }
              for(char c : mapping[digits[i] - '0'].toCharArray()){
                  path[i] = c;
                  backtrace(i + 1);
              }
          }
      }
  ```

## 子集型回溯

* [78. 子集](https://leetcode.cn/problems/subsets/)
  给你一个整数数组 nums ，数组中的元素 互不相同 。返回该数组所有可能的子集（幂集）。
  解集 不能 包含重复的子集。你可以按 任意顺序 返回解集。
  思路：
  回溯模板
  ```java
      class Solution {
          List<List<Integer>> res = new ArrayList();
          public List<List<Integer>> subsets(int[] nums) {
              LinkedList<Integer> track = new LinkedList();
              backtrace(nums, track, 0);
              return res;
          }
          void backtrace(int[] nums, LinkedList<Integer> track, int start){
              res.add(new LinkedList<Integer>(track));//从答案视角（选哪个数）
              for(int i = start; i < nums.length; i++){
                  track.addLast(nums[i]); //做选择
                  backtrace(nums, track, i + 1);
                  track.removeLast(); //撤销选择
              }
          }
      }
  ```
* [131. 分割回文串](https://leetcode.cn/problems/palindrome-partitioning/)
  给你一个字符串 s，请你将 s 分割成一些子串，使每个子串都是 回文串 。返回 s 所有可能的分割方案。
  回文串 是正着读和反着读都一样的字符串。
  思路：
  回溯算法，从 s 的头部开始暴力穷举，如果发现 s[0..i] 是一个回文子串，那么我们就可以把 s 切分为 s[0..i] 和 s[i+1..]，然后我们去尝试把 s[i+1..] 去暴力切分成多个回文子串即可。
  ```java
      class Solution {
          List<List<String>> ans = new LinkedList<>();
          LinkedList<String> path = new LinkedList<>();

          public List<List<String>> partition(String s) {
              backtrace(s, 0);
              return ans;
          }
          private void backtrace(String s, int start){
              if(start == s.length()){
                  ans.add(new LinkedList<String>(path));
                  return;
              }
              for(int i = start; i < s.length(); i++){
                  if(!isPalindrome(s, start, i)){
                      continue;
                  }
                  path.addLast(s.substring(start, i + 1));
                  backtrace(s, i + 1);
                  path.removeLast();
              }
          }
          boolean isPalindrome(String s, int left, int right){
              while(left < right){
                  if(s.charAt(left) != s.charAt(right)){
                      return false;
                  }
                  left++;
                  right--;
              }
              return true;
          }
      }
  ```

## 组合型回溯+剪枝

* 在选子集的过程中加了一个判断过程，即剪枝过程。
* [77. 组合](https://leetcode.cn/problems/combinations/)
  给定两个整数 n 和 k，返回范围 [1, n] 中所有可能的 k 个数的组合。
  你可以按 任何顺序 返回答案。
  思路：
  回溯模板，当path长度等于k为边界。 可以从n开始回溯，使用剪枝，当剩余数字不够选择时直接结束。

  ```java
      //回溯模板
      class Solution {
          List<List<Integer>> ans = new LinkedList<>();
          List<Integer> path = new LinkedList<>();
          public List<List<Integer>> combine(int n, int k) {
              if(n <= 0 || k <= 0){
                  return List.of();
              }
              backtrace(n, k, 1);
              return ans;
          }
          private void backtrace(int n, int k, int start){
              if(k == path.size()){
                  ans.add(new LinkedList<>(path));
                  return;
              }
              for(int i = start; i <= n; i++){
                  path.addLast(i);
                  backtrace(n, k, i + 1);
                  path.removeLast();
              }
          }
      }
  ```
  ```java
      //剪枝，从n开始
      class Solution {
          List<List<Integer>> ans = new LinkedList<>();
          List<Integer> path = new LinkedList<>();
          public List<List<Integer>> combine(int n, int k) {
              if(n <= 0 || k <= 0){
                  return List.of();
              }
              backtrace(n, k, n);
              return ans;
          }
          private void backtrace(int n, int k, int start){
              int d = k - path.size();//记录剩余个数
              if(d == 0){
                  ans.add(new LinkedList<>(path));
                  return;
              }
              for(int i = start; i >= d; i--){//剪枝
                  path.addLast(i);
                  backtrace(n, k, i - 1);
                  path.removeLast();
              }
          }
      }
  ```
* [216. 组合总和 III](https://leetcode.cn/problems/combination-sum-iii/)
  找出所有相加之和为 n 的 k 个数的组合，且满足下列条件：
  只使用数字1到9
  每个数字 最多使用一次
  返回 所有可能的有效组合的列表 。该列表不能包含相同的组合两次，组合可以以任何顺序返回。
  思路：
  组合回溯 + 剪枝（加上剩余数字不够k个， 剩余数字即使都选最大也不够n）

  ```java
      class Solution {
          List<List<Integer>> ans = new LinkedList<>();
          List<Integer> path = new LinkedList<>();
          public List<List<Integer>> combinationSum3(int k, int n) {
              backtrace(k, n, 9);
              return ans;
          }
          private void backtrace(int k, int n, int start){
              int d = k - path.size();
              //剪枝
              if(n < 0 || n > (start * 2 - d + 1) * d / 2) return;
              if(d == 0){
                  ans.add(new LinkedList<>(path));
                  return;
              }
              //回溯模板 枚举下一个数选择哪个
              for(int i = start; i >= d; i--){
                  path.addLast(i);
                  backtrace(k, n - i, i - 1);
                  path.removeLast();
              }
          }
      }
  ```  
* [22. 括号生成](https://leetcode.cn/problems/generate-parentheses/)
    数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。
    思路：
    组合回溯， 需要记录左括号的数量，根据左括号数量进行剪枝操作
    ```java
        class Solution {
            //从2n个位置选择n个左括号，剪枝过程，右括号数量不能超过左括号数量start - cnt < cnt
            //start 为当前括号数量， cnt为左括号数量
            List<String> ans = new ArrayList<>();
            StringBuilder path = new StringBuilder(); 
            public List<String> generateParenthesis(int n) {
                if(n == 0) return List.of();
                //括号数量0-2n，左括号数量0-n
                backtrace(0, 0, n);
                return ans;
            }
            private void backtrace(int start, int cnt, int n){
                if(start == 2 * n){
                    ans.add(new String(path));
                    return;
                }
                //枚举选择左括号还是右括号
                if(cnt < n){//可以选择左括号
                    path.append('(');
                    backtrace(start + 1, cnt + 1, n);
                    path.deleteCharAt(path.length() - 1);
                }
                if(start - cnt < cnt){
                    //可以选择右括号
                    path.append(')');
                    backtrace(start + 1, cnt, n);
                    path.deleteCharAt(path.length() - 1);
                }
            }
        }
    ```
