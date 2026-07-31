# Subsets with Products of Distinct Primes

## Problem Statement

Given an integer array `arr[]`, count the number of different subsets whose product can be represented as a product of **one or more distinct prime numbers**.

**Key Constraint**: No prime factor should appear more than once in the product.

Two subsets are considered different if the set of chosen array indices are not the same.

Return the count modulo `10^9 + 7`.

### Constraints
- `1 ≤ arr.size() ≤ 10^5`
- `1 ≤ arr[i] ≤ 30`

---

## Examples

### Example 1
```
Input: arr[] = [1, 2, 3, 4]
Output: 6

Explanation:
Valid subsets:
- [2]           → product = 2 = 2 (distinct prime)
- [3]           → product = 3 = 3 (distinct prime)
- [1, 2]        → product = 2 = 2 (distinct prime)
- [1, 3]        → product = 3 = 3 (distinct prime)
- [2, 3]        → product = 6 = 2 × 3 (distinct primes)
- [1, 2, 3]     → product = 6 = 2 × 3 (distinct primes)

Invalid subsets:
- [4]           → product = 4 = 2² (repeated prime)
- [1, 4]        → product = 4 = 2² (repeated prime)
```

### Example 2
```
Input: arr[] = [2, 2, 2, 2, 2, 2, 2, 2, 2, 2]
Output: 10

Explanation:
We have 10 identical elements, each with prime factor 2.
We can pick exactly ONE of them (10 different ways).
We cannot pick 2 or more (would create 2² - repeated prime).
```

### Example 3
```
Input: arr[] = [1, 13, 21, 17, 3, 1]
Output: 44

Explanation:
- Non-1 elements generate 11 valid subsets
- We have 2 ones
- Total = 11 × 2² = 44 (ones can be added to any valid subset)
```

---

## Algorithm

### Approach: Bitmask DP with Prime Factorization

#### Key Insights

1. **Limited Primes**: Since `arr[i] ≤ 30`, only primes up to 30 matter: `[2, 3, 5, 7, 11, 13, 17, 19, 23, 29]` (10 primes)

2. **Bitmask Representation**: Each number can be represented as a bitmask indicating which primes it contains:
   - `6 = 2 × 3` → bitmask `0000000011` (primes 2 and 3)
   - `4 = 2²` → Invalid (repeated prime 2)

3. **Combining Subsets**: Two subsets can be combined only if their prime masks don't overlap (no common bits set)

4. **Handling Duplicates**: For k identical elements with the same prime mask, we can pick at most 1 of them (k different choices)

5. **Handling 1's**: The number 1 has no prime factors, so:
   - It cannot form a valid subset alone
   - But it can be added to any valid subset without affecting primes
   - If we have k ones and m valid subsets, total = `m × 2^k`

#### Algorithm Steps

```
1. Preprocess array:
   - Count elements with value 1 separately
   - For other elements, compute prime mask
   - Skip elements with repeated prime factors (mask = -1)
   - Group remaining elements by their prime mask

2. Dynamic Programming:
   - dp[mask] = number of subsets that produce this prime bitmask
   - Initialize dp[0] = 1 (empty subset)
   
3. For each unique prime mask with 'count' occurrences:
   - We can pick at most 1 element (count different choices)
   - For each previous mask that doesn't conflict:
     - Combine: new_mask = prev_mask | current_mask
     - Update: dp[new_mask] += dp[prev_mask] × count

4. Final result:
   - Sum all dp values except dp[0] (exclude empty subset)
   - Multiply by 2^(count of 1's)
```

---

## Solution Code

```python
class Solution:
    def countSubsets(self, arr):
        MOD = 10**9 + 7
        primes = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
        
        def getPrimeMask(num):
            """Convert number to bitmask of prime factors"""
            mask = 0
            for i, p in enumerate(primes):
                if p > num:
                    break
                count = 0
                temp = num
                while temp % p == 0:
                    count += 1
                    temp //= p
                if count > 1:  # Repeated prime - invalid
                    return -1
                if count == 1:
                    mask |= (1 << i)
            return mask
        
        # Separate ones from other elements
        ones_count = 0
        mask_count = {}
        
        for num in arr:
            if num == 1:
                ones_count += 1
            else:
                mask = getPrimeMask(num)
                if mask != -1:  # Skip invalid numbers
                    mask_count[mask] = mask_count.get(mask, 0) + 1
        
        # DP for non-1 elements
        dp = [0] * (1 << len(primes))
        dp[0] = 1  # Empty subset
        
        for mask, count in mask_count.items():
            # Can pick at most 1 element with this mask
            ways = count
            
            # Traverse backwards to avoid reusing same element
            for prev_mask in range((1 << len(primes)) - 1, -1, -1):
                if dp[prev_mask] > 0 and (prev_mask & mask) == 0:
                    combined = prev_mask | mask
                    dp[combined] = (dp[combined] + dp[prev_mask] * ways) % MOD
        
        # Sum all valid subsets (exclude empty)
        result = (sum(dp) - 1) % MOD
        
        # Multiply by 2^ones_count
        # For each valid subset, we can add any combination of the ones
        result = (result * pow(2, ones_count, MOD)) % MOD
        
        return result
```

