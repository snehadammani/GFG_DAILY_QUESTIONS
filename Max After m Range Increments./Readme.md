# Max After m Range Increments

## Problem Statement

Given three arrays `a[]`, `b[]`, and `k[]` representing `m` range increment operations on an array `arr[]` of size `n`, where all elements of `arr[]` are initially `0`.

### Operation Definition
- **Increment(a[i], b[i], k[i])**: Adds `k[i]` to each element `arr[j]` such that `a[i] ≤ j ≤ b[i]` (all indices in range from `a[i]` to `b[i]`)

### Task
After performing all `m` given operations, find and return the **maximum value** present in the array.

### Constraints
```
1 ≤ n ≤ 10⁶
1 ≤ a.size() = b.size() = k.size() = m ≤ 10⁶
0 ≤ aᵢ ≤ bᵢ ≤ n-1
0 ≤ kᵢ ≤ 10⁶
```

---

## Problem Links

| Platform | Link |
|----------|------|
| **GeeksforGeeks** | https://www.geeksforgeeks.org/max-value-after-m-range-increments/ |
| **LeetCode** | https://leetcode.com/problems/range-addition/ |

---

## Examples

### Example 1

**Input:**
```
n = 5
a[] = [0, 1, 2]
b[] = [1, 4, 3]
k[] = [100, 100, 100]
```

**Output:** `200`

**Explanation:**
```
Initially:  arr = [0, 0, 0, 0, 0]

Operation 1: Add 100 to range [0, 1]
            arr = [100, 100, 0, 0, 0]

Operation 2: Add 100 to range [1, 4]
            arr = [100, 200, 100, 100, 100]

Operation 3: Add 100 to range [2, 3]
            arr = [100, 200, 200, 200, 100]

Maximum element = 200
```

---

### Example 2

**Input:**
```
n = 4
a[] = [1, 0, 3]
b[] = [2, 0, 3]
k[] = [603, 286, 882]
```

**Output:** `882`

**Explanation:**
```
Initially:  arr = [0, 0, 0, 0]

Operation 1: Add 603 to range [1, 2]
            arr = [0, 603, 603, 0]

Operation 2: Add 286 to range [0, 0]
            arr = [286, 603, 603, 0]

Operation 3: Add 882 to range [3, 3]
            arr = [286, 603, 603, 882]

Maximum element = 882
```

---

### Example 3 (Edge Case)

**Input:**
```
n = 1
a[] = [0]
b[] = [0]
k[] = [50]
```

**Output:** `50`

---

### Example 4

**Input:**
```
n = 3
a[] = [0, 0, 0]
b[] = [2, 2, 2]
k[] = [10, 20, 30]
```

**Output:** `60`

---

## Approach: Difference Array Technique

### Problem with Naive Approach

**Naive Solution:**
```python
for each operation(a, b, k):
    for j in range(a, b+1):
        arr[j] += k
return max(arr)
```

**Time Complexity:** O(m × n) = 10¹² operations ❌ **TLE**

### Optimal Solution: Difference Array

Instead of updating every element, we:
1. Mark the **start** of the range
2. Mark the **position after the end** of the range
3. Use **prefix sum** to calculate actual values

**Key Insight:**
```
To add value k to range [L, R]:
  diff[L] += k      ← Mark START
  diff[R+1] -= k    ← Mark END (to stop adding)

When we calculate prefix sum:
- From index L to R: value increases
- After index R: value decreases
```

### Visual Example

**Operation 1:** Add 100 to range [0, 1]
```
Before: diff = [0, 0, 0, 0, 0, 0]
Action: diff[0] += 100, diff[2] -= 100
After:  diff = [100, 0, -100, 0, 0, 0]
```

**Operation 2:** Add 100 to range [1, 4]
```
Before: diff = [100, 0, -100, 0, 0, 0]
Action: diff[1] += 100, diff[5] -= 100
After:  diff = [100, 100, -100, 0, 0, -100]
```

**Operation 3:** Add 100 to range [2, 3]
```
Before: diff = [100, 100, -100, 0, 0, -100]
Action: diff[2] += 100, diff[4] -= 100
After:  diff = [100, 100, 0, 0, -100, -100]
```

**Prefix Sum Calculation:**

| Index | diff[i] | Cumulative Sum | arr[i] |
|-------|---------|----------------|--------|
| 0 | 100 | 0 + 100 = 100 | 100 |
| 1 | 100 | 100 + 100 = 200 | 200 ← MAX |
| 2 | 0 | 200 + 0 = 200 | 200 ← MAX |
| 3 | 0 | 200 + 0 = 200 | 200 ← MAX |
| 4 | -100 | 200 - 100 = 100 | 100 |

**Result:** Maximum = 200 ✓

---

## Solution Code

### Python Solution (Recommended)

