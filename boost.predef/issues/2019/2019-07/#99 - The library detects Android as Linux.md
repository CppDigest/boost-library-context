# #99 - The library detects Android as Linux [Closed]

> Username: goodguysoft  
> Created at: 2019-07-17 11:39:26 UTC  
> Updated at: 2020-02-29 15:40:34 UTC  
> Closed at: 2020-02-29 15:40:34 UTC  
> Url: https://github.com/boostorg/predef/issues/99  

The library needs to fix the bug unless it is expected behavior. I use Windows, Android Studio and NDK 20.0.55594570 (the latest one for now) to build native library. My source code follows:  
  
```  
#include <boost/predef.h>  
#if defined(linux) || defined(__linux) || defined(__linux__) || defined(__gnu_linux__)  
#pragma message ("OS is Linux")  
#else  
#pragma message ("OS is NOT Linux")  
#endif  
#if defined(__ANDROID__)  
#pragma message ("OS is Android")  
#else  
#pragma message ("OS is NOT Android")  
#endif  
#if BOOST_OS_LINUX  
#pragma message ("BOOST: OS is Linux")  
#else  
#pragma message ("BOOST: OS is NOT Linux")  
#endif  
#if BOOST_OS_ANDROID  
#pragma message ("BOOST: OS is Android")  
#else  
#pragma message ("BOOST: OS is NOT Android")  
#endif  
```  
  
The output follows:  
  
```  
C:/Users/Vitaliy/Business/src/internal/good_socks/android/app/src/main/cpp/test-os.cpp:3:9: warning: OS is Linux [-W#pragma-messages]  
#pragma message ("OS is Linux")  
        ^  
C:/Users/Vitaliy/Business/src/internal/good_socks/android/app/src/main/cpp/test-os.cpp:8:9: warning: OS is Android [-W#pragma-messages]  
#pragma message ("OS is Android")  
        ^  
C:/Users/Vitaliy/Business/src/internal/good_socks/android/app/src/main/cpp/test-os.cpp:13:9: warning: BOOST: OS is Linux [-W#pragma-messages]  
#pragma message ("BOOST: OS is Linux")  
        ^  
C:/Users/Vitaliy/Business/src/internal/good_socks/android/app/src/main/cpp/test-os.cpp:20:9: warning: BOOST: OS is NOT Android [-W#pragma-messages]  
#pragma message ("BOOST: OS is NOT Android")  
```  
  
As you can see, it looks like Android NDK defines `linux` somewhere, and `boost/predef/os/linux.h` detects the OS as linux as a result; after that it doesn't try to check if Linux is Android in `android.h`; you probably have to add `&& !defined(__ANDROID__)` to `linux.h` to fix it.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-02-29 15:40:33 UTC  
> Url: https://github.com/boostorg/predef/issues/99#issuecomment-592957940  

This is now fixed by https://github.com/boostorg/predef/commit/966707dc1fb44c264fce1697e9559454c50d84c6 -- The OS_ANDROID predef was deprecated some time ago in favor of PLATFORM_ANDROID. Now the expected for this use case is for OS_LINUX *and* PLAFORM_ANDROID to be detected simultaneously.
