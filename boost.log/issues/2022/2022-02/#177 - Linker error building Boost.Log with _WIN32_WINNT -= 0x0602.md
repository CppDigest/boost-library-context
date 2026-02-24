# #177 - Linker error building Boost.Log with _WIN32_WINNT >= 0x0602 [Closed]

> Username: chrullrich  
> Created at: 2022-02-07 12:52:01 UTC  
> Updated at: 2024-05-02 19:33:29 UTC  
> Closed at: 2022-02-07 15:19:40 UTC  
> Url: https://github.com/boostorg/log/issues/177  

Hello,  
  
when using `/DBOOST_USE_WINAPI_VERSION=0x0602 /D_WIN32_WINNT=0x0602 /DWINVER=0x0602` or higher, the build of Boost.Log fails due to linker errors involving two missing symbols, `WaitOnAddress` and `WakeByAddressSingle`. These functions were introduced in Windows 8, i.e. 0x0602, and according to MSDN, their import library is `synchronization.lib`. This library is not mentioned on the linker command line or any build system files.  
  
I added it to build/Jamfile.v2 and the library built successfully, but I'm far from an expert in Boost's build system. I'm also doing everything via vcpkg, which probably introduces even more unpredictability.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-02-07 15:19:40 UTC  
> Updated at: 2022-02-07 15:20:25 UTC  
> Url: https://github.com/boostorg/log/issues/177#issuecomment-1031583468  

The dependency on `synchronization.lib` is [added](https://github.com/boostorg/atomic/blob/8915f67c0e7ab8e2551570e7c5b1a9b98b902153/build/Jamfile.v2#L143) by Boost.Atomic, so Boost.Log inherits it when it is being built. User's code should either use auto-linking, or should add linking with `synchronization.lib` on its own.  
  
If you're not using official Boost build system then you must reproduce its behavior exactly in your own build system.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-07-27 09:30:36 UTC  
> Url: https://github.com/boostorg/log/issues/177#issuecomment-1196490513  

This is causing problems under vcpkg; boost-log fails to build. E.g. https://github.com/microsoft/vcpkg/discussions/22762#discussioncomment-2126877, or when using `clang-win`.  
  
It's not clear why this happens, as Log's usage of synchronization.lib seems to be legitimately indirect via Atomic. The calls are probably getting inlined into the .dll, and the dependency is not inherited for some reason.

---

## Comment 3

> Username: Lastique  
> Created at: 2022-07-27 10:01:12 UTC  
> Url: https://github.com/boostorg/log/issues/177#issuecomment-1196523892  

I don't know how vcpkg works but if it implements its own build system then it needs to work like Boost.Build or Boost's CMake does. vcpkg team needs to investigate this on their end.

---

## Comment 4

> Username: pdimov  
> Created at: 2022-07-27 10:03:00 UTC  
> Url: https://github.com/boostorg/log/issues/177#issuecomment-1196525803  

vcpkg uses b2 to build Boost libraries.

---

## Comment 5

> Username: Lastique  
> Created at: 2022-07-27 10:04:47 UTC  
> Url: https://github.com/boostorg/log/issues/177#issuecomment-1196527650  

Then I'm not sure what the problem is. Boost.Log does build in our CI and Boost releases. I need a repro to go on.

---

## Comment 6

> Username: stephengtuggy  
> Created at: 2022-11-12 22:02:45 UTC  
> Url: https://github.com/boostorg/log/issues/177#issuecomment-1312581304  

> Then I'm not sure what the problem is. Boost.Log does build in our CI and Boost releases. I need a repro to go on.  
  
A repro has been provided.

---

## Comment 7

> Username: Lastique  
> Created at: 2023-06-13 11:15:22 UTC  
> Url: https://github.com/boostorg/log/issues/177#issuecomment-1589092105  

Just in case if anyone is following this or finds this issue, if you have this issue and see "has synchronization.lib : no" at the beginning of your Boost build log, try running your build from the Visual Studio command line prompt.  
  
Normally, Boost.Build should be able to detect Visual Studio paths and set up environment to run the compiler/linker, but apparently this may fail sometimes (possibly, when Visual Studio is installed in a non-default location). If you know a way for Boost.Build to improve detection for header/library paths, please report it to [Boost.Build](https://github.com/bfgroup/b2/).

---

## Comment 8

> Username: stephengtuggy  
> Created at: 2024-05-02 19:33:29 UTC  
> Url: https://github.com/boostorg/log/issues/177#issuecomment-2091401751  

> Just in case if anyone is following this or finds this issue, if you have this issue and see "has synchronization.lib : no" at the beginning of your Boost build log, try running your build from the Visual Studio command line prompt.  
>   
> Normally, Boost.Build should be able to detect Visual Studio paths and set up environment to run the compiler/linker, but apparently this may fail sometimes (possibly, when Visual Studio is installed in a non-default location). If you know a way for Boost.Build to improve detection for header/library paths, please report it to [Boost.Build](https://github.com/bfgroup/b2/).  
  
Good to know, thanks.
