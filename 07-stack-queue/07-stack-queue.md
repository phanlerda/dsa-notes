# Bài 7: Queue và Stack

## 📚 Lý Thuyết Cơ Bản

### Stack (Ngăn xếp)
- **Khái niệm**: Cấu trúc dữ liệu LIFO (Last In, First Out) - phần tử cuối cùng được thêm vào sẽ được lấy ra đầu tiên
- **Đặc điểm quan trọng**:
  - Time Complexity: O(1) cho push, pop, top
  - Space Complexity: O(n) với n là số phần tử
- **Operations chính**: push (thêm), pop (lấy ra), top/peek (xem phần tử đầu), isEmpty

```python
# Stack implementation using list
class Stack:
    def __init__(self):
        self.items = []
    
    def push(self, item):
        self.items.append(item)
    
    def pop(self):
        if not self.is_empty():
            return self.items.pop()
        return None
    
    def top(self):
        if not self.is_empty():
            return self.items[-1]
        return None
    
    def is_empty(self):
        return len(self.items) == 0
    
    def size(self):
        return len(self.items)
```

### Queue (Hàng đợi)
- **Khái niệm**: Cấu trúc dữ liệu FIFO (First In, First Out) - phần tử đầu tiên được thêm vào sẽ được lấy ra đầu tiên
- **Đặc điểm quan trọng**:
  - Time Complexity: O(1) cho enqueue, dequeue, front
  - Space Complexity: O(n) với n là số phần tử
- **Operations chính**: enqueue (thêm), dequeue (lấy ra), front (xem phần tử đầu), isEmpty

```python
from collections import deque

class Queue:
    def __init__(self):
        self.items = deque()
    
    def enqueue(self, item):
        self.items.append(item)
    
    def dequeue(self):
        if not self.is_empty():
            return self.items.popleft()
        return None
    
    def front(self):
        if not self.is_empty():
            return self.items[0]
        return None
    
    def is_empty(self):
        return len(self.items) == 0
    
    def size(self):
        return len(self.items)
```

## 🎯 Kỹ Thuật và Pattern Chính

### Stack Patterns
1. **Parentheses Matching**: Kiểm tra cặp ngoặc hợp lệ
2. **Expression Evaluation**: Tính toán biểu thức
3. **Function Call Management**: Quản lý call stack
4. **Undo Operations**: Hoàn tác thao tác

### Queue Patterns
1. **BFS (Breadth-First Search)**: Duyệt theo chiều rộng
2. **Level Order Traversal**: Duyệt cây theo mức
3. **Task Scheduling**: Lập lịch công việc
4. **Buffer Management**: Quản lý bộ đệm

### Khi nào sử dụng:
- **Stack**: Khi cần xử lý theo thứ tự ngược lại, quản lý trạng thái, hoặc backtracking
- **Queue**: Khi cần xử lý theo thứ tự đến trước, BFS, hoặc scheduling

## 🔍 Ví Dụ Thực Tế

### Ví dụ 1: Valid Parentheses (Stack)
**Mô tả**: Kiểm tra chuỗi ngoặc có hợp lệ không
**Input**: s = "()[]{}"
**Output**: True

```python
def isValid(s):
    """
    Kiểm tra chuỗi ngoặc có hợp lệ không
    Time: O(n), Space: O(n)
    """
    stack = []
    mapping = {')': '(', '}': '{', ']': '['}
    
    for char in s:
        if char in mapping:  # Ngoặc đóng
            if not stack or stack.pop() != mapping[char]:
                return False
        else:  # Ngoặc mở
            stack.append(char)
    
    return len(stack) == 0

# Test cases
print(isValid("()"))        # True
print(isValid("()[]{}"))    # True
print(isValid("(]"))        # False
print(isValid("([)]"))      # False
```

**Logic**: Sử dụng stack để lưu ngoặc mở, khi gặp ngoặc đóng thì kiểm tra với phần tử top của stack.

### Ví dụ 2: Binary Tree Level Order Traversal (Queue)
**Mô tả**: Duyệt cây nhị phân theo từng mức
**Input**: root = [3,9,20,null,null,15,7]
**Output**: [[3],[9,20],[15,7]]

```python
from collections import deque

def levelOrder(root):
    """
    Duyệt cây theo mức sử dụng Queue
    Time: O(n), Space: O(w) với w là width của cây
    """
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        level_nodes = []
        
        # Xử lý tất cả nodes ở mức hiện tại
        for _ in range(level_size):
            node = queue.popleft()
            level_nodes.append(node.val)
            
            # Thêm children vào queue cho mức tiếp theo
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(level_nodes)
    
    return result
```

