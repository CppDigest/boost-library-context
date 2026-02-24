# #20 - Problems building in appveyor for cygwin [Closed]

> Username: jeking3  
> Created at: 2018-05-14 12:03:38 UTC  
> Updated at: 2018-05-19 03:01:40 UTC  
> Closed at: 2018-05-19 03:01:40 UTC  
> Url: https://github.com/boostorg/pool/issues/20  

Example job:  
  
https://ci.appveyor.com/project/jeking3/pool/build/1.0.5-develop/job/g88520uu7ssvdo8r  
  
Example error:  
  
```  
In file included from ./boost/thread/win32/condition_variable.hpp:19:0,  
                 from ./boost/thread/condition_variable.hpp:14,  
                 from ./boost/thread/thread_only.hpp:26,  
                 from libs\thread\src\win32\thread.cpp:11:  
./boost/thread/lock_guard.hpp:65:40: warning: invoking macro BOOST_THREAD_RELEASE argument 1: empty macro arguments are undefined in ISO C++98 [-Wpedantic]  
     ~lock_guard() BOOST_THREAD_RELEASE()  
                                        ^  
libs\thread\src\win32\thread.cpp: In function 'void boost::{anonymous}::create_current_thread_tls_key()':  
libs\thread\src\win32\thread.cpp:86:37: error: 'tss_cleanup_implemented' was not declared in this scope  
             tss_cleanup_implemented(); // if anyone uses TSS, we need the cleanup linked in  
                                     ^  
    "g++"   -std=c++03 -m64 -mthreads -O3 -finline-functions -Wno-inline -Wall -pedantic -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_USES_CHRONO -DBOOST_THREAD_WIN32 -DNDEBUG  -I"." -c -o "bin.v2\libs\thread\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\threadapi-win32\threading-multi\win32\thread.o" "libs\thread\src\win32\thread.cpp"  
...failed gcc.compile.c++ bin.v2\libs\thread\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\threadapi-win32\threading-multi\win32\thread.o...  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2018-05-19 03:01:40 UTC  
> Url: https://github.com/boostorg/pool/issues/20#issuecomment-390373807  

Resolved with the latest CI PR.
