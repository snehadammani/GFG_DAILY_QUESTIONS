# Pairs with Less Than K Difference

## Problem Statement

Given an array of positive integers `arr[]` and an integer `k`, determine the total number of **unique pairs** `(i, j)` such that:

- `i < j`
- `|arr[i] - arr[j]| < k`

> **Note:** Pair `(i, j)` is considered the same as `(j, i)`, so each pair should be counted only once.

---

## Example 1

**Input**

```text
arr = [1, 10, 4, 2]
k = 3
```

**Output**

```text
2
```

**Explanation**

The valid pairs are:

- (1, 2)
- (2, 4)

Therefore, the answer is **2**.

---

## Example 2

**Input**

```text
arr = [2, 3, 4]
k = 5
```

**Output**

```text
3
```

**Explanation**

All possible pairs satisfy the given condition:

- (2, 3)
- (2, 4)
- (3, 4)

Hence, the answer is **3**.

---

## Approach

A brute-force solution would compare every possible pair and check whether their absolute difference is less than `k`. However, this approach requires **O(n²)** time, which is inefficient for large input sizes.

### Optimized Approach

1. Sort the array.
2. For every element `arr[i]`, find the first element that is **greater than or equal to** `arr[i] + k` using **Binary Search (`bisect_left`)**.
3. Every element between `i + 1` and the returned index forms a valid pair.
4. Add the count of these elements to the final answer.

Since the array is sorted, the absolute difference simplifies to:

```text
arr[j] - arr[i]
```

which allows binary search to efficiently find the valid range.

---

## Algorithm

1. Sort the input array.
2. Initialize `result = 0`.
3. Iterate through each element.
4. Compute `target = arr[i] + k`.
5. Use `bisect_left()` to find the first index where the value is greater than or equal to `target`.
6. Count all elements between `i + 1` and that index.
7. Return the total count.

---

## Python Solution

```python
from bisect import bisect_left

class Solution:

    def countPairs(self, arr: list[int], k: int) -> int:
        n = len(arr)

        arr.sort()

        res = 0

        for i in range(n):
            target = arr[i] + k

            index = bisect_left(arr, target)

            res += (index - i - 1)

        return res
```

---

## Dry Run

### Input

```text
arr = [1, 10, 4, 2]
k = 3
```

### Step 1: Sort the Array

```text
[1, 2, 4, 10]
```

| Index | Value | Target (`value + k`) | First Index ≥ Target | Valid Pairs |
|------:|------:|---------------------:|---------------------:|------------:|
| 0 | 1 | 4 | 2 | 1 |
| 1 | 2 | 5 | 3 | 1 |
| 2 | 4 | 7 | 3 | 0 |
| 3 | 10 | 13 | 4 | 0 |

**Total Valid Pairs = 2**

---

## Complexity Analysis

| Complexity | Value |
|------------|-------|
| **Time Complexity** | **O(n log n)** |
| **Space Complexity** | **O(1)** (excluding the sorting algorithm's internal space) |

---

## Key Concepts

- Sorting
- Binary Search
- `bisect_left`
- Pair Counting
- Arrays

---

## Why This Approach?

- Eliminates unnecessary pair comparisons.
- Uses binary search to efficiently locate the valid range.
- Suitable for large constraints (`n ≤ 10⁵`).
- Considerably faster than the brute-force **O(n²)** solution.

---

## Brute Force vs Optimized

| Approach | Time Complexity | Space Complexity |
|----------|-----------------|------------------|
| Brute Force | O(n²) | O(1) |
| Sorting + Binary Search | **O(n log n)** | **O(1)** |

---

## Conclusion

By combining **Sorting** with **Binary Search**, we can efficiently count all pairs whose absolute difference is strictly less than `k`. This optimized approach avoids checking every pair individually, making it ideal for large datasets and interview-level coding problems.
