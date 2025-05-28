# Bài 8: Graph (Đồ Thị)

## 📚 Lý Thuyết Cơ Bản

### Graph (Đồ thị)
- **Khái niệm**: Cấu trúc dữ liệu gồm các đỉnh (vertices/nodes) được kết nối bởi các cạnh (edges)
- **Đặc điểm quan trọng**:
  - Time Complexity: 
    - DFS/BFS: O(V + E) với V là số đỉnh, E là số cạnh
    - Adjacency List: O(V + E) space
    - Adjacency Matrix: O(V²) space
  - Space Complexity: Phụ thuộc vào cách biểu diễn

### Phân loại Graph:
1. **Directed vs Undirected**: Có hướng vs vô hướng
2. **Weighted vs Unweighted**: Có trọng số vs không trọng số
3. **Connected vs Disconnected**: Liên thông vs không liên thông
4. **Cyclic vs Acyclic**: Có chu trình vs không chu trình

### Cách biểu diễn Graph:

```python
# 1. Adjacency List (Danh sách kề) - Phổ biến nhất
class Graph:
    def __init__(self):
        self.graph = {}
    
    def add_edge(self, u, v, weight=1):
        if u not in self.graph:
            self.graph[u] = []
        if v not in self.graph:
            self.graph[v] = []
        
        # Undirected graph
        self.graph[u].append((v, weight))
        self.graph[v].append((u, weight))
    
    def get_neighbors(self, node):
        return self.graph.get(node, [])

# 2. Adjacency Matrix (Ma trận kề)
class GraphMatrix:
    def __init__(self, num_vertices):
        self.V = num_vertices
        self.graph = [[0 for _ in range(num_vertices)] 
                      for _ in range(num_vertices)]
    
    def add_edge(self, u, v, weight=1):
        self.graph[u][v] = weight
        self.graph[v][u] = weight  # Undirected

# 3. Edge List (Danh sách cạnh)
edges = [(0, 1, 4), (0, 7, 8), (1, 2, 8), (1, 7, 11)]  # (u, v, weight)
```

### DFS (Depth-First Search):
```python
def dfs_recursive(graph, node, visited=None):
    """
    DFS đệ quy
    Time: O(V + E), Space: O(V)
    """
    if visited is None:
        visited = set()
    
    visited.add(node)
    print(node, end=' ')
    
    for neighbor, _ in graph.get_neighbors(node):
        if neighbor not in visited:
            dfs_recursive(graph, neighbor, visited)
    
    return visited

def dfs_iterative(graph, start):
    """
    DFS sử dụng stack
    Time: O(V + E), Space: O(V)
    """
    visited = set()
    stack = [start]
    path = []
    
    while stack:
        node = stack.pop()
        if node not in visited:
            visited.add(node)
            path.append(node)
            
            # Add neighbors to stack (reverse order for consistent traversal)
            for neighbor, _ in reversed(graph.get_neighbors(node)):
                if neighbor not in visited:
                    stack.append(neighbor)
    
    return path
```

### BFS (Breadth-First Search):
```python
from collections import deque

def bfs(graph, start):
    """
    BFS sử dụng queue
    Time: O(V + E), Space: O(V)
    """
    visited = set()
    queue = deque([start])
    path = []
    
    visited.add(start)
    
    while queue:
        node = queue.popleft()
        path.append(node)
        
        for neighbor, _ in graph.get_neighbors(node):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return path
```

## 🎯 Kỹ Thuật và Pattern Chính

### 1. Graph Traversal Patterns
- **DFS**: Đi sâu trước, dùng cho pathfinding, cycle detection, topological sort
- **BFS**: Đi rộng trước, dùng cho shortest path, level order traversal

### 2. Shortest Path Algorithms
- **Dijkstra**: Shortest path với positive weights
- **Bellman-Ford**: Shortest path với negative weights
- **Floyd-Warshall**: All pairs shortest path

### 3. Minimum Spanning Tree
- **Kruskal's Algorithm**: Union-Find based
- **Prim's Algorithm**: Greedy approach

### 4. Topological Sort
- **DFS-based**: Sử dụng finish time
- **Kahn's Algorithm**: Sử dụng in-degree