**Logic**: Sử dụng queue để lưu các node, xử lý từng mức một cách có tổ chức.

### Ví dụ 3: Daily Temperatures (Stack)
**Mô tả**: Tìm số ngày phải đợi để có nhiệt độ cao hơn
**Input**: temperatures = [73,74,75,71,69,72,76,73]
**Output**: [1,1,4,2,1,1,0,0]

```python
def dailyTemperatures(temperatures):
    """
    Sử dụng monotonic stack để tìm next greater element
    Time: O(n), Space: O(n)
    """
    n = len(temperatures)
    result = [0] * n
    stack = []  # Lưu index
    
    for i in range(n):
        # Pop stack cho đến khi tìm được nhiệt độ cao hơn
        while stack and temperatures[i] > temperatures[stack[-1]]:
            prev_index = stack.pop()
            result[prev_index] = i - prev_index
        
        stack.append(i)
    
    return result

# Test case
temps = [73,74,75,71,69,72,76,73]
print(dailyTemperatures(temps))  # [1,1,4,2,1,1,0,0]
```

**Logic**: Sử dụng monotonic stack để theo dõi các nhiệt độ chưa tìm được đáp án.

### Ví dụ 4: Implement Queue using Stacks
**Mô tả**: Cài đặt Queue sử dụng 2 Stack
**Input**: ["MyQueue", "push", "push", "peek", "pop", "empty"]
**Output**: [null, null, null, 1, 1, false]

```python
class MyQueue:
    """
    Implement Queue using two stacks
    Push: O(1), Pop: O(1) amortized
    """
    def __init__(self):
        self.input_stack = []   # Stack cho push
        self.output_stack = []  # Stack cho pop
    
    def push(self, x):
        self.input_stack.append(x)
    
    def pop(self):
        self._move_to_output()
        return self.output_stack.pop()
    
    def peek(self):
        self._move_to_output()
        return self.output_stack[-1]
    
    def empty(self):
        return len(self.input_stack) == 0 and len(self.output_stack) == 0
    
    def _move_to_output(self):
        if not self.output_stack:
            while self.input_stack:
                self.output_stack.append(self.input_stack.pop())

# Test
q = MyQueue()
q.push(1)
q.push(2)
print(q.peek())  # 1
print(q.pop())   # 1
print(q.empty()) # False
```

**Logic**: Sử dụng 2 stack, một cho input và một cho output để mô phỏng FIFO behavior.

### Ví dụ 5: Sliding Window Maximum (Deque)
**Mô tả**: Tìm maximum trong mỗi sliding window
**Input**: nums = [1,3,-1,-3,5,3,6,7], k = 3
**Output**: [3,3,5,5,6,7]

```python
from collections import deque

def maxSlidingWindow(nums, k):
    """
    Sử dụng deque để maintain maximum trong sliding window
    Time: O(n), Space: O(k)
    """
    if not nums or k == 0:
        return []
    
    dq = deque()  # Lưu index của các phần tử theo thứ tự giảm dần
    result = []
    
    for i in range(len(nums)):
        # Remove elements outside current window
        while dq and dq[0] <= i - k:
            dq.popleft()
        
        # Remove smaller elements from back
        while dq and nums[dq[-1]] <= nums[i]:
            dq.pop()
        
        dq.append(i)
        
        # Add result when window is complete
        if i >= k - 1:
            result.append(nums[dq[0]])
    
    return result

# Test
nums = [1,3,-1,-3,5,3,6,7]
print(maxSlidingWindow(nums, 3))  # [3,3,5,5,6,7]
```

**Logic**: Sử dụng deque để maintain các candidate cho maximum, loại bỏ các phần tử không cần thiết.

## 🏃‍♂️ Bài Tập Thực Hành

### 1. LeetCode 20: Valid Parentheses
```python
def isValid(s):
    stack = []
    pairs = {'(': ')', '[': ']', '{': '}'}
    
    for char in s:
        if char in pairs:
            stack.append(char)
        elif stack and pairs[stack[-1]] == char:
            stack.pop()
        else:
            return False
    
    return not stack
```
**Link**: https://leetcode.com/problems/valid-parentheses/

### 2. LeetCode 102: Binary Tree Level Order Traversal
```python
def levelOrder(root):
    if not root:
        return []
    
    result, queue = [], deque([root])
    
    while queue:
        level = []
        for _ in range(len(queue)):
            node = queue.popleft()
            level.append(node.val)
            if node.left: queue.append(node.left)
            if node.right: queue.append(node.right)
        result.append(level)
    
    return result
```
**Link**: https://leetcode.com/problems/binary-tree-level-order-traversal/

