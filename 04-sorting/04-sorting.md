# Bài 4: Sorting Algorithms - Thuật Toán Sắp Xếp

## 📚 Lý Thuyết Cơ Bản

### Khái niệm chính
**Sorting (Sắp xếp)** là quá trình sắp xếp lại các phần tử trong một tập hợp theo một thứ tự nhất định (tăng dần hoặc giảm dần).

**Định nghĩa đơn giản**: Biến đổi một dãy không có thứ tự thành dãy có thứ tự theo tiêu chí nhất định.

### Phân loại thuật toán sắp xếp

**Theo stability:**
- **Stable**: Giữ nguyên thứ tự tương đối của các phần tử bằng nhau
- **Unstable**: Có thể thay đổi thứ tự tương đối của các phần tử bằng nhau

**Theo memory usage:**
- **In-place**: Sử dụng O(1) extra space
- **Out-of-place**: Cần thêm không gian lưu trữ

### Đặc điểm quan trọng (Time/Space Complexity)

| Algorithm | Best Case | Average Case | Worst Case | Space | Stable |
|-----------|-----------|--------------|------------|-------|---------|
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Selection Sort | O(n²) | O(n²) | O(n²) | O(1) | No |
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) | No |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | O(1) | No |

### Code Examples Cơ Bản

```python
# Bubble Sort - Sắp xếp nổi bọt
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(0, n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
    return arr

# Selection Sort - Sắp xếp chọn
def selection_sort(arr):
    n = len(arr)
    for i in range(n):
        min_idx = i
        for j in range(i + 1, n):
            if arr[j] < arr[min_idx]:
                min_idx = j
        arr[i], arr[min_idx] = arr[min_idx], arr[i]
    return arr

# Insertion Sort - Sắp xếp chèn
def insertion_sort(arr):
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j -= 1
        arr[j + 1] = key
    return arr
```

## 🎯 Divide and Conquer Sorting

### Giải thích ý tưởng core
**Divide and Conquer** áp dụng cho sorting:
1. **Divide**: Chia mảng thành các phần nhỏ hơn
2. **Conquer**: Sắp xếp các phần nhỏ (đệ quy)
3. **Combine**: Kết hợp các phần đã sắp xếp

### Khi nào sử dụng
- Cần hiệu suất ổn định O(n log n)
- Dữ liệu lớn
- Cần thuật toán stable (Merge Sort)
- Có thể chấp nhận thêm memory (Merge Sort)

### Step-by-step Approach
1. **Chọn thuật toán phù hợp** dựa trên yêu cầu
2. **Implement base case** cho đệ quy
3. **Divide step**: Chia mảng thành các phần
4. **Conquer step**: Sắp xếp đệ quy
5. **Combine step**: Kết hợp kết quả

## 🔍 Ví Dụ Thực Tế

### Ví dụ 1: Merge Sort
**Mô tả bài toán**: Sắp xếp mảng sử dụng thuật toán merge sort

**Input/Output example**:
- Input: [64, 34, 25, 12, 22, 11, 90]
- Output: [11, 12, 22, 25, 34, 64, 90]

```python
def merge_sort(arr):
    """
    Merge Sort - Thuật toán sắp xếp trộn
    Time: O(n log n) tất cả trường hợp
    Space: O(n) - cần thêm mảng tạm
    Stable: Yes
    """
    # Base case: mảng có 0 hoặc 1 phần tử
    if len(arr) <= 1:
        return arr
    
    # Divide: Chia mảng thành 2 nửa
    mid = len(arr) // 2
    left_half = arr[:mid]
    right_half = arr[mid:]
    
    # Conquer: Đệ quy sắp xếp từng nửa
    left_sorted = merge_sort(left_half)
    right_sorted = merge_sort(right_half)
    
    # Combine: Trộn 2 nửa đã sắp xếp
    return merge(left_sorted, right_sorted)

def merge(left, right):
    """Trộn 2 mảng đã sắp xếp thành 1 mảng sắp xếp"""
    result = []
    i = j = 0
    
    # So sánh và chọn phần tử nhỏ hơn
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:  # <= để đảm bảo stable
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    # Thêm các phần tử còn lại
    result.extend(left[i:])
    result.extend(right[j:])
    
    return result

# Test
arr = [64, 34, 25, 12, 22, 11, 90]
print(f"Original: {arr}")
sorted_arr = merge_sort(arr)
print(f"Sorted: {sorted_arr}")
```

