# Bài 3: Recursion - Đệ Quy

## 📚 Lý Thuyết Cơ Bản

### Khái niệm chính
**Recursion (Đệ quy)** là một kỹ thuật lập trình trong đó một hàm gọi chính nó để giải quyết các bài toán nhỏ hơn của cùng một vấn đề.

**Định nghĩa đơn giản**: Đệ quy giống như việc chia một bài toán lớn thành nhiều bài toán nhỏ hơn giống nhau, rồi giải quyết từng bài toán nhỏ.

### Đặc điểm quan trọng

**Cấu trúc cơ bản của đệ quy:**
1. **Base Case (Điều kiện dừng)**: Trường hợp đơn giản nhất không cần đệ quy
2. **Recursive Case (Trường hợp đệ quy)**: Hàm gọi lại chính nó với input nhỏ hơn

**Time/Space Complexity:**
- **Time Complexity**: Thường là O(n) đến O(2^n) tùy thuộc vào số lần gọi đệ quy
- **Space Complexity**: O(n) cho call stack trong trường hợp tốt nhất

### Code Examples Cơ Bản

```python
# Ví dụ 1: Tính giai thừa
def factorial(n):
    # Base case
    if n <= 1:
        return 1
    # Recursive case
    return n * factorial(n - 1)

# Ví dụ 2: Dãy Fibonacci
def fibonacci(n):
    # Base cases
    if n <= 1:
        return n
    # Recursive case
    return fibonacci(n - 1) + fibonacci(n - 2)

# Ví dụ 3: Tính tổng các số từ 1 đến n
def sum_numbers(n):
    # Base case
    if n <= 0:
        return 0
    # Recursive case
    return n + sum_numbers(n - 1)
```

## 🎯 Kỹ Thuật Đệ Quy

### Giải thích ý tưởng core
Đệ quy hoạt động theo nguyên lý "Divide and Conquer" (Chia để trị):
1. **Chia**: Chia bài toán thành các bài toán con nhỏ hơn
2. **Trị**: Giải quyết bài toán con (gọi đệ quy)
3. **Kết hợp**: Kết hợp kết quả của các bài toán con

### Khi nào sử dụng
- Bài toán có thể chia thành các bài toán con tương tự
- Cấu trúc dữ liệu dạng cây (Tree, Binary Tree)
- Thuật toán backtracking
- Bài toán có tính chất tự tương tự (self-similar)

### Step-by-step Approach
1. **Xác định base case**: Trường hợp đơn giản nhất
2. **Xác định recursive relation**: Mối quan hệ giữa bài toán và bài toán con
3. **Đảm bảo tiến về base case**: Input phải giảm dần về base case
4. **Implement và test**: Viết code và kiểm tra

## 🔍 Ví Dụ Thực Tế

### Ví dụ 1: Reverse String
**Mô tả bài toán**: Đảo ngược một chuỗi sử dụng đệ quy

**Input/Output example**:
- Input: "hello"
- Output: "olleh"

```python
def reverse_string(s):
    """
    Đảo ngược chuỗi sử dụng đệ quy
    Time: O(n), Space: O(n)
    """
    # Base case: chuỗi rỗng hoặc 1 ký tự
    if len(s) <= 1:
        return s
    
    # Recursive case: ký tự cuối + đảo ngược phần còn lại
    return s[-1] + reverse_string(s[:-1])

# Test
print(reverse_string("hello"))  # "olleh"
print(reverse_string("a"))      # "a"
print(reverse_string(""))       # ""
```

**Giải thích logic từng bước**:
1. Nếu chuỗi có 0 hoặc 1 ký tự → trả về chuỗi gốc
2. Ngược lại, lấy ký tự cuối + đảo ngược phần còn lại
3. Quá trình lặp lại cho đến khi về base case

### Ví dụ 2: Binary Tree Traversal
**Mô tả bài toán**: Duyệt cây nhị phân theo thứ tự inorder

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def inorder_traversal(root):
    """
    Duyệt cây nhị phân inorder: Left -> Root -> Right
    Time: O(n), Space: O(h) với h là chiều cao cây
    """
    result = []
    
    def inorder(node):
        # Base case: node rỗng
        if not node:
            return
        
        # Recursive case
        inorder(node.left)    # Duyệt cây con trái
        result.append(node.val)  # Thăm node hiện tại
        inorder(node.right)   # Duyệt cây con phải
    
    inorder(root)
    return result

# Test
#     1
#   /   \
#  2     3
# / \
#4   5
root = TreeNode(1)
root.left = TreeNode(2)
root.right = TreeNode(3)
root.left.left = TreeNode(4)
root.left.right = TreeNode(5)

