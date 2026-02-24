# #69 - CMAKE_CXX_STANDARD in cmake/toolchains/common.cmake is ignored [Closed]

> Username: mloskot  
> Created at: 2020-03-29 19:38:20 UTC  
> Updated at: 2020-03-30 14:48:20 UTC  
> Closed at: 2020-03-30 13:51:46 UTC  
> Url: https://github.com/boostorg/json/issues/69  

```  
cmake -B _build.gcc -DBOOST_JSON_STANDALONE=ON -DCMAKE_TOOLCHAIN_FILE=cmake/toolchains/gcc.cmake  
cmake -B _build.gcc -DCMAKE_CXX_STANDARD=14 -DBOOST_JSON_STANDALONE=ON -DCMAKE_TOOLCHAIN_FILE=cmake/toolchains/gcc.cmake  
```  
  
Something seems fishy in how  `set(CMAKE_CXX_STANDARD 11 CACHE STRING "")` is handled, the `src/src.cpp` is compiled with `-std=c++17` but the `test/limits.cpp` with `-std=c++11` or with `-std=c++14`  (the second command).  
  
The fish has been fished:  
  
https://github.com/CPPAlliance/json/blob/d92902c7cf259f4103a0b798050f50ae5b8aa289/CMakeLists.txt#L63  
  
Then, the `common.cmake` needs something like this perhaps  
  
```  
if(BOOST_JSON_STANDALONE)  
    set(CMAKE_CXX_STANDARD 17 CACHE STRING "")  
else()  
    set(CMAKE_CXX_STANDARD 11 CACHE STRING "")  
endif()  
```

---

## Comment 1

> Username: qis  
> Created at: 2020-03-29 19:51:28 UTC  
> Url: https://github.com/boostorg/json/issues/69#issuecomment-605690297  

Yes, this is quite unexpected…  
  
https://cmake.org/cmake/help/latest/manual/cmake-compile-features.7.html#requiring-language-standards  
  
Here it clearly states that `target_compile_features(mylib PUBLIC cxx_std_11)` "[…] applies to sources within `mylib` as well as any dependents (that may include headers from `mylib`)."  
  
Could it be a CMake version and/or policy issue? Setting `CMAKE_CXX_STANDARD` in directory scope for a build that includes tests/examples might be a good idea.

---

## Comment 2

> Username: mloskot  
> Created at: 2020-03-29 20:04:17 UTC  
> Url: https://github.com/boostorg/json/issues/69#issuecomment-605692153  

> Could it be a CMake version and/or policy issue?  
  
No idea. I'm using CMake 3.17.0

---

## Comment 3

> Username: Mike-Devel  
> Created at: 2020-03-29 20:59:34 UTC  
> Updated at: 2020-03-30 09:23:54 UTC  
> Url: https://github.com/boostorg/json/issues/69#issuecomment-605700575  

Are you guys aware that there are two test binaries generated? One with all the test source files and linked against `boost_json`, the other uses only limits.cpp and main.cpp and does not link against `boost_json`. So it isn't a wonder that cxx_std_17 doesn't get propagated in the second case.

---

## Comment 4

> Username: qis  
> Created at: 2020-03-29 21:13:09 UTC  
> Url: https://github.com/boostorg/json/issues/69#issuecomment-605702447  

@Mike-Devel I was not aware of that. Thank you!

---

## Comment 5

> Username: mloskot  
> Created at: 2020-03-29 22:03:04 UTC  
> Url: https://github.com/boostorg/json/issues/69#issuecomment-605708737  

@Mike-Devel Me neither and I only looked into the `common.cmake`. That `cxx_std_17` was pointed out by @vinniefalco

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-03-29 22:38:22 UTC  
> Url: https://github.com/boostorg/json/issues/69#issuecomment-605713003  

Yep sorry about that. The "limits" test compiles limits.cpp, is something I added to CMakeLists.txt that was already built in the Jamfile. This test changes configuration macros which affect the compiled library, thus limits is compiled in the "header-only" configuration:  
https://github.com/CPPAlliance/json/blob/7784fdd45bbc2abab4e179b0ac70c675ace5a582/test/CMakeLists.txt#L24

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-03-30 02:18:29 UTC  
> Url: https://github.com/boostorg/json/issues/69#issuecomment-605746860  

I have refactored the CML with Peter's help. We've added an item to the Travis matrix for standalone building with cmake, to ensure things stay working. Please try the new branch and let me know if it works for you.

---

## Comment 8

> Username: mloskot  
> Created at: 2020-03-30 09:11:07 UTC  
> Url: https://github.com/boostorg/json/issues/69#issuecomment-605879062  

Yes, it works. The `FATAL_ERROR` works too. Thanks!

---

## Comment 9

> Username: mloskot  
> Created at: 2020-03-30 14:12:01 UTC  
> Url: https://github.com/boostorg/json/issues/69#issuecomment-606023762  

@vinniefalco   
> We've added an item to the Travis matrix for standalone building with cmake, to ensure things stay working.  
  
The removed GitHub Actions covered several more of the supported modes that this. May be worth to port.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-03-30 14:48:20 UTC  
> Url: https://github.com/boostorg/json/issues/69#issuecomment-606045736  

> The removed GitHub Actions covered several more of the supported modes that this. May be worth to port.  
  
Yep, as soon as someone holds my hand I'll crank out the other configurations.
