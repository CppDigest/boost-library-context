# #48 Workaround for try_catch [Merged]

> Username: Flast  
> Created at: 2016-09-07 13:59:18 UTC  
> Updated at: 2016-09-07 13:59:33 UTC  
> Merged at: 2016-09-07 13:59:30 UTC  
> Closed at: 2016-09-07 13:59:30 UTC  
> Url: https://github.com/boostorg/phoenix/pull/48  

In GCC 3.4/4.0, SFINAE-based overloading with same parameters doesn't work properly.  
  
tested on:  
- GCC 6.2.1 - fedora rawhide  
- Clang 3.8.0 - fedora rawhide  
- GCC 3.4.4 - fedora core 3  
- GCC 4.0.0 - fedora core 4

---
