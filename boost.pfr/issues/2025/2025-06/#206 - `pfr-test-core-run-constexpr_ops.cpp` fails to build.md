# #206 - `pfr/test/core/run/constexpr_ops.cpp` fails to build [Closed]

> Username: pdimov  
> Created at: 2025-06-08 23:36:03 UTC  
> Updated at: 2025-06-17 06:54:45 UTC  
> Closed at: 2025-06-17 06:51:18 UTC  
> Url: https://github.com/boostorg/pfr/issues/206  

```  
[ 57%] Building CXX object libs/pfr/test/CMakeFiles/pfr_core_constexpr_ops.dir/core/run/constexpr_ops.cpp.o  
/home/runner/work/cmake/boost-root/libs/pfr/test/core/run/constexpr_ops.cpp:16:10: fatal error: boost/config.hpp: No such file or directory  
   16 | #include <boost/config.hpp>  
      |          ^~~~~~~~~~~~~~~~~~  
```  
  
Should probably link to Boost::config.

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-06-10 16:09:16 UTC  
> Url: https://github.com/boostorg/pfr/issues/206#issuecomment-2959873162  

This was probably fixed in develop in https://github.com/boostorg/pfr/commit/b95fd865954a7349df5da7dc71f75c729d0572b2  
  
Will merge the fix to master and deal with the remaining CMake test problems in https://github.com/boostorg/pfr/issues/205

---

## Comment 2

> Username: pdimov  
> Created at: 2025-06-12 16:18:24 UTC  
> Url: https://github.com/boostorg/pfr/issues/206#issuecomment-2967462285  

This doesn't seem fixed; the error now is  
```  
[ 50%] Building CXX object libs/pfr/test/CMakeFiles/pfr_core_constexpr_ops.dir/core/run/constexpr_ops.cpp.o  
/home/runner/work/boost/boost/libs/pfr/test/core/run/constexpr_ops.cpp:16:10: fatal error: boost/core/lightweight_test.hpp: No such file or directory  
   16 | #include <boost/core/lightweight_test.hpp>  
      |          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
(https://github.com/boostorg/boost/actions/runs/15610039897/job/43968542220)

---

## Comment 3

> Username: apolukhin  
> Created at: 2025-06-17 06:54:44 UTC  
> Url: https://github.com/boostorg/pfr/issues/206#issuecomment-2979159937  

@pdimov please test again, I've added a dependency on `Boost::core` for test targets of PFR.  
  
BTW, is there some web page to see all such CMake related errors?
