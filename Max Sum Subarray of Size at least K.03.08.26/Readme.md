# Max Sum Subarray of Size at Least K

## 📌 Problem Statement

Given an integer array `arr[]` and an integer `k`, find the **maximum sum** among all **contiguous subarrays** having a **length greater than or equal to `k`**.

### Example 1

```text
Input:
arr = [1, -2, 2, -3]
k = 3

Output:
1
```

**Explanation:**

Possible subarrays with length ≥ 3

| Subarray | Length | Sum |
|----------|:------:|----:|
| [1, -2, 2] | 3 | 1 |
| [-2, 2, -3] | 3 | -3 |
| [1, -2, 2, -3] | 4 | -2 |

Maximum Sum = **1**

---

### Example 2

```text
Input:
arr = [1,1,1,1,1,1]
k = 2

Output:
6
```

**Explanation**

The complete array itself has the maximum sum.

```
1 + 1 + 1 + 1 + 1 + 1 = 6
```

---

### Example 3

```text
Input:
arr = [-4,-2,1,-3]
k = 2

Output:
-1
```

**Explanation**

The subarray `[-2,1]` has the maximum sum.

---

# Constraints

```text
1 <= arr.length <= 10^5
-10^4 <= arr[i] <= 10^4
1 <= k <= arr.length
```

---

# Approach

This problem cannot be solved using only a **Sliding Window** because the window size is **not fixed**.

The subarray length can be:

```
k
k+1
k+2
...
n
```

Instead, we combine:

- Kadane's Algorithm
- Sliding Window

---

# Step 1 : Build Kadane Array

We create an array called **maxSum[]**

```
maxSum[i]
```

stores

> Maximum subarray sum ending exactly at index **i**

---

## Example

```
arr = [2,-1,3,4,-2,5]
```

### Building maxSum[]

| Index | arr[i] | Start New (`arr[i]`) | Extend Previous (`currMax + arr[i]`) | currMax | maxSum |
|------:|--------:|---------------------:|--------------------------------------:|--------:|---------|
|0|2|-|-|2|[2,0,0,0,0,0]|
|1|-1|-1|1|1|[2,1,0,0,0,0]|
|2|3|3|4|4|[2,1,4,0,0,0]|
|3|4|4|8|8|[2,1,4,8,0,0]|
|4|-2|-2|6|6|[2,1,4,8,6,0]|
|5|5|5|11|11|[2,1,4,8,6,11]|

Final Kadane Array

| Index | maxSum[i] | Meaning |
|------:|----------:|---------|
|0|2|Best subarray ending at index 0|
|1|1|Best subarray ending at index 1|
|2|4|Best subarray ending at index 2|
|3|8|Best subarray ending at index 3|
|4|6|Best subarray ending at index 4|
|5|11|Best subarray ending at index 5|

---

# Step 2 : Calculate First Window of Size K

```
arr = [2,-1,3,4,-2,5]

k = 3
```

Window

```
[2,-1,3]
```

Sum

```
4
```

```
windowSum = 4

answer = 4
```

---

# Step 3 : Slide the Window

Instead of recalculating the window every time,

we use

```python
windowSum = windowSum + arr[i] - arr[i-k]
```

---

## Iteration 1

Window

```
[-1,3,4]
```

| Column | Value |
|---------|------:|
| Previous Window Sum | 4 |
| Remove | 2 |
| Add | 4 |
| New Window Sum | 6 |
| maxSum[i-k] | 2 |
| Candidate | 6 + 2 = 8 |
| Answer | 8 |

---

## Iteration 2

Window

```
[3,4,-2]
```

| Column | Value |
|---------|------:|
| Previous Window Sum | 6 |
| Remove | -1 |
| Add | -2 |
| New Window Sum | 5 |
| maxSum[i-k] | 1 |
| Candidate | 5 + 1 = 6 |
| Answer | 8 |

---

## Iteration 3

Window

```
[4,-2,5]
```

