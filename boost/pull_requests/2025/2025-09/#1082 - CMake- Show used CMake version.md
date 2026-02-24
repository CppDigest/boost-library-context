# #1082 CMake: Show used CMake version [Closed]

> Username: Flamefire  
> Created at: 2025-09-06 11:17:12 UTC  
> Updated at: 2025-09-11 13:45:58 UTC  
> Closed at: 2025-09-11 13:38:45 UTC  
> Url: https://github.com/boostorg/boost/pull/1082  

In super-builds show the CMake version which is especially useful on CI to verify the used CMake and/or compare different jobs on failure.  
  
Limited to the super build to not be too verbose when Boost is used via `add_subdirectory` or similar.

---

## Comment 1

> Username: nigels-com  
> Created_at: 2025-09-06 13:19:40 UTC  
> Url: https://github.com/boostorg/boost/pull/1082#issuecomment-3262145190  

Good idea!  Would be helpful.

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-09-11 12:56:56 UTC  
> Url: https://github.com/boostorg/boost/pull/1082#issuecomment-3280518452  

1. pull requests should be against develop  
2. logic such as this goes into BoostRoot.cmake, to keep CMakeLists.txt minimal

---

## Comment 3

> Username: Flamefire  
> Created_at: 2025-09-11 13:38:45 UTC  
> Url: https://github.com/boostorg/boost/pull/1082#issuecomment-3280757431  

> pull requests should be against develop  
  
Can/Shall we change the default branch to develop? When opening a pull request here it defaulted to the master branch instead and I forgot to verify

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-09-11 13:45:01 UTC  
> Url: https://github.com/boostorg/boost/pull/1082#issuecomment-3280798137  

The default branch here is kept on master so that people doing `get clone --recurse-submodules` get that instead of develop which is going to be much less stable.

---

## Comment 5

> Username: Flamefire  
> Created_at: 2025-09-11 13:45:58 UTC  
> Url: https://github.com/boostorg/boost/pull/1082#issuecomment-3280803883  

> logic such as this goes into BoostRoot.cmake, to keep CMakeLists.txt minimal  
  
--> https://github.com/boostorg/cmake/pull/88

---
