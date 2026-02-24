# #332 - `aligned_alloc` not available in C++11/14 on XCode 12 [Closed]

> Username: Flamefire  
> Created at: 2026-02-19 16:39:42 UTC  
> Updated at: 2026-02-21 11:27:44 UTC  
> Closed at: 2026-02-20 22:19:39 UTC  
> Url: https://github.com/boostorg/container/issues/332  

Compilation fails on OSX with XCode 12 since https://github.com/boostorg/container/commit/c99257c1dc5d9f2a236be9e545b6262ae785aff8:  
  
```  
In file included from /private/var/folders/pd/mqpvptxx1lz4jfzhcs_0fd8w0000gs/T/drone-3KDL8vc3j1f1qqhJ/drone/boost-root/libs/container/src/global_resource.cpp:17:  
In file included from ./boost/container/detail/operator_new_helpers.hpp:26:  
./boost/container/detail/aligned_allocation.hpp:87:26: error: no member named 'aligned_alloc' in the global namespace; did you mean 'aligned_allocate'?  
   return rounded_size ? ::aligned_alloc(al, rounded_size) : 0;  
                         ^~~~~~~~~~~~~~~  
                         aligned_allocate  
./boost/container/detail/aligned_allocation.hpp:77:14: note: 'aligned_allocate' declared here  
inline void* aligned_allocate(std::size_t al, std::size_t sz)  
             ^  
1 error generated.  
```  
  
This is for C++11 & C++14  
  
For C++17 and 2a it works. It also works for XCode 12.5.1  
  
See https://drone.cpp.al/boostorg/locale/354/15/2

---

## Comment 1

> Username: Flamefire  
> Created at: 2026-02-20 17:19:49 UTC  
> Url: https://github.com/boostorg/container/issues/332#issuecomment-3936156540  

FWIW: I made some experiments to find an indicator for a working environment: https://drone.cpp.al/boostorg/boost-ci/979/55/2  
  
Those experiments show that it was fixed in 12.2 But I haven't found any difference in available macros.  
  
So I'd suggest to use `__MAC_OS_X_VERSION_MIN_REQUIRED > 101500 || C++17`.  
In some sources I have found that aligned_alloc is only in OSX 11.x, but `std::aligned_alloc`  is in C++17 and the greater-than check excludes the 12.x SDKs.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2026-02-20 22:05:24 UTC  
> Url: https://github.com/boostorg/container/issues/332#issuecomment-3937380536  

Many thanks for the report. I think I will play safe and check for C++>=17 to detect aligned_alloc. If not defined, posix_memalign is available in all apple platforms that can be targeted today.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2026-02-20 22:20:17 UTC  
> Url: https://github.com/boostorg/container/issues/332#issuecomment-3937427792  

After regressions are done, we'll see if the commit actually fixes something...

---

## Comment 4

> Username: Flamefire  
> Created at: 2026-02-21 11:27:44 UTC  
> Url: https://github.com/boostorg/container/issues/332#issuecomment-3938637807  

Works for me: https://drone.cpp.al/boostorg/locale/355/15/2  
  
Can you merge it to master too?