**Giải thích logic từng bước**:
1. Chia mảng làm đôi cho đến khi còn 1 phần tử
2. Trộn từng cặp mảng con đã sắp xếp
3. Tiếp tục trộn cho đến khi được mảng hoàn chỉnh

### Ví dụ 2: Quick Sort
**Mô tả bài toán**: Sắp xếp nhanh với pivot

```python
def quick_sort(arr, low=0, high=None):
    """
    Quick Sort - Thuật toán sắp xếp nhanh
    Time: O(n log n) average, O(n²) worst case
    Space: O(log n) average - call stack
    Stable: No
    """
    if high is None:
        high = len(arr) - 1
    
    # Base case
    if low < high:
        # Partition: tìm vị trí đúng của pivot
        pivot_index = partition(arr, low, high)
        
        # Đệ quy sắp xếp 2 phần
        quick_sort(arr, low, pivot_index - 1)    # Phần trước pivot
        quick_sort(arr, pivot_index + 1, high)   # Phần sau pivot
    
    return arr

def partition(arr, low, high):
    """
    Lomuto Partition: 
    - Chọn phần tử cuối làm pivot
    - Đưa các phần tử nhỏ hơn pivot về trước
    - Trả về vị trí cuối cùng của pivot
    """
    pivot = arr[high]  # Chọn phần tử cuối làm pivot
    i = low - 1        # Index của phần tử nhỏ hơn pivot
    
    for j in range(low, high):
        # Nếu phần tử hiện tại <= pivot
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    
    # Đặt pivot vào vị trí đúng
    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    return i + 1

# Cải tiến với random pivot để tránh worst case
import random

def quick_sort_random(arr, low=0, high=None):
    """Quick Sort với random pivot"""
    if high is None:
        high = len(arr) - 1
    
    if low < high:
        # Random pivot để tránh worst case
        random_index = random.randint(low, high)
        arr[random_index], arr[high] = arr[high], arr[random_index]
        
        pivot_index = partition(arr, low, high)
        quick_sort_random(arr, low, pivot_index - 1)
        quick_sort_random(arr, pivot_index + 1, high)
    
    return arr

# Test
arr = [64, 34, 25, 12, 22, 11, 90]
print(f"Original: {arr}")
sorted_arr = quick_sort(arr.copy())
print(f"Quick Sort: {sorted_arr}")
```

### Ví dụ 3: Heap Sort
**Mô tả bài toán**: Sắp xếp sử dụng heap (max heap)

```python
def heap_sort(arr):
    """
    Heap Sort - Sắp xếp sử dụng heap
    Time: O(n log n) tất cả trường hợp
    Space: O(1) - in-place
    Stable: No
    """
    n = len(arr)
    
    # Build max heap từ mảng
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)
    
    # Extract elements từ heap một cách có thứ tự
    for i in range(n - 1, 0, -1):
        # Di chuyển root (max) về cuối
        arr[0], arr[i] = arr[i], arr[0]
        
        # Heapify phần còn lại
        heapify(arr, i, 0)
    
    return arr

def heapify(arr, n, i):
    """
    Duy trì tính chất max heap
    n: kích thước heap
    i: root index của subtree cần heapify
    """
    largest = i      # Root
    left = 2 * i + 1    # Left child
    right = 2 * i + 2   # Right child
    
    # Nếu left child lớn hơn root
    if left < n and arr[left] > arr[largest]:
        largest = left
    
    # Nếu right child lớn hơn largest hiện tại
    if right < n and arr[right] > arr[largest]:
        largest = right
    
    # Nếu largest không phải root
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        
        # Đệ quy heapify subtree bị ảnh hưởng
        heapify(arr, n, largest)

# Test
arr = [64, 34, 25, 12, 22, 11, 90]
print(f"Original: {arr}")
sorted_arr = heap_sort(arr.copy())
print(f"Heap Sort: {sorted_arr}")
```

### Ví dụ 4: Counting Sort
**Mô tả bài toán**: Sắp xếp đếm cho số nguyên trong phạm vi nhỏ

