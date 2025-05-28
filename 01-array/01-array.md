# Bài 1: Array - Mảng và Kỹ Thuật Xử Lý

## 📚 Lý Thuyết Cơ Bản

### Khái niệm Array
- **Array (Mảng)**: Cấu trúc dữ liệu lưu trữ các phần tử cùng kiểu dữ liệu trong bộ nhớ liên tiếp
- **Index**: Vị trí của phần tử trong mảng (bắt đầu từ 0)
- **Length**: Số phần tử trong mảng

### Đặc điểm quan trọng
- **Time Complexity**:
  - Access: O(1) - Truy cập trực tiếp qua index
  - Search: O(n) - Tìm kiếm tuyến tính
  - Insert/Delete: O(n) - Cần dịch chuyển phần tử
- **Space Complexity**: O(n) - Tỷ lệ thuận với số phần tử

### Code examples cơ bản
```python
# Khởi tạo array
arr = [1, 2, 3, 4, 5]
empty_arr = []

# Truy cập phần tử
first = arr[0]  # 1
last = arr[-1]  # 5

# Thêm/xóa phần tử
arr.append(6)        # Thêm cuối: [1,2,3,4,5,6]
arr.insert(0, 0)     # Thêm đầu: [0,1,2,3,4,5,6]
arr.pop()            # Xóa cuối: [0,1,2,3,4,5]
arr.remove(0)        # Xóa phần tử: [1,2,3,4,5]

# Duyệt mảng
for i in range(len(arr)):
    print(arr[i])
    
for num in arr:
    print(num)
```

## 🎯 Two Pointers - Kỹ Thuật Hai Con Trỏ

### Giải thích ý tưởng core
- Sử dụng hai con trỏ (left, right) để duyệt mảng từ hai đầu hoặc cùng hướng
- Giảm độ phức tạp từ O(n²) xuống O(n) trong nhiều trường hợp

### Khi nào sử dụng
- Mảng đã được sắp xếp
- Tìm cặp phần tử thỏa mãn điều kiện
- Reverse array, palindrome check
- Remove duplicates

### Step-by-step approach
1. Khởi tạo hai con trỏ (thường là left=0, right=len-1)
2. So sánh/xử lý phần tử tại hai con trỏ
3. Di chuyển con trỏ dựa trên điều kiện
4. Lặp lại cho đến khi left >= right

## 🔍 Ví Dụ Thực Tế

### Ví dụ 1: Two Sum trong Sorted Array
**Mô tả**: Tìm hai số trong mảng sắp xếp có tổng bằng target

```python
def two_sum_sorted(nums, target):
    """
    Input: nums = [2,7,11,15], target = 9
    Output: [0,1] (indices của 2 và 7)
    """
    left, right = 0, len(nums) - 1
    
    while left < right:
        current_sum = nums[left] + nums[right]
        
        if current_sum == target:
            return [left, right]
        elif current_sum < target:
            left += 1  # Cần tổng lớn hơn
        else:
            right -= 1  # Cần tổng nhỏ hơn
    
    return []  # Không tìm thấy

# Test cases
print(two_sum_sorted([2,7,11,15], 9))  # [0,1]
print(two_sum_sorted([2,3,4], 6))      # [0,2]
print(two_sum_sorted([1,2,3], 7))      # []
```

### Ví dụ 2: Remove Duplicates
**Mô tả**: Xóa phần tử trùng lặp trong mảng sắp xếp, trả về độ dài mới

```python
def remove_duplicates(nums):
    """
    Input: nums = [1,1,2,2,3]
    Output: 3 (mảng trở thành [1,2,3,_,_])
    """
    if not nums:
        return 0
    
    # Slow pointer - vị trí phần tử unique tiếp theo
    slow = 1
    
    # Fast pointer - duyệt qua mảng
    for fast in range(1, len(nums)):
        if nums[fast] != nums[fast-1]:
            nums[slow] = nums[fast]
            slow += 1
    
    return slow

# Test cases
nums1 = [1,1,2,2,3]
length = remove_duplicates(nums1)
print(f"Length: {length}, Array: {nums1[:length]}")  # Length: 3, Array: [1,2,3]
```

### Ví dụ 3: Reverse Array
**Mô tả**: Đảo ngược mảng in-place

```python
def reverse_array(nums):
    """
    Input: nums = [1,2,3,4,5]
    Output: [5,4,3,2,1]
    """
    left, right = 0, len(nums) - 1
    
    while left < right:
        # Swap phần tử
        nums[left], nums[right] = nums[right], nums[left]
        left += 1
        right -= 1
    
    return nums

# Test cases
print(reverse_array([1,2,3,4,5]))  # [5,4,3,2,1]
print(reverse_array([1,2,3,4]))    # [4,3,2,1]
```

### Ví dụ 4: Valid Palindrome
**Mô tả**: Kiểm tra chuỗi có phải palindrome (chỉ xét ký tự alphanumeric)

```python
def is_palindrome(s):
    """
    Input: s = "A man, a plan, a canal: Panama"
    Output: True
    """
    # Lọc chỉ lấy ký tự alphanumeric và chuyển về lowercase
    filtered = ''.join(c.lower() for c in s if c.isalnum())
    
    left, right = 0, len(filtered) - 1
    
    while left < right:
        if filtered[left] != filtered[right]:
            return False
        left += 1
        right -= 1
    
    return True

# Test cases
print(is_palindrome("A man, a plan, a canal: Panama"))  # True
print(is_palindrome("race a car"))  # False
```

