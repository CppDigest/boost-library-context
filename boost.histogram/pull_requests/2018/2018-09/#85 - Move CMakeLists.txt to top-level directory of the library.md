# #85 Move CMakeLists.txt to top-level directory of the library [Merged]

> Username: mloskot  
> Created at: 2018-09-17 17:10:08 UTC  
> Updated at: 2018-09-18 08:20:22 UTC  
> Merged at: 2018-09-18 07:52:58 UTC  
> Closed at: 2018-09-18 07:52:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/85  

Add missing copyright header to CMakeLists.txt.  
Rename build directory to [tools](https://www.boost.org/development/requirements.html#Organization).  
Update Travis CI and AppVeyor scripts.  
Ignore variants of build directory.  
Ignore CMakeSettings.json used with CMake integration for VS2017.  
  
-----  
  
I've been playing with building/testing the library directly in VS2017 IDE.

---

## Comment 1

> Username: coveralls  
> Created_at: 2018-09-17 17:15:07 UTC  
> Updated_at: 2018-09-17 17:25:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/85#issuecomment-422097322  

[![Coverage Status](https://coveralls.io/builds/19040054/badge)](https://coveralls.io/builds/19040054)  
  
Coverage increased (+0.02%) to 99.523% when pulling **832db3f90ef74550d73d67378320595259f23f71 on mloskot:ml/move-cmake-top-level** into **a1569b59af870fd0c60f71011edb0b5c7e63f84b on HDembinski:develop**.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2018-09-18 07:52:38 UTC  
> Url: https://github.com/boostorg/histogram/pull/85#issuecomment-422292273  

Agreed, this is much nicer. I only put the CMakeLists.txt in "build", because I vaguely remember reading that all build scripts should be in "build", but like we discussed privately, I don't remember where I got that from and other Boost libs (hana, compute) also put the CMakeLists.txt in the root directory.

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-09-18 08:04:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/85#issuecomment-422295938  

After the fact, the [Steven Watenabe's post](https://lists.boost.org/Archives/boost/2018/09/243361.php) made me realise that it could be even better to refactor as:  
  
```  
CMakeListst.txt  
test/CMakeListst.txt  
examples/CMakeListst.txt  
...  
```  
  
Let me know if you like this idea, and I may try to prepare a PR.

---

## Comment 4

> Username: HDembinski  
> Created_at: 2018-09-18 08:09:02 UTC  
> Updated_at: 2018-09-18 08:09:11 UTC  
> Url: https://github.com/boostorg/histogram/pull/85#issuecomment-422297377  

The build script is small enough that it is not too confusing to have everything in one script, so I would not bother changing this for now.

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-09-18 08:20:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/85#issuecomment-422301094  

Sure, makes sense.

---
