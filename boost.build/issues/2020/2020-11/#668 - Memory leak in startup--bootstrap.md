# #668 - Memory leak in startup::bootstrap [Closed]

> Username: mst7555  
> Created at: 2020-11-06 15:35:25 UTC  
> Updated at: 2020-12-14 21:05:29 UTC  
> Closed at: 2020-12-14 21:05:29 UTC  
> Url: https://github.com/boostorg/build/issues/668  

Building boost with ASAN enabled in CCFLAGS/CXXFLAGS env variables makes b2 with ASAN ;-)  
The b2 in Boost 1.74 started reporting memory leak in a very unspecified place (strdup related).  
I've investigated and found that executable_path allocates a string (strdup) which is never released in startup::bootstrap code.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-12-14 21:05:29 UTC  
> Url: https://github.com/boostorg/build/issues/668#issuecomment-744710019  

Fixed in https://github.com/boostorg/build/commit/550f99c84634539243e70931fb41a59b38743992
