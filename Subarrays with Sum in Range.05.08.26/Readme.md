# Subarrays with Sum in Range

## Problem Statement

Given an integer array `arr[]` and two integers `l` and `r`, count the number of **contiguous subarrays** whose sum lies in the range **[l, r]** (inclusive).

A **subarray** is a contiguous sequence of elements within an array.

---

## Examples

### Example 1

**Input**

```text
arr = [1, 4, 6]
l = 3
r = 8
```

**Output**

```text
3
```

**Explanation**

The valid subarrays are:

```text
[1,4] → Sum = 5
[4]   → Sum = 4
[6]   → Sum = 6
```

Therefore, the answer is **3**.

---

### Example 2

**Input**

```text
arr = [2,3,5,8]
l = 4
r = 13
```

**Output**

```text
6
```

**Explanation**

The valid subarrays are:

```text
[2,3]
[2,3,5]
[3,5]
[5]
[5,8]
[8]
```

Hence, the answer is **6**.

---

## Constraints

```text
1 ≤ arr.size() ≤ 10^5
1 ≤ arr[i] ≤ 10^4
1 ≤ l ≤ r ≤ 10^9
```

---

# Approach

## Brute Force

Generate every possible subarray, calculate its sum, and check whether it lies in the range **[l, r]**.

### Algorithm

1. Start every subarray from index `i`.
2. Extend it until index `j`.
3. Keep adding elements to obtain the current sum.
4. If the sum lies between `l` and `r`, increment the answer.

### Time Complexity

```text
O(n²)
```

This approach results in **Time Limit Exceeded (TLE)** for large inputs.

---

# Optimized Approach (Sliding Window)

### Observation

All elements of the array are **positive integers**.

Since all numbers are positive, the window sum always increases when we expand the window and decreases when we shrink it. This allows us to use the **Sliding Window (Two Pointers)** technique.

Instead of directly counting subarrays whose sum lies in **[l, r]**, we calculate:

```text
Answer =
Count(Subarrays with Sum ≤ r)
-
Count(Subarrays with Sum ≤ l - 1)
```

This works because:

```text
Subarrays in [l, r]
=
Subarrays with sum ≤ r
-
Subarrays with sum < l
```

---

## Counting Subarrays with Sum ≤ K

Maintain two pointers:

* `left`
* `right`

Also maintain the current window sum.

### Steps

1. Expand the window by moving `right`.
2. Add the current element to the window sum.
3. If the sum becomes greater than `K`, move `left` until the sum becomes less than or equal to `K`.
4. Every subarray ending at `right` and starting between `left` and `right` is valid.
5. Add `(right - left + 1)` to the answer.

Repeat until the array is processed.

---

# Python Code

```python
class Solution:
    def countSubarray(self, arr, l, r):

        def countAtMost(k):
            if k < 0:
                return 0

            left = 0
            curr_sum = 0
            count = 0

            for right in range(len(arr)):
                curr_sum += arr[right]

                while curr_sum > k:
                    curr_sum -= arr[left]
                    left += 1

                count += (right - left + 1)

            return count

        return countAtMost(r) - countAtMost(l - 1)
```

---

# Dry Run

### Input

```text
arr = [1,4,6]
l = 3
r = 8
```

## Step 1: Count subarrays with sum ≤ 8

| Window | Sum               | Valid Subarrays Ending Here | Count |
| ------ | ----------------- | --------------------------- | ----: |
| [1]    | 1                 | [1]                         |     1 |
| [1,4]  | 5                 | [1,4], [4]                  |     2 |
| [4,6]  | 10 → shrink → [6] | [6]                         |     1 |

Total = **4**

---

## Step 2: Count subarrays with sum ≤ 2

Valid subarrays:

```text
[1]
```

Total = **1**

---

## Final Answer

```text
4 - 1 = 3
```

---

# Complexity Analysis

### Time Complexity

```text
O(n)
```

Each element is added to the window once and removed once.

---

### Space Complexity

```text
O(1)
```

Only a few variables are used.

---

# Key Learning

* Since all array elements are **positive**, the **Sliding Window** technique can be applied.
* Instead of counting sums in a range directly, convert the problem into:

  * Count of subarrays with sum **≤ r**
  * Minus count of subarrays with sum **≤ (l - 1)**
* This reduces the time complexity from **O(n²)** to **O(n)**, making it suitable for arrays of size up to **10⁵**.