```python
def counting_sort(arr, max_val=None):
    """
    Counting Sort - Sắp xếp đếm
    Time: O(n + k) với k là phạm vi giá trị
    Space: O(k)
    Stable: Yes
    Áp dụng: Số nguyên không âm, phạm vi nhỏ
    """
    if not arr:
        return arr
    
    # Tìm giá trị lớn nhất nếu không được cung cấp
    if max_val is None:
        max_val = max(arr)
    
    # Tạo mảng đếm
    count = [0] * (max_val + 1)
    
    # Đếm tần suất xuất hiện của mỗi phần tử
    for num in arr:
        count[num] += 1
    
    # Tính cumulative count
    for i in range(1, len(count)):
        count[i] += count[i - 1]
    
    # Tạo mảng kết quả
    result = [0] * len(arr)
    
    # Build result array (đi từ cuối để đảm bảo stable)
    for i in range(len(arr) - 1, -1, -1):
        result[count[arr[i]] - 1] = arr[i]
        count[arr[i]] -= 1
    
    return result

# Simplified version cho việc học
def counting_sort_simple(arr):
    """Version đơn giản hơn của counting sort"""
    if not arr:
        return arr
    
    max_val = max(arr)
    count = [0] * (max_val + 1)
    
    # Đếm
    for num in arr:
        count[num] += 1
    
    # Reconstruct
    result = []
    for i, freq in enumerate(count):
        result.extend([i] * freq)
    
    return result

# Test
arr = [4, 2, 2, 8, 3, 3, 1]
print(f"Original: {arr}")
sorted_arr = counting_sort(arr)
print(f"Counting Sort: {sorted_arr}")
```

### Ví dụ 5: Radix Sort
**Mô tả bài toán**: Sắp xếp cơ số cho số nguyên

```python
def radix_sort(arr):
    """
    Radix Sort - Sắp xếp cơ số
    Time: O(d × (n + k)) với d là số chữ số, k là base (thường là 10)
    Space: O(n + k)
    Stable: Yes
    """
    if not arr:
        return arr
    
    # Tìm số có nhiều chữ số nhất
    max_num = max(arr)
    
    # Sắp xếp theo từng chữ số (từ hàng đơn vị)
    exp = 1  # 10^0 = 1 (hàng đơn vị)
    while max_num // exp > 0:
        counting_sort_by_digit(arr, exp)
        exp *= 10
    
    return arr

def counting_sort_by_digit(arr, exp):
    """
    Counting sort theo chữ số tại vị trí exp
    exp: 1 cho hàng đơn vị, 10 cho hàng chục, ...
    """
    n = len(arr)
    output = [0] * n
    count = [0] * 10  # 0-9 digits
    
    # Đếm tần suất của mỗi chữ số
    for i in range(n):
        digit = (arr[i] // exp) % 10
        count[digit] += 1
    
    # Cumulative count
    for i in range(1, 10):
        count[i] += count[i - 1]
    
    # Build output array (từ cuối để stable)
    for i in range(n - 1, -1, -1):
        digit = (arr[i] // exp) % 10
        output[count[digit] - 1] = arr[i]
        count[digit] -= 1
    
    # Copy lại vào mảng gốc
    for i in range(n):
        arr[i] = output[i]

# Test
arr = [170, 45, 75, 90, 2, 802, 24, 66]
print(f"Original: {arr}")
sorted_arr = radix_sort(arr.copy())
print(f"Radix Sort: {sorted_arr}")
```

## 🏃‍♂️ Bài Tập Thực Hành

### 1. Sort Colors (LeetCode 75 - Medium)
**Link**: https://leetcode.com/problems/sort-colors/

```python
def sort_colors(nums):
    """
    Sắp xếp mảng chỉ chứa 0, 1, 2 (Dutch National Flag Problem)
    Time: O(n), Space: O(1)
    
    Approach: 3-way partitioning
    - left: boundary của vùng 0
    - right: boundary của vùng 2  
    - i: current pointer
    """
    left, right = 0, len(nums) - 1
    i = 0
    
    while i <= right:
        if nums[i] == 0:
            # Swap với vùng 0
            nums[left], nums[i] = nums[i], nums[left]
            left += 1
            i += 1
        elif nums[i] == 2:
            # Swap với vùng 2
            nums[right], nums[i] = nums[i], nums[right]
            right -= 1
            # Không tăng i vì phần tử swap từ right chưa được check
        else:  # nums[i] == 1
            i += 1
    
    return nums

# Test
nums = [2, 0, 2, 1, 1, 0]
print(f"Original: {nums}")
sort_colors(nums)
print(f"Sorted: {nums}")
```

