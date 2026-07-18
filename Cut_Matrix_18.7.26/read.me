# Cut Matrix

**Difficulty:** Hard

## Problem Statement

Given a matrix of `0`s and `1`s and an integer `k`, divide the matrix into `k` pieces such that each piece has at least one `1` in it. A cut can be made in the following way:

- Choose a direction: vertical or horizontal.
- Choose an index to cut the matrix into two pieces.
- If the cut is horizontal, only the **bottom** part can be cut further.
- If the cut is vertical, only the **right** part can be cut further.

Return the number of different ways to divide the matrix, modulo `1e9 + 7`.

## Examples

### Example 1

```
Input:  matrix = [[1, 0, 0], [1, 1, 1], [0, 0, 0]], k = 3
Output: 3
```

**Explanation:** There are 3 valid ways to divide the matrix into 3 pieces, each having at least one `1`:
1. Horizontal cut after row 0, then vertical cut after col 0 on the bottom part.
2. Horizontal cut after row 0, then vertical cut after col 1 on the bottom part.
3. Vertical cut after col 0, then vertical cut after col 1 on the right part.

### Example 2

```
Input:  matrix = [[0, 0], [1, 1]], k = 2
Output: 1
```

**Explanation:** The only valid way is to cut vertically down the middle, since the top row has no `1`.

### Example 3

```
Input:  matrix = [[1, 0], [0, 0]], k = 1
Output: 1
```

**Explanation:** No cut is needed since `k = 1` — the whole matrix is one piece, and it contains a `1`.

## Constraints

```
1 <= n, m, k <= 200
```

where `n` = number of rows, `m` = number of columns.

## Function Signature

```python
class Solution:
    def findWays(self, matrix, k):
        # code here
        pass
```

---

## Approach

After any cut, only the **bottom** (for a horizontal cut) or **right** (for a vertical cut) part remains cuttable. This means the "active" region at any point is always `matrix[top:][left:]` for some `(top, left)` — never restricted on the bottom or right edges. So the state space collapses from a general rectangle to just `(top, left, pieces_remaining)`.

Let `f(top, left, kk)` = number of ways to split the region `matrix[top:][left:]` into `kk` valid pieces.

- **Base case** (`kk == 1`): valid iff the region contains at least one `1`.
- **Recursive case:**
  - *Horizontal cut* at row `i` (`top <= i <= n-2`): the top strip `rows[top..i], cols[left..]` must contain a `1`; if so, add `f(i+1, left, kk-1)`.
  - *Vertical cut* at col `j` (`left <= j <= m-2`): the left strip `rows[top..], cols[left..j]` must contain a `1`; if so, add `f(top, j+1, kk-1)`.

**Key optimization:** whether the cut-off strip contains a `1` is *monotonic* in the cut index (once true, it stays true as the strip grows). So each transition sum becomes a suffix sum over the previous `kk-1` DP layer, computable in O(1) per state after an O(n·m) prefix pass. This brings total complexity down to **O(n · m · k)**.

## Solution (Python)

