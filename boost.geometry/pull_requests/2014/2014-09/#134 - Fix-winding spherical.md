# #134 Fix/winding spherical [Merged]

> Username: awulkiew  
> Created at: 2014-09-09 05:39:27 UTC  
> Updated at: 2014-09-10 15:38:21 UTC  
> Merged at: 2014-09-10 08:55:57 UTC  
> Closed at: 2014-09-10 08:55:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/134  

The first commit fixes the ticket: https://svn.boost.org/trac/boost/ticket/9354  
The second commit adds the test for the ticket and a few other cases which are currently failing.  
  
For "POINT(0 10.001)" and "POLYGON((-10 10, 10 10, 10 -10, -10 -10, -10 10))" the Point within() should return true because spherical side (formula) strategy for a Segment(-10 10, 10 10) and a Point(0 10.001) returns -1 which means RIGHT.  
  
And it seems that the winding strategy is not the only one that fails in those cases. Also franklin and crossings_multiply returns wrong? results in the above case.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-09-10 08:55:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/134#issuecomment-55087974  

franklin and crossings_multiply are for Cartesian. Do they then also have this problem?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-09-10 15:38:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/134#issuecomment-55134369  

No, since they're only for cartesian it's obvious that they shouldn't work with spherical. So they shouldn't be tested, I'll remove the test.  
  
Still the winding strategy isn't fully fixed. The mentioned special case should be taken into account.

---
