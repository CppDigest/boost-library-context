# #146 [algorithms][distance] qualify point_iterator by geometry namespace [Merged]

> Username: mkaravel  
> Created at: 2014-09-30 14:06:58 UTC  
> Updated at: 2014-10-01 06:38:43 UTC  
> Merged at: 2014-09-30 15:44:36 UTC  
> Closed at: 2014-09-30 15:44:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/146  

Fixes the broken unit tests of the develop branch.  
  
Needed because of the changes in the point_iterator class, namely  
the introduction of the bg::detail::point_iterator namespace.

---
