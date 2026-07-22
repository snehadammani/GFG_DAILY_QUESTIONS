# Minimum Deletions to Make Sorted

**Difficulty:** Easy
**Accuracy:** 49.53%
**Points:** 2

---

## Problem Statement

Given an array `arr[]`, find the minimum number of elements to delete so that the remaining elements form a **strictly increasing** sequence, while preserving their relative (original) order.

### Examples

**Example 1:**
```
Input:  arr[] = [5, 6, 1, 7, 4]
Output: 2
Explanation: Removing 1 and 4 leaves [5, 6, 7], which is strictly increasing.
```

**Example 2:**
```
Input:  arr[] = [1, 1, 1]
Output: 2
Explanation: Removing any 2 elements leaves [1], which is strictly increasing.
```

### Constraints
```
1 ≤ n ≤ 10^5
1 ≤ arr[i] ≤ 10^5
```

---

## Algorithm / Approach

The key insight is:

> **Minimum deletions = n − (length of the Longest Strictly Increasing Subsequence, LIS)**

This is because the elements we *keep* must already form a strictly increasing subsequence of the original array (order preserved). To minimize deletions, we must maximize the number of elements kept — i.e., find the **Longest Strictly Increasing Subsequence**. Everything not part of this subsequence gets deleted.

### Why LIS and not just "sorted subsequence"?
Because we need the result to be **strictly increasing** (no duplicates, no equal adjacent values), so we specifically need the Longest *Strictly* Increasing Subsequence — not a non-decreasing one.

### Efficient LIS Computation (Patience Sorting + Binary Search)

Instead of the O(n²) DP approach, we use an O(n log n) technique:

1. Maintain a list `tails`, where `tails[i]` represents the **smallest possible tail value** of a strictly increasing subsequence of length `i + 1` found so far.
2. For each element `x` in the array:
   - Use **binary search (`bisect_left`)** to find the leftmost index `i` in `tails` where `x` can be placed.
     - `bisect_left` ensures **strict** increase — if `x` already exists in `tails`, it replaces that value instead of extending the sequence (avoiding duplicates).
   - If `i == len(tails)`, it means `x` is bigger than everything currently in `tails` → **append** `x` (this extends the LIS length).
   - Otherwise, **overwrite** `tails[i] = x` (keeps the potential for future longer subsequences open, without changing the LIS length found so far).
3. At the end, `len(tails)` gives the length of the LIS.
4. The answer is `n - len(tails)`.

---

## Program (Python)

```python
class Solution:
    def minDeletions(self, arr):
        import bisect
        n = len(arr)
        tails = []
        for x in arr:
            i = bisect.bisect_left(tails, x)
            if i == len(tails):
                tails.append(x)
            else:
                tails[i] = x
        return n - len(tails)
```

### Driver Code (for testing)

```python
if __name__ == "__main__":
    sol = Solution()

    arr1 = [5, 6, 1, 7, 4]
    print(sol.minDeletions(arr1))  # Output: 2

    arr2 = [1, 1, 1]
    print(sol.minDeletions(arr2))  # Output: 2
```

---

## Program Explanation

Let's trace through `arr = [5, 6, 1, 7, 4]`:

| Step | x | bisect_left(tails, x) | Action | tails after |
|------|---|------------------------|--------|-------------|
| 1 | 5 | 0 (tails empty) | append | `[5]` |
| 2 | 6 | 1 (end of list) | append | `[5, 6]` |
| 3 | 1 | 0 | overwrite index 0 | `[1, 6]` |
| 4 | 7 | 2 (end of list) | append | `[1, 6, 7]` |
| 5 | 4 | 1 | overwrite index 1 | `[1, 4, 7]` |

- Final `tails = [1, 4, 7]` → length = **3**
- This means the LIS length is 3 (e.g., `[5, 6, 7]` or `[1, 4, 7]` — the *values* in `tails` don't necessarily form the actual LIS, but the *length* is always correct).
- Answer = `n - LIS length` = `5 - 3` = **2** ✓ (matches expected output)

**Note:** The `tails` array is **not** the actual LIS subsequence — it's an auxiliary structure that tracks the smallest tail value for each possible subsequence length. This is what allows the algorithm to run efficiently, since we only need the *length* of the LIS to answer this problem, not the subsequence itself.

### Why `bisect_left` and not `bisect_right`?
- `bisect_right` would allow equal elements to extend the sequence (used for *non-decreasing* / *longest non-decreasing subsequence*).
- `bisect_left` forces strictly greater elements to extend the sequence, and equal elements to replace an existing tail — exactly what we need for **strictly increasing** sequences.

---

## Complexity Analysis

### Time Complexity: **O(n log n)**
- We iterate through the array once → O(n)
- For each element, we perform a binary search on `tails` (which has at most `n` elements) → O(log n)
- Total: O(n log n)

This is a significant improvement over the naive O(n²) DP approach:
```python
# Naive O(n^2) LIS for reference
dp = [1] * n
for i in range(n):
    for j in range(i):
        if arr[j] < arr[i]:
            dp[i] = max(dp[i], dp[j] + 1)
lis_length = max(dp)
```

### Space Complexity: **O(n)**
- The `tails` list can grow up to size `n` in the worst case (when the array is already strictly increasing).

---

## Summary

| Aspect | Detail |
|---|---|
| Core Idea | Minimum deletions = n − LIS length (strictly increasing) |
| Technique | Patience Sorting with Binary Search |
| Time Complexity | O(n log n) |
| Space Complexity | O(n) |
| Key Function | `bisect.bisect_left` |
