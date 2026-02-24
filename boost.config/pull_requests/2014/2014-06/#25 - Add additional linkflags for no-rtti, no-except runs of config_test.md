# #25 Add additional linkflags for no-rtti, no-except runs of config_test. [Merged]

> Username: pdimov  
> Created at: 2014-06-08 22:26:57 UTC  
> Updated at: 2014-06-25 19:10:14 UTC  
> Merged at: 2014-06-10 18:18:28 UTC  
> Closed at: 2014-06-10 18:18:28 UTC  
> Url: https://github.com/boostorg/config/pull/25  

This copies the additional `<linkflags>` to the no-RTTI and no exceptions runs of `config_test`, as the test results show failures indicating a missing `-lpthread` or `-lrt`.

---
