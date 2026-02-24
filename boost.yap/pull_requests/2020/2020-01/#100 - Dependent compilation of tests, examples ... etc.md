# #100 Dependent compilation of tests, examples ... etc  [Merged]

> Username: greole  
> Created at: 2020-01-13 11:38:44 UTC  
> Updated at: 2020-04-08 23:30:27 UTC  
> Merged at: 2020-04-08 23:30:27 UTC  
> Closed at: 2020-04-08 23:30:27 UTC  
> Url: https://github.com/boostorg/yap/pull/100  

Turn of build of optional yap parts (tests, examples, ...) see #99.

---

## Comment 1

> Username: greole  
> Created_at: 2020-01-19 19:02:44 UTC  
> Url: https://github.com/boostorg/yap/pull/100#issuecomment-576035535  

Ok this took me a while to make it work. The tests, example and benchmark can be turned off setting  
```  
 -DYAP_BUILD_TEST=OFF  
 -DYAP_BUILD_EXAMPLE=OFF  
 -DYAP_BUILD_PERF=OFF  
```  
In the process I found several issues that I needed to resolve to make the build process more robust. This includes  
  
- Fixing the compilation order by setting `add_dependencies` so that cmake can figure out the build order.  
- Using a newer version of google benchmark, since version 1.2.0 did not compile with the most recent gcc version  
- Setting the required C++ by requesting compiler  features and letting cmake figure the required compiler flags.  
  
In addition to the last point, CMake can also detect optional compile features using the `write_compiler_detection_header` function which could set` -DBOOST_NO_CONSTEXPR_IF` and  clean up the main CMakeLists.txt a bit more.  But I guess this should be a separate PR.

---

## Comment 2

> Username: tzlaine  
> Created_at: 2020-04-08 23:30:14 UTC  
> Url: https://github.com/boostorg/yap/pull/100#issuecomment-611245858  

Ha!  I just asked for a PR for this in the ticket you opened, and here it is already ready.  LGTM.

---
