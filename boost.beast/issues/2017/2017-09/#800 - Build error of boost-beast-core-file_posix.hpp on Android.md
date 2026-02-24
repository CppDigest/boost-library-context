# #800 - Build error of boost/beast/core/file_posix.hpp on Android [Closed]

> Username: zhaohuaxishi  
> Created at: 2017-09-29 11:17:43 UTC  
> Updated at: 2019-02-23 05:44:00 UTC  
> Closed at: 2019-02-23 05:44:00 UTC  
> Url: https://github.com/boostorg/beast/issues/800  

When complie the `boost/beast/core/file_posix.hpp` use ndk-build from android-ndk-r13b i got following error:  
  
```  
[armeabi-v7a] Compile++ thumb: crvp <= http.cpp  
In file included from /home/guorongfei/Workspace/playground/android-beast-build-error/jni/http.cpp:12:  
In file included from /home/guorongfei/Workspace/playground/android-beast-build-error/jni/boost/beast/http.hpp:24:  
In file included from /home/guorongfei/Workspace/playground/android-beast-build-error/jni/boost/beast/http/file_body.hpp:13:  
In file included from /home/guorongfei/Workspace/playground/android-beast-build-error/jni/boost/beast/core/file.hpp:15:  
In file included from /home/guorongfei/Workspace/playground/android-beast-build-error/jni/boost/beast/core/file_posix.hpp:171:  
boost/beast/core/impl/file_posix.ipp:129:18: error: use of undeclared identifier 'POSIX_FADV_RANDOM'  
        advise = POSIX_FADV_RANDOM;  
                 ^  
boost/beast/core/impl/file_posix.ipp:135:18: error: use of undeclared identifier 'POSIX_FADV_SEQUENTIAL'  
        advise = POSIX_FADV_SEQUENTIAL;  
                 ^  
boost/beast/core/impl/file_posix.ipp:142:18: error: use of undeclared identifier 'POSIX_FADV_RANDOM'  
        advise = POSIX_FADV_RANDOM;  
                 ^  
boost/beast/core/impl/file_posix.ipp:149:18: error: use of undeclared identifier 'POSIX_FADV_RANDOM'  
        advise = POSIX_FADV_RANDOM;  
                 ^  
boost/beast/core/impl/file_posix.ipp:156:18: error: use of undeclared identifier 'POSIX_FADV_RANDOM'  
        advise = POSIX_FADV_RANDOM;  
                 ^  
boost/beast/core/impl/file_posix.ipp:163:18: error: use of undeclared identifier 'POSIX_FADV_SEQUENTIAL'  
        advise = POSIX_FADV_SEQUENTIAL;  
                 ^  
boost/beast/core/impl/file_posix.ipp:170:18: error: use of undeclared identifier 'POSIX_FADV_SEQUENTIAL'  
        advise = POSIX_FADV_SEQUENTIAL;  
                 ^  
boost/beast/core/impl/file_posix.ipp:177:18: error: use of undeclared identifier 'POSIX_FADV_SEQUENTIAL'  
        advise = POSIX_FADV_SEQUENTIAL;  
                 ^  
boost/beast/core/impl/file_posix.ipp:194:10: error: no member named 'posix_fadvise' in the global namespace  
    if(::posix_fadvise(fd_, 0, 0, advise))  
       ~~^  
9 errors generated.  
  
```  
  
### Version of Beast  
  
```  
#define BOOST_BEAST_VERSION 117   
```  
  
### Steps necessary to reproduce the problem  
  
[source code](https://github.com/zhaohuaxishi/android-beast-build-error)  
  
### All relevant compiler information  
  
I use android-ndk-r13b, and here is the version of the compiler:  
  
```  
$ arm-linux-androideabi-clang++ --version     
Android clang version 3.8.256229  (based on LLVM 3.8.256229)  
Target: armv7a-none-linux-android  
Thread model: posix  
InstalledDir: /opt/standalone-toolchains/arm-gnustl/bin  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-29 11:52:25 UTC  
> Url: https://github.com/boostorg/beast/issues/800#issuecomment-333106349  

I don't build on Android so I don't have any good way to fix this. You have a couple of options:  
  
1. Fix this yourself. I can help you understand the code (it is easy). We can merge your changes into the main repository once it works.  
2. Define `BOOST_BEAST_NO_POSIX_FILE=0` in your project. Beast will use `file_stdio` for the implementation, which should work

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-02-23 05:44:00 UTC  
> Url: https://github.com/boostorg/beast/issues/800#issuecomment-466619561  

There's no need to throw out the whole `file_posix`, we just need to conditionally disable the fadvise. And it looks like this is already done, see `BOOST_BEAST_USE_POSIX_FADVISE` in file_posix.hpp. If this is still a problem, feel free to open a new issue.
