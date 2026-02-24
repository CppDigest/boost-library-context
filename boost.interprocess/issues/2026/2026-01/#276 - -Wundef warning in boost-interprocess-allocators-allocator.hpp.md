# #276 - -Wundef warning in boost/interprocess/allocators/allocator.hpp [Closed]

> Username: pdimov  
> Created at: 2026-01-04 15:21:10 UTC  
> Updated at: 2026-01-04 19:40:19 UTC  
> Closed at: 2026-01-04 19:23:17 UTC  
> Url: https://github.com/boostorg/interprocess/issues/276  

```  
In file included from ./boost/interprocess/allocators/allocator.hpp:23,  
                 from libs/unordered/test/debuggability/visualization_tests.cpp:19:  
./boost/interprocess/detail/workaround.hpp:44:82: error: "__VXWORKS__" is not defined, evaluates to 0 [-Werror=undef]  
   44 |    #if !defined(__QNXNTO__) && !defined(__ANDROID__) && !defined(__HAIKU__) && !(__VXWORKS__) && !(__EMSCRIPTEN__)  
      |                                                                                  ^~~~~~~~~~~  
```  
These look like typos to me, because of the parentheses and because they are not consistent with the rest. They should probably be `!defined(__VXWORKS__)` and `!defined((__EMSCRIPTEN__)`.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2026-01-04 19:40:19 UTC  
> Url: https://github.com/boostorg/interprocess/issues/276#issuecomment-3708365364  

VxWorks and Emscripten portions came from proposed pull requests:  
  
https://github.com/boostorg/interprocess/pull/46  
  
https://github.com/boostorg/interprocess/pull/188  
  
Thanks for the report.
