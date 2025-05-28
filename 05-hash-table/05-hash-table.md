# Bài 5: Hash Table (Bảng Băm)

## 📚 Lý Thuyết Cơ Bản

### Khái niệm chính
Hash Table (Bảng băm) là cấu trúc dữ liệu sử dụng hash function để ánh xạ keys thành indices trong một mảng, cho phép truy xuất, thêm và xóa dữ liệu với độ phức tạp trung bình O(1).

### Đặc điểm quan trọng
- **Time Complexity:**
  - Average case: O(1) cho insert, delete, search
  - Worst case: O(n) khi có nhiều collision
- **Space Complexity:** O(n) với n là số phần tử

### Code examples cơ bản trong Python

```python
# Sử dụng dictionary built-in
hash_map = {}
hash_map['key1'] = 'value1'
hash_map['key2'] = 'value2'

# Truy xuất
print(hash_map['key1'])  # Output: value1

# Kiểm tra key tồn tại
if 'key1' in hash_map:
    print("Key exists")

# Xóa
del hash_map['key1']

# Hash table với Counter
from collections import Counter
count_map = Counter([1, 2, 2, 3, 3, 3])
print(count_map)  # Counter({3: 3, 2: 2, 1: 1})

# Hash table với defaultdict
from collections import defaultdict
dd = defaultdict(list)
dd['fruits'].append('apple')
dd['fruits'].append('banana')
```

## 🎯 Kỹ Thuật Hash Table

### Giải thích ý tưởng core
Hash Table sử dụng hash function để chuyển đổi key thành index trong mảng. Điều này cho phép truy xuất trực tiếp thay vì tìm kiếm tuần tự.

### Khi nào sử dụng
- Cần tra cứu nhanh với key
- Đếm tần suất xuất hiện
- Lưu trữ mapping giữa hai giá trị
- Loại bỏ duplicate
- Implement cache/memoization

### Step-by-step approach
1. **Xác định key-value relationship**
2. **Chọn cấu trúc phù hợp** (dict, Counter, defaultdict)
3. **Xử lý collision** (nếu cần implement từ đầu)
4. **Optimize cho use case cụ thể**

## 🔍 Ví Dụ Thực Tế

### Ví dụ 1: Two Sum Problem
**Mô tả:** Tìm hai số trong mảng có tổng bằng target.

**Input:** nums = [2, 7, 11, 15], target = 9  
**Output:** [0, 1]

```python
def two_sum(nums, target):
    """
    Sử dụng hash table để lưu các số đã gặp và index của chúng
    Time: O(n), Space: O(n)
    """
    num_to_index = {}  # num -> index
    
    for i, num in enumerate(nums):
        complement = target - num
        
        # Kiểm tra complement có trong hash table không
        if complement in num_to_index:
            return [num_to_index[complement], i]
        
        # Lưu số hiện tại vào hash table
        num_to_index[num] = i
    
    return []

# Test case
nums = [2, 7, 11, 15]
target = 9
print(two_sum(nums, target))  # [0, 1]
```

### Ví dụ 2: Group Anagrams
**Mô tả:** Nhóm các từ là anagram của nhau.

**Input:** strs = ["eat","tea","tan","ate","nat","bat"]  
**Output:** [["bat"],["nat","tan"],["ate","eat","tea"]]

```python
def group_anagrams(strs):
    """
    Sử dụng sorted string làm key để nhóm anagrams
    Time: O(n * m log m), Space: O(n * m)
    """
    from collections import defaultdict
    
    anagram_groups = defaultdict(list)
    
    for s in strs:
        # Sắp xếp các ký tự để tạo key
        key = ''.join(sorted(s))
        anagram_groups[key].append(s)
    
    return list(anagram_groups.values())

# Test case
strs = ["eat","tea","tan","ate","nat","bat"]
result = group_anagrams(strs)
print(result)  # [['eat', 'tea', 'ate'], ['tan', 'nat'], ['bat']]
```

### Ví dụ 3: Valid Anagram
**Mô tả:** Kiểm tra hai string có phải anagram không.

**Input:** s = "anagram", t = "nagaram"  
**Output:** true

```python
def is_anagram(s, t):
    """
    Đếm tần suất ký tự trong hai string
    Time: O(n), Space: O(1) - tối đa 26 ký tự
    """
    if len(s) != len(t):
        return False
    
    from collections import Counter
    return Counter(s) == Counter(t)

# Cách 2: Sử dụng dictionary thủ công
def is_anagram_v2(s, t):
    if len(s) != len(t):
        return False
    
    char_count = {}
    
    # Đếm ký tự trong s
    for char in s:
        char_count[char] = char_count.get(char, 0) + 1
    
    # Trừ đi ký tự trong t
    for char in t:
        if char not in char_count:
            return False
        char_count[char] -= 1
        if char_count[char] == 0:
            del char_count[char]
    
    return len(char_count) == 0

# Test cases
print(is_anagram("anagram", "nagaram"))  # True
print(is_anagram("rat", "car"))          # False
```