### Khi nào sử dụng:
- **DFS**: Tìm đường đi, phát hiện chu trình, connected components
- **BFS**: Shortest path trong unweighted graph, level traversal
- **Dijkstra**: Shortest path với weighted graph
- **Union-Find**: Dynamic connectivity, MST

## 🔍 Ví Dụ Thực Tế

### Ví dụ 1: Number of Islands (DFS/BFS)
**Mô tả**: Đếm số lượng đảo trong lưới 2D
**Input**: grid = [["1","1","0","0","0"],["1","1","0","0","0"],["0","0","1","0","0"],["0","0","0","1","1"]]
**Output**: 3

```python
def numIslands(grid):
    """
    Đếm số đảo sử dụng DFS
    Time: O(M*N), Space: O(M*N) trong worst case
    """
    if not grid or not grid[0]:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    islands = 0
    
    def dfs(r, c):
        # Base case: out of bounds hoặc đã thăm/nước
        if (r < 0 or r >= rows or c < 0 or c >= cols or 
            grid[r][c] != '1'):
            return
        
        # Mark as visited
        grid[r][c] = '0'
        
        # Explore 4 directions
        directions = [(0, 1), (1, 0), (0, -1), (-1, 0)]
        for dr, dc in directions:
            dfs(r + dr, c + dc)
    
    # Iterate through each cell
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                islands += 1
                dfs(r, c)  # Mark entire island
    
    return islands

# Test case
grid = [["1","1","0","0","0"],
        ["1","1","0","0","0"],
        ["0","0","1","0","0"],
        ["0","0","0","1","1"]]
print(numIslands(grid))  # 3
```

**Logic**: Sử dụng DFS để explore từng connected component (đảo), mark visited cells as '0'.

### Ví dụ 2: Course Schedule (Topological Sort)
**Mô tả**: Kiểm tra có thể hoàn thành tất cả courses không (phát hiện cycle)
**Input**: numCourses = 2, prerequisites = [[1,0]]
**Output**: True

```python
def canFinish(numCourses, prerequisites):
    """
    Detect cycle trong directed graph sử dụng DFS
    Time: O(V + E), Space: O(V + E)
    """
    # Build adjacency list
    graph = [[] for _ in range(numCourses)]
    for course, prereq in prerequisites:
        graph[prereq].append(course)
    
    # States: 0=unvisited, 1=visiting, 2=visited
    state = [0] * numCourses
    
    def has_cycle(node):
        if state[node] == 1:  # Back edge found -> cycle
            return True
        if state[node] == 2:  # Already processed
            return False
        
        state[node] = 1  # Mark as visiting
        
        # Check all neighbors
        for neighbor in graph[node]:
            if has_cycle(neighbor):
                return True
        
        state[node] = 2  # Mark as visited
        return False
    
    # Check each component
    for i in range(numCourses):
        if state[i] == 0 and has_cycle(i):
            return False
    
    return True

# Test case
print(canFinish(2, [[1,0]]))        # True
print(canFinish(2, [[1,0],[0,1]]))  # False (cycle)
```

**Logic**: Sử dụng DFS với 3 states để detect back edges, tức là cycles trong directed graph.

### Ví dụ 3: Shortest Path in Binary Matrix (BFS)
**Mô tả**: Tìm đường đi ngắn nhất từ top-left đến bottom-right
**Input**: grid = [[0,1],[1,0]]
**Output**: 2

```python
from collections import deque

def shortestPathBinaryMatrix(grid):
    """
    Shortest path sử dụng BFS
    Time: O(N²), Space: O(N²)
    """
    n = len(grid)
    if grid[0][0] != 0 or grid[n-1][n-1] != 0:
        return -1
    
    if n == 1:
        return 1
    
    # BFS setup
    queue = deque([(0, 0, 1)])  # (row, col, path_length)
    visited = set([(0, 0)])
    
    # 8 directions (including diagonals)
    directions = [(-1,-1), (-1,0), (-1,1), (0,-1), 
                  (0,1), (1,-1), (1,0), (1,1)]
    
    while queue:
        row, col, path_len = queue.popleft()
        
        # Check all 8 directions
        for dr, dc in directions:
            new_row, new_col = row + dr, col + dc
            
            # Check bounds and if cell is clear
            if (0 <= new_row < n and 0 <= new_col < n and 
                grid[new_row][new_col] == 0 and 
                (new_row, new_col) not in visited):
                
                # Check if reached destination
                if new_row == n-1 and new_col == n-1:
                    return path_len + 1
                
                visited.add((new_row, new_col))
                queue.append((new_row, new_col, path_len + 1))
    
    return -1

# Test case
grid = [[0,1],[1,0]]
print(shortestPathBinaryMatrix(grid))  # 2
```

