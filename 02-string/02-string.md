# Bài 2: String - Chuỗi Ký Tự và Kỹ Thuật Xử Lý

## 📚 Lý Thuyết Cơ Bản

### Khái niệm String
- **String (Chuỗi)**: Dãy các ký tự được lưu trữ liên tiếp trong bộ nhớ
- **Immutable**: Trong Python, string không thể thay đổi (tạo string mới khi modify)
- **Index**: Vị trí ký tự trong chuỗi (bắt đầu từ 0)
- **Length**: Số ký tự trong chuỗi

### Đặc điểm quan trọng
- **Time Complexity**:
  - Access: O(1) - Truy cập ký tự qua index
  - Search: O(n) - Tìm kiếm substring
  - Concatenation: O(n) - Nối chuỗi
  - Slicing: O(k) - k là độ dài substring
- **Space Complexity**: O(n) - Tỷ lệ thuận với độ dài chuỗi

### Code examples cơ bản
```python
# Khởi tạo string
s = "Hello World"
empty_s = ""

# Truy cập ký tự
first = s[0]        # 'H'
last = s[-1]        # 'd'
substring = s[0:5]  # 'Hello'

# String methods quan trọng
s.lower()           # 'hello world'
s.upper()           # 'HELLO WORLD'
s.split()           # ['Hello', 'World']
s.replace('o', '0') # 'Hell0 W0rld'
s.strip()           # Xóa whitespace đầu cuối
len(s)              # 11

# Kiểm tra ký tự
'H' in s            # True
s.startswith('He')  # True
s.endswith('ld')    # True
s.isalpha()         # False (có space)
s.isdigit()         # False
s.isalnum()         # False

# String formatting
name = "Alice"
age = 25
f"My name is {name}, I'm {age}"  # F-string (Python 3.6+)
```

## 🎯 Sliding Window - Kỹ Thuật Cửa Sổ Trượt

### Giải thích ý tưởng core
- Sử dụng hai con trỏ (left, right) tạo thành "cửa sổ" có thể mở rộng/thu hẹp
- Duy trì một cửa sổ thỏa mãn điều kiện và tối ưu hóa kết quả
- Giảm độ phức tạp từ O(n²) hoặc O(n³) xuống O(n)

### Khi nào sử dụng
- Tìm substring thỏa mãn điều kiện (longest, shortest)
- Đếm số lượng substring
- Pattern matching problems
- Anagram, permutation problems

### Step-by-step approach
1. Khởi tạo left = 0, right = 0
2. Mở rộng cửa sổ (tăng right) cho đến khi thỏa mãn điều kiện
3. Thu hẹp cửa sổ (tăng left) để tối ưu hóa
4. Cập nhật kết quả tốt nhất
5. Lặp lại cho đến khi right đạt cuối chuỗi

## 🔍 Ví Dụ Thực Tế

### Ví dụ 1: Longest Substring Without Repeating Characters
**Mô tả**: Tìm độ dài substring dài nhất không có ký tự lặp lại

```python
def length_of_longest_substring(s):
    """
    Input: s = "abcabcbb"
    Output: 3 (substring "abc")
    """
    char_set = set()
    left = 0
    max_length = 0
    
    for right in range(len(s)):
        # Thu hẹp cửa sổ khi gặp ký tự trùng lặp
        while s[right] in char_set:
            char_set.remove(s[left])
            left += 1
        
        # Thêm ký tự mới vào cửa sổ
        char_set.add(s[right])
        
        # Cập nhật kết quả
        max_length = max(max_length, right - left + 1)
    
    return max_length

# Test cases
print(length_of_longest_substring("abcabcbb"))  # 3
print(length_of_longest_substring("bbbbb"))     # 1
print(length_of_longest_substring("pwwkew"))    # 3
print(length_of_longest_substring(""))          # 0
```

### Ví dụ 2: Valid Anagram
**Mô tả**: Kiểm tra hai chuỗi có phải anagram của nhau không

```python
def is_anagram(s, t):
    """
    Input: s = "anagram", t = "nagaram"
    Output: True
    """
    if len(s) != len(t):
        return False
    
    # Phương pháp 1: Sử dụng Counter
    from collections import Counter
    return Counter(s) == Counter(t)

# Phương pháp 2: Sử dụng dictionary
def is_anagram_dict(s, t):
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

### Ví dụ 3: Group Anagrams
**Mô tả**: Nhóm các chuỗi là anagram của nhau

```python
def group_anagrams(strs):
    """
    Input: strs = ["eat","tea","tan","ate","nat","bat"]
    Output: [["bat"],["nat","tan"],["ate","eat","tea"]]
    """
    from collections import defaultdict
    
    anagram_groups = defaultdict(list)
    
    for s in strs:
        # Sử dụng sorted string làm key
        key = ''.join(sorted(s))
        anagram_groups[key].append(s)
    
    return list(anagram_groups.values())

