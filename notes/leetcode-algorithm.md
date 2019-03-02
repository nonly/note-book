# 并查集(UnionFind)
### [200] Number of Islands

``` * algorithms
 * Medium (40.03%)
 * Testcase Example:  '[["1","1","1","1","0"],["1","1","0","1","0"],["1","1","0","0","0"],["0","0","0","0","0"]]'
```
 
 Given a 2d grid map of '1's (land) and '0's (water), count the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

Example 1:
```
Input:
11110
11010
11000
00000

Output: 1
```
Example 2:
```
Input:
11000
11000
00100
00011

Output: 3
```
 - **分析** 该题目至少有三种解法，UF/DFS/BFS，UF唯一需要注意的是union的条件(2D校验)&&union时根据rank缩短树的高度
 
```class Solution {
    class UnionFind {
        private int[] union;
        private int size = 0;
        private int[] rank;
        public UnionFind(int n) {
            union = new int[n];
            rank = new int[n];
            for (int i = 0; i < n; i++) {
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
        int row  = grid.length;
        if (row == 0) return 0;
        int col = grid[0].length;
        if (col == 0) return 0;     
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


