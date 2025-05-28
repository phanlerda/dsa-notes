# Bài 6: Linked List (Danh Sách Liên Kết)

## 📚 Lý Thuyết Cơ Bản

### Khái niệm chính
Linked List là cấu trúc dữ liệu tuyến tính gồm các nodes, mỗi node chứa data và pointer trỏ đến node tiếp theo. Khác với array, các elements không được lưu liên tiếp trong memory.

### Đặc điểm quan trọng
- **Time Complexity:**
  - Access: O(n) - phải traverse từ head
  - Search: O(n) - tìm kiếm tuần tự
  - Insertion: O(1) nếu có pointer, O(n) nếu insert tại vị trí cụ thể
  - Deletion: O(1) nếu có pointer, O(n) nếu delete theo value
- **Space Complexity:** O(n) với n là số nodes

### Code examples cơ bản trong Python

```python
# Định nghĩa Node
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
    
    def __repr__(self):
        return f"ListNode({self.val})"

# Tạo linked list: 1 -> 2 -> 3 -> None
head = ListNode(1)
head.next = ListNode(2)
head.next.next = ListNode(3)

# Traverse linked list
def print_list(head):
    current = head
    result = []
    while current:
        result.append(str(current.val))
        current = current.next
    print(" -> ".join(result) + " -> None")

print_list(head)  # 1 -> 2 -> 3 -> None

# Helper function để tạo linked list từ array
def create_linked_list(arr):
    if not arr:
        return None
    head = ListNode(arr[0])
    current = head
    for val in arr[1:]:
        current.next = ListNode(val)
        current = current.next
    return head

# Helper function để convert linked list thành array
def linked_list_to_array(head):
    result = []
    current = head
    while current:
        result.append(current.val)
        current = current.next
    return result
```

## 🎯 Kỹ Thuật Linked List

### Giải thích ý tưởng core
Linked List sử dụng pointers để liên kết các nodes, cho phép dynamic memory allocation và efficient insertion/deletion. Key techniques bao gồm Two Pointers, Fast/Slow Pointers, và Dummy Node.

### Khi nào sử dụng
- Cần insertion/deletion frequent ở giữa danh sách
- Kích thước danh sách thay đổi động
- Không cần random access
- Implement stack, queue, hoặc other data structures
- Xử lý cycle detection
- Merge operations

### Step-by-step approach
1. **Xác định problem type** (traversal, manipulation, cycle, merge)
2. **Chọn technique phù hợp** (two pointers, dummy node, recursion)
3. **Handle edge cases** (empty list, single node, cycles)
4. **Implement và test** với various inputs

## 🔍 Ví Dụ Thực Tế

### Ví dụ 1: Reverse Linked List
**Mô tả:** Đảo ngược một linked list.

**Input:** head = [1,2,3,4,5]  
**Output:** [5,4,3,2,1]

```python
def reverse_list(head):
    """
    Iterative approach: Sử dụng 3 pointers
    Time: O(n), Space: O(1)
    """
    prev = None
    current = head
    
    while current:
        # Lưu next node trước khi thay đổi pointer
        next_temp = current.next
        
        # Reverse link
        current.next = prev
        
        # Move pointers forward
        prev = current
        current = next_temp
    
    return prev  # prev là head mới

def reverse_list_recursive(head):
    """
    Recursive approach
    Time: O(n), Space: O(n) - call stack
    """
    # Base case
    if not head or not head.next:
        return head
    
    # Recursively reverse rest of list
    new_head = reverse_list_recursive(head.next)
    
    # Reverse current connection
    head.next.next = head
    head.next = None
    
    return new_head

# Test case
head = create_linked_list([1, 2, 3, 4, 5])
reversed_head = reverse_list(head)
print(linked_list_to_array(reversed_head))  # [5, 4, 3, 2, 1]
```

### Ví dụ 2: Merge Two Sorted Lists
**Mô tả:** Merge hai sorted linked lists thành một sorted list.

**Input:** list1 = [1,2,4], list2 = [1,3,4]  
**Output:** [1,1,2,3,4,4]

