# #1 - CMake config files on windows installed into wrong folder [Closed]

> Username: Flamefire  
> Created at: 2019-12-20 09:53:04 UTC  
> Updated at: 2019-12-20 17:16:03 UTC  
> Closed at: 2019-12-20 17:16:03 UTC  
> Url: https://github.com/boostorg/cmake/issues/1  

First continued from https://github.com/boostorg/nowide/pull/27#discussion_r359476938:  
  
> It does. Try it.  
  
After some poking around I noticed that the docu for `find_package` states `<name>*` as part of the prefix dirs searched. It actually means a GLOB. So everything with `Boost-` prefix will be searched... Didn't know that. :confused:   
  
From https://cmake.org/cmake/help/v3.9/command/find_package.html the search paths are:  
  
```  
<prefix>/                                                       (W)  
<prefix>/(cmake|CMake)/                                         (W)  
<prefix>/<name>*/                                               (W)  
<prefix>/<name>*/(cmake|CMake)/                                 (W)  
<prefix>/(lib/<arch>|lib|share)/cmake/<name>*/                  (U)  
<prefix>/(lib/<arch>|lib|share)/<name>*/                        (U)  
<prefix>/(lib/<arch>|lib|share)/<name>*/(cmake|CMake)/          (U)  
<prefix>/<name>*/(lib/<arch>|lib|share)/cmake/<name>*/          (W/U)  
<prefix>/<name>*/(lib/<arch>|lib|share)/<name>*/                (W/U)  
<prefix>/<name>*/(lib/<arch>|lib|share)/<name>*/(cmake|CMake)/  (W/U)  
```  
  
However the CMake config files are placed at `lib/cmake/<name>-<version>/<name>Config.cmake`  
  
Hence on Windows one would need to add 'lib/cmake` to the prefix path so Boost could be found.  
  
It is a shame that `lib/cmake/<name>*/` is not searched on windows. and `/cmake/<name>*/` isn't either.  
  
How to deal with that on Windows? Install into `Boost/lib/cmake/<name>-<version>`? Into `<name>-<version>/cmake`? Assume Boost gets installed into `C:\Boost` and `C:\` is a prefix?

---

## Comment 1

> Username: Flamefire  
> Created at: 2019-12-20 10:12:10 UTC  
> Url: https://github.com/boostorg/cmake/issues/1#issuecomment-567870906  

Related question: https://gitlab.kitware.com/cmake/cmake/issues/20118

---

## Comment 2

> Username: pdimov  
> Created at: 2019-12-20 17:13:09 UTC  
> Url: https://github.com/boostorg/cmake/issues/1#issuecomment-568005219  

> Directories above marked with (W) are intended for installations on Windows where the prefix may point at the top of an application’s installation directory. Those marked with (U) are intended for installations on UNIX platforms where the prefix is shared by multiple packages. This is merely a convention, so all (W) and (U) directories are still searched on all platforms.

---

## Comment 3

> Username: Flamefire  
> Created at: 2019-12-20 17:16:03 UTC  
> Url: https://github.com/boostorg/cmake/issues/1#issuecomment-568006101  

Thx
