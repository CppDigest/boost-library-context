# #556 Misc fixes [Merged]

> Username: jszuppe  
> Created at: 2016-02-16 20:44:26 UTC  
> Updated at: 2016-02-17 21:41:43 UTC  
> Merged at: 2016-02-17 21:38:41 UTC  
> Closed at: 2016-02-17 21:38:41 UTC  
> Url: https://github.com/boostorg/compute/pull/556  

This PR fixes:  
- missing <iostream> includes in some benchmarks  
- valarray binary operators (they didn't work on POCL)  
- buffer test, now destructor callback test is not run for POCL devices (POCL does not support clSetMemObjectDestructorCallback)  
  
After those commits Boost.Compute works with POCL + OpenCL 1.1 headers (all tests pass for me).

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-02-16 22:09:21 UTC  
> Url: https://github.com/boostorg/compute/pull/556#issuecomment-184895696  

Also now everything builds on OSX  (5 tests fail).

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-02-17 21:38:49 UTC  
> Url: https://github.com/boostorg/compute/pull/556#issuecomment-185418743  

Looks good, thanks!

---
