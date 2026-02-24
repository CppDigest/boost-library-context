# #343 Do not use C++98 binders when _GLIBCXX_USE_DEPRECATED=0 [Merged]

> Username: viccie30  
> Created at: 2020-09-14 12:58:08 UTC  
> Updated at: 2020-09-14 15:51:40 UTC  
> Merged at: 2020-09-14 15:51:40 UTC  
> Closed at: 2020-09-14 15:51:40 UTC  
> Url: https://github.com/boostorg/config/pull/343  

They are not available from libstdc++ in that case. This has been the case since 2007 ([see GCC commit, in libstdc++-v3/include/bits/stl_function.h](https://gcc.gnu.org/git/?p=gcc.git;a=commit;h=40abbf1f1737b16b1ae6a8d8094e825f6a3b41f2)). I've only added the define to the _GLIBCXX_USE_DEPRECATED branch, because that is the only define checked in the original GCC commit.  
  
With this change Boost builds correctly with _GLIBCXX_USE_DEPRECATED=0.

---