```python
class Solution:
    def findMax(self, n, a, b, k):
        """
        Find maximum value after m range increment operations
        
        Args:
            n (int): Size of the array
            a (list): List of start indices
            b (list): List of end indices
            k (list): List of values to add
        
        Returns:
            int: Maximum value in array after all operations
        """
        
        # Step 1: Create difference array of size n+1
        diff = [0] * (n + 1)
        
        # Step 2: Mark boundaries for each operation
        for i in range(len(a)):
            diff[a[i]] += k[i]      # Add at start
            diff[b[i] + 1] -= k[i]  # Subtract after end
        
        # Step 3: Calculate prefix sum and find maximum
        max_value = 0
        current_sum = 0
        
        for i in range(n):
            current_sum += diff[i]          # Build actual value
            max_value = max(max_value, current_sum)  # Track maximum
        
        return max_value


# Test Cases
if __name__ == "__main__":
    solution = Solution()
    
    # Test 1
    result = solution.findMax(5, [0, 1, 2], [1, 4, 3], [100, 100, 100])
    assert result == 200, f"Test 1 failed: expected 200, got {result}"
    print("✓ Test 1 passed: 200")
    
    # Test 2
    result = solution.findMax(4, [1, 0, 3], [2, 0, 3], [603, 286, 882])
    assert result == 882, f"Test 2 failed: expected 882, got {result}"
    print("✓ Test 2 passed: 882")
    
    # Test 3
    result = solution.findMax(1, [0], [0], [50])
    assert result == 50, f"Test 3 failed: expected 50, got {result}"
    print("✓ Test 3 passed: 50")
    
    # Test 4
    result = solution.findMax(3, [0, 0, 0], [2, 2, 2], [10, 20, 30])
    assert result == 60, f"Test 4 failed: expected 60, got {result}"
    print("✓ Test 4 passed: 60")
    
    print("\n✅ All tests passed!")
```

### C++ Solution

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long findMax(int n, vector<int>& a, vector<int>& b, vector<int>& k) {
        // Create difference array
        vector<long long> diff(n + 1, 0);
        
        // Mark boundaries
        for (int i = 0; i < a.size(); i++) {
            diff[a[i]] += k[i];
            diff[b[i] + 1] -= k[i];
        }
        
        // Prefix sum and find max
        long long max_value = 0;
        long long current_sum = 0;
        
        for (int i = 0; i < n; i++) {
            current_sum += diff[i];
            max_value = max(max_value, current_sum);
        }
        
        return max_value;
    }
};
```

### JavaScript Solution

```javascript
class Solution {
    findMax(n, a, b, k) {
        // Create difference array
        const diff = new Array(n + 1).fill(0);
        
        // Mark boundaries
        for (let i = 0; i < a.length; i++) {
            diff[a[i]] += k[i];
            diff[b[i] + 1] -= k[i];
        }
        
        // Prefix sum and find max
        let maxValue = 0;
        let currentSum = 0;
        
        for (let i = 0; i < n; i++) {
            currentSum += diff[i];
            maxValue = Math.max(maxValue, currentSum);
        }
        
        return maxValue;
    }
}
```

---

## Complexity Analysis

### Time Complexity

| Operation | Complexity |
|-----------|------------|
| Creating diff array | O(n) |
| Marking boundaries | O(m) |
| Prefix sum calculation | O(n) |
| **Total** | **O(m + n)** |

**Comparison:**
```
Naive Approach:       O(m × n) = 10¹² ❌
Difference Array:     O(m + n) = 2×10⁶ ✅
```

### Space Complexity

| Component | Space |
|-----------|-------|
| Difference array | O(n) |
| Variables | O(1) |
| **Total** | **O(n)** |

---

## Code Explanation

### Line 1: Function Definition
```python
def findMax(self, n, a, b, k):
```
Parameters:
- `n`: Size of the array
- `a`: Array of start indices
- `b`: Array of end indices
- `k`: Array of values to increment

### Line 5: Create Difference Array
```python
diff = [0] * (n + 1)
```
**Why size n+1?**
- If `b[i] = n-1` (last index), we need to access `diff[n]` for marking
- Example: If n=5, max index = 4. If b[i] = 4, we need diff[5]. Size must be 6 (n+1)

### Line 8-10: Mark Boundaries
```python
for i in range(len(a)):
    diff[a[i]] += k[i]
    diff[b[i] + 1] -= k[i]
```
**What happens:**
- `diff[a[i]] += k[i]`: Add k at start of range
- `diff[b[i] + 1] -= k[i]`: Subtract k after end of range

**Why `b[i] + 1` and not `b[i]`?**
```
Goal: Add value to indices a, a+1, ..., b (inclusive)

Wrong:  diff[b] -= k   → At index b: sum + k - k = sum (b not included!) ❌
Correct: diff[b+1] -= k → At index b: sum + k (included!) ✓
                         → At index b+1: sum + k - k = sum (b+1 not included!) ✓
```

### Line 13-18: Calculate Prefix Sum
```python
max_value = 0
current_sum = 0

for i in range(n):
    current_sum += diff[i]
    max_value = max(max_value, current_sum)
