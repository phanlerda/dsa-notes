# Bài 9: BFS (Breadth-First Search)

## 📚 Lý Thuyết Cơ Bản

### BFS (Breadth-First Search)
- **Khái niệm**: Thuật toán duyệt đồ thị theo chiều rộng, thăm tất cả nodes ở level hiện tại trước khi chuyển sang level tiếp theo
- **Đặc điểm quan trọng**:
  - Time Complexity: O(V + E) với V là số đỉnh, E là số cạnh
  - Space Complexity: O(V) cho queue và visited set
  - **Guarantee**: Tìm được shortest path trong unweighted graph
- **Cấu trúc dữ liệu**: Sử dụng Queue (FIFO - First In First Out)

### Core Principle:
BFS explore nodes level by level, đảm bảo tất cả nodes ở distance d được thăm trước nodes ở distance d+1.

### Basic BFS Implementation:

```python
from collections import deque

def bfs_basic(graph, start):
    """
    Basic BFS traversal
    Time: O(V + E), Space: O(V)
    """
    visited = set()
    queue = deque([start])
    result = []
    
    visited.add(start)
    
    while queue:
        node = queue.popleft()
        result.append(node)
        
        # Add all unvisited neighbors
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return result

def bfs_with_levels(graph, start):
    """
    BFS với tracking levels
    Time: O(V + E), Space: O(V)
    """
    visited = set([start])
    queue = deque([(start, 0)])  # (node, level)
    levels = {}
    
    while queue:
        node, level = queue.popleft()
        levels[node] = level
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, level + 1))
    
    return levels

def bfs_shortest_path(graph, start, target):
    """
    BFS để tìm shortest path
    Time: O(V + E), Space: O(V)
    """
    if start == target:
        return [start]
    
    visited = set([start])
    queue = deque([start])
    parent = {start: None}
    
    while queue:
        node = queue.popleft()
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                parent[neighbor] = node
                queue.append(neighbor)
                
                if neighbor == target:
                    # Reconstruct path
                    path = []
                    current = target
                    while current is not None:
                        path.append(current)
                        current = parent[current]
                    return path[::-1]
    
    return None  # No path found
```

### BFS trên Grid (2D Array):
```python
def bfs_grid(grid, start_row, start_col):
    """
    BFS trên lưới 2D
    Time: O(M*N), Space: O(M*N)
    """
    if not grid or not grid[0]:
        return []
    
    rows, cols = len(grid), len(grid[0])
    visited = set()
    queue = deque([(start_row, start_col)])
    visited.add((start_row, start_col))
    
    # 4 directions: up, right, down, left
    directions = [(-1, 0), (0, 1), (1, 0), (0, -1)]
    result = []
    
    while queue:
        row, col = queue.popleft()
        result.append((row, col))
        
        for dr, dc in directions:
            new_row, new_col = row + dr, col + dc
            
            if (0 <= new_row < rows and 0 <= new_col < cols and 
                (new_row, new_col) not in visited and
                grid[new_row][new_col] != 0):  # Assuming 0 is obstacle
                
                visited.add((new_row, new_col))
                queue.append((new_row, new_col))
    
    return result
```

## 🎯 Kỹ Thuật và Pattern Chính

### 1. Level-by-Level BFS
**Ý tưởng**: Process tất cả nodes ở cùng một level trước khi chuyển sang level tiếp theo
**Khi nào sử dụng**: Tree level traversal, minimum steps problems, layer-wise processing

```python
def level_order_bfs(root):
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        level_nodes = []
        
        for _ in range(level_size):
            node = queue.popleft()
            level_nodes.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(level_nodes)
    
    return result
```

### 2. Multi-Source BFS
**Ý tưởng**: Bắt đầu BFS từ multiple sources cùng lúc
**Khi nào sử dụng**: Spreading problems, nearest distance from multiple points

```python
def multi_source_bfs(grid, sources):
    queue = deque()
    visited = set()
    
    # Add all sources to queue
    for source in sources:
        queue.append((source[0], source[1], 0))  # (row, col, distance)
        visited.add((source[0], source[1]))
    
    while queue:
        row, col, dist = queue.popleft()
        
        for dr, dc in [(-1,0), (1,0), (0,-1), (0,1)]:
            new_row, new_col = row + dr, col + dc
            
            if (is_valid(new_row, new_col, grid) and 
                (new_row, new_col) not in visited):
                
                visited.add((new_row, new_col))
                queue.append((new_row, new_col, dist + 1))
```