### Ví dụ 4: Top K Frequent Elements
**Mô tả:** Tìm k phần tử xuất hiện nhiều nhất.

**Input:** nums = [1,1,1,2,2,3], k = 2  
**Output:** [1,2]

```python
def top_k_frequent(nums, k):
    """
    Đếm tần suất rồi sắp xếp theo frequency
    Time: O(n log n), Space: O(n)
    """
    from collections import Counter
    import heapq
    
    # Đếm tần suất
    count = Counter(nums)
    
    # Sử dụng heap để tìm k phần tử top
    # Python heapq là min heap, nên dùng negative frequency
    heap = [(-freq, num) for num, freq in count.items()]
    heapq.heapify(heap)
    
    result = []
    for _ in range(k):
        freq, num = heapq.heappop(heap)
        result.append(num)
    
    return result

# Cách 2: Bucket sort - O(n) time
def top_k_frequent_v2(nums, k):
    from collections import Counter
    
    count = Counter(nums)
    # Tạo bucket theo frequency
    buckets = [[] for _ in range(len(nums) + 1)]
    
    for num, freq in count.items():
        buckets[freq].append(num)
    
    result = []
    # Duyệt từ frequency cao xuống thấp
    for i in range(len(buckets) - 1, -1, -1):
        if buckets[i]:
            result.extend(buckets[i])
            if len(result) >= k:
                break
    
    return result[:k]

# Test case
nums = [1,1,1,2,2,3]
k = 2
print(top_k_frequent(nums, k))  # [1, 2]
```

### Ví dụ 5: Longest Consecutive Sequence
**Mô tả:** Tìm độ dài của dãy số liên tiếp dài nhất.

**Input:** nums = [100,4,200,1,3,2]  
**Output:** 4 (dãy [1,2,3,4])

```python
def longest_consecutive(nums):
    """
    Sử dụng set để check existence trong O(1)
    Time: O(n), Space: O(n)
    """
    if not nums:
        return 0
    
    num_set = set(nums)
    longest = 0
    
    for num in num_set:
        # Chỉ bắt đầu đếm từ số đầu tiên của sequence
        if num - 1 not in num_set:
            current_num = num
            current_length = 1
            
            # Đếm độ dài sequence
            while current_num + 1 in num_set:
                current_num += 1
                current_length += 1
            
            longest = max(longest, current_length)
    
    return longest

# Test case
nums = [100,4,200,1,3,2]
print(longest_consecutive(nums))  # 4
```

## 🏃‍♂️ Bài Tập Thực Hành

### 1. Contains Duplicate (LeetCode 217)
```python
def contains_duplicate(nums):
    """
    Kiểm tra mảng có phần tử trùng lặp không
    Time: O(n), Space: O(n)
    """
    seen = set()
    for num in nums:
        if num in seen:
            return True
        seen.add(num)
    return False

# Hoặc đơn giản hơn
def contains_duplicate_v2(nums):
    return len(set(nums)) != len(nums)
```

### 2. Valid Parentheses với Hash Table (LeetCode 20)
```python
def is_valid(s):
    """
    Kiểm tra dấu ngoặc hợp lệ
    Time: O(n), Space: O(n)
    """
    stack = []
    mapping = {')': '(', '}': '{', ']': '['}
    
    for char in s:
        if char in mapping:  # Closing bracket
            if not stack or stack.pop() != mapping[char]:
                return False
        else:  # Opening bracket
            stack.append(char)
    
    return not stack
```

### 3. Find All Anagrams in a String (LeetCode 438)
```python
def find_anagrams(s, p):
    """
    Tìm tất cả vị trí bắt đầu của anagram của p trong s
    Time: O(n), Space: O(1)
    """
    from collections import Counter
    
    if len(p) > len(s):
        return []
    
    p_count = Counter(p)
    window_count = Counter()
    result = []
    
    # Sliding window
    for i in range(len(s)):
        # Thêm ký tự mới vào window
        window_count[s[i]] += 1
        
        # Xóa ký tự cũ khỏi window
        if i >= len(p):
            left_char = s[i - len(p)]
            window_count[left_char] -= 1
            if window_count[left_char] == 0:
                del window_count[left_char]
        
        # Kiểm tra anagram
        if window_count == p_count:
            result.append(i - len(p) + 1)
    
    return result
```

