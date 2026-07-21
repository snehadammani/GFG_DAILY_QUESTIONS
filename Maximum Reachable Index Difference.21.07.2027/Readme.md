# Maximum Reachable Index Difference

**Difficulty:** Medium | **Accuracy:** 48.14% | **Points:** 4

## Problem Statement

Given a string `s` containing lowercase English alphabets.

- Start from any index containing the character `'a'` and perform jump operations.
- In each jump operation, move to any index on the right side whose character is the immediate next letter of the current character in the alphabet (i.e., `'a' -> 'b'`, `'b' -> 'c'`, `'c' -> 'd'`, and so on).
- Continue performing jumps until no further jump is possible.

Find the maximum possible difference between the starting index and the ending index. If it is not possible to choose a starting index, return `-1`.

### Examples

```
Input: s = "aaabcb"
Output: 5
Explanation: Start at index 0 ('a'), jump to index 5 ('b'). Difference = 5 - 0 = 5.
```

```
Input: s = "xynjir"
Output: -1
Explanation: The string does not contain any character 'a'. So, the answer is -1.
```

```
Input: s = "abcbzzd"
Output: 6
Explanation: Start from index 0 ('a') -> index 1 ('b') -> index 2 ('c') -> index 6 ('d').
```

### Constraints

`1 <= s.size() <= 10^5`

---

## Approach

**Key insight:** Since jumps only go rightward and follow consecutive letters, the optimal starting point is always the *leftmost* `'a'`, because any chain reachable from a later `'a'` is also reachable from an earlier `'a'`.

For the ending point, define `f[i]` = the maximum index reachable starting a chain at index `i`.

- If `s[i] == 'z'` (or no valid next-letter index exists to the right of `i`), then `f[i] = i`.
- Otherwise, `f[i] = max(f[j])` for all `j > i` where `s[j]` is the next character after `s[i]`.

**Efficient computation:** Process letters from `'z'` down to `'a'`. For each letter group, maintain a suffix-maximum array of `f`-values. For each index `i`, binary-search (`bisect_right`) in the next letter's group to find the best reachable value in O(log n).

Final answer = `max(f[i] - i)` over all `'a'` indices, or `-1` if none exist.

**Complexity:** O(n log n) time, O(n) space.

---

## Solution (Python)

```python
from bisect import bisect_right

class Solution:
    def maxIndexDifference(self, s):
        n = len(s)
        groups = [[] for _ in range(26)]
        for i, ch in enumerate(s):
            groups[ord(ch) - 97].append(i)

        f = [0] * n
        suffix_max = [None] * 26

        def compute_suffix_max(c):
            idxs = groups[c]
            sm = [0] * len(idxs)
            if idxs:
                sm[-1] = f[idxs[-1]]
                for k in range(len(idxs) - 2, -1, -1):
                    sm[k] = max(f[idxs[k]], sm[k + 1])
            return sm

        # Base case: letter 'z'
        for i in groups[25]:
            f[i] = i
        suffix_max[25] = compute_suffix_max(25)

        # Process letters 'y' down to 'a'
        for c in range(24, -1, -1):
            idxs_c = groups[c]
            idxs_next = groups[c + 1]
            sm_next = suffix_max[c + 1]
            for i in idxs_c:
                if idxs_next:
                    pos = bisect_right(idxs_next, i)
                    f[i] = sm_next[pos] if pos < len(idxs_next) else i
                else:
                    f[i] = i
            suffix_max[c] = compute_suffix_max(c)

        if not groups[0]:
            return -1

        return max(f[i] - i for i in groups[0])
```

### Trace on `"abcbzzd"`
- `f[4] = 4, f[5] = 5` (z's)
- `f[6] = 6` (d, no 'e' ahead)
- `f[2] = 6` (c at index 2 -> d at index 6)
- `f[1] = 6, f[3] = 3`
- `f[0] = 6` (a at index 0 -> chain reaches 6)
- **Answer:** `6 - 0 = 6`

### Complexity

- **Time:** O(n log n) -- each index does one binary search.
- **Space:** O(n) -- for groups, `f`, and suffix-max arrays.
