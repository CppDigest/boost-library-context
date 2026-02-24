# #19 - improve slot allocation [Open]

> Username: jll63  
> Created at: 2025-05-10 23:55:29 UTC  
> Updated at: 2025-05-12 21:15:12 UTC  
> Url: https://github.com/boostorg/openmethod/issues/19  

From Steven Watanabe:  
  
Considering the optimization of not storing leading unused slots,  
filling the lowest available slot is a suboptimal strategy. We can pack  
it tighter by finding the ranges from bases (and derived) and filling in  
holes.  
  
Notice that the algorithm's correctness does not depend on visiting the  
nodes in any particular order. If we're allocating from 0, it's probably  
most effective to visit types bases first instead of depth first. If  
we're trying to allocate contiguous ranges, we can move both up and down  
from types that are already visited. I believe that in the absence of  
virtual inheritance, this is guaranteed to assign slots contiguously  
without leaving any holes.  
  
After assigning a slot, we only need to propagate a single bit across  
the hierarchy. We don't need to merge all the bit vectors.  
  
Finally, we could pull reserved slots up from transitive derived before  
allocating slots instead of pushing them up to transitive bases of  
transitive derived after slot allocation. This would reduce the nesting  
depth of the loops.