print(inorder_traversal(root))  # [4, 2, 5, 1, 3]
```

### Ví dụ 3: Generate Parentheses
**Mô tả bài toán**: Sinh tất cả các cách đặt n cặp dấu ngoặc hợp lệ

**Input/Output example**:
- Input: n = 3
- Output: ["((()))", "(()())", "(())()", "()(())", "()()()"]

```python
def generate_parentheses(n):
    """
    Sinh tất cả cách đặt n cặp dấu ngoặc hợp lệ
    Time: O(4^n / sqrt(n)), Space: O(4^n / sqrt(n))
    """
    result = []
    
    def backtrack(current, open_count, close_count):
        # Base case: đã dùng hết n cặp ngoặc
        if len(current) == 2 * n:
            result.append(current)
            return
        
        # Thêm ngoặc mở nếu còn có thể
        if open_count < n:
            backtrack(current + "(", open_count + 1, close_count)
        
        # Thêm ngoặc đóng nếu hợp lệ
        if close_count < open_count:
            backtrack(current + ")", open_count, close_count + 1)
    
    backtrack("", 0, 0)
    return result

# Test
print(generate_parentheses(2))  # ["(())", "()()"]
print(generate_parentheses(3))  # ["((()))", "(()())", "(())()", "()(())", "()()()"]
```

### Ví dụ 4: Power Function
**Mô tả bài toán**: Tính x^n một cách hiệu quả

```python
def power(x, n):
    """
    Tính x^n sử dụng đệ quy (Fast Exponentiation)
    Time: O(log n), Space: O(log n)
    """
    # Base cases
    if n == 0:
        return 1
    if n < 0:
        return 1 / power(x, -n)
    
    # Recursive case: sử dụng tính chất x^n = (x^(n/2))^2
    if n % 2 == 0:
        half = power(x, n // 2)
        return half * half
    else:
        return x * power(x, n - 1)

# Test
print(power(2, 10))   # 1024
print(power(2, -2))   # 0.25
print(power(3, 4))    # 81
```

### Ví dụ 5: Merge Sort
**Mô tả bài toán**: Sắp xếp mảng sử dụng thuật toán merge sort

```python
def merge_sort(arr):
    """
    Sắp xếp mảng sử dụng merge sort
    Time: O(n log n), Space: O(n)
    """
    # Base case: mảng có 0 hoặc 1 phần tử
    if len(arr) <= 1:
        return arr
    
    # Chia mảng thành 2 nửa
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])    # Đệ quy sắp xếp nửa trái
    right = merge_sort(arr[mid:])   # Đệ quy sắp xếp nửa phải
    
    # Trộn 2 nửa đã sắp xếp
    return merge(left, right)

def merge(left, right):
    """Trộn 2 mảng đã sắp xếp"""
    result = []
    i = j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    # Thêm phần tử còn lại
    result.extend(left[i:])
    result.extend(right[j:])
    return result

# Test
print(merge_sort([64, 34, 25, 12, 22, 11, 90]))  # [11, 12, 22, 25, 34, 64, 90]
```

## 🏃‍♂️ Bài Tập Thực Hành

### 1. Climbing Stairs (LeetCode 70 - Easy)
**Link**: https://leetcode.com/problems/climbing-stairs/

```python
def climb_stairs(n):
    """
    Có n bước cầu thang, mỗi lần có thể bước 1 hoặc 2 bước.
    Tính số cách để lên đến bước thứ n.
    
    Phân tích: climb_stairs(n) = climb_stairs(n-1) + climb_stairs(n-2)
    Time: O(2^n) - chưa tối ưu, Space: O(n)
    """
    # Base cases
    if n <= 2:
        return n
    
    # Recursive case
    return climb_stairs(n - 1) + climb_stairs(n - 2)

# Tối ưu với memoization
def climb_stairs_memo(n, memo={}):
    if n in memo:
        return memo[n]
    
    if n <= 2:
        return n
    
    memo[n] = climb_stairs_memo(n - 1, memo) + climb_stairs_memo(n - 2, memo)
    return memo[n]
```

### 2. Maximum Depth of Binary Tree (LeetCode 104 - Easy)
**Link**: https://leetcode.com/problems/maximum-depth-of-binary-tree/

```python
def max_depth(root):
    """
    Tìm chiều sâu tối đa của cây nhị phân
    Time: O(n), Space: O(h)
    """
    # Base case: node rỗng
    if not root:
        return 0
    
    # Recursive case: 1 + max của 2 cây con
    left_depth = max_depth(root.left)
    right_depth = max_depth(root.right)
    
    return 1 + max(left_depth, right_depth)
```

### 3. Permutations (LeetCode 46 - Medium)
**Link**: https://leetcode.com/problems/permutations/

```python
def permute(nums):
    """
    Tạo tất cả hoán vị của mảng nums
    Time: O(n! * n), Space: O(n! * n)
    """
    result = []
    
    def backtrack(current_perm, remaining):
        # Base case: không còn số nào để chọn
        if not remaining:
            result.append(current_perm[:])  # Copy list
            return
        
        # Recursive case: thử từng số còn lại
        for i in range(len(remaining)):
            # Chọn số thứ i
            current_perm.append(remaining[i])
            # Đệ quy với các số còn lại
            backtrack(current_perm, remaining[:i] + remaining[i+1:])
            # Backtrack (bỏ chọn)
            current_perm.pop()
    
    backtrack([], nums)
    return result
