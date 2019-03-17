<!-- GFM-TOC -->
* [深度优先搜索](#DFS)
  * [连通问题搜索-无向图连通元素的数量](#无向图连通元素的数量)
* [广度优先搜索](#BFS)
  * [连通问题搜索-无向图连通元素的数量](#无向图连通元素的数量)
* [并查集](#并查集)
  * [静态连通问题-岛屿的数量1](#岛屿的数量1)
  * [动态连通问题-岛屿的数量2](#岛屿的数量2)
<!-- GFM-TOC -->

## DFS

深度优先搜索，采用函数栈区进行搜索，一般采用一些数据结构作为已搜索的标记；唯一需要注意的是题目切入点以及中止条件

#### 无向图连通元素的数量

🔒[323. Number of Connected Components in an Undirected Graph](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/)

```
 Medium (51.4%)
 Cost: 15min
```

```html
Input: n = 5 and edges = [[0, 1], [1, 2], [3, 4]]

     0          3
     |          |
     1 --- 2    4 

Output: 2
```

```html
Input: n = 5 and edges = [[0, 1], [1, 2], [2, 3], [3, 4]]

     0           4
     |           |
     1 --- 2 --- 3

Output:  1
```

题目描述：一张含n节点的无向图，以及一组无向边，编写一个函数来查找无向图中连接的组件的数量。

> **分析** 解法DFS/BFS/并查集，DFS时间复杂度O(e + n), 并查集时间复杂度O(e + n)(路径压缩的权重quickUnion算法union接近O(1));e是edges的长度，n是节点数量

```java
public class Solution {
    // 搜索区域
    private Map<Integer, List<Integer>> relation = new HashMap<>();
    
    // 已搜索的标志，当然也可以用Set
    private boolean[] hasSearch;

    public int countComponents(int n, int[][] edges) {
        if (n < 1) {
            return 0;
        }
        hasSearch = new boolean[n];
        for (int i = 0; i < n; i++) {
            relation.put(i, new LinkedList<>());
        }

        // 关键：构建一个搜索区域，不像《岛屿的数量》题目直接构建好了搜索区域int[][] grid;
        for (int i = 0; i < edges.length; i++) {
            relation.get(edges[i][0]).add(edges[i][1]);
            relation.get(edges[i][1]).add(edges[i][0]);
        }

        int count = 0;
        
        // 连通图问题的计算数量的模版
        for (int i = 0; i < n; i++) {
            if (!hasSearch[i]) {
                dfs(i);
                count++;
            }
        }
        return count;
    }

    private void dfs(int i) {
        for (Integer son : relation.get(i)) {
            if (!hasSearch[son]) {
                hasSearch[son] = true;
                dfs(son);
            }
        }
    }
}
```

## BFS

广度优先搜索，技术上借助队列进行搜索，一般采用一些数据结构作为已搜索的标记；唯一需要注意的是构建搜索区域

#### 无向图连通元素的数量

题目描述：一张含n节点的无向图，以及一组无向边，编写一个函数来查找无向图中连接的组件的数量

> **分析** 时间复杂度O(e + n), 参见上面BFS里面的题目描述


🔒[323. Number of Connected Components in an Undirected Graph](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/)

```java
public class Solution {
    private Map<Integer, List<Integer>> relation = new HashMap<>();
    private boolean[] hasSearch;

    public int countComponents(int n, int[][] edges) {
        if (n < 1) {
            return 0;
        }
        hasSearch = new boolean[n];
        for (int i = 0; i < n; i++) {
            relation.put(i, new LinkedList<>());
        }

        for (int i = 0; i < edges.length; i++) {
            relation.get(edges[i][0]).add(edges[i][1]);
            relation.get(edges[i][1]).add(edges[i][0]);
        }

        int count = 0;
        Queue<Integer> queue = new LinkedList<>();

        for (int i = 0; i < n; i++) {
            if (!hasSearch[i]) {
                count++;
                queue.clear();
                queue.add(i);
                while (!queue.isEmpty()) {
                    int cur = queue.poll();
                    if (!hasSearch[cur]) {
                        hasSearch[cur] = true;
                        for (Integer e : relation.get(cur)) {
                            if (!hasSearch[e]) {
                                queue.offer(e);
                            }
                        }
                    }
                }
            }
        }
        return count;
    }
}

```

#### 去除无效的括号

[301. Remove Invalid Parentheses](https://leetcode.com/problems/remove-invalid-parentheses/)

```
 Hard (38.6%)
 Cost: 15min
```

```html
Input: "()())()"
Output: ["()()()", "(())()"]
```

```html
Input: "(a)())()"
Output: ["(a)()()", "(a())()"]
```

```html
Input: ")("
Output: [""]
```

题目描述：去除最小数量的无效括号，保证输入的括号有效，输出所有可能。

> **分析** 此题适合BFS，按照删除的字符数量来分层，每个节点的子节点数量<=当前字符串的长度；因为涉及到最小数量，BFS可以知道满足条件的level，及时停止搜索；每层表示 - 去除${level}个括号的字符串；root是原字符串，第一层是去除一个括号的字符串；

```java
public class P301_RemoveInvalidParentheses {
    public List<String> removeInvalidParentheses(String s) {
        List<String> res = new LinkedList<>();
        Queue<String> queue = new LinkedList<>();
        Set<String> visited = new HashSet<>();
        queue.add(s);
        boolean found = false;
        while (!queue.isEmpty()) {
            String cur = queue.poll();
            if (isValidParentheses(cur)) {
                res.add(cur);
                found = true;
            }

            if (found) {
                continue;
            }

            for (int i = 0; i < cur.length(); i++) {
                if (cur.charAt(i) != '(' && cur.charAt(i) != ')') {
                    continue;
                }
                String sub = cur.substring(0, i) + (i == cur.length() - 1 ? "" : cur.substring(i + 1));
                if (visited.add(sub)) {
                    queue.offer(sub);
                }
            }
        }
        return res;
    }

    private boolean isValidParentheses(String s) {
        int count = 0;
        for (int i = 0; i < s.length(); i++) {
            char x = s.charAt(i);
            if (x == '(') {
                count++;
            } else if (x == ')') {
                count--;
            }
            if (count < 0) {
                return false;
            }
        }
        return  count == 0;
    }
}
```

## 并查集

并查集主要解决动态连通区域问题，UF唯一需要注意的是union的条件；
《算法》中比较过各种UF算法性能，quickFind的union复杂度N；quickUnion的union和find复杂度均为树的高度；加权quickUnion复杂度均为lgN；路径压缩的加权quickUnion算法复杂度接近为1；

#### 岛屿的数量1

[200. Number of Islands (Medium)](https://leetcode.com/problems/number-of-islands/)

```
 Medium (40.03%)
 Cost: 30min
```

```html
 Input:
 11110
 11010
 11000
 00000

 Output: 1
```

 ```html
 Input:
 11000
 11000
 00100
 00011

 Output: 3
```

题目描述：2D网格中1为陆地，0为水面，计算岛屿的数量；岛屿是由连通的陆地组成并且被水包围；假设网格周围都为水面。


> **分析** 该题目至少有三种解法，UF/DFS/BFS; 如下UF解法(使用路径压缩的加权quickUnion算法,union的时间复杂度接近为1)-时间和空间复杂度均为**O(row * col)** 
 
```java
class Solution {
    class UnionFind {
        private int[] union;
        // 记录岛屿大小，即连通区域数量
        private int size = 0;
        // 记录权重
        private int[] rank;
        public UnionFind(int n) {
            union = new int[n];
            rank = new int[n];
            for (int i = 0; i < n; i++) {
                // **关键：-1表示不可用的连通区域；（不需要修改gird或者HashSet来记录访问过的区域）**
                union[i] = -1;
            }
        }

        private int root(int x) {
            while (x != union[x]) {
                x = union[x];
            }
            return x;
        }

        public boolean same(int x, int y) {
            return root(x) == root(y);
        }

        public void union(int x, int y) {
            if (union[x] == -1 || union[y] == -1) return;
            if (same(x, y)) return;
            size--;
            if (rank[root(x)] < rank[root(y)]) {
                union[root(y)] = root(x);
            } else if (rank[root(x)] > rank[root(y)]) {
                union[root(x)] = root(y);
            } else {
                union[root(y)] = root(x);
                rank[root(x)]++;
            }
        }

        public int getSize() {
            return this.size;
        }

        public void add(int i) {
            if (union[i] == -1) {
                union[i] = i;
                size++;
            }
        }
    }

    public int numIslands(char[][] grid) {
        if (grid.length == 0 || grid[0].length == 0) return 0;
        int row = grid.length;
        int col = grid[0].length;
        UnionFind unionFind = new UnionFind(row * col);
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                if (grid[i][j] == '0') continue;            
                unionFind.add(i * col + j);
                if (j < col - 1) unionFind.union(i * col + j, i * col + j + 1);
                if (j > 0) unionFind.union(i * col + j, i * col + j - 1);
                if (i < row - 1) unionFind.union(i * col + j, (i + 1) * col + j);
                if (i > 0) unionFind.union(i * col + j, (i - 1) * col + j);
                
            }
        }
        return unionFind.getSize();
    }
}
```

#### 岛屿的数量2

[305. Number of Islands II (Hard)](https://leetcode.com/problems/number-of-islands-ii/)

```
 Hard (41.8 %)
 Cost: 15min
```

```html
Input: m = 3, n = 3, positions = [[0,0], [0,1], [1,2], [2,1]]
Output: [1,1,2,3]
```

题目描述：m\*n的网格区域，刚开始都是水，addLand操作会把某个区域变成陆地；动态求出所有addLand操作后的陆地连通区域数量；

> **分析** DFS/BFS不适合处理动态连通问题; 如下UF解法-时间均摊复杂度非常接近**O(k)** ，时间复杂度O(k * log mn)k代表posotions的长度，空间复杂度为O(m * n)

```java
class Solution {
    class UnionFind {
        private int union[];
        private int rank[];
        private int size = 0;
        
        public UnionFind(int n) {
            union = new int[n];
            rank = new int[n];
            for (int i = 0; i < n; i++) {
                union[i] = -1;
            }
        }
        
        private int find(int i) {
            while (i != union[i]) i = union[i];
            return i;
        }
        
        public void union(int x, int y) {
            if (union[x] == -1 || union[y] == -1) return;
            int rootx = find(x);
            int rooty = find(y);
            if (rootx == rooty) return;
            if (rank[rootx] > rank[rooty]) {
                union[rooty] = rootx;
            } else if (rank[rootx] < rank[rooty]) {
                union[rootx] = rooty;
            } else {
                union[rooty] = rootx;
                rank[rootx]++;
            }
            size--;
        }
        
        public void add(int i) {
            if (union[i] == -1) {
                union[i] = i;
                size++;
            }
        }
        
        public int getSize() {
            return size;
        }
    }
    
    public List<Integer> numIslands2(int m, int n, int[][] positions) {
        if (m <= 0 || n <= 0) return new ArrayList<Integer>();
        UnionFind uf = new UnionFind(m * n);
        List<Integer> res = new ArrayList<Integer>();
        for (int i = 0; i < positions.length; i++) {
            int r = positions[i][0];
            int c = positions[i][1];
            // assuming r and c are valid
            uf.add(r * n + c);
            if (r > 0) uf.union(r * n + c, (r - 1) * n + c);
            if (r < m - 1) uf.union(r * n + c, (r + 1) * n + c);
            if (c > 0) uf.union(r * n + c, r * n + (c - 1));
            if (c < n - 1) uf.union(r * n + c, r * n + (c + 1));
            res.add(uf.getSize());
        }
        return res;
    }
}
```
