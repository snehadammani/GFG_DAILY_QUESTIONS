# Maximum Subset XOR

## Problem Statement

Given an array `arr[]`, choose any subset of elements (possibly all elements) such that the XOR of the chosen elements is maximized.

Return the maximum possible XOR value.

## Examples

### Example 1

**Input**
```text
arr = [2, 4, 5]
```

**Output**
```text
7
```

**Explanation**

The subset `{2, 5}` produces the maximum XOR.

```
2 ^ 5 = 7
```

---

### Example 2

**Input**
```text
arr = [9, 8, 5]
```

**Output**
```text
13
```

**Explanation**

The subset `{8, 5}` gives the maximum XOR.

```
8 ^ 5 = 13
```

---

## Approach

A brute-force solution would examine every possible subset and compute its XOR value. Since an array of size `N` has `2^N` subsets, this approach is computationally infeasible for large inputs.

To solve the problem efficiently, we use the **XOR Basis (Linear Basis)** technique.

The idea is to build a set of linearly independent numbers by processing bits from the most significant bit (MSB) to the least significant bit (LSB). For each bit position:

1. Select a number containing the current bit.
2. Use it as the pivot.
3. Eliminate the same bit from all other numbers using the XOR operation.

After constructing the basis, greedily combine the basis elements to maximize the XOR value.

This method is analogous to Gaussian Elimination performed over binary numbers.

---

## Algorithm

1. Traverse bits from the highest bit to the lowest.
2. Find a pivot element with the current bit set.
3. Swap the pivot into the current basis position.
4. XOR the pivot with every other element having the same bit set to eliminate that bit.
5. Repeat for all bit positions.
6. Starting with `answer = 0`, greedily XOR basis elements whenever they increase the current answer.
7. Return the maximum XOR obtained.

---

## Complexity Analysis

- **Time Complexity:** `O(32 × N)` ≈ `O(N)`
- **Space Complexity:** `O(1)`

---

## Key Concepts

- XOR Operation
- Bit Manipulation
- Linear Basis (XOR Basis)
- Gaussian Elimination over GF(2)
- Greedy Approach

---

## Python Solution

```python
class Solution:
    def maxSubsetXOR(self, arr):
        n = len(arr)
        index = 0

        # Build XOR Basis
        for bit in range(31, -1, -1):

            pivot = -1
            for i in range(index, n):
                if (arr[i] >> bit) & 1:
                    pivot = i
                    break

            if pivot == -1:
                continue

            arr[index], arr[pivot] = arr[pivot], arr[index]

            for i in range(n):
                if i != index and ((arr[i] >> bit) & 1):
                    arr[i] ^= arr[index]

            index += 1

        ans = 0

        for x in arr:
            ans = max(ans, ans ^ x)

        return ans
```

---

## Takeaways

- Checking all subsets is impossible because it requires `2^N` operations.
- The XOR Basis efficiently removes redundant numbers while preserving all possible XOR combinations.
- The final basis contains independent values that can generate the maximum XOR greedily.
- This is the standard optimal solution for **Maximum Subset XOR** problems and is widely used in competitive programming and technical interviews.