### 3. Bidirectional BFS
**Ý tưởng**: BFS từ cả start và end, meet in the middle
**Khi nào sử dụng**: Optimize shortest path finding, reduce search space

### 4. BFS với State
**Ý tưởng**: Track additional state information cùng với position
**Khi nào sử dụng**: Problems với constraints, multiple conditions

## 🔍 Ví Dụ Thực Tế

### Ví dụ 1: Binary Tree Level Order Traversal
**Mô tả**: Duyệt cây nhị phân theo từng level
**Input**: root = [3,9,20,null,null,15,7]
**Output**: [[3],[9,20],[15,7]]

```python
from collections import deque

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def levelOrder(root):
    """
    Level order traversal sử dụng BFS
    Time: O(N), Space: O(W) với W là width của tree
    """
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        current_level = []
        
        # Process all nodes at current level
        for _ in range(level_size):
            node = queue.popleft()
            current_level.append(node.val)
            
            # Add children for next level
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(current_level)
    
    return result

# Test case
# Tree: [3,9,20,null,null,15,7]
root = TreeNode(3)
root.left = TreeNode(9)
root.right = TreeNode(20)
root.right.left = TreeNode(15)
root.right.right = TreeNode(7)

print(levelOrder(root))  # [[3], [9, 20], [15, 7]]
```

**Logic**: Sử dụng queue size để determine số nodes ở current level, process level by level.

### Ví dụ 2: Rotting Oranges (Multi-Source BFS)
**Mô tả**: Tính thời gian để tất cả oranges bị rotten
**Input**: grid = [[2,1,1],[1,1,0],[0,1,1]]
**Output**: 4

```python
def orangesRotting(grid):
    """
    Multi-source BFS để spread rot từ tất cả rotten oranges
    Time: O(M*N), Space: O(M*N)
    """
    if not grid or not grid[0]:
        return -1
    
    rows, cols = len(grid), len(grid[0])
    queue = deque()
    fresh_count = 0
    
    # Find all initially rotten oranges và count fresh oranges
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 2:  # Rotten
                queue.append((r, c, 0))  # (row, col, time)
            elif grid[r][c] == 1:  # Fresh
                fresh_count += 1
    
    if fresh_count == 0:
        return 0
    
    directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]
    max_time = 0
    
    while queue:
        row, col, time = queue.popleft()
        max_time = max(max_time, time)
        
        # Spread rot to adjacent fresh oranges
        for dr, dc in directions:
            new_row, new_col = row + dr, col + dc
            
            if (0 <= new_row < rows and 0 <= new_col < cols and 
                grid[new_row][new_col] == 1):  # Fresh orange
                
                grid[new_row][new_col] = 2  # Make it rotten
                fresh_count -= 1
                queue.append((new_row, new_col, time + 1))
    
    return max_time if fresh_count == 0 else -1

# Test case
grid = [[2,1,1],[1,1,0],[0,1,1]]
print(orangesRotting(grid))  # 4
```

**Logic**: Multi-source BFS từ tất cả rotten oranges, track time và fresh count.

### Ví dụ 3: Word Ladder (BFS with Transformation)
**Mô tả**: Tìm shortest transformation sequence từ beginWord đến endWord
**Input**: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
**Output**: 5

```python
def ladderLength(beginWord, endWord, wordList):
    """
    BFS để tìm shortest word transformation
    Time: O(M²*N) với M là length của word, N là số words
    Space: O(M*N)
    """
    if endWord not in wordList:
        return 0
    
    wordSet = set(wordList)
    queue = deque([(beginWord, 1)])  # (word, steps)
    visited = set([beginWord])
    
    while queue:
        word, steps = queue.popleft()
        
        if word == endWord:
            return steps
        
        # Try all possible one-character changes
        for i in range(len(word)):
            for c in 'abcdefghijklmnopqrstuvwxyz':
                if c != word[i]:
                    new_word = word[:i] + c + word[i+1:]
                    
                    if new_word in wordSet and new_word not in visited:
                        visited.add(new_word)
                        queue.append((new_word, steps + 1))
    
    return 0

# Optimized version with preprocessing
def ladderLengthOptimized(beginWord, endWord, wordList):
    """
    Optimized với pattern matching
    Time: O(M*N), Space: O(M*N)
    """
    if endWord not in wordList:
        return 0
    
    # Build pattern dictionary
    patterns = {}
    for word in wordList:
        for i in range(len(word)):
            pattern = word[:i] + '*' + word[i+1:]
            if pattern not in patterns:
                patterns[pattern] = []
            patterns[pattern].append(word)
    
    queue = deque([(beginWord, 1)])
    visited = set([beginWord])
    
    while queue:
        word, steps = queue.popleft()
        
        if word == endWord:
            return steps
        
        # Find all possible next words using patterns
        for i in range(len(word)):
            pattern = word[:i] + '*' + word[i+1:]
            
            for next_word in patterns.get(pattern, []):
                if next_word not in visited:
                    visited.add(next_word)
                    queue.append((next_word, steps + 1))
    
    return 0

# Test case
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log","cog"]
print(ladderLength(beginWord, endWord, wordList))  # 5
```

