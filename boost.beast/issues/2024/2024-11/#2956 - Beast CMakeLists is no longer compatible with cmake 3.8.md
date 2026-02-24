# #2956 - Beast CMakeLists is no longer compatible with cmake 3.8 [Closed]

> Username: anarthal  
> Created at: 2024-11-27 12:51:48 UTC  
> Updated at: 2024-11-27 19:25:09 UTC  
> Closed at: 2024-11-27 19:25:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2956  

The CMakeLists.txt refactor performed in commit 75e45287fbe115448281e41222aeac2023bb7ca1 seem to have broken compatibility with CMake 3.8. See this MySQL build: https://drone.cpp.al/boostorg/mysql/955/3/3  
  
The problem seems to be that `target_sources(... PRIVATE)` is not supported for `INTERFACE` targets in CMake 3.8.  
  
This problem only happens in develop.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-11-27 13:02:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2956#issuecomment-2503825005  

Thanks for reporting this! Did you check if `target_sources` is the only issue?

---

## Comment 2

> Username: anarthal  
> Created at: 2024-11-27 13:07:49 UTC  
> Updated at: 2024-11-27 13:08:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2956#issuecomment-2503836335  

It's the only problem reported by the build. It may be covering other issues. If you want to test locally, you can use the `ghcr.io/anarthal-containers/build-cmake3_8` Docker image (it's an Ubuntu with cmake 3.8)

---

## Comment 3

> Username: ashtum  
> Created at: 2024-11-27 13:27:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2956#issuecomment-2503878819  

Yes, it seems that's the only issue.
