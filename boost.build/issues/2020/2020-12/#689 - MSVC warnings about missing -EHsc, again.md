# #689 - MSVC warnings about missing /EHsc, again [Closed]

> Username: Lastique  
> Created at: 2020-12-23 07:20:47 UTC  
> Updated at: 2020-12-25 02:21:56 UTC  
> Closed at: 2020-12-25 02:21:56 UTC  
> Url: https://github.com/boostorg/build/issues/689  

https://github.com/boostorg/build/issues/654 has reappeared.  
  
MSVC-14.0 [produces](https://ci.appveyor.com/project/Lastique/filesystem/build/job/8re6gv0hleqfu1kd) warnings like the following when building bjam:  
  
```  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\xlocale(341): warning C4530: C++ exception handler used, but unwind semantics are not enabled. Specify /EHsc (compiling source file modules/set.cpp)  
```

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-12-23 13:36:35 UTC  
> Url: https://github.com/boostorg/build/issues/689#issuecomment-750303626  

Are you sure? I'm not seeing it on filesystem (https://ci.appveyor.com/project/Lastique/filesystem/builds/36972166/job/q436vigonrct5exi) nor on b2's builds (https://ci.appveyor.com/project/grafikrobot/build/builds/36970516/job/g0xhuom2a3x5p0x7).

---

## Comment 2

> Username: Lastique  
> Created at: 2020-12-23 13:55:30 UTC  
> Url: https://github.com/boostorg/build/issues/689#issuecomment-750310644  

The recent Boost.Filesystem build (https://ci.appveyor.com/project/Lastique/filesystem/builds/36951881/job/8re6gv0hleqfu1kd) showed these warnings.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2020-12-25 02:21:56 UTC  
> Url: https://github.com/boostorg/build/issues/689#issuecomment-751149631  

The reason, AFAICT, is as mentioned in https://github.com/boostorg/build/commit/2a7844ee3587fc6d55e09383bd93ccfd2e23a9b2 that you are using the B2 master branch which doesn't have the fix yet. It will soon'ish when I do the next B2 release.