```python
def merge_two_lists(list1, list2):
    """
    Sử dụng dummy node để simplify logic
    Time: O(m + n), Space: O(1)
    """
    # Dummy node để dễ xử lý
    dummy = ListNode(0)
    current = dummy
    
    # Merge cho đến khi một list hết
    while list1 and list2:
        if list1.val <= list2.val:
            current.next = list1
            list1 = list1.next
        else:
            current.next = list2
            list2 = list2.next
        current = current.next
    
    # Append remaining nodes
    current.next = list1 or list2
    
    return dummy.next

def merge_two_lists_recursive(list1, list2):
    """
    Recursive approach
    Time: O(m + n), Space: O(m + n) - call stack
    """
    if not list1:
        return list2
    if not list2:
        return list1
    
    if list1.val <= list2.val:
        list1.next = merge_two_lists_recursive(list1.next, list2)
        return list1
    else:
        list2.next = merge_two_lists_recursive(list1, list2.next)
        return list2

# Test case
list1 = create_linked_list([1, 2, 4])
list2 = create_linked_list([1, 3, 4])
merged = merge_two_lists(list1, list2)
print(linked_list_to_array(merged))  # [1, 1, 2, 3, 4, 4]
```

### Ví dụ 3: Linked List Cycle Detection
**Mô tả:** Kiểm tra linked list có cycle không.

**Input:** head = [3,2,0,-4], pos = 1 (cycle tại index 1)  
**Output:** true

```python
def has_cycle(head):
    """
    Floyd's Cycle Detection (Tortoise and Hare)
    Time: O(n), Space: O(1)
    """
    if not head or not head.next:
        return False
    
    slow = head  # Tortoise
    fast = head  # Hare
    
    while fast and fast.next:
        slow = slow.next          # Di chuyển 1 bước
        fast = fast.next.next     # Di chuyển 2 bước
        
        if slow == fast:
            return True
    
    return False

def detect_cycle_start(head):
    """
    Tìm node bắt đầu của cycle
    Time: O(n), Space: O(1)
    """
    if not head or not head.next:
        return None
    
    # Phase 1: Detect cycle
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break
    else:
        return None  # No cycle
    
    # Phase 2: Find cycle start
    # Di chuyển một pointer về head, giữ nguyên pointer kia
    # Di chuyển cả hai với tốc độ 1, điểm gặp nhau là cycle start
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    
    return slow

# Test với cycle
def create_cycle_list():
    head = ListNode(3)
    node2 = ListNode(2)
    node3 = ListNode(0)
    node4 = ListNode(-4)
    
    head.next = node2
    node2.next = node3
    node3.next = node4
    node4.next = node2  # Create cycle
    
    return head

cycle_head = create_cycle_list()
print(has_cycle(cycle_head))  # True
```

### Ví dụ 4: Remove Nth Node From End
**Mô tả:** Xóa node thứ n từ cuối linked list.

**Input:** head = [1,2,3,4,5], n = 2  
**Output:** [1,2,3,5]

```python
def remove_nth_from_end(head, n):
    """
    Two-pass approach: Đếm length rồi remove
    Time: O(n), Space: O(1)
    """
    # Pass 1: Đếm length
    length = 0
    current = head
    while current:
        length += 1
        current = current.next
    
    # Edge case: Remove head
    if length == n:
        return head.next
    
    # Pass 2: Tìm node trước node cần xóa
    current = head
    for _ in range(length - n - 1):
        current = current.next
    
    # Remove node
    current.next = current.next.next
    
    return head

def remove_nth_from_end_one_pass(head, n):
    """
    One-pass approach: Sử dụng two pointers
    Time: O(n), Space: O(1)
    """
    dummy = ListNode(0)
    dummy.next = head
    
    first = dummy
    second = dummy
    
    # Di chuyển first pointer n+1 bước
    for _ in range(n + 1):
        first = first.next
    
    # Di chuyển cả hai pointers cho đến khi first đến cuối
    while first:
        first = first.next
        second = second.next
    
    # second.next là node cần xóa
    second.next = second.next.next
    
    return dummy.next

# Test case
head = create_linked_list([1, 2, 3, 4, 5])
result = remove_nth_from_end_one_pass(head, 2)
print(linked_list_to_array(result))  # [1, 2, 3, 5]
```

