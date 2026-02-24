# #110 Add emscripten toolset to Boost.Build and emscripten.bat to build boost filesystem for Web [Closed]

> Username: bluesfdw  
> Created at: 2016-01-07 03:56:53 UTC  
> Updated at: 2016-01-07 04:51:31 UTC  
> Closed at: 2016-01-07 04:07:07 UTC  
> Url: https://github.com/boostorg/boost/pull/110  

Add emscripten toolset to Boost.Build and emscripten.bat to build boost filesystem for Web. please make sure you have activated emscripten SDK before building boost for Web.  
- Open Windows console  
- Activate Emscripten. In AutoCAD build environment, you could invoke "cmakebuild/script/web/amake.bat activate" or in standaline emscripten, invoke "emsdk activate" to activate emscripten.  
- Call Emscripten.bat

---

## Comment 1

> Username: mclow  
> Created_at: 2016-01-07 04:04:07 UTC  
> Url: https://github.com/boostorg/boost/pull/110#issuecomment-169542454  

We don't take pull requests into `master`; everything goes into `develop` first, and then after the test have passed, gets merged into `master`.

---

## Comment 2

> Username: bluesfdw  
> Created_at: 2016-01-07 04:07:07 UTC  
> Url: https://github.com/boostorg/boost/pull/110#issuecomment-169542776  

Sorry, I would like to submit this to one fork boost repository instead of office one. I will close this pull request

---

## Comment 3

> Username: mclow  
> Created_at: 2016-01-07 04:51:31 UTC  
> Url: https://github.com/boostorg/boost/pull/110#issuecomment-169550083  

Are you planning on running a test bot? That would be a great thing for emscripten support.

---
