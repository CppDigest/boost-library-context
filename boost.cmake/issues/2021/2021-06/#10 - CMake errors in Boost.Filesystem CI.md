# #10 - CMake errors in Boost.Filesystem CI [Closed]

> Username: Lastique  
> Created at: 2021-06-10 12:44:40 UTC  
> Updated at: 2021-06-11 15:16:08 UTC  
> Closed at: 2021-06-11 14:43:20 UTC  
> Url: https://github.com/boostorg/cmake/issues/10  

It seems, something in Boost.CMake was changed recently that caused CI failures in Boost.Filesystem:  
  
```  
CMake Error: install(EXPORT "boost_move-targets" ...) includes target "boost_move" which requires target "boost_assert" that is not in the export set.  
CMake Error: install(EXPORT "boost_move-targets" ...) includes target "boost_move" which requires target "boost_core" that is not in the export set.  
```  
  
https://travis-ci.org/github/boostorg/filesystem/jobs/774080073  
  
The test `libs/filesystem/test/test_cmake` uses Boost.CMake to define CMake targets for all libraries, which is where, it looks like, CMake fails. At least `install()` is not used in Boost.Filesystem.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-06-10 13:29:09 UTC  
> Url: https://github.com/boostorg/cmake/issues/10#issuecomment-858624145  

It requires this commit https://github.com/boostorg/move/commit/283d36c0b580f4a886239e89a19910f06d0dbff4 and should fix itself when @igaztanaga merges Move to master. There were too many non-CMake-related changes in develop so I didn't want to do it, and it didn't seem necessary to cherry-pick.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-06-11 13:41:05 UTC  
> Url: https://github.com/boostorg/cmake/issues/10#issuecomment-859590347  

Although it looks like I'll have to cherry-pick it, because too many CI things are now failing for me too. :-)

---

## Comment 3

> Username: pdimov  
> Created at: 2021-06-11 14:43:20 UTC  
> Url: https://github.com/boostorg/cmake/issues/10#issuecomment-859631520  

Done.

---

## Comment 4

> Username: Lastique  
> Created at: 2021-06-11 15:16:08 UTC  
> Url: https://github.com/boostorg/cmake/issues/10#issuecomment-859653335  

Thanks!
