# #139 Fix ‘closure’ is not a member of ‘boost::geometry’. [Merged]

> Username: mlang  
> Created at: 2014-09-17 08:34:04 UTC  
> Updated at: 2014-09-17 09:44:30 UTC  
> Merged at: 2014-09-17 08:37:46 UTC  
> Closed at: 2014-09-17 08:37:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/139  

Building examples uncovers following missing include in num_points.hpp:  
  
../../boost/geometry/algorithms/num_points.hpp:68:16: error: ‘closure’ is not a member of ‘boost::geometry’  
  
Adding an include of core/closure.hpp fixes this.

---
