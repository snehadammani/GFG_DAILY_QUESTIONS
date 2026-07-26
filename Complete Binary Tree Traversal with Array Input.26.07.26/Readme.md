# Complete Binary Tree Traversal with Array Input

## Problem Statement

Given an integer array `arr[]` representing the nodes of a **Complete Binary Tree** in level-order traversal, return the nodes at each level in **sorted ascending order**.

For every level of the binary tree, sort the values present at that level independently and return the resulting levels as a 2D array, where the i-th row contains the sorted values of the i-th level.

## Problem Constraints

- `1 ≤ arr.size() ≤ 10⁴`
- `1 ≤ arr[i] ≤ 10⁹`

## Solution Approach

### Algorithm Overview

The solution uses a **level-by-level processing** approach without actually constructing the tree structure:

1. **Track Positions**: Use `currentIndex` to mark the start of the current level and `levelNumber` to track which level we're processing.

2. **Calculate Level Boundaries**: For a complete binary tree in level-order traversal:
   - Level 0 has 1 node (indices 0-0)
   - Level 1 has 2 nodes (indices 1-2)
   - Level 2 has 4 nodes (indices 3-6)
   - Level k has 2^k nodes
   - The end index of level k = 2^(k+1) - 1

3. **Extract and Sort**: For each level:
   - Extract nodes from `currentIndex` to `levelEnd`
   - Sort them in ascending order
   - Add the sorted level to the result
   - Move to the next level

### Key Insight: Bit Shifting

The code uses bit shifting `(1 << levelNumber) - 1` to calculate the end index:
- `1 << n` is equivalent to 2^n
- For level 1: `(1 << 1) - 1 = 2 - 1 = 1` (ends at index 1)
- For level 2: `(1 << 2) - 1 = 4 - 1 = 3` (ends at index 3)
- For level 3: `(1 << 3) - 1 = 8 - 1 = 7` (ends at index 7)

## Code Implementation

```python
class Solution:
    def levelSort(self, arr):
        sortedLevels = []
        currentIndex = 0
        levelNumber = 1
        
        while currentIndex < len(arr):
            # Calculate the end index for the current level
            levelEnd = (1 << levelNumber) - 1
            levelEnd = min(levelEnd, len(arr))
            
            # Sort the current level
            arr[currentIndex:levelEnd] = sorted(arr[currentIndex:levelEnd])
            
            # Collect the sorted level
            currentLevel = []
            for i in range(currentIndex, levelEnd):
                currentLevel.append(arr[i])
            
            sortedLevels.append(currentLevel)
            
            # Move to the next level
            currentIndex = levelEnd
            levelNumber += 1
        
        return sortedLevels
```

## Examples

### Example 1

**Input:** `arr = [7, 6, 5, 4, 3, 2, 1]`

**Output:** `[[7], [5, 6], [1, 2, 3, 4]]`

**Explanation:**
```
Complete Binary Tree (level-order):
        7
       / \
      6   5
     / \ / \
    4  3 2  1

After sorting each level:
Level 0: [7]
Level 1: [5, 6]  (sorted: 6, 5 → 5, 6)
Level 2: [1, 2, 3, 4]  (sorted: 4, 3, 2, 1 → 1, 2, 3, 4)
```

### Example 2

**Input:** `arr = [7, 16, 1, 4, 13]`

**Output:** `[[7], [1, 16], [4, 13]]`

**Explanation:**
```
Complete Binary Tree (level-order):
        7
       / \
     16   1
     / \
    4  13

After sorting each level:
Level 0: [7]
Level 1: [1, 16]  (sorted: 16, 1 → 1, 16)
Level 2: [4, 13]  (sorted: 4, 13 → 4, 13)
```

## Complexity Analysis

### Time Complexity: **O(n log n)**
- We visit each element exactly once: **O(n)**
- Each element is sorted as part of its level
- Sorting operations at each level: In the worst case, all elements are at the last level, requiring O(n log n) for sorting
- Overall: **O(n log n)**

### Space Complexity: **O(n)**
- **Output array**: Stores all n elements in the 2D result array
- **Sorting**: Python's sort uses O(log n) space for recursion/temporary storage
- **Overall**: **O(n)** for storing the result

## Edge Cases

| Case | Input | Output |
|------|-------|--------|
| Single element | `[5]` | `[[5]]` |
| Two elements | `[3, 1]` | `[[3], [1]]` |
| All sorted | `[1, 2, 3, 4]` | `[[1], [2, 3], [4]]` |
| Reverse sorted | `[4, 3, 2, 1]` | `[[4], [2, 3], [1]]` |
| Large numbers | `[1000000000, 999999999]` | `[[1000000000], [999999999]]` |

## How to Use

```python
# Create an instance of the Solution class
solution = Solution()

# Example usage
arr1 = [7, 6, 5, 4, 3, 2, 1]
result1 = solution.levelSort(arr1)
print(result1)  # Output: [[7], [5, 6], [1, 2, 3, 4]]

arr2 = [7, 16, 1, 4, 13]
result2 = solution.levelSort(arr2)
print(result2)  # Output: [[7], [1, 16], [4, 13]]
```

## Advantages of This Approach

✅ **No tree construction needed** - Works directly with the array  
✅ **Efficient** - Single pass through the array with sorting at each level  
✅ **Space-efficient** - Only stores the result, not the entire tree structure  
✅ **Clear logic** - Easy to understand and maintain  

## Related Concepts

- **Level Order Traversal**: Process nodes level by level
- **Complete Binary Tree**: All levels are filled except possibly the last, which is filled from left to right
- **In-place Sorting**: Modifies the input array during processing
- **Bit Manipulation**: Using bitwise operations for efficient calculations

## Difficulty Level

**Medium** (46.62% Accuracy | 16K+ Submissions)

This problem requires understanding:
- Binary tree properties and level-order representation
- Array indexing in binary trees
- Sorting algorithms
- Problem-solving with arrays

---

**Last Updated:** 2026  
**Language:** Python 3