**Logic**: BFS trên word graph, mỗi valid transformation là một edge.

### Ví dụ 4: Shortest Path in Binary Matrix
**Mô tả**: Tìm shortest path từ top-left đến bottom-right
**Input**: grid = [[0,0,0],[1,1,0],[1,1,0]]
**Output**: 4

```python
def shortestPathBinaryMatrix(grid):
    """
    BFS để tìm shortest path với 8-directional movement
    Time: O(N²), Space: O(N²)
    """
    n = len(grid)
    
    # Check if start or end is blocked
    if grid[0][0] != 0 or grid[n-1][n-1] != 0:
        return -1
    
    if n == 1:
        return 1
    
    # 8 directions including diagonals
    directions = [(-1,-1), (-1,0), (-1,1), (0,-1), 
                  (0,1), (1,-1), (1,0), (1,1)]
    
    queue = deque([(0, 0, 1)])  # (row, col, path_length)
    visited = set([(0, 0)])
    
    while queue:
        row, col, path_len = queue.popleft()
        
        # Try all 8 directions
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
grid = [[0,0,0],[1,1,0],[1,1,0]]
print(shortestPathBinaryMatrix(grid))  # 4
```

**Logic**: BFS với 8-directional movement, guarantee shortest path.

### Ví dụ 5: Open the Lock (BFS with State)
**Mô tả**: Tìm minimum turns để unlock combination lock
**Input**: deadends = ["0201","0101","0102","1212","2002"], target = "0202"
**Output**: 6

```python
def openLock(deadends, target):
    """
    BFS với state là combination string
    Time: O(10000), Space: O(10000)
    """
    if "0000" in deadends:
        return -1
    
    if target == "0000":
        return 0
    
    dead_set = set(deadends)
    queue = deque([("0000", 0)])  # (combination, steps)
    visited = set(["0000"])
    
    def get_neighbors(combo):
        """Generate all possible next combinations"""
        neighbors = []
        for i in range(4):
            digit = int(combo[i])
            
            # Turn up
            new_digit = (digit + 1) % 10
            new_combo = combo[:i] + str(new_digit) + combo[i+1:]
            neighbors.append(new_combo)
            
            # Turn down
            new_digit = (digit - 1) % 10
            new_combo = combo[:i] + str(new_digit) + combo[i+1:]
            neighbors.append(new_combo)
        
        return neighbors
    
    while queue:
        combo, steps = queue.popleft()
        
        for next_combo in get_neighbors(combo):
            if next_combo == target:
                return steps + 1
            
            if (next_combo not in visited and 
                next_combo not in dead_set):
                
                visited.add(next_combo)
                queue.append((next_combo, steps + 1))
    
    return -1

# Test case
deadends = ["0201","0101","0102","1212","2002"]
target = "0202"
print(openLock(deadends, target))  # 6
```

**Logic**: BFS trong state space của combinations, avoid deadends.

## 🏃‍♂️ Bài Tập Thực Hành

### 1. LeetCode 102: Binary Tree Level Order Traversal
```python
def levelOrder(root):
    """Level-by-level BFS traversal"""
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            
            if node.left: queue.append(node.left)
            if node.right: queue.append(node.right)
        
        result.append(level)
    
    return result
```
**Link**: https://leetcode.com/problems/binary-tree-level-order-traversal/