| Column | Value |
|---------|------:|
| Previous Window Sum | 5 |
| Remove | 3 |
| Add | 5 |
| New Window Sum | 7 |
| maxSum[i-k] | 4 |
| Candidate | 7 + 4 = 11 |
| Answer | 11 |

Final Answer

```
11
```

---

# Python Code

```python
def maxSumWithK(arr, k):
    n = len(arr)

    # Step 1: Build Kadane array
    maxSum = [0] * n
    maxSum[0] = arr[0]

    currMax = arr[0]

    for i in range(1, n):
        currMax = max(arr[i], currMax + arr[i])
        maxSum[i] = currMax

    # Step 2: Calculate first window sum
    windowSum = sum(arr[:k])

    res = windowSum

    # Step 3: Slide the window
    for i in range(k, n):

        windowSum = windowSum + arr[i] - arr[i-k]

        # Window of exactly K
        res = max(res, windowSum)

        # Window of size greater than K
        res = max(res, windowSum + maxSum[i-k])

    return res


arr = [2, -1, 3, 4, -2, 5]
k = 3

print(maxSumWithK(arr, k))
```

---

# Code Explanation

## Step 1

```python
n = len(arr)
```

Stores the size of the array.

---

## Step 2

```python
maxSum = [0] * n
```

Creates the Kadane array.

It stores

> Maximum subarray sum ending at every index.

---

## Step 3

```python
currMax = arr[0]
```

Keeps track of the maximum subarray ending at the current index.

---

## Step 4

```python
for i in range(1, n):
```

Traverse every element.

---

## Step 5

```python
currMax = max(arr[i], currMax + arr[i])
```

Two choices

1. Start a new subarray.
2. Extend the previous subarray.

Choose whichever gives a larger sum.

Store it in

```python
maxSum[i]
```

---

## Step 6

```python
windowSum = sum(arr[:k])
```

Calculates the first window of exactly **K** elements.

---

## Step 7

```python
res = windowSum
```

Initial answer.

---

## Step 8

Slide the window.

```python
windowSum = windowSum + arr[i] - arr[i-k]
```

Instead of calculating the entire window again, remove the left element and add the new right element.

Time becomes **O(1)** for each window.

---

## Step 9

```python
res = max(res, windowSum)
```

Checks whether the current window of exactly **K** elements gives a better answer.

---

## Step 10

```python
res = max(res, windowSum + maxSum[i-k])
```

This is the most important line.

`windowSum` contains the sum of the current window of exactly **K** elements.

`maxSum[i-k]` contains the best subarray ending immediately before the current window.

Adding them allows us to consider subarrays whose length is **greater than K**.

---

# Dry Run

```
Array

2 -1 3 4 -2 5
```

Kadane Array

```
2 1 4 8 6 11
```

Sliding Window

```
Window 1

[2 -1 3]

Sum = 4

Answer = 4
```

```
Window 2

[-1 3 4]

Window Sum = 6

Left Extension = 2

Candidate = 8
```

```
Window 3

[3 4 -2]

Window Sum = 5

Left Extension = 1

Candidate = 6
```

```
Window 4

[4 -2 5]

Window Sum = 7

Left Extension = 4

Candidate = 11
```

Final Answer

```
11
```

---

# Time Complexity

### Building Kadane Array

```
O(n)
```

### Sliding Window

```
O(n)
```

### Total Time Complexity

```
O(n)
```

---

# Space Complexity

We store the Kadane array.

```
maxSum = [0] * n
```

Therefore,

```
O(n)
```

---

# Key Takeaways

- Sliding Window alone cannot solve this problem because the subarray length is **at least K**, not exactly K.
- Kadane's Algorithm computes the best subarray ending at every index.
- Sliding Window computes every subarray of exactly **K** elements.
- Combining both techniques allows us to efficiently consider **all subarrays of length ≥ K** in **O(n)** time.

---

# References

- **GeeksforGeeks Problem:** https://www.geeksforgeeks.org/problems/max-sum-subarray-of-size-at-least-k3121/1
- **Related Concept (Kadane's Algorithm):** https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/