**Logic**: Sử dụng BFS để tìm shortest path, vì BFS guarantee tìm được đường đi ngắn nhất trong unweighted graph.

### Ví dụ 4: Clone Graph (DFS + HashMap)
**Mô tả**: Clone một undirected graph
**Input**: adjList = [[2,4],[1,3],[2,4],[1,3]]
**Output**: Cloned graph

```python
class Node:
    def __init__(self, val=0, neighbors=None):
        self.val = val
        self.neighbors = neighbors if neighbors is not None else []

def cloneGraph(node):
    """
    Clone graph sử dụng DFS và HashMap
    Time: O(N), Space: O(N)
    """
    if not node:
        return None
    
    # HashMap để track cloned nodes
    cloned = {}
    
    def dfs(original):
        if original in cloned:
            return cloned[original]
        
        # Create clone
        clone = Node(original.val)
        cloned[original] = clone
        
        # Clone all neighbors
        for neighbor in original.neighbors:
            clone.neighbors.append(dfs(neighbor))
        
        return clone
    
    return dfs(node)

# Alternative: BFS approach
def cloneGraphBFS(node):
    """
    Clone graph sử dụng BFS
    Time: O(N), Space: O(N)
    """
    if not node:
        return None
    
    cloned = {node: Node(node.val)}
    queue = deque([node])
    
    while queue:
        original = queue.popleft()
        
        for neighbor in original.neighbors:
            if neighbor not in cloned:
                cloned[neighbor] = Node(neighbor.val)
                queue.append(neighbor)
            
            cloned[original].neighbors.append(cloned[neighbor])
    
    return cloned[node]
```

**Logic**: Sử dụng HashMap để track các node đã clone, tránh infinite loop và duplicate cloning.

### Ví dụ 5: Network Delay Time (Dijkstra)
**Mô tả**: Tìm thời gian minimum để signal đến tất cả nodes
**Input**: times = [[2,1,1],[2,3,1],[3,4,1]], n = 4, k = 2
**Output**: 2

```python
import heapq
from collections import defaultdict

def networkDelayTime(times, n, k):
    """
    Dijkstra's algorithm để tìm shortest path to all nodes
    Time: O(E log V), Space: O(V + E)
    """
    # Build adjacency list
    graph = defaultdict(list)
    for u, v, w in times:
        graph[u].append((v, w))
    
    # Dijkstra's algorithm
    dist = {}
    heap = [(0, k)]  # (distance, node)
    
    while heap:
        d, node = heapq.heappop(heap)
        
        if node in dist:
            continue
        
        dist[node] = d
        
        # Update distances to neighbors
        for neighbor, weight in graph[node]:
            if neighbor not in dist:
                heapq.heappush(heap, (d + weight, neighbor))
    
    # Check if all nodes are reachable
    if len(dist) != n:
        return -1
    
    return max(dist.values())

# Test case
times = [[2,1,1],[2,3,1],[3,4,1]]
print(networkDelayTime(times, 4, 2))  # 2
```

**Logic**: Sử dụng Dijkstra để tìm shortest path từ source đến tất cả nodes, return maximum distance.

## 🏃‍♂️ Bài Tập Thực Hành

