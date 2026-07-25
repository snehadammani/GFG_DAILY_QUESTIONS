# Max Sum Square Sub-Matrix - Complete Guide

## Problem Understanding

**Input:** n×n matrix + size k  
**Goal:** Find maximum sum among all k×k sub-matrices  
**Constraint:** Matrix values can be negative

---

## Solution Approaches

### ❌ Approach 1: Brute Force (Your Current Solution)

```python
for i in range(n-k+1):           # O(n)
    for j in range(n-k+1):       # O(n)
        for r in range(i,i+k):   # O(k)
            for c in range(j,j+k): # O(k)
                total += mat[r][c]
```

**Time:** O(n² × k²)  
**Problem:** Recalculates overlapping elements repeatedly

**Example with k=3, n=4:**
```
Position (0,0):  adds mat[0][0] through mat[2][2]
Position (0,1):  adds mat[0][1] through mat[2][3]  ← Recalculates mat[0][1:2], mat[1][1:2], mat[2][1:2]
```

---

### ✅ Approach 2: Sliding Window (RECOMMENDED)

**Key Insight:** Process in two phases
1. **Row Phase:** Slide window across each row to get k-element sums
2. **Column Phase:** Slide window down columns to get final sums

#### Step-by-Step Example

**Matrix:**
```
[1,   2,  -1,  4]
[-8, -3,   4,  2]
[3,   8,  10, -8]
[-4, -1,   1,  7]
```

**k = 3**

**Phase 1: Row Sums**
```
Row 0: [1+2-1=2, 2-1+4=5]                      → [2, 5]
Row 1: [-8-3+4=-7, -3+4+2=3]                   → [-7, 3]
Row 2: [3+8+10=21, 8+10-8=10]                  → [21, 10]
Row 3: [-4-1+1=-4, -1+1+7=7]                   → [-4, 7]
```

**Phase 2: Sliding Window Down**
```
Column 0: 2 + (-7) + 21 = 16
          (-7) + 21 + (-4) = 10

Column 1: 5 + 3 + 10 = 18
          3 + 10 + 7 = 20  ← MAXIMUM!
```

#### Code Implementation

```python
def maximumSum(self, mat, k):
    n = len(mat)
    max_sum = float('-inf')
    
    # Phase 1: Calculate row-wise sums
    row_sums = []
    for i in range(n):
        row = []
        # Sum of first k elements
        current_sum = sum(mat[i][j] for j in range(k))
        row.append(current_sum)
        
        # Slide window for remaining positions
        for j in range(1, n - k + 1):
            # Remove leftmost element, add new rightmost element
            current_sum = current_sum - mat[i][j-1] + mat[i][j+k-1]
            row.append(current_sum)
        row_sums.append(row)
    
    # Phase 2: Slide window down columns
    for j in range(n - k + 1):
        # Sum of first k rows
        current_sum = sum(row_sums[i][j] for i in range(k))
        max_sum = max(max_sum, current_sum)
        
        # Slide window down
        for i in range(1, n - k + 1):
            current_sum = current_sum - row_sums[i-1][j] + row_sums[i+k-1][j]
            max_sum = max(max_sum, current_sum)
    
    return max_sum
```

**Time:** O(n²) - Each element touched constant times  
**Space:** O(n²) - Store row sums

---

### ✅ Approach 3: 2D Prefix Sum

**Concept:** Precompute cumulative sums so any rectangle sum is O(1)

```
prefix[i][j] = sum of all elements in rectangle (0,0) to (i-1,j-1)

To get sum from (r1,c1) to (r2,c2):
sum = prefix[r2+1][c2+1] - prefix[r1][c2+1] - prefix[r2+1][c1] + prefix[r1][c1]
```

**Visualization:**
```
Original Matrix:    Prefix Sum:
[1  2  -1  4]      [0  0   0   0   0]
[-8 -3  4  2]      [0  1   3   2   6]
[3   8  10 -8]     [0  -7  2   5   13]
[-4  -1  1  7]     [0  -4  4   14  16]
                   [0  -8  3   18  29]
```

**Code:**
```python
def maximumSum(self, mat, k):
    n = len(mat)
    prefix = [[0] * (n+1) for _ in range(n+1)]
    
    # Build prefix sum
    for i in range(1, n+1):
        for j in range(1, n+1):
            prefix[i][j] = (mat[i-1][j-1] + prefix[i-1][j] + 
                           prefix[i][j-1] - prefix[i-1][j-1])
    
    # Check all k×k sub-grids
    max_sum = float('-inf')
    for i in range(k, n+1):
        for j in range(k, n+1):
            sub_sum = (prefix[i][j] - prefix[i-k][j] - 
                      prefix[i][j-k] + prefix[i-k][j-k])
            max_sum = max(max_sum, sub_sum)
    
    return max_sum
```

**Time:** O(n²)  
**Space:** O(n²)

---

## Performance Comparison

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **Brute Force** | O(n²k²) | O(1) | ✗ Too slow for n=1000 |
| **Sliding Window** | O(n²) | O(n²) | ✓ **Best choice** - efficient |
| **Prefix Sum** | O(n²) | O(n²) | ✓ Good alternative - intuitive |

---

## Why Sliding Window is Best

1. **Eliminates Redundant Calculations**
   - Each element only processed ~4 times (once in row phase, ~3 times in column phase)
   - Brute force processes each element k² times

2. **Natural Two-Phase Structure**
   - Row phase → Compress rows to 1D problem
   - Column phase → Apply same sliding window logic
   - Easy to understand and debug

3. **Memory Efficient**
   - Only stores n rows of size (n-k+1)
   - Prefix sum stores full n×n array

---

## Edge Cases to Consider

```python
# Case 1: k = 1 (single element)
mat = [[4]], k = 1
# Answer: 4 (just the element itself)

# Case 2: k = n (entire matrix)
mat = [[1,2],[3,4]], k = 2
# Answer: sum of entire matrix = 10

# Case 3: All negative values
mat = [[-5, -3], [-2, -8]], k = 1
# Answer: -2 (best single element)

# Case 4: Mix of positive and negative
# Already handled - algorithm naturally finds best sub-grid
```

---

## Implementation Tips

1. **Sliding Window Window:** Remove `old` element before adding `new`
   ```python
   window_sum = window_sum - arr[old_idx] + arr[new_idx]
   ```

2. **Index Bounds:** For k×k in n×n, positions go from 0 to n-k
   ```python
   for i in range(n - k + 1):  # Correct!
   for i in range(n - k):      # Wrong - misses last position
   ```

3. **Initialization:** Always calculate sum of first window before sliding
   ```python
   sum_first = sum(arr[0:k])  # First window
   for i in range(1, n-k+1):  # Slide from index 1
   ```

---

## Recommendation

**Use Sliding Window Approach** for this problem. It's:
- ✓ Faster: O(n²) vs O(n²k²)
- ✓ Cleaner: Clear two-phase logic
- ✓ Interview-friendly: Shows optimization thinking
- ✓ Scalable: Works for n up to 1000
