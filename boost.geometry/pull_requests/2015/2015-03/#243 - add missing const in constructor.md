# #243 [iterators][point iterator] add missing const in constructor [Merged]

> Username: mkaravel  
> Created at: 2015-03-06 06:30:21 UTC  
> Updated at: 2015-03-07 07:28:42 UTC  
> Merged at: 2015-03-06 14:20:17 UTC  
> Closed at: 2015-03-06 14:20:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/243  



---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-03-06 06:31:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/243#issuecomment-77513588  

After merging PR #239 the iterator unit test for `point_iterator<>` started to fail.  
The reason is a missing `const` in the point iterator constructor.  
This PR fixes this problem and iterator unit tests pass again.

---
