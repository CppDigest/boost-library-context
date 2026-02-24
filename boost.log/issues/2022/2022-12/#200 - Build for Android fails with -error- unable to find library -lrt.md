# #200 - Build for Android fails with "error: unable to find library -lrt" [Closed]

> Username: mojca  
> Created at: 2022-12-03 21:36:06 UTC  
> Updated at: 2022-12-04 10:24:35 UTC  
> Closed at: 2022-12-03 21:52:45 UTC  
> Url: https://github.com/boostorg/log/issues/200  

The code  
  
https://github.com/boostorg/log/blob/develop/CMakeLists.txt#L321-L324  
  
```cmake  
if (CMAKE_SYSTEM_NAME STREQUAL "Linux")  
    list(APPEND boost_log_common_private_defines _XOPEN_SOURCE=600)  
    list(APPEND boost_log_private_libs rt)  
endif()  
```  
  
results in  
```  
ld.lld: error: unable to find library -lrt  
```  
when building our codebase for Android.  
```  
export NDK_PATH=$HOME/android/android-ndk-r25b  
cmake -G Ninja \  
    -DCMAKE_TOOLCHAIN_FILE=${NDK_PATH}/build/cmake/android.toolchain.cmake \  
    -DANDROID_ABI=arm64-v8a \  
    -DANDROID_NDK=${NDK_PATH} \  
    -DANDROID_PLATFORM=android-24 \  
    -DCMAKE_ANDROID_NDK=${NDK_PATH} \  
    -DCMAKE_EXPORT_COMPILE_COMMANDS=ON \  
    -DCMAKE_SYSTEM_NAME=Android \  
    -DCMAKE_SYSTEM_VERSION=24 \  
    ..  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2022-12-03 21:52:45 UTC  
> Url: https://github.com/boostorg/log/issues/200#issuecomment-1336264845  

That line is guarded by `if (CMAKE_SYSTEM_NAME STREQUAL "Linux")`, which is correct and intended. Apparently, your `-DCMAKE_SYSTEM_NAME=Android` argument is not applied for some reason or overridden somewhere outside Boost. Maybe your CMake version doesn't support Android, I'm not sure. In any case, I don't see what I can do about this.  
  
You may want to try building Boost using Boost.Build instead of CMake.

---

## Comment 2

> Username: mojca  
> Created at: 2022-12-04 10:24:34 UTC  
> Url: https://github.com/boostorg/log/issues/200#issuecomment-1336370498  

I'm sorry. I kept digging further and it seems that I made a false diagnosis (I wasn't initially able to find the flag anywhere else, but eventually spotted it in another external library that was dislocated from the source where I searched).  
  
I'm sorry for the noise.  
  
https://github.com/open62541/open62541/issues/5495
