# #2954 Refactor CMakeLists [Merged]

> Username: ashtum  
> Created at: 2024-11-20 20:43:07 UTC  
> Updated at: 2024-11-25 08:11:41 UTC  
> Merged at: 2024-11-25 08:11:40 UTC  
> Closed at: 2024-11-25 08:11:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2954  



---

## Comment 1

> Username: vinniefalco  
> Created_at: 2024-11-23 23:46:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2954#issuecomment-2495695253  

This is looking great now. If you could please change the README.md to include updated instructions for building the 32 bit and the 64 bit solutions. I used:  
  
```  
cmake -G "Visual Studio 17 2022" -A x64 -B bin64 -DBUILD_TESTING=ON -DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake -DVCPKG_CHAINLOAD_TOOLCHAIN_FILE="C:/Users/vinnie/src/boost/libs/http_io/cmake/toolchains/msvc.cmake"  
```

---

## Comment 2

> Username: ashtum  
> Created_at: 2024-11-24 04:26:46 UTC  
> Url: https://github.com/boostorg/beast/pull/2954#issuecomment-2495796986  

> This is looking great now. If you could please change the README.md to include updated instructions for building the 32 bit and the 64 bit solutions. I used:  
>   
> ```  
> cmake -G "Visual Studio 17 2022" -A x64 -B bin64 -DBUILD_TESTING=ON -DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake -DVCPKG_CHAINLOAD_TOOLCHAIN_FILE="C:/Users/vinnie/src/boost/libs/http_io/cmake/toolchains/msvc.cmake"  
> ```  
  
There's no need to set `BUILD_TESTING` anymore. When Boost.Beast is the root project, it includes `CTest`, which automatically sets `BUILD_TESTING`:  
  
```cpp  
if (BOOST_BEAST_IS_ROOT)  
    include(CTest)  
endif ()  
option(BOOST_BEAST_BUILD_TESTS "Build boost::beast tests" ${BUILD_TESTING})  
```

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2024-11-24 18:00:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2954#issuecomment-2496140693  

Don't tell me that just edit the command line as you see fit, and insert it into the README

---

## Comment 4

> Username: ashtum  
> Created_at: 2024-11-24 18:04:14 UTC  
> Updated_at: 2024-11-24 18:22:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2954#issuecomment-2496141935  

> Don't tell me that just edit the command line as you see fit, and insert it into the README  
  
It's already in the README, do you want me to change `C:/boost/libs/beast/cmake/toolchains/msvc.cmake` to `C:/Users/vinnie/src/boost/libs/beast/cmake/toolchains/msvc.cmake`?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2024-11-24 23:46:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2954#issuecomment-2496350622  

There is no file "C:/boost/libs/beast/cmake/toolchains/msvc.cmake". You could refer to this path instead:  
https://github.com/boostorg/url/blob/develop/cmake/toolchains/msvc.cmake

---
