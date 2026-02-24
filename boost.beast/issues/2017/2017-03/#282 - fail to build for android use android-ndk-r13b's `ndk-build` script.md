# #282 - fail to build for android use android-ndk-r13b's `ndk-build` script [Closed]

> Username: zhaohuaxishi  
> Created at: 2017-03-17 06:56:41 UTC  
> Updated at: 2017-03-18 10:22:03 UTC  
> Closed at: 2017-03-18 10:22:03 UTC  
> Url: https://github.com/boostorg/beast/issues/282  

i trying to use ndk-build to build beast for android, and i got following error:  
  
```  
../src/contrib/beast/http/detail/rfc7230.hpp:241:9: error: constant expression evaluates to -1 which cannot be narrowed to type 'char' [-Wc++11-narrowing]  
        -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, // 0  
        ^~  
```

---

## Comment 1

> Username: zhaohuaxishi  
> Created at: 2017-03-18 10:22:03 UTC  
> Url: https://github.com/boostorg/beast/issues/282#issuecomment-287533110  

if i add ` -Wno-narrowing` to cppflags  
  
```  
LOCAL_CPPFLAGS += -Wno-narrowing  
```  
  
and link against `c++_static` as c++ runtime, it works.  
  
```  
APP_STL = c++_static  
```  
  
[C++ Library Support](https://developer.android.com/ndk/guides/cpp-support.html)  
[Android.mk](https://developer.android.com/ndk/guides/android_mk.html)
