# #351 Fix/cart intersect for different RobustPoint types [Merged]

> Username: awulkiew  
> Created at: 2016-06-06 19:34:30 UTC  
> Updated at: 2016-06-21 15:58:30 UTC  
> Merged at: 2016-06-13 08:25:47 UTC  
> Closed at: 2016-06-13 08:25:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/351  

This fixes the ticket: https://svn.boost.org/trac/boost/ticket/12189  
and issue reported in this thread: http://lists.boost.org/geometry/2016/05/3550.php  
  
`relate_cartesian_segments::apply()` expects single `RobustPoint` type. This causes compilation error in e.g. relational operations when geometries using different point types are passed.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2016-06-08 09:55:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/351#issuecomment-224543456  

Looks all good to me, I'm OK with this, thanks!

---
