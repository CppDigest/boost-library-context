# #5 [point_iterator] fix errors and warnings for g++ and clang++ with C++11 enabled [Merged]

> Username: mkaravel  
> Created at: 2014-04-05 22:48:01 UTC  
> Updated at: 2014-04-07 09:17:26 UTC  
> Merged at: 2014-04-07 09:17:26 UTC  
> Closed at: 2014-04-07 09:17:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/5  

Fixed some errors and warnings that have appeared in regression tests for flatten and concatenate iterators for the clang++ and g++ compilers when C++11 mode is enabled (-std=c++11).  
  
Also changed a bit the copy constructor for the flatten iterator (the enable_if part).

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-04-07 08:37:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/5#issuecomment-39706969  

replaced enable_if mechanism by static asserts

---
