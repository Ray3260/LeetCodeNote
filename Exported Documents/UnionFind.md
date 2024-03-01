## 并查集
并查集是一种用于管理元素所属集合的数据结构，实现为一个森林，其中每棵树表示为一个集合，树中的节点表示对应集合中的元素。
并查集支持两种操作：
* 合并(Union)：合并两个元素所属集合
* 查询(Find)：查询某个元素所属集合（查询对应的树的根节点），这可用于判断两个元素是否属于同一集合

## 代表元法（quick-union:基于parent）
* 记录每个顶点的父亲顶点是谁，这种设计并查集的思想也叫代表元法。
* 代表元的三个不重要：
  * 谁作为根节点不重要：根节点与非根节点只是位置不同，没有附加含义
  * 树怎么形成的不重要：合并的时候任何一个集合的根节点指向另一个集合的根节点即可
  * 树的形态不重要
* 代表元可能造成链式结构，导致树的高度太深，影响效率
* 解决方法：
  * 路径压缩
    * 隔代压缩：两步一跳，将当前节点指向父亲节点的父亲节点
    * 完全压缩：把从「查询结点」到「根结点」沿途经过的所有结点都指向根结点
  * 按秩合并：让秩较小的树的根节点指向秩较大的树的根节点，秩有两种含义（按size合并、按rank合并），按size合并是让树的节点总数小的树的根节点指向节点数较大的树的根节点，按rank合并是让树的高度较小的根节点指向高度较大的树的根节点。如果同时使用按秩合并和路径压缩时，树的高度很难准确维护。

## 并查集模板：
```java
class UnionFind{
    private int cnt;//记录集合（连通分量）的数量
    private int[] parent;//代表元法
    public UnionFind(int n){
        parent = new int[n];
        for(int i = 0; i < n; i++){
            parent[i] = i;
        }
        cnt = n;
    }
    public void union(int i, int j){
       if(isConnected(i, j)) return;
       cnt--;
       parent[find(i)] = find(j);
    }
    public int find(int i){
        while(parent[i] != i){ //当前节点非根节点进行路经压缩
            parent[i] = parent[parent[i]];//隔代压缩，指向父亲的父亲
            i = parent[i];
        }
        return i;
    }
    public int getCount(){
        return cnt;
    }
    public boolean isConnected(int i, int j){
        return find(i) == find(j);
    }
}
```
## 等式方程的可满足性
给定一个由表示变量之间关系的字符串方程组成的数组，每个字符串方程 equations[i] 的长度为 4，并采用两种不同的形式之一："a==b" 或 "a!=b"。在这里，a 和 b 是小写字母（不一定不同），表示单字母变量名。

只有当可以将整数分配给变量名，以便满足所有给定的方程时才返回 true，否则返回 false。
```
输入：["a==b","b!=a"]
输出：false
解释：如果我们指定，a = 1 且 b = 1，那么可以满足第一个方程，但无法满足第二个方程。没有办法分配变量同时满足这两个方程。
```

提示：

* 1 <= equations.length <= 500
* equations[i].length == 4
* equations[i][0] 和 equations[i][3] 是小写字母
* equations[i][1] 要么是 '='，要么是 '!'
* equations[i][2] 是 '='

思路：使用并查集将等式两边的字母union到同一个集合，再find不等式的字母如果在同一个集合则返回false，反之返回true。
```java
class Solution {
    public boolean equationsPossible(String[] equations) {
        UnionFind uf = new UnionFind(26);
        for(String e : equations){
            if(e.charAt(1) == '='){
                uf.union(e.charAt(0) - 'a', e.charAt(3) - 'a');
            }
        }
        for(String e : equations){
            if(e.charAt(1) == '!'){
                if(uf.isconnected(e.charAt(0) - 'a', e.charAt(3) - 'a')){
                    return false;
                }
            }
        }
        return true;
    }
    class UnionFind{
        private int cnt;
        private int[] parent;
        public UnionFind(int n){
            parent = new int[n];
            for(int i = 0; i < n; i++){
                parent[i] = i;
            }
            cnt = n;
        }
        public void union(int i, int j){
            if(isconnected(i, j)) return;
            cnt--;
            parent[find(i)] = find(j);
        }
        public int find(int i){
            while(parent[i] != i){
                parent[i] = parent[parent[i]];
                i = parent[i];
            }
            return i;
        }
        public boolean isconnected(int i , int j){
            return find(i) == find(j);
        }
        public int getcount(){
            return cnt;
        }
    }
}
```

