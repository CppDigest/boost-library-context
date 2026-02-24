# #13 use std::mutex if available [Merged]

> Username: olk  
> Created at: 2016-02-27 19:44:07 UTC  
> Updated at: 2016-03-04 17:10:40 UTC  
> Merged at: 2016-02-27 20:20:59 UTC  
> Closed at: 2016-02-27 20:20:59 UTC  
> Url: https://github.com/boostorg/pool/pull/13  

if compiled for C++11 etc. use std::mutex. drawback of boost::mutex is that boost.thread depends on boost.system (requires linking against boost.system). boost.system is already part of C++11.  
Additionally boost::mutex is much more slower than std::mutex!

---

## Comment 1

> Username: mclow  
> Created_at: 2016-02-27 20:21:26 UTC  
> Url: https://github.com/boostorg/pool/pull/13#issuecomment-189717445  

This looks fine to me; please watch the test bots and ping me in a week (about) to merge to master.

---

## Comment 2

> Username: olk  
> Created_at: 2016-03-04 17:10:40 UTC  
> Url: https://github.com/boostorg/pool/pull/13#issuecomment-192362046  

Could you merge the path into master?! Test test have some failures, but I believe they are not caused to this patch.

---
