# Mountain Subarray Queries

**Difficulty:** Medium
**Points:** 4
**Accuracy:** 46.22%

## Problem Statement

Given an array `arr[]` and a list of queries. For each query `[l, r]`, find whether the subarray `arr[l...r]` is a mountain array.

A subarray is called a **mountain array** if there exists an index `k` (`l <= k <= r`) such that:

```
arr[l] <= arr[l + 1] <= ... <= arr[k] >= arr[k + 1] >= ... >= arr[r]
```

- Elements of a mountain subarray are first non-decreasing and then non-increasing.
- A subarray that is entirely non-decreasing or entirely non-increasing is also considered a mountain.

## Examples

### Example 1
```
Input: arr[] = [2, 3, 2, 4, 4, 6, 3, 2], queries[][] = [[0, 2], [1, 3]]
Output: [true, false]
```
**Explanation:**
- For query `[0, 2]`, the subarray is `[2, 3, 2]`. The elements first increase and then decrease, so it forms a mountain.
- For query `[1, 3]`, the subarray is `[3, 2, 4]`. The elements decrease and then increase, so it does not form a mountain.

### Example 2
```
Input: arr[] = [2, 2, 2, 2], queries[][] = [[0, 2], [1, 3]]
Output: [true, true]
```
**Explanation:**
All subarrays of the given array are mountain (flat arrays count as mountains).

## Constraints

- `1 <= arr.size(), queries.size() <= 10^5`
- `1 <= arr[i] <= 10^6`
- `0 <= l <= r < arr.size()`

## Approach

Brute-forcing each query by scanning the subarray is too slow (`O(n)` per query, `O(n*q)` total, up to `10^10` operations). Instead, precompute two arrays once over the whole input, then answer each query in O(1).

**1. `incEnd[i]`** — starting at index `i`, the rightmost index reachable while the array stays **non-decreasing**.

**2. `desc[i]`** — starting at index `i`, the rightmost index reachable while the array stays **non-increasing**.

Both arrays are built with a single right-to-left pass using a DP recurrence:
- `incEnd[i] = incEnd[i+1]` if `arr[i] <= arr[i+1]`, else `incEnd[i] = i`
- `desc[i] = desc[i+1]` if `arr[i] >= arr[i+1]`, else `desc[i] = i`

**Answering a query `[l, r]`:**
- The climb starting at `l` naturally extends to `incEnd[l]`. The best candidate peak within the query is `k = min(incEnd[l], r)` — capped so it doesn't exceed the query's right boundary.
- Check whether the descent from that peak reaches all the way to `r`: `desc[k] >= r`.
- If true, the subarray is a mountain; otherwise, it isn't.

Checking only this single candidate `k` is sufficient because `desc[]` is non-decreasing as you move `k` further along the same declining slope — so the largest valid peak candidate always gives the best chance of reaching `r`. If even the best candidate fails, no earlier peak could succeed either.

## Code

```python
class Solution:
    def mountainQueries(self, arr, queries):
        """
        :type arr: List[int]
        :type queries: List[List[int]]
        :rtype: List[bool]
        """
        n = len(arr)

        # incEnd[i] = rightmost index reachable from i while staying non-decreasing
        incEnd = [0] * n
        incEnd[n - 1] = n - 1
        for i in range(n - 2, -1, -1):
            if arr[i] <= arr[i + 1]:
                incEnd[i] = incEnd[i + 1]
            else:
                incEnd[i] = i

        # desc[i] = rightmost index reachable from i while staying non-increasing
        desc = [0] * n
        desc[n - 1] = n - 1
        for i in range(n - 2, -1, -1):
            if arr[i] >= arr[i + 1]:
                desc[i] = desc[i + 1]
            else:
                desc[i] = i

        result = []
        for l, r in queries:
            k = min(incEnd[l], r)
            result.append(desc[k] >= r)

        return result
```

## Explanation of the Code

| Line(s) | What it does | Why |
|---|---|---|
| `n = len(arr)` | Stores array length | Reused multiple times, avoids repeated `len()` calls |
| `incEnd = [0] * n` | Preallocates a list of size `n` | Lists give O(1) index access; dense integer indices don't need a dictionary |
| `incEnd[n - 1] = n - 1` | Base case for last index | The last element trivially reaches only itself |
| `for i in range(n - 2, -1, -1):` | Iterates right-to-left | `incEnd[i]` depends on `incEnd[i+1]`, so later indices must be computed first |
| `if arr[i] <= arr[i+1]: incEnd[i] = incEnd[i+1]` | Extends the non-decreasing run | If climbing continues, inherit how far the next index's climb reached |
| `else: incEnd[i] = i` | Breaks the run | If the climb stops here, this index can't reach further than itself |
| `desc[]` block | Same logic, mirrored | Computes non-increasing reach using `>=` instead of `<=` |
| `for l, r in queries:` | Unpacks each `[l, r]` query pair | Cleaner than indexing `query[0]`, `query[1]` manually |
| `k = min(incEnd[l], r)` | Finds best candidate peak within the query bounds | Climb can't be considered past the query's right boundary |
| `result.append(desc[k] >= r)` | Appends `True`/`False` for this query | Checks whether descent from the peak reaches the query's end |
| `return result` | Returns answers in the same order as queries | Preserves query order using a list |

## Complexity Analysis

- **Time Complexity:** O(n + q) — one O(n) pass to build `incEnd`, one O(n) pass to build `desc`, then O(1) per query.
- **Space Complexity:** O(n) for the two auxiliary arrays.

This comfortably handles `arr.size()` and `queries.size()` up to `10^5`.

## Trace Through Example 1

`arr = [2, 3, 2, 4, 4, 6, 3, 2]`

```
incEnd = [1, 1, 5, 5, 5, 5, 6, 7]
desc   = [0, 2, 2, 4, 4, 7, 7, 7]
```

**Query [0, 2]:** `k = min(incEnd[0]=1, 2) = 1` → `desc[1] = 2 >= 2` → **True**

**Query [1, 3]:** `k = min(incEnd[1]=1, 3) = 1` → `desc[1] = 2 >= 3` → **False**

Output: `[True, False]` ✅ matches expected output.
