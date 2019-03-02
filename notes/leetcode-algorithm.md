<!-- GFM-TOC -->
* [并查集](#并查集)
  * [岛屿的数量1](#岛屿的数量1)
  * [岛屿的数量2](#岛屿的数量2)
<!-- GFM-TOC -->


## 并查集

并查集主要解决连通区域问题，UF唯一需要注意的是union的条件；
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
