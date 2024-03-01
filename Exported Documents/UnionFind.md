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