## 🎯 Template Tổng Quát

```python
# Template 1: Frequency Counting
def frequency_pattern(arr):
    from collections import Counter
    count = Counter(arr)
    
    # Xử lý logic dựa trên frequency
    for item, freq in count.items():
        # Logic here
        pass
    
    return result

# Template 2: Two Pointer với Hash Table
def two_pointer_hash_pattern(arr, target):
    hash_map = {}
    
    for i, val in enumerate(arr):
        complement = target - val
        if complement in hash_map:
            return [hash_map[complement], i]
        hash_map[val] = i
    
    return []

# Template 3: Grouping Pattern
def grouping_pattern(items):
    from collections import defaultdict
    groups = defaultdict(list)
    
    for item in items:
        key = generate_key(item)  # Tự định nghĩa
        groups[key].append(item)
    
    return list(groups.values())

# Template 4: Set cho Existence Check
def existence_check_pattern(arr):
    item_set = set(arr)
    
    for item in arr:
        if condition_check(item, item_set):  # Tự định nghĩa
            # Process
            pass
    
    return result
```

## 💡 Tips Quan Trọng

### Khi nào dùng Hash Table
- Cần tra cứu nhanh theo key
- Đếm tần suất (frequency counting)
- Loại bỏ duplicate
- Grouping dữ liệu
- Implement cache/memoization
- Two sum và các variant

### Common mistakes cần tránh
1. **Quên xử lý collision** khi implement từ đầu
2. **Không kiểm tra key existence** trước khi truy xuất
3. **Sử dụng mutable object làm key** (list, dict)
4. **Không tối ưu space** khi không cần thiết
5. **Hash function kém** dẫn đến nhiều collision

### Time/Space Complexity Analysis
- **Average case:** O(1) cho tất cả operations
- **Worst case:** O(n) khi tất cả keys hash về cùng 1 bucket
- **Space complexity:** O(n) với n là số key-value pairs
- **Load factor:** Nên giữ < 0.75 để maintain performance

### Edge cases cần lưu ý
- Empty input
- Duplicate keys
- Null/None values
- Integer overflow trong hash function
- Memory limit với large datasets

## 🏆 Bài Tập Về Nhà

### Easy Level
1. **Two Sum (LeetCode 1)**
   - Approach: Hash table để lưu complement
   - Time: O(n), Space: O(n)

2. **Roman to Integer (LeetCode 13)**
   - Approach: Hash table cho mapping symbols
   - Focus: String processing với lookup

3. **Valid Anagram (LeetCode 242)**
   - Approach: Character frequency counting
   - Alternative: Sorting approach

4. **Missing Number (LeetCode 268)**
   - Approach: Set difference hoặc bit manipulation
   - Time: O(n), Space: O(n) hoặc O(1)

### Medium Level
1. **Group Anagrams (LeetCode 49)**
   - Approach: Sorted string làm key
   - Time: O(n * m log m), Space: O(n * m)

2. **Top K Frequent Elements (LeetCode 347)**
   - Approach: Counter + Heap hoặc Bucket sort
   - Time: O(n log k) hoặc O(n), Space: O(n)

3. **Product of Array Except Self (LeetCode 238)**
   - Approach: Không dùng hash table trực tiếp nhưng tư duy tương tự
   - Focus: Optimize space complexity

4. **Subarray Sum Equals K (LeetCode 560)**
   - Approach: Prefix sum với hash table
   - Time: O(n), Space: O(n)

5. **Longest Consecutive Sequence (LeetCode 128)**
   - Approach: Set cho O(1) lookup
   - Time: O(n), Space: O(n)

### Hard Level
1. **First Missing Positive (LeetCode 41)**
   - Approach: Array như hash table với index mapping
   - Challenge: O(1) space complexity

2. **Substring with Concatenation of All Words (LeetCode 30)**
   - Approach: Sliding window với hash table
   - Time: O(n * m), Space: O(m)

3. **Design Twitter (LeetCode 355)**
   - Approach: Multiple hash tables cho users, tweets, follows
   - Focus: System design với hash table

### Gợi ý approach chung:
- **Easy:** Focus vào basic hash table operations
- **Medium:** Combine với other techniques (sliding window, two pointers)
- **Hard:** Multiple hash tables hoặc advanced optimizations

### Pattern Recognition:
- **Frequency problems:** Dùng Counter
- **Grouping problems:** Dùng defaultdict
- **Existence check:** Dùng set
- **Two sum variants:** Dùng dict cho complement lookup
- **Sliding window:** Combine hash table với window technique