# Phương pháp 2: Sử dụng character count
def group_anagrams_count(strs):
    from collections import defaultdict
    
    anagram_groups = defaultdict(list)
    
    for s in strs:
        # Tạo key từ character count
        count = [0] * 26
        for char in s:
            count[ord(char) - ord('a')] += 1
        
        # Tuple có thể dùng làm key
        key = tuple(count)
        anagram_groups[key].append(s)
    
    return list(anagram_groups.values())

# Test cases
strs = ["eat","tea","tan","ate","nat","bat"]
print(group_anagrams(strs))
```

### Ví dụ 4: Minimum Window Substring
**Mô tả**: Tìm substring ngắn nhất chứa tất cả ký tự của pattern

```python
def min_window(s, t):
    """
    Input: s = "ADOBECODEBANC", t = "ABC"
    Output: "BANC"
    """
    if not s or not t:
        return ""
    
    from collections import Counter
    
    # Đếm ký tự cần thiết trong t
    dict_t = Counter(t)
    required = len(dict_t)
    
    # Sliding window variables
    left = right = 0
    formed = 0  # Số ký tự unique đã thỏa mãn
    
    # Dictionary để track ký tự trong window
    window_counts = {}
    
    # Kết quả: (window length, left, right)
    ans = float("inf"), None, None
    
    while right < len(s):
        # Thêm ký tự từ phải vào window
        character = s[right]
        window_counts[character] = window_counts.get(character, 0) + 1
        
        # Kiểm tra nếu ký tự này đã đủ số lượng cần thiết
        if character in dict_t and window_counts[character] == dict_t[character]:
            formed += 1
        
        # Thu hẹp window cho đến khi không còn valid
        while left <= right and formed == required:
            character = s[left]
            
            # Lưu window nhỏ nhất
            if right - left + 1 < ans[0]:
                ans = (right - left + 1, left, right)
            
            # Loại bỏ ký tự từ trái
            window_counts[character] -= 1
            if character in dict_t and window_counts[character] < dict_t[character]:
                formed -= 1
            
            left += 1
        
        right += 1
    
    return "" if ans[0] == float("inf") else s[ans[1]:ans[2] + 1]

# Test cases
print(min_window("ADOBECODEBANC", "ABC"))  # "BANC"
print(min_window("a", "a"))                # "a"
print(min_window("a", "aa"))               # ""
```

### Ví dụ 5: Palindromic Substrings
**Mô tả**: Đếm số substring là palindrome

```python
def count_substrings(s):
    """
    Input: s = "abc"
    Output: 3 ("a", "b", "c")
    
    Input: s = "aaa" 
    Output: 6 ("a", "a", "a", "aa", "aa", "aaa")
    """
    count = 0
    
    def expand_around_centers(left, right):
        nonlocal count
        while left >= 0 and right < len(s) and s[left] == s[right]:
            count += 1
            left -= 1
            right += 1
    
    for i in range(len(s)):
        # Palindrome có độ dài lẻ (center là i)
        expand_around_centers(i, i)
        
        # Palindrome có độ dài chẵn (center là i và i+1)
        expand_around_centers(i, i + 1)
    
    return count

