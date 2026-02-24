# #511 Fix for search and search_n algorithm [Merged]

> Username: jszuppe  
> Created at: 2015-09-12 17:50:10 UTC  
> Updated at: 2016-05-01 11:19:27 UTC  
> Merged at: 2015-09-15 17:41:10 UTC  
> Closed at: 2015-09-15 17:41:10 UTC  
> Url: https://github.com/boostorg/compute/pull/511  

This addresses https://github.com/boostorg/compute/issues/245.  
  
I found the bug:  
  
If you analyse [search_n](https://github.com/boostorg/compute/blob/master/include/boost/compute/algorithm/search_n.hpp) (check lines 52, 53, 79), you'll see that it puts 1 or 0 in the first `iterator_range_size(t_first, t_last) + 1 - n` positions (that happens in kernel), but later first occurrence of value 1 is searched in range `[matching_indices.begin(); matching_indices.end())` (line 121). The same bug is in `search`.  
  
I fixed it by reducing the size of `matching_indices` to `iterator_range_size(t_first, t_last) + 1 - n`  and adding if clause for cases when pattern does not occur in input vector.  
  
I also added tests to make sure that the pattern gets found even when it occurs at the end of the input vector.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-09-15 17:41:13 UTC  
> Url: https://github.com/boostorg/compute/pull/511#issuecomment-140478239  

Awesome! Merged!

---
