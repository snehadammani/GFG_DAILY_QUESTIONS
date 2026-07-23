# Check Preorder of BST

**Difficulty:** Medium
**Accuracy:** 36.84%
**Points:** 4

## Problem Statement

Given an array `arr[]` consisting of distinct integers, check if the given array can represent the preorder traversal of a **Binary Search Tree (BST)**.

## Examples

### Example 1
```
Input:  arr[] = [2, 4, 3]
Output: true
```
**Explanation:** `arr[]` can represent the preorder traversal of the following BST:

```
    2
     \
      4
     /
    3
```

### Example 2
```
Input:  arr[] = [2, 4, 1]
Output: false
```
**Explanation:** `arr[]` cannot represent the preorder traversal of any BST.

## Constraints
- `1 ≤ arr.size() ≤ 10^5`
- `0 ≤ arr[i] ≤ 10^5`

## Approach

A preorder traversal visits nodes in the order **root → left subtree → right subtree**. In a BST, every value in the left subtree is smaller than the root, and every value in the right subtree is larger than the root.

Given this, for any valid preorder sequence:
1. The first element `arr[l]` is the **root** of the current subtree.
2. All elements immediately following the root that are **smaller** than it belong to the **left subtree**.
3. The first element that is **greater** than the root marks the start of the **right subtree** — and from that point on, **every remaining element must be greater** than the root (otherwise the array is invalid).
4. Recursively validate the left and right subtree segments using the same rule.

This recursive check runs over the range `[l, r]` of the array, splitting it into a left segment `[l+1, j-1]` and a right segment `[j, r]`, where `j` is the index of the first element greater than `arr[l]`.

### Algorithm Steps
1. If the subtree has 0 or 1 elements (`l >= r`), it's trivially a valid BST — return `True`.
2. Scan forward from `l+1` while elements are smaller than `arr[l]` to find `j`, the boundary where the right subtree begins.
3. Verify that **all** elements from `j` to `r` are greater than `arr[l]`. If any element in this range is smaller, the array is invalid — return `False`.
4. Recursively check the left segment `[l+1, j-1]` and the right segment `[j, r]`.

## Solution (Python)

```python
def check(arr, l, r):
    # If subtree has 0 or 1 node
    if l >= r:
        return True

    j = l + 1
    # Find first element greater than root
    while j <= r and arr[j] < arr[l]:
        j += 1

    # Check whether all elements in right subtree
    # are greater than root
    for k in range(j, r + 1):
        if arr[k] < arr[l]:
            return False

    # Recursively check left and right subtrees
    return check(arr, l + 1, j - 1) and check(arr, j, r)


def canRepresentBST(arr):
    return check(arr, 0, len(arr) - 1)


# Driver Code
if __name__ == "__main__":
    arr = [40, 30, 35, 80, 100]
    if canRepresentBST(arr):
        print("true")
    else:
        print("false")
```

## Complexity Analysis

| Metric | Complexity |
|---|---|
| Time Complexity | O(n²) worst case (each recursive call may scan the remaining range) |
| Space Complexity | O(n) (recursion stack in the worst case of a skewed tree) |

> **Note:** This can be optimized to **O(n)** time using a stack-based iterative approach that tracks a running lower bound (`min_val`) while scanning the array once.

### Optimized Approach (O(n)) — Stack Based
An alternative, more efficient solution uses a stack to simulate the traversal:

```python
def canRepresentBST(arr):
    stack = []
    min_val = float('-inf')

    for value in arr:
        # If current value violates the lower bound, it's invalid
        if value < min_val:
            return False

        # Pop elements smaller than current value,
        # updating the lower bound (min_val) to the last popped value
        while stack and stack[-1] < value:
            min_val = stack.pop()

        stack.append(value)

    return True
```

This runs in **O(n)** time and **O(n)** space, making it more suitable for large inputs (up to `10^5` elements as per constraints).

## Sample Run

```
Input:  arr = [40, 30, 35, 80, 100]
Output: true
```