# Phương pháp 2: Dynamic Programming
def count_substrings_dp(s):
    n = len(s)
    dp = [[False] * n for _ in range(n)]
    count = 0
    
    # Mọi ký tự đơn đều là palindrome
    for i in range(n):
        dp[i][i] = True
        count += 1
    
    # Kiểm tra palindrome độ dài 2
    for i in range(n - 1):
        if s[i] == s[i + 1]:
            dp[i][i + 1] = True
            count += 1
    
    # Kiểm tra palindrome độ dài >= 3
    for length in range(3, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            if s[i] == s[j] and dp[i + 1][j - 1]:
                dp[i][j] = True
                count += 1
    
    return count

# Test cases
print(count_substrings("abc"))  # 3
print(count_substrings("aaa"))  # 6
```

## 🏃‍♂️ Bài Tập Thực Hành

### 1. LeetCode 3: Longest Substring Without Repeating Characters
```python
def lengthOfLongestSubstring(s):
    char_set = set()
    left = 0
    max_length = 0
    
    for right in range(len(s)):
        while s[right] in char_set:
            char_set.remove(s[left])
            left += 1
        
        char_set.add(s[right])
        max_length = max(max_length, right - left + 1)
    
    return max_length
```
**Link**: https://leetcode.com/problems/longest-substring-without-repeating-characters/

### 2. LeetCode 242: Valid Anagram
```python
def isAnagram(s, t):
    from collections import Counter
    return Counter(s) == Counter(t)
```
**Link**: https://leetcode.com/problems/valid-anagram/

### 3. LeetCode 49: Group Anagrams
```python
def groupAnagrams(strs):
    from collections import defaultdict
    
    groups = defaultdict(list)
    for s in strs:
        key = ''.join(sorted(s))
        groups[key].append(s)
    
    return list(groups.values())
```
**Link**: https://leetcode.com/problems/group-anagrams/

## 🎯 Template Tổng Quát

### Sliding Window Template (Variable Size)
```python
def sliding_window_variable(s):
    left = 0
    result = 0  # hoặc float('inf') cho minimum
    window_state = {}  # hash map để track window state
    
    for right in range(len(s)):
        # Mở rộng window: thêm s[right]
        # Cập nhật window_state
        
        # Thu hẹp window nếu cần
        while not is_valid_window(window_state):
            # Loại bỏ s[left] khỏi window
            # Cập nhật window_state
            left += 1
        
        # Cập nhật kết quả
        result = max(result, right - left + 1)  # cho maximum
        # result = min(result, right - left + 1)  # cho minimum
    
    return result
```

### Character Frequency Template
```python
def solve_with_frequency(s):
    from collections import Counter, defaultdict
    
    # Đếm tần suất ký tự
    char_count = Counter(s)
    # hoặc
    char_count = defaultdict(int)
    for char in s:
        char_count[char] += 1
    
    # Xử lý logic dựa trên frequency
    return result
```

### Palindrome Check Template
```python
def expand_around_center(s, left, right):
    while left >= 0 and right < len(s) and s[left] == s[right]:
        # Xử lý palindrome tìm được
        left -= 1
        right += 1

def find_palindromes(s):
    for i in range(len(s)):
        # Palindrome độ dài lẻ
        expand_around_center(s, i, i)
        
        # Palindrome độ dài chẵn  
        expand_around_center(s, i, i + 1)
```

## 💡 Tips Quan Trọng

### Khi nào dùng Sliding Window
- **Substring problems**: Tìm substring thỏa mãn điều kiện
- **Contiguous sequence**: Dãy con liên tiếp
- **Optimization problems**: Tìm max/min với constraint

### Khi nào dùng Hash Map/Counter
- **Character counting**: Đếm tần suất ký tự
- **Anagram problems**: So sánh composition của strings
- **Pattern matching**: Kiểm tra pattern xuất hiện

### Common Mistakes cần tránh
- **String immutability**: Nhớ string trong Python không thể thay đổi
- **Index out of bounds**: Kiểm tra điều kiện biên
- **Case sensitivity**: Chú ý phân biệt hoa/thường
- **Empty string**: Xử lý trường hợp chuỗi rỗng

### Time/Space Complexity Analysis
- **Sliding Window**: Time O(n), Space O(k) với k là size của character set
- **Hash Map approaches**: Time O(n), Space O(k)
- **Nested loops**: Thường O(n²) - cần tối ưu

### Edge Cases cần lưu ý
- Chuỗi rỗng: `""`
- Chuỗi 1 ký tự: `"a"`
- Tất cả ký tự giống nhau: `"aaaa"`
- Không có kết quả thỏa mãn điều kiện

## 🏆 Bài Tập Về Nhà

### Easy Level
1. **LeetCode 125: Valid Palindrome** - Kiểm tra palindrome (ignore case và non-alphanumeric)
2. **LeetCode 387: First Unique Character** - Tìm ký tự đầu tiên không lặp lại
3. **LeetCode 383: Ransom Note** - Kiểm tra có thể tạo ransom note từ magazine

### Medium Level
4. **LeetCode 5: Longest Palindromic Substring** - Tìm palindrome substring dài nhất
5. **LeetCode 647: Palindromic Substrings** - Đếm số palindromic substrings
6. **LeetCode 438: Find All Anagrams** - Tìm tất cả anagram của pattern trong string
7. **LeetCode 76: Minimum Window Substring** - Tìm minimum window chứa tất cả ký tự của pattern
8. **LeetCode 424: Longest Repeating Character Replacement** - Với k replacement, tìm longest repeating substring

### Hard Level
9. **LeetCode 30: Substring with Concatenation of All Words** - Tìm substring chứa concatenation của tất cả words
10. **LeetCode 239: Sliding Window Maximum** - Tìm maximum trong mỗi sliding window

### Gợi ý Approach
- **Palindrome problems**: Sử dụng expand around center hoặc DP
- **Anagram problems**: Hash map với character frequency
- **Window problems**: Sliding window với two pointers
- **Pattern matching**: KMP algorithm hoặc rolling hash cho advanced cases
- **Multiple patterns**: Trie data structure có thể hữu ích