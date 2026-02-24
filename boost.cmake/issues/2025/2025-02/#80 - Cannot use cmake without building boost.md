# #80 - Cannot use cmake without building boost [Closed]

> Username: Chocobo1  
> Created at: 2025-02-22 12:42:44 UTC  
> Updated at: 2025-02-22 16:53:47 UTC  
> Closed at: 2025-02-22 16:53:46 UTC  
> Url: https://github.com/boostorg/cmake/issues/80  

First, I understand that my use case could be a bit unique and I'm not sure whether this is a bug, it could be more like a feature request.  
  
* Previously we setup boost like this:  
  1. Download https://archives.boost.io/release/1.87.0/source/boost_1_87_0.zip and extract it to `c:\boost`  
  2. In our own project, run: `cmake -DBOOST_ROOT=c:\boost ...`  
    This will utilize [`FindBoost.cmake`](https://github.com/Kitware/CMake/blob/master/Modules/FindBoost.cmake) and find the boost installation at `c:\boost`.  
    We use boost header-only in our project and that is all it needs.  
  
* Now cmake had deprecated `FindBoost.cmake` and the replacement is `BoostConfig.cmake`. Our setup steps becomes:  
  1. Download https://archives.boost.io/release/1.87.0/source/boost_1_87_0.zip and extract it  
  2. Run `bootstrap.bat`  
  3. Run `b2 install --with-headers --prefix="C:\boost"`  
    AFAIK this will copy all the headers and generate `BoostConfig.cmake`.  
  4. In our own project, run: `cmake -DBOOST_ROOT=c:\boost ...`  
  
My problem is that, for CI purpose, step 2 & step 3 took too much time especially step 3. It will copy all headers to the final directory which took a lot of time. I mean why copy all of them when they are already extracted from archive and on disk? Isn't there a better way to avoid all the duplication (both space and time)?  
  
Anyway, I figured that it would be great if we can omit the copy in step 3 and somehow still be able to generate `BoostConfig.cmake`. But I haven't able to achieve it. Thus I would like to make a request to generate `BoostConfig.cmake` independently without copy the files.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-02-22 14:03:35 UTC  
> Url: https://github.com/boostorg/cmake/issues/80#issuecomment-2676226669  

Have you tried `b2 stage --with-headers`?

---

## Comment 2

> Username: Chocobo1  
> Created at: 2025-02-22 16:53:46 UTC  
> Url: https://github.com/boostorg/cmake/issues/80#issuecomment-2676302537  

>Have you tried b2 stage --with-headers?  
  
I just tried and yes this is exactly what I'm looking!  
Thank you!
