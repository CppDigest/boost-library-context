# #340 Fix PGI toolset to recognize the cxxstd feature [Merged]

> Username: dkolsen-pgi  
> Created at: 2018-09-10 21:23:29 UTC  
> Updated at: 2021-10-02 21:19:37 UTC  
> Merged at: 2018-10-02 02:58:46 UTC  
> Closed at: 2018-10-02 02:58:46 UTC  
> Url: https://github.com/boostorg/build/pull/340  

Add the appropriate language level option to pgc++ when b2 is invoked  
with toolset=pgi and cxxstd set to something.

---