```python
class Solution:
    def findWays(self, matrix, k):
        MOD = 10**9 + 7
        n = len(matrix)
        m = len(matrix[0])

        # rowSuffix[i][c] = 1 if row i has a 1 in columns [c, m-1]
        rowSuffix = [[0]*(m+1) for _ in range(n)]
        for i in range(n):
            for c in range(m-1, -1, -1):
                rowSuffix[i][c] = 1 if (matrix[i][c] == 1 or rowSuffix[i][c+1]) else 0

        # colSuffix[c][top] = 1 if column c has a 1 in rows [top, n-1]
        colSuffix = [[0]*(n+1) for _ in range(m)]
        for c in range(m):
            for top in range(n-1, -1, -1):
                colSuffix[c][top] = 1 if (matrix[top][c] == 1 or colSuffix[c][top+1]) else 0

        # nextRowWithOne[left][top] = min row i>=top with a 1 in cols>=left, else n
        nextRowWithOne = [[n]*(n+1) for _ in range(m)]
        for left in range(m):
            arr = nextRowWithOne[left]
            for top in range(n-1, -1, -1):
                arr[top] = top if rowSuffix[top][left] else arr[top+1]

        # nextColWithOne[top][left] = min col j>=left with a 1 in rows>=top, else m
        nextColWithOne = [[m]*(m+1) for _ in range(n)]
        for top in range(n):
            arr = nextColWithOne[top]
            for left in range(m-1, -1, -1):
                arr[left] = left if colSuffix[left][top] else arr[left+1]

        # suffix2D[top][left] = 1 if any 1 exists in rows>=top, cols>=left
        suffix2D = [[0]*(m+1) for _ in range(n+1)]
        for top in range(n-1, -1, -1):
            for left in range(m-1, -1, -1):
                suffix2D[top][left] = 1 if (matrix[top][left] == 1
                                             or suffix2D[top+1][left]
                                             or suffix2D[top][left+1]) else 0

        if k == 1:
            return 1 if suffix2D[0][0] else 0
        if k > n * m:
            return 0

        # DP layer for kk = 1 piece
        f_prev = [[1 if suffix2D[top][left] else 0 for left in range(m)] for top in range(n)]

        for kk in range(2, k+1):
            # suffix sums of previous layer, needed for O(1) transitions
            S = [[0]*(n+1) for _ in range(m)]          # S[left][r] = sum_{row=r}^{n-1} f_prev[row][left]
            for left in range(m):
                s, acc = S[left], 0
                for row in range(n-1, -1, -1):
                    acc = (acc + f_prev[row][left]) % MOD
                    s[row] = acc

            T = [[0]*(m+1) for _ in range(n)]          # T[top][c] = sum_{col=c}^{m-1} f_prev[top][col]
            for top in range(n):
                t, acc, rowv = T[top], 0, f_prev[top]
                for col in range(m-1, -1, -1):
                    acc = (acc + rowv[col]) % MOD
                    t[col] = acc

            f_curr = [[0]*m for _ in range(n)]
            for top in range(n):
                ncw = nextColWithOne[top]
                for left in range(m):
                    total = 0
                    if top <= n-2:                      # horizontal cut possible
                        i0 = nextRowWithOne[left][top]
                        if i0 <= n-2:
                            total += S[left][i0+1]
                    if left <= m-2:                      # vertical cut possible
                        j0 = ncw[left]
                        if j0 <= m-2:
                            total += T[top][j0+1]
                    f_curr[top][left] = total % MOD
            f_prev = f_curr

        return f_prev[0][0] % MOD
```

## Complexity

- **Time:** `O(n * m * k)`
- **Space:** `O(n * m)` (only the current and previous DP layers are kept)

## Tests

```python
import unittest


class TestCutMatrix(unittest.TestCase):
    def setUp(self):
        self.sol = Solution()

    def test_example_1(self):
        matrix = [[1, 0, 0], [1, 1, 1], [0, 0, 0]]
        self.assertEqual(self.sol.findWays(matrix, 3), 3)

    def test_example_2(self):
        matrix = [[0, 0], [1, 1]]
        self.assertEqual(self.sol.findWays(matrix, 2), 1)

    def test_example_3(self):
        matrix = [[1, 0], [0, 0]]
        self.assertEqual(self.sol.findWays(matrix, 1), 1)

    def test_single_cell_with_one(self):
        self.assertEqual(self.sol.findWays([[1]], 1), 1)

    def test_single_cell_without_one(self):
        self.assertEqual(self.sol.findWays([[0]], 1), 0)

    def test_impossible_more_pieces_than_ones(self):
        # Only two 1s available but 3 pieces requested -> 0 ways
        matrix = [[1, 0], [0, 1]]
        self.assertEqual(self.sol.findWays(matrix, 3), 0)

    def test_all_ones_row_vector(self):
        # 1x4 all-ones row, any single cut point works for k=2
        matrix = [[1, 1, 1, 1]]
        self.assertEqual(self.sol.findWays(matrix, 2), 3)

    def test_all_ones_column_vector(self):
        matrix = [[1], [1], [1], [1]]
        self.assertEqual(self.sol.findWays(matrix, 2), 3)

    def test_large_random_no_crash(self):
        import random
        random.seed(1)
        n, m, k = 200, 200, 200
        matrix = [[random.choice([0, 1]) for _ in range(m)] for _ in range(n)]
        result = self.sol.findWays(matrix, k)
        self.assertIsInstance(result, int)
        self.assertGreaterEqual(result, 0)
        self.assertLess(result, 10**9 + 7)


if __name__ == "__main__":
    unittest.main()
```