### 2. LeetCode 994: Rotting Oranges
```python
def orangesRotting(grid):
    """Multi-source BFS for spreading rot"""
    queue = deque()
    fresh = 0
    
    for i in range(len(grid)):
        for j in range(len(grid[0])):
            if grid[i][j] == 2:
                queue.append((i, j, 0))
            elif grid[i][j] == 1:
                fresh += 1
    
    time = 0
    while queue:
        r, c, t = queue.popleft()
        time = max(time, t)
        
        for dr, dc in [(-1,0), (1,0), (0,-1), (0,1)]:
            nr, nc = r + dr, c + dc
            if (0 <= nr < len(grid) and 0 <= nc < len(grid[0]) and 
                grid[nr][nc] == 1):
                grid[nr][nc] = 2
                fresh -= 1
                queue.append((nr, nc, t + 1))
    
    return time if fresh == 0 else -1
```
**Link**: https://leetcode.com/problems/rotting-oranges/

### 3. LeetCode 127: Word Ladder
```python
def ladderLength(beginWord, endWord, wordList):
    """BFS for word transformation"""
    if endWord not in wordList:
        return 0
    
    wordSet = set(wordList)
    queue = deque([(beginWord, 1)])
    
    while queue:
        word, length = queue.popleft()
        
        if word == endWord:
            return length
        
        for i in range(len(word)):
            for c in 'abcdefghijklmnopqrstuvwxyz':
                new_word = word[:i] + c + word[i+1:]
                if new_word in wordSet:
                    wordSet.remove(new_word)
                    queue.append((new_word, length + 1))
    
    return 0
```
**Link**: https://leetcode.com/problems/word-ladder/

## 🎯 Template Tổng Quát

### Basic BFS Template
```python
def bfs_template(graph, start):
    from collections import deque
    
    queue = deque([start])
    visited = set([start])
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node)
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return result
```

### Level-by-Level BFS Template
```python
def level_bfs_template(start, get_neighbors):
    from collections import deque
    
    queue = deque([start])
    visited = set([start])
    levels = []
    
    while queue:
        level_size = len(queue)
        current_level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            current_level.append(node)
            
            for neighbor in get_neighbors(node):
                if neighbor not in visited:
                    visited.add(neighbor)
                    queue.append(neighbor)
        
        levels.append(current_level)
    
    return levels
```

### Shortest Path BFS Template
```python
def shortest_path_bfs(start, target, get_neighbors):
    from collections import deque
    
    if start == target:
        return 0
    
    queue = deque([(start, 0)])  # (node, distance)
    visited = set([start])
    
    while queue:
        node, dist = queue.popleft()
        
        for neighbor in get_neighbors(node):
            if neighbor == target:
                return dist + 1
            
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, dist + 1))
    
    return -1  # Target not reachable
```

### Grid BFS Template
```python
def grid_bfs_template(grid, start_row, start_col, is_valid):
    from collections import deque
    
    if not grid or not grid[0]:
        return []
    
    rows, cols = len(grid), len(grid[0])
    queue = deque([(start_row, start_col)])
    visited = set([(start_row, start_col)])
    
    directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]  # 4-directional
    # directions = [(-1,-1),(-1,0),(-1,1),(0,-1),(0,1),(1,-1),(1,0),(1,1)]  # 8-directional
    
    result = []
    
    while queue:
        row, col = queue.popleft()
        result.append((row, col))
        
        for dr, dc in directions:
            new_row, new_col = row + dr, col + dc
            
            if (0 <= new_row < rows and 0 <= new_col < cols and 
                (new_row, new_col) not in visited and
                is_valid(grid, new_row, new_col)):
                
                visited.add((new_row, new_col))
                queue.append((new_row, new_col))
    
    return result
```

### Multi-Source BFS Template
```python
def multi_source_bfs(sources, get_neighbors, is_target):
    from collections import deque
    
    queue = deque()
    visited = set()
    
    # Initialize with all sources
    for source in sources:
        queue.append((source, 0))  # (node, distance)
        visited.add(source)
    
    while queue:
        node, dist = queue.popleft()
        
        if is_target(node):
            return dist
        
        for neighbor in get_neighbors(node):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, dist + 1))
    
    return -1
```

## 💡 Tips Quan Trọng

### Khi nào sử dụng BFS:
- **Shortest Path**: Trong unweighted graph hoặc grid
- **Level Order Traversal**: Tree/graph level-by-level processing
- **Minimum Steps**: Problems yêu cầu minimum operations
- **Spreading**: Multi-source spreading problems
- **State Space Search**: Với finite state space

