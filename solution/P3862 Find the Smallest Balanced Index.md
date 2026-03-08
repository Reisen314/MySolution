# Intuition
<!-- Describe your first thoughts on how to solve this problem. -->
Compute the prefix sums of the left-side elements in advance. Then iterate from right to left, maintaining the product of the elements on the right.

If the target condition is met, terminate the search. If the product of the right-side elements exceeds the sum of the left-side elements, terminate early as well.


# Complexity
- Time complexity: $$O(n)$$
<!-- Add your time complexity here, e.g. $$O(n)$$ -->

- Space complexity: $$O(n)$$
<!-- Add your space complexity here, e.g. $$O(n)$$ -->

# Code

```python 
class Solution:
    def smallestBalancedIndex(self, nums):
        n = len(nums)
        right=1
        # Prefix Sum
        prefix=[0]*(n+1)
        for i in range(1,n+1):
            prefix[i]=prefix[i-1]+nums[i-1]
        # From right to left
        for i in range(n-1,-1,-1):
            left=prefix[i]
            if right>left:
                break
            if right==left:
                return i
            # update the product of elements at right
            right*=nums[i]
        return -1
```