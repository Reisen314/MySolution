

# P1536. Minimum Swaps to Arrange a Binary Grid


# Intuition
<!-- Describe your first thoughts on how to solve this problem. -->
There are two intuitive approaches to solving this type of optimization problem:

1. Enumerate all feasible solutions, compute the number of swaps (i.e., inversion count) for each, and then select the optimal one.

2. Design a greedy strategy that directly constructs the optimal solution step by step. And calculate the inversion count of it at the end

I initially considered the first approach. However, the inversion counting method I used runs in $$O(n^2)$$ time (Please tell me if there are better one!). If there are $$O(n)$$ feasible solutions to evaluate, the overall time complexity would become $$O(n^3)$$, which is inefficient.

Therefore, I turned to the second approach. I conjectured that the optimal strategy is to always choose the nearest feasible row at each step. The justification for this greedy choice is given in the proof below.
# Approach
<!-- Describe your approach to solving the problem. -->
To coding, i use **defaultdict** to group rows by the earliest position at which they become valid. , use **Sortedlist** to make sure we take the row with smallest index. Therefore, we always pop the smallest index using pop(0), ensuring we greedily choose the nearest feasible row and thus minimize the inversion count.
# Complexity
- Time complexity: $$O(n^2)$$
<!-- Add your time complexity here, e.g. $$O(n)$$ -->

- Space complexity: $$O(n)$$
<!-- Add your space complexity here, e.g. $$O(n)$$ -->

# Proof of Correctness for the Greedy Strategy

## Problem Setting

Let zeros[i] denote the number of trailing zeros in row i.  
For position i (0-indexed), the requirement is:

zeros[row] ≥ n − i − 1

A row that satisfies this inequality can be placed at position i.

Importantly:

If a row can be placed at position i, it can also be placed at any position j > i.

This monotonic feasibility property is crucial here.

Btw, The minimum number of adjacent swaps equals the 
[inversion count](https://en.wikipedia.org/wiki/Inversion_(discrete_mathematics)).

---

## Greedy Strategy

For each position i, among all rows in [i, n-1] that satisfy the requirement,  
choose the smallest index j and move it to position i using adjacent swaps.

We prove this strategy is optimal.

---

## Key Fact: Swaps = Inversion Count

The minimum number of adjacent swaps required to transform one permutation into another equals the number of inversions in the permutation.

Thus, minimizing swaps is equivalent to minimizing inversion count.

---

## Exchange Argument

Assume at position i there are two feasible rows:

j1 < j2

The greedy algorithm chooses j1.  
Suppose there exists an optimal solution that instead chooses j2.

Since j1 is feasible at position i but not chosen, it must appear at some later position in that optimal arrangement.  
Hence the permutation contains:

... j2 ... j1 ...

This contributes one inversion (j2, j1).

---

## Constructing a Better Solution

Swap the roles of j1 and j2:

- Place j1 at position i  
- Place j2 at the later position where j1 was  

This new arrangement remains feasible because:

- j1 satisfies the requirement at position i  
- j2 can be placed at any later position due to monotonic feasibility  

Thus feasibility is preserved.

However, the inversion (j2, j1) is removed.

So the new arrangement has strictly fewer inversions.

---

## Contradiction

We assumed the original solution was optimal.  
But we constructed another feasible solution with fewer inversions.

This contradicts optimality.

Therefore, at every position i, selecting the smallest feasible index is necessary for optimality.

---

## Conclusion

Because feasible rows form a monotonic set (if valid at position i, valid for all later positions),  
choosing the nearest feasible row minimizes inversion count locally and globally.

Hence, the greedy algorithm is optimal.


# Code
```python3 []
# Count the number of trailing zeros in a binary row (from right to left)
def count_zero(nums):
    n = len(nums)
    ans = 0
    for i in range(n - 1, -1, -1):
        if not nums[i]:
            ans += 1
        else:
            return ans
    return ans

# Count inversions in the array (brute-force O(n^2) approach)
def count_reverse(nums):
    n = len(nums)
    cnt = 0
    for i in range(1, n):
        for j in range(i):
            if nums[j] > nums[i]:
                cnt += 1
    return cnt

class Solution:
    def minSwaps(self, grid: List[List[int]]) -> int:
        count = defaultdict(list)
        n = len(grid)

        for i in range(n):
            # If the first k elements are non-zero,
            # this row can only be placed at row index >= k - 1
            k = n - count_zero(grid[i])
            count[k].append(i)

        # Use a SortedList to always pick the smallest available row index
        temp = SortedList()
        ans_list = []

        # Rows with all zeros can be placed anywhere, add them first
        temp.update(count[0])

        for j in range(n):
            # Add rows that become available at this step
            temp.update(count[j + 1])

            # If no row is available, construction is impossible
            if not temp:
                return -1

            # Always pick the smallest index to minimize inversions
            ans_list.append(temp.pop(0))

        # The minimum number of swaps equals the inversion count
        return count_reverse(ans_list)
```