# #146 Fix NumPy detection bug in numpy-test. [Merged]

> Username: stefanseefeld  
> Created at: 2016-10-28 17:27:25 UTC  
> Updated at: 2021-10-02 22:18:59 UTC  
> Merged at: 2016-10-28 17:32:01 UTC  
> Closed at: 2016-10-28 17:32:01 UTC  
> Url: https://github.com/boostorg/build/pull/146  

This fixes a bug whereby on test runs that don't have NumPy installed the tests would be (attempted to be) executed anyhow (and fail). With this, the NumPy tests will simply be skipped.

---