### Ví dụ 5: Container With Most Water
**Mô tả**: Tìm hai đường thẳng tạo thành container chứa được nhiều nước nhất

```python
def max_area(height):
    """
    Input: height = [1,8,6,2,5,4,8,3,7]
    Output: 49 (giữa index 1 và 8)
    """
    left, right = 0, len(height) - 1
    max_water = 0
    
    while left < right:
        # Tính diện tích = chiều rộng × chiều cao nhỏ hơn
        width = right - left
        current_area = width * min(height[left], height[right])
        max_water = max(max_water, current_area)
        
        # Di chuyển con trỏ có chiều cao nhỏ hơn
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    
    return max_water

# Test cases
print(max_area([1,8,6,2,5,4,8,3,7]))  # 49
print(max_area([1,1]))  # 1
```

## 🏃‍♂️ Bài Tập Thực Hành

### 1. LeetCode 167: Two Sum II - Input Array Is Sorted
```python
def two_sum(numbers, target):
    left, right = 0, len(numbers) - 1
    
    while left < right:
        current_sum = numbers[left] + numbers[right]
        if current_sum == target:
            return [left + 1, right + 1]  # 1-indexed
        elif current_sum < target:
            left += 1
        else:
            right -= 1
    
    return []
```
**Link**: https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/

### 2. LeetCode 26: Remove Duplicates from Sorted Array
```python
def removeDuplicates(nums):
    if not nums:
        return 0
    
    slow = 1
    for fast in range(1, len(nums)):
        if nums[fast] != nums[fast-1]:
            nums[slow] = nums[fast]
            slow += 1
    
    return slow
```
**Link**: https://leetcode.com/problems/remove-duplicates-from-sorted-array/

### 3. LeetCode 11: Container With Most Water
```python
def maxArea(height):
    left, right = 0, len(height) - 1
    max_area = 0
    
    while left < right:
        area = (right - left) * min(height[left], height[right])
        max_area = max(max_area, area)
        
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    
    return max_area
```
**Link**: https://leetcode.com/problems/container-with-most-water/

## 🎯 Template Tổng Quát

### Two Pointers Template (Opposite Direction)
```python
def two_pointers_opposite(arr):
    left, right = 0, len(arr) - 1
    
    while left < right:
        # Xử lý logic tại arr[left] và arr[right]
        if condition_move_left:
            left += 1
        elif condition_move_right:
            right -= 1
        else:
            # Xử lý khi tìm thấy kết quả
            break
    
    return result
```

### Two Pointers Template (Same Direction)
```python
def two_pointers_same_direction(arr):
    slow = 0
    
    for fast in range(len(arr)):
        if condition_to_keep_element:
            arr[slow] = arr[fast]
            slow += 1
    
    return slow  # hoặc arr[:slow]
```

## 💡 Tips Quan Trọng

### Khi nào dùng Two Pointers
- **Sorted Array**: Hầu hết các bài toán với mảng đã sắp xếp
- **Find Pairs**: Tìm cặp phần tử thỏa mãn điều kiện
- **Remove/Filter**: Loại bỏ phần tử không mong muốn
- **Palindrome**: Kiểm tra tính đối xứng

### Common Mistakes cần tránh
- **Off-by-one errors**: Nhầm lẫn điều kiện `left < right` vs `left <= right`
- **Index out of bounds**: Không kiểm tra mảng rỗng
- **Infinite loop**: Quên di chuyển con trỏ trong một số điều kiện

### Time/Space Complexity Analysis
- **Time**: O(n) - Mỗi phần tử được duyệt tối đa 1 lần
- **Space**: O(1) - Chỉ sử dụng thêm biến con trỏ

### Edge Cases cần lưu ý
- Mảng rỗng: `[]`
- Mảng có 1 phần tử: `[1]`
- Mảng có 2 phần tử: `[1,2]`
- Tất cả phần tử giống nhau: `[1,1,1,1]`
- Không tìm thấy kết quả

## 🏆 Bài Tập Về Nhà

### Easy Level
1. **LeetCode 344: Reverse String** - Đảo ngược string sử dụng two pointers
2. **LeetCode 125: Valid Palindrome** - Kiểm tra palindrome
3. **LeetCode 283: Move Zeroes** - Di chuyển tất cả số 0 về cuối mảng

### Medium Level
4. **LeetCode 15: 3Sum** - Tìm tất cả triplet có tổng bằng 0
5. **LeetCode 16: 3Sum Closest** - Tìm triplet có tổng gần target nhất
6. **LeetCode 18: 4Sum** - Tìm tất cả quadruplet có tổng bằng target
7. **LeetCode 75: Sort Colors** - Sắp xếp mảng chỉ có 3 màu (0,1,2)

### Hard Level
8. **LeetCode 42: Trapping Rain Water** - Tính lượng nước mưa có thể chứa
9. **LeetCode 239: Sliding Window Maximum** - Tìm max trong mỗi sliding window

### Gợi ý Approach
- **3Sum/4Sum**: Sử dụng one pointer + two pointers, chú ý skip duplicates
- **Sort Colors**: Dutch National Flag algorithm với 3 pointers
- **Trapping Rain Water**: Two pointers với pre-computed max heights
- **Sliding Window**: Combine với deque hoặc monotonic stack