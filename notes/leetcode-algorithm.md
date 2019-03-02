# 并查集(UnionFind)
### [200] Number of Islands
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
}```

