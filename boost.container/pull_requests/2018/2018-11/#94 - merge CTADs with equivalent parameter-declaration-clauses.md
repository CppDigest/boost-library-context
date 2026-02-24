# #94  merge CTADs with equivalent parameter-declaration-clauses [Merged]

> Username: tobias-loew  
> Created at: 2018-11-21 14:20:21 UTC  
> Updated at: 2018-11-29 22:39:39 UTC  
> Merged at: 2018-11-29 22:39:14 UTC  
> Closed at: 2018-11-29 22:39:14 UTC  
> Url: https://github.com/boostorg/container/pull/94  

This PR fixes compilation errors with VS 2017 (/std:c++latest /permissive-).  
VS 2017 seems to have a very strict interpretation of  [temp.deduct.guide] (3):  
  
> ... Two deduction guide declarations in the same translation unit for the same class template shall  
not have equivalent parameter-declaration-clauses.  
  
Both gcc and clang seem to ignore [temp.deduct.guide] (3), or only considering it for viable CTAD candidates.  
  
NB: There seems to be no explicit explanation, when two parameter-declaration-clauses are equivalent, especially if templates are involved.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2018-11-29 22:39:39 UTC  
> Url: https://github.com/boostorg/container/pull/94#issuecomment-443020687  

Many thanks for the patch.

---