### Ví dụ 5: Add Two Numbers
**Mô tả:** Cộng hai số được biểu diễn bằng linked list (reversed order).

**Input:** l1 = [2,4,3], l2 = [5,6,4] (342 + 465)  
**Output:** [7,0,8] (807)

```python
def add_two_numbers(l1, l2):
    """
    Simulate addition với carry
    Time: O(max(m, n)), Space: O(max(m, n))
    """
    dummy = ListNode(0)
    current = dummy
    carry = 0
    
    while l1 or l2 or carry:
        # Lấy values (0 nếu node không tồn tại)
        val1 = l1.val if l1 else 0
        val2 = l2.val if l2 else 0
        
        # Tính tổng
        total = val1 + val2 + carry
        carry = total // 10
        digit = total % 10
        
        # Tạo node mới
        current.next = ListNode(digit)
        current = current.next
        
        # Di chuyển pointers
        if l1:
            l1 = l1.next
        if l2:
            l2 = l2.next
    
    return dummy.next

# Test case
l1 = create_linked_list([2, 4, 3])  # 342
l2 = create_linked_list([5, 6, 4])  # 465
result = add_two_numbers(l1, l2)
print(linked_list_to_array(result))  # [7, 0, 8] = 807
```

## 🏃‍♂️ Bài Tập Thực Hành

### 1. Middle of the Linked List (LeetCode 876)
```python
def find_middle(head):
    """
    Sử dụng slow/fast pointers
    Time: O(n), Space: O(1)
    """
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    return slow

# Test case
head = create_linked_list([1, 2, 3, 4, 5])
middle = find_middle(head)
print(middle.val)  # 3
```

### 2. Palindrome Linked List (LeetCode 234)
```python
def is_palindrome(head):
    """
    Tìm middle, reverse half thứ hai, so sánh
    Time: O(n), Space: O(1)
    """
    if not head or not head.next:
        return True
    
    # Tìm middle
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    # Reverse second half  
    second_half = reverse_list(slow)
    
    # Compare
    first_half = head
    while second_half:
        if first_half.val != second_half.val:
            return False
        first_half = first_half.next
        second_half = second_half.next
    
    return True
```

### 3. Intersection of Two Linked Lists (LeetCode 160)
```python
def get_intersection_node(headA, headB):
    """
    Sử dụng two pointers với length adjustment
    Time: O(m + n), Space: O(1)
    """
    if not headA or not headB:
        return None
    
    pA, pB = headA, headB
    
    while pA != pB:
        # Khi đến cuối, chuyển sang list kia
        pA = pA.next if pA else headB
        pB = pB.next if pB else headA
    
    return pA  # Intersection node hoặc None
```

## 🎯 Template Tổng Quát

```python
# Template 1: Basic Traversal
def traverse_template(head):
    current = head
    while current:
        # Process current node
        process(current)
        current = current.next

# Template 2: Two Pointers (Fast/Slow)
def two_pointers_template(head):
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        # Check condition (cycle, middle, etc.)
        if condition_met(slow, fast):
            return result
    
    return slow  # Usually middle or end

# Template 3: Dummy Node Pattern
def dummy_node_template(head):
    dummy = ListNode(0)
    dummy.next = head
    current = dummy
    
    while current.next:
        if condition_to_modify():
            # Modify pointers
            current.next = current.next.next
        else:
            current = current.next
    
    return dummy.next

# Template 4: Recursive Pattern
def recursive_template(head):
    # Base case
    if not head or not head.next:
        return head
    
    # Recursive call
    result = recursive_template(head.next)
    
    # Process current level
    # Modify pointers if needed
    
    return result

# Template 5: Multiple Lists Merge
def merge_multiple_lists(lists):
    if not lists:
        return None
    
    while len(lists) > 1:
        merged_lists = []
        
        for i in range(0, len(lists), 2):
            l1 = lists[i]
            l2 = lists[i + 1] if i + 1 < len(lists) else None
            merged_lists.append(merge_two_lists(l1, l2))
        
        lists = merged_lists
    
    return lists[0]
```

