# #115 Fix a compiling problem under Clang/C2. [Merged]

> Username: gongminmin  
> Created at: 2017-02-05 22:41:48 UTC  
> Updated at: 2017-02-08 05:20:51 UTC  
> Merged at: 2017-02-07 18:25:19 UTC  
> Closed at: 2017-02-07 18:25:19 UTC  
> Url: https://github.com/boostorg/config/pull/115  

In Clang/C2, __builtin_expect is defined. But using __builtin_expect end up with an compiler internal error. Use _MSC_VER to detect it's in Clang/C2, and ignore the __builtin_expect.

---