### 2. Merge Sorted Array (LeetCode 88 - Easy)
**Link**: https://leetcode.com/problems/merge-sorted-array/

```python
def merge_sorted_arrays(nums1, m, nums2, n):
    """
    Merge 2 sorted arrays in-place
    nums1 có đủ chỗ để chứa tất cả elements
    Time: O(m + n), Space: O(1)
    
    Approach: Merge từ cuối về đầu để tránh overwrite
    """
    # Pointers cho nums1, nums2, và vị trí cuối nums1
    i, j, k = m - 1, n - 1, m + n - 1
    
    # Merge từ cuối về đầu
    while i >= 0 and j >= 0:
        if nums1[i] > nums2[j]:
            nums1[k] = nums1[i]
            i -= 1
        else:
            nums1[k] = nums2[j]
            j -= 1
        k -= 1
    
    # Copy phần còn lại của nums2 (nếu có)
    while j >= 0:
        nums1[k] = nums2[j]
        j -= 1
        k -= 1
    
    # nums1 đã có sẵn phần còn lại nên không cần copy

# Test
nums1 = [1, 2, 3, 0, 0, 0]
nums2 = [2, 5, 6]
merge_sorted_arrays(nums1, 3, nums2, 3)
print(f"Merged: {nums1}")  # [1, 2, 2, 3, 5, 6]
```

### 3. Largest Number (LeetCode 179 - Medium)
**Link**: https://leetcode.com/problems/largest-number/

```python
from functools import cmp_to_key

def largest_number(nums):
    """
    Sắp xếp các số để tạo số lớn nhất
    Time: O(n log n), Space: O(n)
    
    Key insight: So sánh xy vs yx thay vì x vs y
    """
    # Convert to strings
    nums_str = [str(num) for num in nums]
    
    # Custom comparator
    def compare(x, y):
        # So sánh xy với yx
        if x + y > y + x:
            return -1  # x should come before y
        elif x + y < y + x:
            return 1   # y should come before x
        else:
            return 0   # equal

    # Sort với custom comparator
    nums_str.sort(key=cmp_to_key(compare))
    
    # Join và handle edge case all zeros
    result = ''.join(nums_str)
    return '0' if result[0] == '0' else result

# Test
nums = [10, 2]
print(f"Input: {nums}")
print(f"Largest Number: {largest_number(nums)}")  # "210"

nums = [3, 30, 34, 5, 9]
print(f"Input: {nums}")
print(f"Largest Number: {largest_number(nums)}")  # "9534330"
```

## 🎯 Template Tổng Quát

```python
# Template cho Comparison-based Sorting
def comparison_sort_template(arr, compare_func=None):
    """
    Template tổng quát cho thuật toán sắp xếp dựa trên so sánh
    """
    if compare_func is None:
        compare_func = lambda x, y: x <= y
    
    # Implement specific sorting algorithm logic here
    # Example với bubble sort
    n = len(arr)
    for i in range(n):
        for j in range(0, n - i - 1):
            if not compare_func(arr[j], arr[j + 1]):
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
    
    return arr

# Template cho Divide and Conquer Sorting  
def divide_conquer_sort_template(arr):
    """Template cho divide and conquer sorting"""
    # Base case
    if len(arr) <= 1:
        return arr
    
    # Divide
    mid = len(arr) // 2
    left = arr[:mid]
    right = arr[mid:]
    
    # Conquer
    left_sorted = divide_conquer_sort_template(left)
    right_sorted = divide_conquer_sort_template(right)
    
    # Combine
    return combine(left_sorted, right_sorted)

def combine(left, right):
    """Combine two sorted arrays"""
    result = []
    i = j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    result.extend(left[i:])
    result.extend(right[j:])
    return result

# Template cho Non-comparison Sorting
def counting_sort_template(arr, key_func=None, max_key=None):
    """
    Template cho counting sort và các thuật toán tương tự
    key_func: function để extract key từ element
    max_key: giá trị key lớn nhất
    """
    if not arr:
        return arr
    
    if key_func is None:
        key_func = lambda x: x
    
    if max_key is None:
        max_key = max(key_func(x) for x in arr)
    
    # Counting phase
    count = [0] * (max_key + 1)
    for item in arr:
        count[key_func(item)] += 1
    
    # Reconstruct phase
    result = []
    for key in range(max_key + 1):
        # Add all items with this key
        # (Implementation depends on specific needs)
        pass
    
    return result
```