### 3. LeetCode 155: Min Stack
```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []
    
    def push(self, val):
        self.stack.append(val)
        if not self.min_stack or val <= self.min_stack[-1]:
            self.min_stack.append(val)
    
    def pop(self):
        if self.stack.pop() == self.min_stack[-1]:
            self.min_stack.pop()
    
    def top(self):
        return self.stack[-1]
    
    def getMin(self):
        return self.min_stack[-1]
```
**Link**: https://leetcode.com/problems/min-stack/

## 🎯 Template Tổng Quát

### Stack Template
```python
def stack_pattern(items):
    stack = []
    result = []
    
    for item in items:
        # Process current item
        while stack and condition(stack[-1], item):
            processed = stack.pop()
            # Do something with processed item
        
        # Add current item to stack
        stack.append(item)
    
    # Process remaining items in stack
    while stack:
        # Handle remaining items
        pass
    
    return result
```

### Queue Template (BFS)
```python
def bfs_pattern(start):
    queue = deque([start])
    visited = set([start])
    result = []
    
    while queue:
        current = queue.popleft()
        result.append(current)
        
        for neighbor in get_neighbors(current):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return result
```

### Level Order Template
```python
def level_order_pattern(root):
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        level_result = []
        
        for _ in range(level_size):
            node = queue.popleft()
            level_result.append(process(node))
            
            # Add children to queue
            for child in get_children(node):
                queue.append(child)
        
        result.append(level_result)
    
    return result
```

## 💡 Tips Quan Trọng

### Khi nào sử dụng Stack:
- Cần reverse order processing (LIFO)
- Nested structures (parentheses, HTML tags)
- Undo/Redo operations
- Expression evaluation
- Backtracking algorithms
- Function call management

### Khi nào sử dụng Queue:
- First-come-first-served processing (FIFO)
- BFS traversal
- Level order processing
- Task scheduling
- Buffer systems
- Producer-consumer patterns

### Common Mistakes:
1. **Stack**: Quên kiểm tra empty trước khi pop
2. **Queue**: Sử dụng list.pop(0) thay vì deque (O(n) vs O(1))
3. **Both**: Không handle edge cases (empty structure)
4. **Memory**: Không clear unused references

### Time/Space Complexity:
- **Stack Operations**: Push/Pop/Top - O(1)
- **Queue Operations**: Enqueue/Dequeue/Front - O(1)
- **Space**: Both use O(n) space với n là số phần tử

### Edge Cases:
- Empty structure
- Single element
- Maximum capacity (nếu có giới hạn)
- Null/None values
- Duplicate elements

## 🏆 Bài Tập Về Nhà

### Easy Level:
1. **LeetCode 232**: Implement Queue using Stacks
   - *Approach*: Sử dụng 2 stacks, một cho input một cho output
2. **LeetCode 225**: Implement Stack using Queues  
   - *Approach*: Sử dụng 1 hoặc 2 queues, reverse order khi cần
3. **LeetCode 1021**: Remove Outermost Parentheses
   - *Approach*: Stack để track depth của parentheses

### Medium Level:
4. **LeetCode 394**: Decode String
   - *Approach*: Stack để handle nested brackets và numbers
5. **LeetCode 739**: Daily Temperatures
   - *Approach*: Monotonic stack để tìm next greater element
6. **LeetCode 103**: Binary Tree Zigzag Level Order Traversal
   - *Approach*: Level order với alternating direction
7. **LeetCode 150**: Evaluate Reverse Polish Notation
   - *Approach*: Stack để evaluate postfix expression
8. **LeetCode 946**: Validate Stack Sequences
   - *Approach*: Simulate stack operations để validate sequence

### Hard Level:
9. **LeetCode 84**: Largest Rectangle in Histogram
   - *Approach*: Monotonic stack để tìm boundaries cho mỗi bar
10. **LeetCode 42**: Trapping Rain Water
    - *Approach*: Stack hoặc two pointers để calculate trapped water
11. **LeetCode 85**: Maximal Rectangle
    - *Approach*: Reduce về largest rectangle problem cho mỗi row
12. **LeetCode 239**: Sliding Window Maximum
    - *Approach*: Deque để maintain maximum trong sliding window
13. **LeetCode 301**: Remove Invalid Parentheses
    - *Approach*: BFS hoặc DFS với pruning để tìm valid combinations

### Bonus Challenges:
14. **LeetCode 32**: Longest Valid Parentheses
    - *Approach*: Stack với indices hoặc DP
15. **LeetCode 316**: Remove Duplicate Letters
    - *Approach*: Monotonic stack với greedy approach
16. **LeetCode 402**: Remove K Digits
    - *Approach*: Monotonic stack để build smallest number