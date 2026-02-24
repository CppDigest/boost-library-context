# #1 do not set alloc number if 1 [Closed]

> Username: forderud  
> Created at: 2013-12-19 22:45:14 UTC  
> Updated at: 2015-08-23 20:52:18 UTC  
> Closed at: 2015-08-23 20:52:18 UTC  
> Url: https://github.com/boostorg/test/pull/1  

Please (cherry-pick) merge this change to the "master" branch, so that it becomes part of the next boost release.  
  
As of today, boost test always overrides the allocation number to break on. This is not a good idea if the user has already set a value from the debugger.  
  
REF: http://lists.boost.org/Archives/boost/2013/09/206204.php

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2015-08-23 20:52:18 UTC  
> Url: https://github.com/boostorg/test/pull/1#issuecomment-133932156  

Coming back to this: it is already in boost 1.59.

---
