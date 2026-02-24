# #3 Fix tests for MSVC [Merged]

> Username: MarcelRaad  
> Created at: 2014-07-03 17:07:41 UTC  
> Updated at: 2014-07-09 17:42:20 UTC  
> Merged at: 2014-07-09 17:22:16 UTC  
> Closed at: 2014-07-09 17:22:16 UTC  
> Url: https://github.com/boostorg/pool/pull/3  

warnings-as-errors caused the simple_seg_storage and threading tests to fail on all Visual C++ compilers. This define suppresses the warnings about unchecked iterators, which I don't think are solvable in a portable way.

---
