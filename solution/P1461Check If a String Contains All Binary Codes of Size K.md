# Intuition
<!-- Describe your first thoughts on how to solve this problem. -->
There are at most $2^{k}$possible substring for length k, so the group of substring for s cover all the possible sub string, if and only if there are $2^{k}$ different element in the group of substring for s

# Complexity
- Time complexity:$$O(n)$$
<!-- Add your time complexity here, e.g. $$O(n)$$ -->

- Space complexity:$$O(n)$$
<!-- Add your space complexity here, e.g. $$O(n)$$ -->

# Code
```python 
class Solution:
    def hasAllCodes(self, s: str, k: int) -> bool:
        n=len(s)
        #enumerate all the possible substring
        sub=[int(s[i:i+k],2) for i in range(n-k+1)]
        sub_set=set(sub)
        #judge from length e.g.{0,1,2,3} -> 4 -> True
        return len(sub_set)==1<<k
```