## 💡 Tips Quan Trọng

### Khi nào dùng thuật toán nào
- **Bubble/Selection/Insertion Sort**: Mảng nhỏ (<50 elements), implementation đơn giản
- **Merge Sort**: Cần stable, worst-case O(n log n), có thêm memory
- **Quick Sort**: Average case tốt, in-place, không cần stable
- **Heap Sort**: Worst-case O(n log n), in-place, không cần stable
- **Counting Sort**: Integers trong phạm vi nhỏ, cần tốc độ
- **Radix Sort**: Integers hoặc strings, cần stable và nhanh

### Common Mistakes cần tránh
1. **Không hiểu stability**: Chọn sai thuật toán khi cần stable sort
2. **Quên handle edge cases**: Empty array, single element, duplicates
3. **Sai implementation**: Off-by-one errors, infinite loops
4. **Không optimize**: Không sử dụng built-in sort khi có thể
5. **Memory issues**: Không cân nhắc space complexity

### Time/Space Complexity Analysis
- **Comparison-based**: Lower bound là Ω(n log n)
- **Non-comparison**: Có thể đạt O(n) nhưng có constraints
- **Space**: Trade-off giữa time và space
- **Best/Average/Worst**: Phân tích kỹ từng case

### Edge Cases cần lưu ý
- Empty array hoặc null
- Array với 1 phần tử
- Array đã sắp xếp (best case)
- Array sắp xếp ngược (worst case)
- Array có nhiều duplicates
- Very large arrays (memory constraints)

## 🏆 Bài Tập Về Nhà

### Easy Level
1. **Merge Sorted Array** (LeetCode 88)
2. **Sort Array By Parity** (LeetCode 905)
3. **Squares of a Sorted Array** (LeetCode 977)
4. **Intersection of Two Arrays** (LeetCode 349)
5. **Contains Duplicate** (LeetCode 217)

**Gợi ý approach**: Sử dụng built-in sort hoặc implement basic sorting algorithms.

### Medium Level
1. **Sort Colors** (LeetCode 75)
2. **Largest Number** (LeetCode 179)
3. **Sort List** (LeetCode 148) 
4. **Kth Largest Element in Array** (LeetCode 215)
5. **Top K Frequent Elements** (LeetCode 347)
6. **Meeting Rooms II** (LeetCode 253)
7. **Wiggle Sort II** (LeetCode 324)

**Gợi ý approach**:
- **Sort Colors**: Dutch National Flag (3-way partitioning)
- **Largest Number**: Custom comparator
- **Sort List**: Merge sort cho linked list
- **Kth Largest**: Quick select hoặc heap
- **Top K Frequent**: Heap hoặc bucket sort

### Hard Level
1. **Count of Smaller Numbers After Self** (LeetCode 315)
2. **Reverse Pairs** (LeetCode 493)
3. **Maximum Gap** (LeetCode 164)
4. **Count of Range Sum** (LeetCode 327)
5. **Median of Two Sorted Arrays** (LeetCode 4)

**Gợi ý approach**:
- **Count of Smaller**: Merge sort với counting, Binary Indexed Tree
- **Reverse Pairs**: Modified merge sort
- **Maximum Gap**: Radix sort hoặc bucket sort
- **Count of Range Sum**: Merge sort với prefix sums
- **Median of Two Sorted**: Binary search

### Practice Strategy
1. **Tuần 1**: Master basic sorting algorithms (bubble, selection, insertion)
2. **Tuần 2**: Learn divide-and-conquer sorts (merge, quick, heap)
3. **Tuần 3**: Non-comparison sorts (counting, radix, bucket)
4. **Tuần 4**: Apply sorting to solve complex problems
5. **Review**: Optimize solutions và understand trade-offs

### Sorting Applications
- **Database indexing**: B-tree sorting
- **External sorting**: Large datasets không fit memory
- **Parallel sorting**: Multi-threaded sorting algorithms
- **Stable sorting**: Khi thứ tự tương đối quan trọng
- **Custom sorting**: Sort theo multiple criteria