## 💡 Tips Quan Trọng

### Khi nào dùng Linked List
- Frequent insertions/deletions ở middle
- Unknown size hoặc dynamic size
- Implement stack, queue, hash table chaining
- Memory constraints (no need for contiguous memory)
- When you don't need random access

### Common mistakes cần tránh
1. **Null pointer access** - luôn check `current is not None`
2. **Losing reference** - lưu `next` pointer trước khi modify
3. **Off-by-one errors** trong traversal
4. **Forgot edge cases** - empty list, single node
5. **Memory leaks** trong languages có manual memory management
6. **Cycle creation** khi modify pointers

### Time/Space Complexity Analysis
- **Access/Search:** O(n) - phải traverse từ head
- **Insertion/Deletion:** O(1) với pointer, O(n) without pointer
- **Space:** O(1) cho operations, O(n) cho storage
- **Recursive solutions:** O(n) space cho call stack

### Edge cases cần lưu ý
- Empty list (`head is None`)
- Single node list
- Two node list
- Cycles trong linked list
- Lists với different lengths
- Very large lists (stack overflow với recursion)

## 🏆 Bài Tập Về Nhà

### Easy Level
1. **Remove Duplicates from Sorted List (LeetCode 83)**
   - Approach: Single pass traversal
   - Key: Compare current với current.next

2. **Merge Two Sorted Lists (LeetCode 21)**
   - Approach: Two pointers với dummy node
   - Time: O(m + n), Space: O(1)

3. **Linked List Cycle (LeetCode 141)**
   - Approach: Floyd's cycle detection
   - Key: Fast/slow pointers

4. **Reverse Linked List (LeetCode 206)**
   - Approach: Iterative với 3 pointers
   - Alternative: Recursive approach

### Medium Level
1. **Add Two Numbers (LeetCode 2)**
   - Approach: Simulate addition với carry
   - Key: Handle different lengths và final carry

2. **Remove Nth Node From End of List (LeetCode 19)**
   - Approach: Two pointers với n+1 gap
   - Key: One-pass solution với dummy node

3. **Reorder List (LeetCode 143)**
   - Approach: Find middle + Reverse + Merge
   - Steps: Split, reverse second half, interleave

4. **Copy List with Random Pointer (LeetCode 138)**
   - Approach: Hash map hoặc interleaving nodes
   - Challenge: Handle random pointers

5. **Linked List Cycle II (LeetCode 142)**
   - Approach: Floyd's algorithm extended
   - Key: Mathematical proof cho cycle start

### Hard Level
1. **Merge k Sorted Lists (LeetCode 23)**
   - Approach: Divide & conquer hoặc heap
   - Time: O(n log k), Space: O(log k)

2. **Reverse Nodes in k-Group (LeetCode 25)**
   - Approach: Reverse in chunks
   - Challenge: Handle last incomplete group

3. **Design Twitter (LeetCode 355)**
   - Approach: Hash tables + merge sorted lists
   - Focus: System design với linked structures

### Gợi ý approach chung:

**Pattern Recognition:**
- **Cycle problems:** Fast/slow pointers
- **Middle finding:** Fast/slow pointers  
- **Reverse operations:** Three pointers iterative
- **Merge operations:** Dummy node + two pointers
- **Nth from end:** Two pointers với gap
- **Palindrome check:** Find middle + reverse + compare

**Key Techniques:**
- **Dummy node:** Simplify edge cases
- **Two pointers:** Fast/slow, previous/current
- **Recursion:** For elegant solutions (watch stack space)
- **Hash table:** For complex pointer relationships

**Debugging Tips:**
- Draw out the linked list structure
- Trace through pointer movements step by step  
- Test với edge cases: empty, single, two nodes
- Use helper functions để print intermediate states