### 1. LeetCode 200: Number of Islands
```python
def numIslands(grid):
    """DFS approach để đếm connected components"""
    if not grid:
        return 0
    
    count = 0
    for i in range(len(grid)):
        for j in range(len(grid[0])):
            if grid[i][j] == '1':
                count += 1
                dfs(grid, i, j)
    return count

def dfs(grid, i, j):
    if (i < 0 or i >= len(grid) or j < 0 or j >= len(grid[0]) 
        or grid[i][j] != '1'):
        return
    grid[i][j] = '0'
    dfs(grid, i+1, j)
    dfs(grid, i-1, j)
    dfs(grid, i, j+1)
    dfs(grid, i, j-1)
```
**Link**: https://leetcode.com/problems/number-of-islands/

### 2. LeetCode 207: Course Schedule
```python
def canFinish(numCourses, prerequisites):
    """Cycle detection trong directed graph"""
    graph = [[] for _ in range(numCourses)]
    for course, prereq in prerequisites:
        graph[prereq].append(course)
    
    # 0: unvisited, 1: visiting, 2: visited
    state = [0] * numCourses
    
    def hasCycle(node):
        if state[node] == 1: return True
        if state[node] == 2: return False
        
        state[node] = 1
        for neighbor in graph[node]:
            if hasCycle(neighbor): return True
        state[node] = 2
        return False
    
    for i in range(numCourses):
        if state[i] == 0 and hasCycle(i):
            return False
    return True
```
**Link**: https://leetcode.com/problems/course-schedule/

### 3. LeetCode 133: Clone Graph
```python
def cloneGraph(node):
    """Deep clone graph using DFS + HashMap"""
    if not node:
        return None
    
    cloned = {}
    
    def dfs(original):
        if original in cloned:
            return cloned[original]
        
        clone = Node(original.val)
        cloned[original] = clone
        
        for neighbor in original.neighbors:
            clone.neighbors.append(dfs(neighbor))
        
        return clone
    
    return dfs(node)
```
**Link**: https://leetcode.com/problems/clone-graph/

## 🎯 Template Tổng Quát

### DFS Template
```python
def dfs_template(graph, start, target=None):
    visited = set()
    path = []
    
    def dfs(node):
        if node in visited:
            return False
        
        visited.add(node)
        path.append(node)
        
        if node == target:  # Found target
            return True
        
        for neighbor in graph[node]:
            if dfs(neighbor):
                return True
        
        path.pop()  # Backtrack if needed
        return False
    
    dfs(start)
    return path, visited
```

### BFS Template
```python
def bfs_template(graph, start, target=None):
    from collections import deque
    
    queue = deque([start])
    visited = set([start])
    parent = {start: None}  # For path reconstruction
    
    while queue:
        node = queue.popleft()
        
        if node == target:
            # Reconstruct path
            path = []
            current = target
            while current is not None:
                path.append(current)
                current = parent[current]
            return path[::-1]
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                parent[neighbor] = node
                queue.append(neighbor)
    
    return None  # Target not found
```

### Dijkstra Template
```python
def dijkstra_template(graph, start):
    import heapq
    
    dist = {start: 0}
    heap = [(0, start)]
    visited = set()
    
    while heap:
        d, node = heapq.heappop(heap)
        
        if node in visited:
            continue
        
        visited.add(node)
        
        for neighbor, weight in graph[node]:
            new_dist = d + weight
            if neighbor not in dist or new_dist < dist[neighbor]:
                dist[neighbor] = new_dist
                heapq.heappush(heap, (new_dist, neighbor))
    
    return dist
```

### Union-Find Template
```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
        self.components = n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # Path compression
        return self.parent[x]
    
    def union(self, x, y):
        root_x, root_y = self.find(x), self.find(y)
        
        if root_x == root_y:
            return False
        
        # Union by rank
        if self.rank[root_x] < self.rank[root_y]:
            self.parent[root_x] = root_y
        elif self.rank[root_x] > self.rank[root_y]:
            self.parent[root_y] = root_x
        else:
            self.parent[root_y] = root_x
            self.rank[root_x] += 1
        
        self.components -= 1
        return True
    
    def connected(self, x, y):
        return self.find(x) == self.find(y)
```

## 💡 Tips Quan Trọng

### Khi nào sử dụng từng algorithm:

**DFS:**
- Tìm đường đi (có thể không ngắn nhất)
- Detect cycles
- Topological sorting
- Connected components
- Backtracking problems