```

## 🎯 Template Tổng Quát

```python
def recursive_function(input_params):
    """
    Template tổng quát cho hàm đệ quy
    """
    # Base case(s) - Điều kiện dừng
    if base_condition:
        return base_result
    
    # Recursive case(s) - Gọi đệ quy
    # Xử lý input để tạo input nhỏ hơn
    smaller_input = process_input(input_params)
    
    # Gọi đệ quy với input nhỏ hơn
    recursive_result = recursive_function(smaller_input)
    
    # Kết hợp kết quả để trả về
    final_result = combine_results(recursive_result, current_level_work)
    
    return final_result

# Template cho backtracking
def backtrack_template(candidates, target, current_solution, all_solutions):
    # Base case: tìm được solution hoặc không thể tiếp tục
    if is_solution_complete(current_solution, target):
        all_solutions.append(current_solution[:])  # Copy solution
        return
    
    # Pruning: cắt tỉa nhánh không cần thiết
    if should_prune(current_solution, target):
        return
    
    # Thử tất cả các lựa chọn có thể
    for candidate in get_candidates(candidates, current_solution):
        # Chọn
        current_solution.append(candidate)
        
        # Đệ quy
        backtrack_template(candidates, target, current_solution, all_solutions)
        
        # Backtrack (bỏ chọn)
        current_solution.pop()
```

## 💡 Tips Quan Trọng

### Khi nào dùng Recursion
- **Cấu trúc dữ liệu dạng cây**: Binary tree, N-ary tree
- **Bài toán chia để trị**: Merge sort, Quick sort
- **Backtracking**: Tìm tất cả solutions, permutations, combinations
- **Dynamic Programming**: Có thể optimize bằng memoization
- **Mathematical problems**: Factorial, Fibonacci, GCD

### Common Mistakes cần tránh
1. **Quên base case**: Gây stack overflow
2. **Base case sai**: Logic không đúng
3. **Không tiến về base case**: Infinite recursion
4. **Không optimize**: Time complexity quá cao (như Fibonacci thuần túy)
5. **Stack overflow**: Với input lớn, cần cân nhắc iterative solution

### Time/Space Complexity Analysis
- **Time**: Phân tích số lần gọi đệ quy và work ở mỗi level
- **Space**: Call stack depth + auxiliary space
- **Optimization**: Memoization, tail recursion, iterative conversion

### Edge Cases cần lưu ý
- Input rỗng hoặc null
- Input có 1 phần tử
- Input âm (nếu applicable)
- Very large input (stack overflow risk)

## 🏆 Bài Tập Về Nhà

### Easy Level
1. **Factorial** - Tính giai thừa
2. **Sum of Natural Numbers** - Tổng từ 1 đến n
3. **Binary Tree Preorder Traversal** (LeetCode 144)
4. **Same Tree** (LeetCode 100)
5. **Invert Binary Tree** (LeetCode 226)

**Gợi ý approach**: Focus on identifying base case và recursive relation đơn giản.

### Medium Level
1. **Generate Parentheses** (LeetCode 22)
2. **Subsets** (LeetCode 78) 
3. **Letter Combinations of Phone Number** (LeetCode 17)
4. **Binary Tree Level Order Traversal** (LeetCode 102)
5. **Validate Binary Search Tree** (LeetCode 98)
6. **Kth Smallest Element in BST** (LeetCode 230)

**Gợi ý approach**: Sử dụng backtracking cho generation problems, tree traversal cho tree problems.

### Hard Level
1. **N-Queens** (LeetCode 51)
2. **Word Ladder II** (LeetCode 126)
3. **Serialize and Deserialize Binary Tree** (LeetCode 297)
4. **Regular Expression Matching** (LeetCode 10)
5. **Sudoku Solver** (LeetCode 37)

**Gợi ý approach**:
- **N-Queens**: Backtracking với pruning
- **Word Ladder II**: BFS + DFS/Backtracking để rebuild paths
- **Serialize/Deserialize**: Preorder traversal với null markers
- **Regex Matching**: 2D recursion với memoization
- **Sudoku**: Backtracking với constraint checking

### Practice Strategy
1. **Tuần 1**: Focus on Easy problems, master basic recursion
2. **Tuần 2**: Medium problems, learn backtracking patterns
3. **Tuần 3**: Tree-related recursion problems
4. **Tuần 4**: Hard problems, combine multiple techniques
5. **Review**: Tối ưu solutions với memoization/iterative approaches