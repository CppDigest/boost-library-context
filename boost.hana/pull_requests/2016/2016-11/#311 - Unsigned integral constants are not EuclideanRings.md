# #311 Unsigned integral constants are not EuclideanRings [Open]

> Username: ldionne  
> Created at: 2016-11-17 08:55:36 UTC  
> Updated at: 2021-03-31 10:55:07 UTC  
> Url: https://github.com/boostorg/hana/pull/311  

This PR is an attempt to fix #240. Unfortunately, this is a potentially breaking change, so it will have to wait until I'm ready to bump the major version. In addition, it introduces additional problems, like the fact that we can't write `size_c<n> % size_c<k>` (because they're not `EuclideanRing`s), but we also can't write `int_c<n> % size_c<k>`, since they don't have the same signedness. This is very annoying, and all of this pushes me in the direction where I just want a dumb `IntegralConstant` concept with only syntactical requirements, no semantics.  
  
When I'm ready to bump the major version (i.e. when we have a sufficiently large amount of breaking changes to make at once), I'll either close this PR (if I remove numeric concepts) or fix this PR and merge it.

---