**BFS:**
- Shortest path trong unweighted graph
- Level order traversal
- Minimum steps problems
- Finding nearest neighbor

**Dijkstra:**
- Shortest path trong weighted graph (positive weights)
- Network routing
- GPS navigation

**Union-Find:**
- Dynamic connectivity
- Minimum Spanning Tree (Kruskal)
- Detect cycles trong undirected graph

### Common Mistakes:
1. **DFS**: Stack overflow với deep recursion → dùng iterative
2. **BFS**: Quên mark visited khi add vào queue → infinite loop
3. **Dijkstra**: Sử dụng với negative weights → dùng Bellman-Ford
4. **Graph representation**: Confuse directed vs undirected
5. **Cycle detection**: Không phân biệt directed vs undirected graphs

### Time/Space Complexity Analysis:
- **Adjacency List**: Space O(V + E), better for sparse graphs
- **Adjacency Matrix**: Space O(V²), better for dense graphs
- **DFS/BFS**: Time O(V + E), Space O(V) for visited set
- **Dijkstra**: Time O((V + E) log V) with min-heap

### Edge Cases cần lưu ý:
- Empty graph hoặc single node
- Disconnected graph
- Self-loops và multiple edges
- Negative weights (cho weighted graphs)
- Very large graphs (memory constraints)

### Graph Problems Pattern Recognition:
1. **Connected Components**: DFS/BFS traversal
2. **Shortest Path**: BFS (unweighted), Dijkstra (weighted)
3. **Cycle Detection**: DFS with colors, Union-Find
4. **Topological Sort**: DFS post-order, Kahn's algorithm
5. **Minimum Spanning Tree**: Kruskal + Union-Find, Prim's

## 🏆 Bài Tập Về Nhà

### Easy Level:
1. **LeetCode 997**: Find the Town Judge
   - *Approach*: Count in-degree và out-degree của mỗi node
2. **LeetCode 1971**: Find if Path Exists in Graph
   - *Approach*: Simple DFS/BFS hoặc Union-Find
3. **LeetCode 463**: Island Perimeter
   - *Approach*: Count boundaries của island cells

### Medium Level:
4. **LeetCode 695**: Max Area of Island
   - *Approach*: DFS để tính area của mỗi connected component
5. **LeetCode 547**: Number of Provinces
   - *Approach*: DFS/Union-Find để count connected components
6. **LeetCode 994**: Rotting Oranges
   - *Approach*: Multi-source BFS để spread infection
7. **LeetCode 210**: Course Schedule II
   - *Approach*: Topological sort để tìm valid ordering
8. **LeetCode 1091**: Shortest Path in Binary Matrix
   - *Approach*: BFS với 8-directional movement
9. **LeetCode 130**: Surrounded Regions
   - *Approach*: DFS từ boundary để mark connected 'O's
10. **LeetCode 417**: Pacific Atlantic Water Flow
    - *Approach*: DFS từ hai oceans để tìm intersection

### Hard Level:
11. **LeetCode 269**: Alien Dictionary
    - *Approach*: Build graph từ word ordering, topological sort
12. **LeetCode 839**: Similar String Groups
    - *Approach*: Union-Find với custom similarity check
13. **LeetCode 1192**: Critical Connections in a Network
    - *Approach*: Tarjan's algorithm để tìm bridges
14. **LeetCode 787**: Cheapest Flights Within K Stops
    - *Approach*: Modified Dijkstra hoặc Bellman-Ford với constraints
15. **LeetCode 924**: Minimize Malware Spread
    - *Approach*: Union-Find với component size tracking

### Advanced Challenges:
16. **LeetCode 1584**: Min Cost to Connect All Points
    - *Approach*: Minimum Spanning Tree (Kruskal/Prim)
17. **LeetCode 684**: Redundant Connection
    - *Approach*: Union-Find để detect cycle forming edge
18. **LeetCode 721**: Accounts Merge
    - *Approach*: Union-Find để merge connected accounts
19. **LeetCode 399**: Evaluate Division
    - *Approach*: Weighted graph với DFS để tính ratios
20. **LeetCode 685**: Redundant Connection II
    - *Approach*: Directed graph cycle detection với multiple cases