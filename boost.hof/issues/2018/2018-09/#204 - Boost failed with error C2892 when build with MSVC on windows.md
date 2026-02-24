# #204 - Boost failed with error C2892 when build with MSVC on windows [Open]

> Username: QuellaZhang  
> Created at: 2018-09-11 03:10:01 UTC  
> Updated at: 2021-01-10 13:10:35 UTC  
> Url: https://github.com/boostorg/hof/issues/204  

Hi All,  
  
We tried to build and run hof test for Boost with VS2017 Update 7 on Windows. It failed to build due to the error C2892. Could you please help take a look at this? Thank you!  
  
**Reproduce steps:**  
  
1. git clone -c core.autocrlf=true --recursive https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2015 x86 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
5. .\b2 variant=release --build-dir=..\out\x86rel address-model=32  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\hof\test  
  
**Failures:**  
libs\hof\test\lift.cpp(42): error C2892: a template cannot be a member of a local class  
libs\hof\test\lift.cpp(45): error C2892: a template cannot be a member of a local class  
libs\hof\test\lift.cpp(46): error C2892: a template cannot be a member of a local class  
libs\hof\test\lift.cpp(49): error C2892: a template cannot be a member of a local class  
libs\hof\test\lift.cpp(50): error C2892: a template cannot be a member of a local class

---

## Comment 1

> Username: pfultz2  
> Created at: 2018-09-11 15:51:59 UTC  
> Url: https://github.com/boostorg/hof/issues/204#issuecomment-420323125  

So it looks like the workaround for MSVC no longer works. You can try disabling the workaround [here](https://github.com/boostorg/hof/blob/develop/include/boost/hof/lift.hpp#L89) by changing `#if defined (_MSC_VER)` to `#if 0`. If that works then the workaround is no longer needed for VS2017 Update 7, and we probably should update the `#if` to check the MSVC version.  
  
I dont have access to windows right now to try this locally.

---

## Comment 2

> Username: QuellaZhang  
> Created at: 2018-09-12 10:26:16 UTC  
> Updated at: 2018-09-18 05:44:05 UTC  
> Url: https://github.com/boostorg/hof/issues/204#issuecomment-420596135  

Thanks @pfultz2. I tried what you said and it works well on default mode. Maybe you can consider updating this.

---

## Comment 3

> Username: pfultz2  
> Created at: 2018-09-20 16:49:12 UTC  
> Url: https://github.com/boostorg/hof/issues/204#issuecomment-423254403  

What is `_MSC_VER` set to for VS2017 Update 7?

---

## Comment 4

> Username: erikzenker  
> Created at: 2021-01-10 13:05:27 UTC  
> Updated at: 2021-01-10 13:05:47 UTC  
> Url: https://github.com/boostorg/hof/issues/204#issuecomment-757473255  

You can use the compiler explorer to reproduce the error: https://godbolt.org/z/fEaqWx

---

## Comment 5

> Username: erikzenker  
> Created at: 2021-01-10 13:10:34 UTC  
> Url: https://github.com/boostorg/hof/issues/204#issuecomment-757473885  

@pfultz2 I can confirm that the workaround for MSVC is not necessary anymore (see here https://godbolt.org/z/W8oEPG). So we should check the version of the MSVC.
