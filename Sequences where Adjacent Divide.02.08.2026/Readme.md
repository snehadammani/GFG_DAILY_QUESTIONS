# Sequences where Adjacent Elements Divide Each Other

A dynamic programming solution to count arrays where adjacent elements have a divisibility relationship.

## Problem Statement

Given two positive integers `n` and `m`, find the number of arrays of size `n` that can be formed such that:

- Each element is in the range `[1, m]`
- Adjacent elements satisfy the divisibility condition: **Element Aᵢ divides Aᵢ₊₁ OR Aᵢ₊₁ divides Aᵢ**

### Examples

#### Example 1
```
Input: n = 3, m = 3
Output: 17
Explanation: The possible arrays are:
[1,1,1], [1,1,2], [1,1,3], [1,2,1], [1,2,2], [1,3,1], [1,3,3], 
[2,1,1], [2,1,2], [2,1,3], [2,2,1], [2,2,2], [3,1,1], [3,1,2], 
[3,1,3], [3,3,1], [3,3,3]
```

#### Example 2
```
Input: n = 1, m = 10
Output: 10
Explanation: The possible arrays are [1], [2], [3], [4], [5], [6], [7], [8], [9], [10]
```

## Constraints

- `1 ≤ n ≤ 11`
- `1 ≤ m ≤ 11`

## Approach

This problem uses **Dynamic Programming** to efficiently count valid sequences.

### Algorithm

1. **State Definition**: 
   - `dp[length][value]` = number of valid sequences of length `length` ending with element `value`

2. **Base Case**:
   - `dp[1][val] = 1` for all values from 1 to m (each single-element array is valid)

3. **Transition**:
   - For each sequence length from 2 to n:
     - For each possible previous element (`prev`):
       - For each possible current element (`curr`):
         - If `curr % prev == 0` OR `prev % curr == 0` (divisibility check):
           - Add `dp[length-1][prev]` to `dp[length][curr]`

4. **Result**: 
   - Sum all `dp[n][val]` for val from 1 to m

### Complexity Analysis

- **Time Complexity**: O(n × m²) 
  - Three nested loops: length (n), previous value (m), current value (m)
  
- **Space Complexity**: O(n × m)
  - 2D DP table of size (n+1) × (m+1)

## Solution

```python
class Solution:
    def count(self, n: int, m: int) -> int:
        mod = 10**9 + 7
        
        # dp[length][value] = count of sequences of given length ending with value
        dp = [[0] * (m + 1) for i in range(n + 1)]
        
        # Base case: single element sequences
        for val in range(1, m + 1):
            dp[1][val] = 1
        
        # Fill DP table for sequences of increasing length
        for length in range(2, n + 1):
            for prev in range(1, m + 1):
                for curr in range(1, m + 1):
                    # Check divisibility condition
                    if (curr % prev == 0) or (prev % curr == 0):
                        dp[length][curr] = (dp[length][curr] + dp[length - 1][prev]) % mod
        
        # Sum all valid sequences of length n
        ans = 0
        for val in range(1, m + 1):
            ans = (ans + dp[n][val]) % mod
        
        return ans
```

## Example Walkthrough

### For n=3, m=3

**Step 1: Initialize base case**
```
dp[1] = [0, 1, 1, 1]  (sequences: [1], [2], [3])
```

**Step 2: Build sequences of length 2**
- From 1: can go to 1, 2, 3 (1 divides all)
- From 2: can go to 1, 2 (2 and 1 divide each other, 2 divides 2)
- From 3: can go to 1, 3 (3 and 1 divide each other, 3 divides 3)

**Step 3: Build sequences of length 3**
- Continue building valid sequences by checking divisibility

**Step 4: Sum all values in dp[3]**
- Result: 17

## How to Use

1. Clone the repository:
```bash
git clone https://github.com/yourusername/sequences-adjacent-divide.git
cd sequences-adjacent-divide
```

2. Run the solution:
```python
from solution import Solution

sol = Solution()
print(sol.count(3, 3))    # Output: 17
print(sol.count(1, 10))   # Output: 10
```

3. Run tests:
```bash
python test.py
```

## Test Cases

| n | m | Expected Output |
|---|---|---|
| 1 | 10 | 10 |
| 3 | 3 | 17 |
| 2 | 5 | 13 |
| 1 | 1 | 1 |

## Key Insights

1. **Divisibility is Bidirectional**: Either A divides B or B divides A (or both)
2. **1 is Special**: 1 divides every number, making it a bridge between sequences
3. **Modulo Operations**: Used to handle large results (10⁹ + 7)

## Related Problems

- Count of sequences with specific element relationships
- Number of valid paths in a graph
- Dynamic programming on divisibility

## License

MIT License - feel free to use this for educational purposes

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Author

Created as a solution to the GeeksforGeeks/LeetCode problem: "Sequences where Adjacent Divide"

---

**Last Updated**: 2024