---

## Complexity Analysis

| Aspect | Complexity | Explanation |
|--------|-----------|-------------|
| **Time** | O(n + 2^10 × 2^10) = O(n) | n for preprocessing, then at most 1024 unique masks each processed with 1024 DP states |
| **Space** | O(2^10) = O(1024) | DP array with size 2^10 for bitmask combinations |

**Why O(n) time?**
- Preprocessing: O(n log 30) = O(n)
- DP iterations: O(1024 × 1024) = O(2^20) ≈ 10^6 operations (constant, doesn't depend on n)

---

## Detailed Walkthrough: Example 1

**Input**: `arr[] = [1, 2, 3, 4]`

### Step 1: Preprocessing
```
1       → ones_count = 1
2       → mask = 1 (bit 0 for prime 2)
3       → mask = 2 (bit 1 for prime 3)
4 = 2²  → mask = -1 (invalid)

mask_count = {1: 1, 2: 1}
ones_count = 1
```

### Step 2: DP Initialization
```
dp = [1, 0, 0, 0, 0, 0, 0, 0, ...]
dp[0] = 1 (empty subset)
```

### Step 3: Process mask = 1 (from element 2)
```
Backward iteration:
- prev_mask = 0: (0 & 1) = 0 ✓
  dp[1] = dp[1] + dp[0] × 1 = 0 + 1 × 1 = 1
  
Result: dp = [1, 1, 0, 0, ...]
```

### Step 4: Process mask = 2 (from element 3)
```
Backward iteration:
- prev_mask = 1: (1 & 2) = 0 ✓
  dp[3] = dp[3] + dp[1] × 1 = 0 + 1 × 1 = 1
  
- prev_mask = 0: (0 & 2) = 0 ✓
  dp[2] = dp[2] + dp[0] × 1 = 0 + 1 × 1 = 1
  
Result: dp = [1, 1, 1, 1, 0, ...]
```

### Step 5: Calculate Result
```
sum(dp) - 1 = 4 - 1 = 3 (subsets: [2], [3], [2,3])
Multiply by 2^ones_count = 2^1 = 2
Result = 3 × 2 = 6 ✓
```

---

## Test Cases

### Test 1: Basic Example
```python
arr = [1, 2, 3, 4]
Output: 6 ✓
```

### Test 2: All Identical Elements
```python
arr = [2, 2, 2, 2, 2, 2, 2, 2, 2, 2]
Output: 10 ✓
(Can pick exactly 1 of the 10 elements)
```

### Test 3: With Multiple 1's
```python
arr = [1, 13, 21, 17, 3, 1]
Output: 44 ✓
(11 valid subsets × 2² ones = 44)
```

### Test 4: Only 1's
```python
arr = [1, 1, 1]
Output: 0
(Cannot form valid subset with only 1's)
```

### Test 5: Single Prime
```python
arr = [2]
Output: 1
(Only [2])
```

---

## Edge Cases Handled

| Case | Handling |
|------|----------|
| **Array with only 1's** | `ones_count` counted, but result = 0 (no valid subsets) |
| **Repeated prime factors (4, 8, 9, ...)** | Marked as invalid (-1 mask), skipped |
| **Duplicate elements with same prime** | Stored in `mask_count`, can pick at most 1 |
| **Mixed valid and invalid numbers** | Invalid ones skipped, valid ones processed |
| **Large modulo arithmetic** | Applied after each operation to prevent overflow |

---

## How to Use

### Local Testing
```python
solution = Solution()

# Test case 1
arr1 = [1, 2, 3, 4]
print(solution.countSubsets(arr1))  # Output: 6

# Test case 2
arr2 = [2, 2, 2, 2, 2, 2, 2, 2, 2, 2]
print(solution.countSubsets(arr2))  # Output: 10

# Test case 3
arr3 = [1, 13, 21, 17, 3, 1]
print(solution.countSubsets(arr3))  # Output: 44
```

### Submission
This solution is optimized for:
- **LeetCode** / **GeeksforGeeks** / **HackerRank**
- Time Limit: ✓ Passes all constraints
- Space Limit: ✓ Minimal memory usage
- Modulo Operations: ✓ Correctly applied

---

## Key Takeaways

1. **Bitmask DP** is powerful for problems with limited distinct states
2. **Prime factorization** helps identify which elements are valid
3. **Grouping duplicates** reduces DP iterations significantly
4. **Backward iteration** in DP prevents reusing the same element
5. **Multiplicative principle** applies when combining independent groups (1's and other elements)

---

## References

- [Bitmask DP](https://en.wikipedia.org/wiki/Bitmask_DP)
- [Prime Factorization](https://en.wikipedia.org/wiki/Integer_factorization)
- [Subset Problems](https://www.geeksforgeeks.org/subset-sum-problem/)

---

## Author

Created for competitive programming practice and coding interviews.

## License

MIT License - Feel free to use and modify!