## 省份数量
有 n 个城市，其中一些彼此相连，另一些没有相连。如果城市 a 与城市 b 直接相连，且城市 b 与城市 c 直接相连，那么城市 a 与城市 c 间接相连。

省份 是一组直接或间接相连的城市，组内不含其他没有相连的城市。

给你一个 n x n 的矩阵 isConnected ，其中 isConnected[i][j] = 1 表示第 i 个城市和第 j 个城市直接相连，而 isConnected[i][j] = 0 表示二者不直接相连。

返回矩阵中 省份 的数量。
```java
class Solution {
    public int findCircleNum(int[][] isConnected) {
        int n = isConnected.length;
        UnionFind uf = new UnionFind(n);
        for(int i = 0; i < n; i++){
            for(int j = i + 1; j < n; j++){
                if(isConnected[i][j] == 1){
                    uf.union(i, j);
                }
            }
        }
        return uf.getcount();
    }
    class UnionFind{
        private int cnt;
        private int[] parent;
        public UnionFind(int n){
            parent = new int[n];
            for(int i = 0; i < n; i++){
                parent[i] = i;
            }
            cnt = n;
        }
        public void union(int i, int j){
            if(isconnected(i, j)) return;
            cnt--;
            parent[find(i)] = find(j);
        }
        public int find(int i){
            while(parent[i] != i){
                parent[i] = parent[parent[i]];
                i = parent[i];
            }
            return i;
        }
        public boolean isconnected(int i , int j){
            return find(i) == find(j);
        }
        public int getcount(){
            return cnt;
        }
    }
    
}
```
## 情侣牵手
n 对情侣坐在连续排列的 2n 个座位上，想要牵到对方的手。

人和座位由一个整数数组 row 表示，其中 row[i] 是坐在第 i 个座位上的人的 ID。情侣们按顺序编号，第一对是 (0, 1)，第二对是 (2, 3)，以此类推，最后一对是 (2n-2, 2n-1)。

返回 最少交换座位的次数，以便每对情侣可以并肩坐在一起。 每次交换可选择任意两人，让他们站起来交换座位。

```
示例 1:
输入: row = [0,2,1,3]
输出: 1
解释: 只需要交换row[1]和row[2]的位置即可。

提示:
2n == row.length
2 <= n <= 30
n 是偶数
0 <= row[i] < 2n
row 中所有元素均无重复
```

思路：使用并查集获得n个联通分量，每个联通分量表示逻辑上连在一起的情侣，逻辑相连：比如三对情侣首尾相连，即使每一对都做错位置，但是这三对之间互相交换则可以做对位置。只有一对情侣时是做对位置的。\
假设一共有N对情侣，包括逻辑上连在一起的情侣，分别由N1、N2、N3、··· 、Nn对，n为并查集连通分量数量，N1 + N2 + ··· + Nn = N, 把逻辑上相连的情侣拆开，至少需要交换N1 - 1、N2 - 1、··· 、Nn - 1次
最少交换次数 = N1 - 1 + N2 - 1 + ··· + Nn - 1 = N - n = 情侣对数 - 连通分量数量

```java
class Solution {
    public int minSwapsCouples(int[] row) {
        //交换次数 = 未正确情侣对数 - 1
        //交换次数 = 交换后的连通分量个数（n对情侣） - 交换前的连通分量个数（cnt）
        int n = row.length;
        UnionFind uf = new UnionFind(n);
        for(int i = 0; i < n; i += 2){
            uf.union(row[i], row[i + 1]);
        }
        return n / 2 - uf.getCount();
    }
    private class UnionFind{
        private int cnt;
        private int[] parent;

        public UnionFind(int n){
            //n为偶数
            parent = new int[n];
            for(int i = 0; i < n; i++){
                parent[i] = i / 2 * 2;//两个为一对，左边为父亲节点
            }
            cnt = n / 2;
        }
        public int find(int i){
            while(parent[i] != i){
                parent[i] = parent[parent[i]];
                i = parent[i];
            }
            return i;
        }
        public void union(int i, int j){
            if(isConnected(i, j)) return;
            cnt--;
            parent[find(i)] = find(j);
        }
        public boolean isConnected(int i, int j){
            return find(i) == find(j);
        }
        public int getCount(){
            return cnt;
        }
    }
}
```

