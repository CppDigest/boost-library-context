# #38 - fails to compile with Clang on Windows [Closed]

> Username: SSE4  
> Created at: 2017-09-01 07:51:59 UTC  
> Updated at: 2017-09-02 17:25:56 UTC  
> Closed at: 2017-09-02 17:25:56 UTC  
> Url: https://github.com/boostorg/log/issues/38  

boost log fails to compile with Clang on WIndows  
```  
libs\log\src\timestamp.cpp:60:8: error: 'alignas' attribute cannot be applied to types  
static BOOST_ALIGNMENT(BOOST_LOG_CPU_CACHE_LINE_SIZE) boost::atomic< uint64_t > g_ticks;  
       ^  
.\boost/config/detail/suffix.hpp:681:32: note: expanded from macro 'BOOST_ALIGNMENT'  
#    define BOOST_ALIGNMENT(x) alignas(x)  
                               ^  
6 warnings and 1 error generated.  
```  
my user-config.jam  
```  
using clang : 5.0 :  
 "C:/Program Files/LLVM/bin/clang.exe" :  
 <compileflags>"-fmsc-version=1910 -DBOOST_USE_WINAPI_VERSION=0x0502 -DBOOST_USE_WINDOWS_H=1 -Wno-c++11-narrowing -D_WIN32_WINNT=0x0502 -DNOMINMAX"  
 <ranlib>"C:/Program Files/LLVM/bin/llvm-ranlib.exe"   
 <archiver>"C:/Program Files/LLVM/bin/llvm-ar.exe"   
 <linkflags>-fuse-ld=lld ;  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2017-09-01 12:53:35 UTC  
> Url: https://github.com/boostorg/log/issues/38#issuecomment-326572473  

Does it compile if you swap places `static` and `BOOST_ALIGNMENT(BOOST_LOG_CPU_CACHE_LINE_SIZE)` in this line?

---

## Comment 2

> Username: SSE4  
> Created at: 2017-09-02 05:50:53 UTC  
> Url: https://github.com/boostorg/log/issues/38#issuecomment-326724193  

@Lastique yes, it helps