### BFS vs DFS:
**Sử dụng BFS khi:**
- Cần shortest path
- Level-by-level processing
- Memory không là vấn đề lớn
- Graph có branching factor cao

**Sử dụng DFS khi:**
- Cần explore tất cả paths
- Memory hạn chế
- Backtracking problems
- Detect cycles

### Common Mistakes:
1. **Forget to mark visited when adding to queue** → Infinite loop
2. **Mark visited when popping instead of adding** → Duplicate processing
3. **Not handling empty input** → Runtime error
4. **Confuse level-by-level vs normal BFS** → Wrong logic
5. **Not checking bounds in grid problems** → Index error

### Optimization Techniques:
1. **Early termination**: Return ngay khi tìm được target
2. **Bidirectional BFS**: Meet in the middle để reduce search space
3. **Visited set**: Sử dụng set thay vì list để O(1) lookup
4. **Space optimization**: Remove từ wordList thay vì maintain visited set

### Time/Space Complexity:
- **Time**: O(V + E) cho graph, O(M×N) cho grid
- **Space**: O(V) cho visited set và queue
- **Level-by-level**: Space có thể lên đến O(W) với W là width của level

### Edge Cases cần lưu ý:
- Empty input (graph, grid, wordList)
- Single node/cell
- No path exists
- Start equals target
- All cells blocked/invalid
- Very large input (memory constraints)

### BFS Variants:
1. **Standard BFS**: Basic traversal
2. **Level-by-level BFS**: Process nodes level by level
3. **Multi-source BFS**: Start từ multiple sources
4. **Bidirectional BFS**: Search từ both ends
5. **BFS with state**: Track additional information

## 🏆 Bài Tập Về Nhà

### Easy Level:
1. **LeetCode 111**: Minimum Depth of Binary Tree
   - *Approach*: Level-by-level BFS, return depth khi gặp leaf node đầu tiên
2. **LeetCode 199**: Binary Tree Right Side View
   - *Approach*: Level-by-level BFS, lấy node cuối cùng của mỗi level
3. **LeetCode 637**: Average of Levels in Binary Tree
   - *Approach*: Level-by-level BFS, tính average cho mỗi level

### Medium Level:
4. **LeetCode 103**: Binary Tree Zigzag Level Order Traversal
   - *Approach*: Level-by-level BFS với alternating direction
5. **LeetCode 542**: 01 Matrix
   - *Approach*: Multi-source BFS từ tất cả cells có value 0
6. **LeetCode 1091**: Shortest Path in Binary Matrix
   - *Approach*: BFS với 8-directional movement
7. **LeetCode 317**: Shortest Distance from All Buildings
   - *Approach*: Multi-source BFS từ mỗi building
8. **LeetCode 286**: Walls and Gates
   - *Approach*: Multi-source BFS từ tất cả gates
9. **LeetCode 130**: Surrounded Regions
   - *Approach*: BFS từ boundary để mark connected regions
10. **LeetCode 133**: Clone Graph
    - *Approach*: BFS với HashMap để track cloned nodes

### Hard Level:
11. **LeetCode 126**: Word Ladder II
    - *Approach*: BFS để tìm shortest path length, sau đó DFS để construct paths
12. **LeetCode 815**: Bus Routes
    - *Approach*: BFS trên bus routes graph
13. **LeetCode 847**: Shortest Path Visiting All Nodes
    - *Approach*: BFS với bitmask state
14. **LeetCode 752**: Open the Lock
    - *Approach*: BFS trong state space với deadends avoidance
15. **LeetCode 773**: Sliding Puzzle
    - *Approach*: BFS với board state representation

### Advanced Challenges:
16. **LeetCode 1197**: Minimum Knight Moves
    - *Approach*: Bidirectional BFS để optimize search space
17. **LeetCode 1293**: Shortest Path in a Grid with Obstacles Elimination
    - *Approach*: BFS với state (row, col, obstacles_left)
18. **LeetCode 909**: Snakes and Ladders
    - *Approach*: BFS trên board với special movement rules
19. **LeetCode 1263**: Minimum Moves to Move a Box to Their Target Location
    - *Approach*: BFS với complex state (box_pos, player_pos)
20. **LeetCode 864**: Shortest Path to Get All Keys
    - *Approach*: BFS với bitmask state cho keys collected