```
**What happens:**
- `current_sum += diff[i]`: Accumulate value at index i
- `max_value = max(...)`: Track maximum value seen

**Example:**
```
i=0: current_sum = 0 + diff[0] = 0 + 100 = 100
i=1: current_sum = 100 + diff[1] = 100 + 100 = 200
i=2: current_sum = 200 + diff[2] = 200 + 0 = 200
...
```

---

## Test Cases

| Test | n | a | b | k | Expected | Result | Status |
|------|---|---|---|---|----------|--------|--------|
| 1 | 5 | [0,1,2] | [1,4,3] | [100,100,100] | 200 | 200 | ✅ PASS |
| 2 | 4 | [1,0,3] | [2,0,3] | [603,286,882] | 882 | 882 | ✅ PASS |
| 3 | 1 | [0] | [0] | [50] | 50 | 50 | ✅ PASS |
| 4 | 3 | [0,0,0] | [2,2,2] | [10,20,30] | 60 | 60 | ✅ PASS |

---

## Common Mistakes to Avoid

### ❌ Mistake 1: Using diff[b[i]] Instead of diff[b[i]+1]
```python
# WRONG
diff[b[i]] -= k[i]

# CORRECT
diff[b[i] + 1] -= k[i]
```
**Why:** You need to stop adding AFTER the range ends

### ❌ Mistake 2: Creating diff Array of Size n
```python
# WRONG
diff = [0] * n

# CORRECT
diff = [0] * (n + 1)
```
**Why:** You might need to access diff[n] when b[i] = n-1

### ❌ Mistake 3: Using int Instead of long long (C++)
```cpp
// WRONG
int max_value = 0;

// CORRECT
long long max_value = 0;
```
**Why:** k can be 10⁶, m can be 10⁶ → total can overflow int

### ❌ Mistake 4: Not Updating max_value in Loop
```python
# WRONG
for i in range(n):
    current_sum += diff[i]
    
max_value = max(arr)  # After loop

# CORRECT
for i in range(n):
    current_sum += diff[i]
    max_value = max(max_value, current_sum)  # Inside loop
```
**Why:** You need to track maximum while calculating

### ❌ Mistake 5: Iterating Beyond n Elements
```python
# WRONG
for i in range(n + 1):  # Goes to n, which is out of bounds
    current_sum += diff[i]
    max_value = max(max_value, current_sum)

# CORRECT
for i in range(n):  # Goes from 0 to n-1
    current_sum += diff[i]
    max_value = max(max_value, current_sum)
```

---

## Key Concepts

### 1. Difference Array
- Instead of updating range elements directly, mark boundaries
- Reduces time complexity from O(m×n) to O(m+n)

### 2. Prefix Sum
- Cumulative sum allows us to recover actual values from difference array
- Each element's value = sum of all differences up to that point

### 3. Range Update Optimization
```
Traditional:  for j in range(a, b+1): arr[j] += k  [O(b-a+1) per operation]
Optimized:    diff[a] += k; diff[b+1] -= k         [O(1) per operation]
```

### 4. Why Size Must Be n+1
```
Array indices: 0, 1, 2, 3, 4  (n=5)
If b[i] = 4 (last index):
  We access diff[b[i] + 1] = diff[5]
  
Required indices: 0, 1, 2, 3, 4, 5
Size needed: 6 = n+1
```

---

## How to Use This Solution

### For Online Judge Submission

```python
class Solution:
    def findMax(self, n, a, b, k):
        diff = [0] * (n + 1)
        for i in range(len(a)):
            diff[a[i]] += k[i]
            diff[b[i] + 1] -= k[i]
        
        max_value = 0
        current_sum = 0
        
        for i in range(n):
            current_sum += diff[i]
            max_value = max(max_value, current_sum)
        
        return max_value
```

**Steps:**
1. Copy the Solution class
2. Paste into GeeksforGeeks/LeetCode
3. Click Submit
4. ✅ Accepted!

### For Local Testing

```bash
python solution_code.py
# Output:
# ✓ Test 1 passed: 200
# ✓ Test 2 passed: 882
# ✓ Test 3 passed: 50
# ✓ Test 4 passed: 60
# ✅ All tests passed!
```

---

## Summary

| Aspect | Details |
|--------|---------|
| **Problem Type** | Array Optimization |
| **Difficulty** | Medium |
| **Approach** | Difference Array + Prefix Sum |
| **Time Complexity** | O(m + n) |
| **Space Complexity** | O(n) |
| **Key Insight** | Mark boundaries instead of updating each element |
| **All Tests** | ✅ PASS |
| **Status** | ✅ Production Ready |

---

## Related Problems

After mastering this problem, practice:
1. Range Addition (LeetCode 370)
2. Increment Array After N Queries (LeetCode 2283)
3. Difference Array Applications
4. Segment Trees for Range Queries

---

**Ready to solve? Copy the Solution code and submit! 🚀**
