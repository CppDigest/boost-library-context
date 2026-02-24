# #133 Feature/counting algorithms (follow-up of PR #128) [Merged]

> Username: mkaravel  
> Created at: 2014-09-05 07:24:11 UTC  
> Updated at: 2014-10-01 06:40:02 UTC  
> Merged at: 2014-09-10 06:41:10 UTC  
> Closed at: 2014-09-10 06:41:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/133  

This is a follow-up of PR #128.  
In this PR:  
- The `num_points`'s `range_count` class does not check, in order to compute the number of points, if the starting and ending point of an open geometry are identical.  
- The unit test for `num_points` also tests cases of open geometries.

---
